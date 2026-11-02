/* Skyline service worker
   - App shell: cache-first (instant load, works offline)
   - Weather/AQI API calls: network-first, falling back to the last cached response
   - Navigation requests: network first, falling back to offline.html
*/
const SHELL_CACHE = "skyline-shell-v1";
const DATA_CACHE = "skyline-data-v1";

const SHELL_FILES = [
  "./index.html",
  "./style.css",
  "./script.js",
  "./manifest.json",
  "./offline.html",
  "./icon-192.png",
  "./icon-512.png"
];

self.addEventListener("install", (event) => {
  event.waitUntil(
    caches.open(SHELL_CACHE).then((cache) => cache.addAll(SHELL_FILES)).then(() => self.skipWaiting())
  );
});

self.addEventListener("activate", (event) => {
  event.waitUntil(
    caches.keys().then((keys) =>
      Promise.all(keys.filter((k) => k !== SHELL_CACHE && k !== DATA_CACHE).map((k) => caches.delete(k)))
    ).then(() => self.clients.claim())
  );
});

const isWeatherApi = (url) =>
  url.includes("api.open-meteo.com") ||
  url.includes("air-quality-api.open-meteo.com") ||
  url.includes("archive-api.open-meteo.com") ||
  url.includes("geocoding-api.open-meteo.com") ||
  url.includes("bigdatacloud.net");

self.addEventListener("fetch", (event) => {
  const req = event.request;
  const url = req.url;

  // Navigation: network first, offline page fallback
  if (req.mode === "navigate") {
    event.respondWith(
      fetch(req).catch(() => caches.match("./offline.html"))
    );
    return;
  }

  // Weather/geocoding data: network-first, cache fallback (offline forecast)
  if (isWeatherApi(url)) {
    event.respondWith(
      fetch(req)
        .then((res) => {
          const clone = res.clone();
          caches.open(DATA_CACHE).then((cache) => cache.put(req, clone));
          return res;
        })
        .catch(() => caches.match(req))
    );
    return;
  }

  // App shell + same-origin static assets: cache-first
  if (SHELL_FILES.some((f) => url.endsWith(f.replace("./", "")))) {
    event.respondWith(
      caches.match(req).then((cached) => cached || fetch(req))
    );
    return;
  }

  // Everything else (CDN libs, map tiles): try network, fall back to cache if present
  event.respondWith(
    fetch(req).catch(() => caches.match(req))
  );
});

/* Background Sync (optional) — requires a backend endpoint to actually sync data.
   Registered here as a stub so the hook exists; without a server this simply
   re-fetches on the next connectivity event handled in script.js. */
self.addEventListener("sync", (event) => {
  if (event.tag === "skyline-refresh") {
    // No server endpoint configured — left as an extension point.
  }
});

/* Push Notifications (optional) — requires a backend + VAPID keys to send pushes.
   Listener kept as a stub; wire up your push server and call
   registration.pushManager.subscribe(...) from script.js to activate. */
self.addEventListener("push", (event) => {
  if (!event.data) return;
  const data = event.data.json();
  event.waitUntil(
    self.registration.showNotification(data.title || "Skyline Weather", {
      body: data.body || "",
      icon: "./icon-192.png"
    })
  );
});
