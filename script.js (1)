/* =========================================================
   SKYLINE — Weather Website
   APIs used (all free, no API key required):
   - Geocoding:        https://geocoding-api.open-meteo.com
   - Reverse geocode:  https://api.bigdatacloud.net (client, no key)
   - Forecast:         https://api.open-meteo.com
   - Air Quality:      https://air-quality-api.open-meteo.com
   ========================================================= */

(() => {
  "use strict";

  /* ---------------- Config ---------------- */
  // Optional: paste a free OpenWeatherMap API key here to unlock the
  // Temperature / Cloud / Wind color map overlays (Part 4). Radar,
  // satellite and terrain work with no key at all.
  const OPENWEATHERMAP_KEY = "";

  const POPULAR_CITIES = ["Dhaka", "London", "New York", "Tokyo", "Dubai", "Kolkata"];
  const MAX_RECENT = 6;
  const MAX_FAVORITES = 10;

  const WMO = {
    0:  { icon: "☀️", en: "Clear sky",        bn: "পরিষ্কার আকাশ" },
    1:  { icon: "🌤️", en: "Mostly clear",      bn: "মোটামুটি পরিষ্কার" },
    2:  { icon: "⛅",  en: "Partly cloudy",     bn: "আংশিক মেঘলা" },
    3:  { icon: "☁️",  en: "Overcast",          bn: "মেঘলা" },
    45: { icon: "🌫️", en: "Fog",               bn: "কুয়াশা" },
    48: { icon: "🌫️", en: "Rime fog",          bn: "তুষার কুয়াশা" },
    51: { icon: "🌦️", en: "Light drizzle",     bn: "হালকা গুঁড়ি বৃষ্টি" },
    53: { icon: "🌦️", en: "Drizzle",           bn: "গুঁড়ি বৃষ্টি" },
    55: { icon: "🌧️", en: "Dense drizzle",     bn: "ঘন গুঁড়ি বৃষ্টি" },
    61: { icon: "🌧️", en: "Light rain",        bn: "হালকা বৃষ্টি" },
    63: { icon: "🌧️", en: "Rain",              bn: "বৃষ্টি" },
    65: { icon: "🌧️", en: "Heavy rain",        bn: "ভারী বৃষ্টি" },
    66: { icon: "🌨️", en: "Freezing rain",     bn: "হিমায়িত বৃষ্টি" },
    67: { icon: "🌨️", en: "Heavy freezing rain", bn: "ভারী হিমায়িত বৃষ্টি" },
    71: { icon: "🌨️", en: "Light snow",        bn: "হালকা তুষারপাত" },
    73: { icon: "🌨️", en: "Snow",              bn: "তুষারপাত" },
    75: { icon: "❄️", en: "Heavy snow",        bn: "ভারী তুষারপাত" },
    77: { icon: "❄️", en: "Snow grains",       bn: "তুষার কণা" },
    80: { icon: "🌦️", en: "Light showers",     bn: "হালকা ঝরনা বৃষ্টি" },
    81: { icon: "🌧️", en: "Showers",           bn: "ঝরনা বৃষ্টি" },
    82: { icon: "⛈️", en: "Violent showers",   bn: "প্রবল ঝরনা বৃষ্টি" },
    85: { icon: "🌨️", en: "Snow showers",      bn: "তুষার ঝরনা" },
    86: { icon: "❄️", en: "Heavy snow showers", bn: "ভারী তুষার ঝরনা" },
    95: { icon: "⛈️", en: "Thunderstorm",      bn: "বজ্রঝড়" },
    96: { icon: "⛈️", en: "Thunderstorm + hail", bn: "শিলাসহ বজ্রঝড়" },
    99: { icon: "⛈️", en: "Severe thunderstorm", bn: "তীব্র বজ্রঝড়" }
  };

  const I18N = {
    en: {
      offline: "You're offline — showing the last saved forecast.",
      searchPlaceholder: "Search city or country…",
      recent: "Recent:", popular: "Popular:",
      loading: "Fetching the sky…", cityNotFound: "City not found. Check the spelling and try again.",
      retry: "Retry", details: "Weather Details", sun: "Sun", moon: "Moon",
      moonPhase: "Phase", illumination: "Illumination",
      moonNote: "Moonrise/moonset times need a premium ephemeris API — not included in the free data source used here.",
      hourly: "Next 24 Hours", daily: "7-Day Forecast", airQuality: "Air Quality", rain: "Rain",
      map: "Weather Map", mapNote: "Base map shows your location. Precipitation/wind/cloud tile layers require a paid tile provider and aren't included here.",
      charts: "Charts", settings: "Settings", unit: "Units", language: "Language",
      autoLocate: "Auto-detect location", refresh: "Refresh Now", favorites: "Favorite Cities",
      noFavorites: "No favorites yet — tap ♡ on a city to save it.", maxmin: "max/min",
      aiInsights: "Smart Weather Assistant",
      aiNote: "Rule-based on-device suggestions from the live data above — not a hosted AI model, so it works without any API key.",
      aiSummary: "Summary", aiClothing: "What to wear", aiUmbrella: "Umbrella",
      aiActivity: "Outdoor activity", aiTravel: "Travel", aiFarming: "Farming note", aiShare: "Share AI Report"
    },
    bn: {
      offline: "আপনি অফলাইনে আছেন — শেষ সংরক্ষিত পূর্বাভাস দেখানো হচ্ছে।",
      searchPlaceholder: "শহর বা দেশ খুঁজুন…",
      recent: "সাম্প্রতিক:", popular: "জনপ্রিয়:",
      loading: "আকাশের তথ্য আনা হচ্ছে…", cityNotFound: "শহর পাওয়া যায়নি। বানান পরীক্ষা করে আবার চেষ্টা করুন।",
      retry: "আবার চেষ্টা করুন", details: "আবহাওয়ার বিস্তারিত", sun: "সূর্য", moon: "চাঁদ",
      moonPhase: "দশা", illumination: "আলোকিত অংশ",
      moonNote: "চাঁদ ওঠা/অস্তের সময় জানতে প্রিমিয়াম API প্রয়োজন — এখানে ব্যবহৃত ফ্রি ডেটা সোর্সে নেই।",
      hourly: "পরবর্তী ২৪ ঘণ্টা", daily: "৭ দিনের পূর্বাভাস", airQuality: "বাতাসের মান", rain: "বৃষ্টি",
      map: "আবহাওয়ার মানচিত্র", mapNote: "মূল মানচিত্রে আপনার অবস্থান দেখানো হয়। বৃষ্টি/বাতাস/মেঘের লেয়ারের জন্য পেইড টাইল প্রোভাইডার লাগে, যা এখানে নেই।",
      charts: "গ্রাফ", settings: "সেটিংস", unit: "একক", language: "ভাষা",
      autoLocate: "স্বয়ংক্রিয় অবস্থান শনাক্তকরণ", refresh: "রিফ্রেশ করুন", favorites: "প্রিয় শহরসমূহ",
      noFavorites: "এখনো কোনো প্রিয় শহর নেই — যোগ করতে ♡ চাপুন।", maxmin: "সর্বোচ্চ/সর্বনিম্ন",
      aiInsights: "স্মার্ট ওয়েদার অ্যাসিস্ট্যান্ট",
      aiNote: "উপরের লাইভ ডেটা থেকে রুল-ভিত্তিক পরামর্শ — কোনো হোস্টেড AI মডেল নয়, তাই API কী ছাড়াই কাজ করে।",
      aiSummary: "সারাংশ", aiClothing: "কী পরবেন", aiUmbrella: "ছাতা",
      aiActivity: "বাইরের কার্যক্রম", aiTravel: "ভ্রমণ", aiFarming: "কৃষি পরামর্শ", aiShare: "AI রিপোর্ট শেয়ার করুন"
    }
  };

  /* ---------------- State ---------------- */
  const state = {
    unit: localStorage.getItem("sky_unit") || "c",
    lang: localStorage.getItem("sky_lang") || "en",
    theme: localStorage.getItem("sky_theme") || "dark",
    autoLocate: localStorage.getItem("sky_autolocate") !== "false",
    current: null,       // last fetched raw data bundle
    charts: {},
    map: null,
    mapMarker: null,
    liveMarker: null,
    baseLayer: null,
    overlayLayer: null,
    activeOverlay: null,
    activeBasemap: "streets",
    soundOn: false,
    audioCtx: null,
    audioNodes: null,
    displayedTemp: null
  };

  const $ = (id) => document.getElementById(id);

  /* ---------------- Utility ---------------- */
  function cToF(c) { return c * 9 / 5 + 32; }
  function fmtTemp(celsius) {
    if (celsius === null || celsius === undefined || isNaN(celsius)) return "--";
    const v = state.unit === "f" ? cToF(celsius) : celsius;
    return Math.round(v);
  }
  function fmtTime(iso) {
    if (!iso) return "--:--";
    const d = new Date(iso);
    return d.toLocaleTimeString(state.lang === "bn" ? "bn-BD" : "en-US", { hour: "2-digit", minute: "2-digit" });
  }
  function fmtHour(iso) {
    const d = new Date(iso);
    return d.toLocaleTimeString("en-US", { hour: "numeric" });
  }
  function fmtDay(iso) {
    const d = new Date(iso + "T00:00:00");
    return d.toLocaleDateString("en-US", { weekday: "short" });
  }
  function animateTempCounter(target) {
    const el = $("current-temp");
    const start = state.displayedTemp ?? target;
    const duration = 700;
    const t0 = performance.now();
    function step(now) {
      const p = Math.min(1, (now - t0) / duration);
      const eased = 1 - Math.pow(1 - p, 3);
      const val = Math.round(start + (target - start) * eased);
      el.textContent = val;
      if (p < 1) requestAnimationFrame(step);
      else state.displayedTemp = target;
    }
    requestAnimationFrame(step);
  }
  function windDir(deg) {
    const dirs = ["N", "NE", "E", "SE", "S", "SW", "W", "NW"];
    return dirs[Math.round(deg / 45) % 8];
  }
  function toast(msg) {
    const t = $("toast");
    t.textContent = msg;
    t.classList.add("show");
    setTimeout(() => t.classList.remove("show"), 2400);
  }
  function t(key) { return (I18N[state.lang] && I18N[state.lang][key]) || I18N.en[key] || key; }

  /* ---------------- i18n apply ---------------- */
  function applyI18n() {
    document.querySelectorAll("[data-i18n]").forEach(el => {
      el.textContent = t(el.getAttribute("data-i18n"));
    });
    document.querySelectorAll("[data-i18n-placeholder]").forEach(el => {
      el.placeholder = t(el.getAttribute("data-i18n-placeholder"));
    });
    $("unit-label").textContent = state.unit === "f" ? "F" : "C";
  }

  /* ---------------- Moon phase (Conway-ish algorithm) ---------------- */
  function moonPhase(date) {
    let year = date.getFullYear(), month = date.getMonth() + 1, day = date.getDate();
    if (month < 3) { year--; month += 12; }
    month++;
    const c = 365.25 * year, e = 30.6 * month;
    let jd = c + e + day - 694039.09;
    jd /= 29.5305882;
    let b = Math.floor(jd);
    jd -= b;
    const age = jd * 29.5305882; // days since new moon
    const illum = Math.round((1 - Math.cos(jd * 2 * Math.PI)) / 2 * 100);
    let phaseName, icon;
    if (jd < 0.03 || jd > 0.97) { phaseName = "New Moon"; icon = "🌑"; }
    else if (jd < 0.22) { phaseName = "Waxing Crescent"; icon = "🌒"; }
    else if (jd < 0.28) { phaseName = "First Quarter"; icon = "🌓"; }
    else if (jd < 0.47) { phaseName = "Waxing Gibbous"; icon = "🌔"; }
    else if (jd < 0.53) { phaseName = "Full Moon"; icon = "🌕"; }
    else if (jd < 0.72) { phaseName = "Waning Gibbous"; icon = "🌖"; }
    else if (jd < 0.78) { phaseName = "Last Quarter"; icon = "🌗"; }
    else { phaseName = "Waning Crescent"; icon = "🌘"; }
    return { phaseName, icon, illum, age };
  }

  /* ================= API calls ================= */
  async function geocodeCity(name) {
    const url = `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(name)}&count=6&language=en&format=json`;
    const res = await fetch(url);
    if (!res.ok) throw new Error("geocode_failed");
    const data = await res.json();
    return data.results || [];
  }

  async function reverseGeocode(lat, lon) {
    try {
      const url = `https://api.bigdatacloud.net/data/reverse-geocode-client?latitude=${lat}&longitude=${lon}&localityLanguage=en`;
      const res = await fetch(url);
      if (!res.ok) throw new Error("reverse_failed");
      const data = await res.json();
      return {
        name: data.city || data.locality || data.principalSubdivision || "Current location",
        country: data.countryName || ""
      };
    } catch (e) {
      return { name: "Current location", country: "" };
    }
  }

  async function fetchForecast(lat, lon) {
    const params = new URLSearchParams({
      latitude: lat, longitude: lon,
      current: "temperature_2m,relative_humidity_2m,apparent_temperature,is_day,precipitation,rain,weather_code,cloud_cover,pressure_msl,wind_speed_10m,wind_direction_10m,wind_gusts_10m",
      hourly: "temperature_2m,precipitation_probability,weather_code,wind_speed_10m,relative_humidity_2m,dew_point_2m,uv_index,visibility,precipitation,snowfall,pressure_msl",
      daily: "weather_code,temperature_2m_max,temperature_2m_min,sunrise,sunset,uv_index_max,precipitation_probability_max,precipitation_sum,snowfall_sum",
      timezone: "auto",
      forecast_days: "7"
    });
    const res = await fetch(`https://api.open-meteo.com/v1/forecast?${params.toString()}`);
    if (!res.ok) throw new Error("forecast_failed");
    return res.json();
  }

  async function fetchAirQuality(lat, lon) {
    const params = new URLSearchParams({
      latitude: lat, longitude: lon,
      current: "pm10,pm2_5,carbon_monoxide,nitrogen_dioxide,sulphur_dioxide,ozone,us_aqi",
      hourly: "us_aqi",
      forecast_days: "3",
      timezone: "auto"
    });
    try {
      const res = await fetch(`https://air-quality-api.open-meteo.com/v1/air-quality?${params.toString()}`);
      if (!res.ok) throw new Error("aqi_failed");
      return res.json();
    } catch (e) { return null; }
  }

  /* ================= Rendering ================= */
  function aqiStatus(aqi) {
    if (aqi == null) return { label: "--", color: "var(--muted)" };
    if (aqi <= 50) return { label: "Good", color: "#7CE38B" };
    if (aqi <= 100) return { label: "Moderate", color: "#FFD166" };
    if (aqi <= 150) return { label: "Unhealthy (Sensitive)", color: "#FF9F5A" };
    if (aqi <= 200) return { label: "Unhealthy", color: "#FF6B6B" };
    if (aqi <= 300) return { label: "Very Unhealthy", color: "#C77DFF" };
    return { label: "Hazardous", color: "#8B2942" };
  }

  function updateSkyBackground(code, isDay) {
    const grad = $("sky-gradient");
    let colors;
    if (!isDay) {
      colors = "radial-gradient(ellipse 120% 60% at 50% -10%, rgba(111,231,221,0.08), transparent 60%), linear-gradient(180deg,#05070F 0%,#0B1026 60%,#05070F 100%)";
    } else if (code >= 95) {
      colors = "radial-gradient(ellipse 120% 60% at 50% -10%, rgba(140,147,184,0.18), transparent 60%), linear-gradient(180deg,#232744 0%,#141A2E 60%,#0B1026 100%)";
    } else if (code >= 61 || (code >= 51 && code <= 55) || (code >= 80 && code <= 82)) {
      colors = "radial-gradient(ellipse 120% 60% at 50% -10%, rgba(111,231,221,0.14), transparent 60%), linear-gradient(180deg,#1B2540 0%,#141B34 60%,#0B1026 100%)";
    } else if (code >= 71 && code <= 86) {
      colors = "radial-gradient(ellipse 120% 60% at 50% -10%, rgba(242,240,233,0.15), transparent 60%), linear-gradient(180deg,#232A44 0%,#161C34 60%,#0B1026 100%)";
    } else if (code >= 1 && code <= 3) {
      colors = "radial-gradient(ellipse 120% 60% at 50% -10%, rgba(255,180,84,0.14), transparent 60%), linear-gradient(180deg,#182140 0%,#141B34 60%,#0B1026 100%)";
    } else {
      colors = "radial-gradient(ellipse 120% 60% at 50% -10%, rgba(255,180,84,0.20), transparent 60%), linear-gradient(180deg,#101736 0%,#0F1530 55%,#0B1026 100%)";
    }
    grad.style.background = colors;
    setupSkyParticles(code, isDay);
  }

  function render(bundle) {
    const { place, forecast, aqi } = bundle;
    const cur = forecast.current;
    const code = cur.weather_code;
    const wmo = WMO[code] || WMO[0];
    const isDay = !!cur.is_day;

    // Location + time
    $("city-name").textContent = place.name;
    $("country-name").textContent = place.country || "";
    const now = new Date();
    $("current-date").textContent = now.toLocaleDateString(state.lang === "bn" ? "bn-BD" : "en-US", { weekday: "long", month: "short", day: "numeric" });
    $("current-time").textContent = now.toLocaleTimeString(state.lang === "bn" ? "bn-BD" : "en-US", { hour: "2-digit", minute: "2-digit" });

    // Hero
    const iconEl = $("weather-icon");
    iconEl.textContent = wmo.icon;
    iconEl.classList.remove("spin-slow", "pulse", "shake");
    if (code === 0 && isDay) iconEl.classList.add("spin-slow");
    else if (code >= 95) iconEl.classList.add("pulse");
    else if ((code >= 51 && code <= 82) || (cur.wind_speed_10m ?? 0) > 25) iconEl.classList.add("shake");
    animateTempCounter(fmtTemp(cur.temperature_2m));
    $("weather-desc").textContent = state.lang === "bn" ? wmo.bn : wmo.en;
    $("feels-like").textContent = fmtTemp(cur.apparent_temperature);
    const today = forecast.daily;
    $("temp-max").textContent = fmtTemp(today.temperature_2m_max[0]);
    $("temp-min").textContent = fmtTemp(today.temperature_2m_min[0]);
    $("unit-label").textContent = state.unit === "f" ? "F" : "C";

    // Details
    $("stat-humidity").textContent = `${cur.relative_humidity_2m ?? "--"}%`;
    $("stat-wind").textContent = `${Math.round(cur.wind_speed_10m ?? 0)} km/h`;
    $("stat-wind-dir").textContent = cur.wind_direction_10m != null ? windDir(cur.wind_direction_10m) : "--";
    $("stat-pressure").textContent = `${Math.round(cur.pressure_msl ?? 0)} hPa`;
    const visKm = forecast.hourly.visibility ? (forecast.hourly.visibility[0] / 1000).toFixed(1) : "--";
    $("stat-visibility").textContent = `${visKm} km`;
    $("stat-uv").textContent = today.uv_index_max ? today.uv_index_max[0].toFixed(1) : "--";
    $("stat-dewpoint").textContent = `${forecast.hourly.dew_point_2m ? fmtTemp(forecast.hourly.dew_point_2m[0]) : "--"}°`;
    $("stat-cloud").textContent = `${cur.cloud_cover ?? "--"}%`;

    // Sun
    $("sunrise").textContent = fmtTime(today.sunrise[0]);
    $("sunset").textContent = fmtTime(today.sunset[0]);
    const sr = new Date(today.sunrise[0]), ss = new Date(today.sunset[0]);
    const dayMs = ss - sr;
    $("day-length").textContent = `${Math.floor(dayMs / 3600000)}h ${Math.round((dayMs % 3600000) / 60000)}m`;
    const golden = new Date(ss.getTime() - 55 * 60000);
    $("golden-hour").textContent = fmtTime(golden.toISOString());
    // sun arc position based on time-of-day fraction
    const frac = Math.min(1, Math.max(0, (now - sr) / dayMs));
    const angle = Math.PI * (1 - frac);
    const cx = 100 - 90 * Math.cos(angle), cy = 90 - 90 * Math.sin(angle);
    $("sun-pos").setAttribute("cx", cx);
    $("sun-pos").setAttribute("cy", cy);

    // Moon
    const moon = moonPhase(now);
    $("moon-icon").textContent = moon.icon;
    $("moon-phase").textContent = moon.phaseName;
    $("moon-illum").textContent = `${moon.illum}%`;

    // Hourly (next 24h from now)
    renderHourly(forecast.hourly);

    // Daily
    renderDaily(today);

    // AQI
    renderAqi(aqi);

    // Rain
    const rainIdx = 0;
    $("rain-chance").textContent = `${today.precipitation_probability_max ? today.precipitation_probability_max[0] : 0}%`;
    $("rain-volume").textContent = `${today.precipitation_sum ? today.precipitation_sum[0].toFixed(1) : "0.0"} mm`;
    $("snow-volume").textContent = `${today.snowfall_sum ? today.snowfall_sum[0].toFixed(1) : "0.0"} cm`;

    // Charts
    renderCharts(forecast.hourly);
    renderAqiTrendChart(aqi);
    renderWeeklyChart(today);
    renderMonthlyStats(place.latitude, place.longitude);

    // Map
    renderMap(place.latitude, place.longitude, place.name);

    // Sky background
    updateSkyBackground(code, isDay);

    // Favorite button state
    updateFavoriteBtn(place);

    // AI insights
    generateAiInsights(bundle);

    // Sound mode follows the current condition
    if (state.soundOn) startSoundForCode(code);

    $("weather-content").classList.remove("hidden");
    $("error-box").classList.add("hidden");
    $("loading").classList.add("hidden");
  }

  function renderHourly(hourly) {
    const wrap = $("hourly-scroll");
    wrap.innerHTML = "";
    const now = new Date();
    let startIdx = hourly.time.findIndex(tm => new Date(tm) >= now);
    if (startIdx < 0) startIdx = 0;
    for (let i = startIdx; i < Math.min(startIdx + 24, hourly.time.length); i++) {
      const code = hourly.weather_code[i];
      const wmo = WMO[code] || WMO[0];
      const card = document.createElement("div");
      card.className = "hour-card";
      card.innerHTML = `
        <div class="h-time">${fmtHour(hourly.time[i])}</div>
        <div class="h-icon">${wmo.icon}</div>
        <div class="h-temp">${fmtTemp(hourly.temperature_2m[i])}°</div>
        <div class="h-rain">💧 ${hourly.precipitation_probability[i] ?? 0}%</div>
      `;
      wrap.appendChild(card);
    }
  }

  function renderDaily(daily) {
    const wrap = $("daily-list");
    wrap.innerHTML = "";
    for (let i = 0; i < daily.time.length; i++) {
      const code = daily.weather_code[i];
      const wmo = WMO[code] || WMO[0];
      const row = document.createElement("div");
      row.className = "day-row";
      row.innerHTML = `
        <span>${i === 0 ? "Today" : fmtDay(daily.time[i])}</span>
        <span class="d-icon">${wmo.icon}</span>
        <span class="day-rain">💧 ${daily.precipitation_probability_max ? daily.precipitation_probability_max[i] : 0}%</span>
        <span class="day-temps"><span class="max">${fmtTemp(daily.temperature_2m_max[i])}°</span><span class="min">${fmtTemp(daily.temperature_2m_min[i])}°</span></span>
      `;
      wrap.appendChild(row);
    }
  }

  function renderAqi(aqi) {
    if (!aqi || !aqi.current) {
      $("aqi-value").textContent = "--";
      $("aqi-status").textContent = "--";
      return;
    }
    const c = aqi.current;
    const status = aqiStatus(c.us_aqi);
    $("aqi-value").textContent = Math.round(c.us_aqi ?? 0);
    $("aqi-status").textContent = status.label;
    $("aqi-status").style.color = status.color;
    $("aqi-status").style.background = status.color + "26";
    $("aqi-pm25").textContent = c.pm2_5 != null ? c.pm2_5.toFixed(1) : "--";
    $("aqi-pm10").textContent = c.pm10 != null ? c.pm10.toFixed(1) : "--";
    $("aqi-co").textContent = c.carbon_monoxide != null ? Math.round(c.carbon_monoxide) : "--";
    $("aqi-no2").textContent = c.nitrogen_dioxide != null ? c.nitrogen_dioxide.toFixed(1) : "--";
    $("aqi-so2").textContent = c.sulphur_dioxide != null ? c.sulphur_dioxide.toFixed(1) : "--";
    $("aqi-o3").textContent = c.ozone != null ? c.ozone.toFixed(1) : "--";
  }

  function renderCharts(hourly) {
    if (typeof Chart === "undefined") return;
    const now = new Date();
    let startIdx = hourly.time.findIndex(tm => new Date(tm) >= now);
    if (startIdx < 0) startIdx = 0;
    const labels = hourly.time.slice(startIdx, startIdx + 12).map(fmtHour);
    const slice = (arr) => (arr || []).slice(startIdx, startIdx + 12);

    const baseOpts = {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend: { display: false } },
      scales: {
        x: { ticks: { color: "#8C93B8", font: { size: 10 } }, grid: { display: false } },
        y: { ticks: { color: "#8C93B8", font: { size: 10 } }, grid: { color: "rgba(255,255,255,0.06)" } }
      }
    };

    const makeChart = (key, canvasId, data, color, label) => {
      if (state.charts[key]) state.charts[key].destroy();
      const ctx = $(canvasId).getContext("2d");
      state.charts[key] = new Chart(ctx, {
        type: "line",
        data: { labels, datasets: [{ data, borderColor: color, backgroundColor: color + "33", fill: true, tension: 0.4, pointRadius: 0, borderWidth: 2, label }] },
        options: baseOpts
      });
    };

    makeChart("temp", "chart-temp", slice(hourly.temperature_2m).map(v => state.unit === "f" ? cToF(v) : v), "#FFB454", "Temp");
    makeChart("humidity", "chart-humidity", slice(hourly.relative_humidity_2m), "#6FE7DD", "Humidity");
    makeChart("wind", "chart-wind", slice(hourly.wind_speed_10m), "#C77DFF", "Wind");
    makeChart("rain", "chart-rain", slice(hourly.precipitation_probability), "#7CB9FF", "Rain %");
    if (hourly.pressure_msl) makeChart("pressure", "chart-pressure", slice(hourly.pressure_msl), "#7CE38B", "Pressure");
    if (hourly.uv_index) makeChart("uv", "chart-uv", slice(hourly.uv_index), "#FFD166", "UV Index");
  }

  function renderAqiTrendChart(aqiData) {
    if (typeof Chart === "undefined" || !aqiData || !aqiData.hourly || !aqiData.hourly.us_aqi) return;
    const now = new Date();
    let startIdx = aqiData.hourly.time.findIndex(tm => new Date(tm) >= now);
    if (startIdx < 0) startIdx = 0;
    const labels = aqiData.hourly.time.slice(startIdx, startIdx + 12).map(fmtHour);
    const data = aqiData.hourly.us_aqi.slice(startIdx, startIdx + 12);
    if (state.charts.aqiTrend) state.charts.aqiTrend.destroy();
    state.charts.aqiTrend = new Chart($("chart-aqi").getContext("2d"), {
      type: "line",
      data: { labels, datasets: [{ data, borderColor: "#FF9F5A", backgroundColor: "#FF9F5A33", fill: true, tension: 0.4, pointRadius: 0, borderWidth: 2, label: "AQI" }] },
      options: {
        responsive: true, maintainAspectRatio: false,
        plugins: { legend: { display: false } },
        scales: {
          x: { ticks: { color: "#8C93B8", font: { size: 10 } }, grid: { display: false } },
          y: { ticks: { color: "#8C93B8", font: { size: 10 } }, grid: { color: "rgba(255,255,255,0.06)" } }
        }
      }
    });
  }

  function renderWeeklyChart(daily) {
    if (typeof Chart === "undefined") return;
    const labels = daily.time.map((tm, i) => i === 0 ? "Today" : fmtDay(tm));
    const maxV = daily.temperature_2m_max.map(v => state.unit === "f" ? cToF(v) : v);
    const minV = daily.temperature_2m_min.map(v => state.unit === "f" ? cToF(v) : v);
    if (state.charts.weekly) state.charts.weekly.destroy();
    state.charts.weekly = new Chart($("chart-weekly").getContext("2d"), {
      type: "bar",
      data: {
        labels,
        datasets: [
          { data: maxV, backgroundColor: "#FFB454", label: "Max", borderRadius: 4 },
          { data: minV, backgroundColor: "#6FE7DD", label: "Min", borderRadius: 4 }
        ]
      },
      options: {
        responsive: true, maintainAspectRatio: false,
        plugins: { legend: { display: true, labels: { color: "#8C93B8", font: { size: 10 }, boxWidth: 10 } } },
        scales: {
          x: { ticks: { color: "#8C93B8", font: { size: 10 } }, grid: { display: false } },
          y: { ticks: { color: "#8C93B8", font: { size: 10 } }, grid: { color: "rgba(255,255,255,0.06)" } }
        }
      }
    });
  }

  async function renderMonthlyStats(lat, lon) {
    try {
      const end = new Date(); end.setDate(end.getDate() - 1);
      const start = new Date(); start.setDate(start.getDate() - 30);
      const fmt = (d) => d.toISOString().slice(0, 10);
      const params = new URLSearchParams({
        latitude: lat, longitude: lon, start_date: fmt(start), end_date: fmt(end),
        daily: "temperature_2m_max,temperature_2m_min,precipitation_sum", timezone: "auto"
      });
      const res = await fetch(`https://archive-api.open-meteo.com/v1/archive?${params.toString()}`);
      if (!res.ok) return;
      const data = await res.json();
      const avgMax = data.daily.temperature_2m_max.reduce((a, b) => a + b, 0) / data.daily.temperature_2m_max.length;
      const avgMin = data.daily.temperature_2m_min.reduce((a, b) => a + b, 0) / data.daily.temperature_2m_min.length;
      const totalRain = data.daily.precipitation_sum.reduce((a, b) => a + b, 0);
      const u = state.unit === "f";
      $("monthly-stats-note").textContent =
        `Last 30 days: avg high ${Math.round(u ? cToF(avgMax) : avgMax)}°, avg low ${Math.round(u ? cToF(avgMin) : avgMin)}°, total rainfall ${totalRain.toFixed(0)} mm.`;
    } catch (e) { /* optional feature — fail silently */ }
  }

  const BASEMAPS = {
    streets: () => L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", { attribution: "&copy; OpenStreetMap contributors", maxZoom: 19 }),
    satellite: () => L.tileLayer("https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}", { attribution: "Tiles &copy; Esri", maxZoom: 19 }),
    terrain: () => L.tileLayer("https://{s}.tile.opentopomap.org/{z}/{x}/{y}.png", { attribution: "&copy; OpenTopoMap (CC-BY-SA)", maxZoom: 17 })
  };

  function setBasemap(key) {
    if (!state.map) return;
    if (state.baseLayer) state.map.removeLayer(state.baseLayer);
    state.baseLayer = BASEMAPS[key]().addTo(state.map);
    state.activeBasemap = key;
    document.querySelectorAll("[data-basemap]").forEach(b => b.classList.toggle("active", b.dataset.basemap === key));
  }

  async function setOverlay(key) {
    if (!state.map) return;
    if (state.overlayLayer) { state.map.removeLayer(state.overlayLayer); state.overlayLayer = null; }
    document.querySelectorAll("[data-overlay]").forEach(b => b.classList.toggle("active", b.dataset.overlay === key));
    const legend = $("map-legend");

    if (state.activeOverlay === key) { state.activeOverlay = null; legend.classList.add("hidden"); document.querySelectorAll("[data-overlay]").forEach(b => b.classList.remove("active")); return; }
    state.activeOverlay = key;

    if (key === "radar") {
      try {
        const res = await fetch("https://api.rainviewer.com/public/weather-maps.json");
        const data = await res.json();
        const frame = data.radar && data.radar.past ? data.radar.past.slice(-1)[0] : null;
        if (frame) {
          state.overlayLayer = L.tileLayer(`https://tilecache.rainviewer.com${frame.path}/256/{z}/{x}/{y}/2/1_1.png`, { opacity: 0.65 }).addTo(state.map);
        }
        legend.innerHTML = `<div class="legend-row"><span class="swatch" style="background:#3EDBF0"></span>Light rain</div><div class="legend-row"><span class="swatch" style="background:#FFD166"></span>Moderate</div><div class="legend-row"><span class="swatch" style="background:#FF5A5A"></span>Heavy</div>`;
        legend.classList.remove("hidden");
      } catch (e) { toast("Radar unavailable right now"); }
      return;
    }

    // Temp / Cloud / Wind — needs a free OpenWeatherMap key
    if (!OPENWEATHERMAP_KEY) {
      toast("Add a free OpenWeatherMap key in script.js to enable this layer");
      state.activeOverlay = null;
      document.querySelectorAll("[data-overlay]").forEach(b => b.classList.remove("active"));
      return;
    }
    const owmLayer = { temp: "temp_new", cloud: "clouds_new", wind: "wind_new" }[key];
    state.overlayLayer = L.tileLayer(`https://tile.openweathermap.org/map/${owmLayer}/{z}/{x}/{y}.png?appid=${OPENWEATHERMAP_KEY}`, { opacity: 0.55 }).addTo(state.map);
    legend.innerHTML = `<div class="legend-row">Live ${key} layer (OpenWeatherMap)</div>`;
    legend.classList.remove("hidden");
  }

  function renderMap(lat, lon, name) {
    if (typeof L === "undefined") return;
    if (!state.map) {
      state.map = L.map("weather-map", { zoomControl: true }).setView([lat, lon], 9);
      setBasemap(state.activeBasemap);
      state.mapMarker = L.marker([lat, lon]).addTo(state.map).bindPopup(name);
      document.querySelectorAll("[data-basemap]").forEach(btn => btn.addEventListener("click", () => setBasemap(btn.dataset.basemap)));
      document.querySelectorAll("[data-overlay]").forEach(btn => btn.addEventListener("click", () => setOverlay(btn.dataset.overlay)));
    } else {
      state.map.setView([lat, lon], 9);
      state.mapMarker.setLatLng([lat, lon]).bindPopup(name);
    }
    // Live location marker (distinct from the searched-city marker)
    if (navigator.geolocation) {
      navigator.geolocation.getCurrentPosition((pos) => {
        const { latitude, longitude } = pos.coords;
        const liveIcon = L.divIcon({ className: "live-dot", html: '<div style="width:14px;height:14px;border-radius:50%;background:#6FE7DD;border:2px solid #fff;box-shadow:0 0 0 4px rgba(111,231,221,0.35)"></div>' });
        if (state.liveMarker) state.liveMarker.setLatLng([latitude, longitude]);
        else state.liveMarker = L.marker([latitude, longitude], { icon: liveIcon }).addTo(state.map).bindPopup("You are here");
      }, () => {}, { timeout: 5000 });
    }
    setTimeout(() => state.map.invalidateSize(), 200);
  }

  $("map-fullscreen-btn").addEventListener("click", () => {
    const wrap = $("map-wrap");
    if (!document.fullscreenElement) wrap.requestFullscreen?.();
    else document.exitFullscreen?.();
  });
  document.addEventListener("fullscreenchange", () => { if (state.map) setTimeout(() => state.map.invalidateSize(), 200); });

  /* ---------------- Sky particles (rain / snow / stars) ---------------- */
  let particles = [], particleMode = "clear", rafId = null;
  function setupSkyParticles(code, isDay) {
    const canvas = $("sky-canvas");
    canvas.width = window.innerWidth; canvas.height = window.innerHeight;
    if (!isDay && code < 45) particleMode = "stars";
    else if (code >= 61 && code <= 82) particleMode = "rain";
    else if (code >= 71 && code <= 86) particleMode = "snow";
    else particleMode = "none";

    const count = particleMode === "stars" ? 80 : particleMode === "none" ? 0 : 90;
    particles = Array.from({ length: count }, () => ({
      x: Math.random() * canvas.width,
      y: Math.random() * canvas.height,
      speed: particleMode === "rain" ? 6 + Math.random() * 6 : particleMode === "snow" ? 0.6 + Math.random() * 1.2 : 0,
      len: particleMode === "rain" ? 10 + Math.random() * 10 : 0,
      r: particleMode === "snow" ? 1 + Math.random() * 2 : particleMode === "stars" ? 0.6 + Math.random() * 1.4 : 0,
      alpha: 0.3 + Math.random() * 0.7
    }));
    if (rafId) cancelAnimationFrame(rafId);
    animateSky();
  }
  function animateSky() {
    const canvas = $("sky-canvas");
    const ctx = canvas.getContext("2d");
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    if (particleMode === "rain") {
      ctx.strokeStyle = "rgba(111,231,221,0.5)";
      ctx.lineWidth = 1;
      particles.forEach(p => {
        ctx.beginPath(); ctx.moveTo(p.x, p.y); ctx.lineTo(p.x, p.y + p.len); ctx.stroke();
        p.y += p.speed; if (p.y > canvas.height) { p.y = -p.len; p.x = Math.random() * canvas.width; }
      });
    } else if (particleMode === "snow") {
      ctx.fillStyle = "rgba(242,240,233,0.85)";
      particles.forEach(p => {
        ctx.beginPath(); ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2); ctx.fill();
        p.y += p.speed; p.x += Math.sin(p.y * 0.02) * 0.4;
        if (p.y > canvas.height) { p.y = -4; p.x = Math.random() * canvas.width; }
      });
    } else if (particleMode === "stars") {
      particles.forEach(p => {
        ctx.fillStyle = `rgba(255,255,255,${p.alpha})`;
        ctx.beginPath(); ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2); ctx.fill();
        p.alpha += (Math.random() - 0.5) * 0.03;
        p.alpha = Math.max(0.2, Math.min(1, p.alpha));
      });
    }
    rafId = requestAnimationFrame(animateSky);
  }
  window.addEventListener("resize", () => {
    const canvas = $("sky-canvas");
    canvas.width = window.innerWidth; canvas.height = window.innerHeight;
  });

  /* ================= Load flow ================= */
  async function loadByCoords(lat, lon, knownName, knownCountry) {
    showLoading();
    try {
      const [place, forecast, aqi] = await Promise.all([
        knownName ? Promise.resolve({ name: knownName, country: knownCountry, latitude: lat, longitude: lon })
                  : reverseGeocode(lat, lon).then(r => ({ name: r.name, country: r.country, latitude: lat, longitude: lon })),
        fetchForecast(lat, lon),
        fetchAirQuality(lat, lon)
      ]);
      const bundle = { place, forecast, aqi };
      state.current = bundle;
      localStorage.setItem("sky_last", JSON.stringify(bundle));
      render(bundle);
    } catch (e) {
      showError(t("cityNotFound"));
    }
  }

  async function loadByCityName(name) {
    showLoading();
    try {
      const results = await geocodeCity(name);
      if (!results.length) { showError(t("cityNotFound")); return; }
      const r = results[0];
      addRecent(r.name + (r.country ? ", " + r.country : ""));
      await loadByCoords(r.latitude, r.longitude, r.name, r.country);
    } catch (e) {
      showError(t("cityNotFound"));
    }
  }

  function showLoading() {
    $("loading").classList.remove("hidden");
    $("error-box").classList.add("hidden");
  }
  function showError(msg) {
    $("error-message").textContent = msg;
    $("error-box").classList.remove("hidden");
    $("loading").classList.add("hidden");
    $("weather-content").classList.add("hidden");
  }

  /* ================= Recent / Popular / Favorites ================= */
  function getRecent() { return JSON.parse(localStorage.getItem("sky_recent") || "[]"); }
  function addRecent(label) {
    let list = getRecent().filter(c => c !== label);
    list.unshift(label);
    list = list.slice(0, MAX_RECENT);
    localStorage.setItem("sky_recent", JSON.stringify(list));
    renderRecent();
  }
  function renderRecent() {
    const wrap = $("recent-searches");
    wrap.querySelectorAll(".chip").forEach(c => c.remove());
    getRecent().forEach(label => {
      const b = document.createElement("button");
      b.className = "chip"; b.textContent = label;
      b.onclick = () => loadByCityName(label.split(",")[0]);
      wrap.appendChild(b);
    });
  }
  function renderPopular() {
    const wrap = $("popular-cities");
    wrap.querySelectorAll(".chip").forEach(c => c.remove());
    POPULAR_CITIES.forEach(city => {
      const b = document.createElement("button");
      b.className = "chip"; b.textContent = city;
      b.onclick = () => loadByCityName(city);
      wrap.appendChild(b);
    });
  }

  function getFavorites() { return JSON.parse(localStorage.getItem("sky_favorites") || "[]"); }
  function isFavorite(place) { return getFavorites().some(f => f.name === place.name); }
  function toggleFavorite(place) {
    let list = getFavorites();
    if (isFavorite(place)) {
      list = list.filter(f => f.name !== place.name);
    } else {
      if (list.length >= MAX_FAVORITES) { toast("Favorites list is full"); return; }
      list.push({ name: place.name, country: place.country, latitude: place.latitude, longitude: place.longitude });
    }
    localStorage.setItem("sky_favorites", JSON.stringify(list));
    updateFavoriteBtn(place);
    renderFavorites();
  }
  function updateFavoriteBtn(place) {
    $("favorite-btn").textContent = isFavorite(place) ? "♥" : "♡";
  }
  function renderFavorites() {
    const wrap = $("favorites-list");
    const list = getFavorites();
    wrap.innerHTML = "";
    if (!list.length) {
      const span = document.createElement("span");
      span.className = "muted tiny"; span.id = "favorites-empty";
      span.setAttribute("data-i18n", "noFavorites");
      span.textContent = t("noFavorites");
      wrap.appendChild(span);
      return;
    }
    list.forEach(f => {
      const b = document.createElement("button");
      b.className = "chip"; b.textContent = f.name;
      b.onclick = () => loadByCoords(f.latitude, f.longitude, f.name, f.country);
      wrap.appendChild(b);
    });
  }

  /* ================= Search / Autocomplete ================= */
  let debounceTimer = null;
  $("search-input").addEventListener("input", (e) => {
    clearTimeout(debounceTimer);
    const q = e.target.value.trim();
    const list = $("autocomplete-list");
    if (q.length < 2) { list.classList.add("hidden"); return; }
    debounceTimer = setTimeout(async () => {
      try {
        const results = await geocodeCity(q);
        if (!results.length) { list.classList.add("hidden"); return; }
        list.innerHTML = "";
        results.forEach(r => {
          const b = document.createElement("button");
          b.type = "button";
          b.textContent = `${r.name}${r.admin1 ? ", " + r.admin1 : ""}${r.country ? ", " + r.country : ""}`;
          b.onclick = () => {
            $("search-input").value = "";
            list.classList.add("hidden");
            addRecent(r.name + (r.country ? ", " + r.country : ""));
            loadByCoords(r.latitude, r.longitude, r.name, r.country);
          };
          list.appendChild(b);
        });
        list.classList.remove("hidden");
      } catch (e) { list.classList.add("hidden"); }
    }, 350);
  });
  document.addEventListener("click", (e) => {
    if (!$("search-form").contains(e.target)) $("autocomplete-list").classList.add("hidden");
  });

  $("search-form").addEventListener("submit", (e) => {
    e.preventDefault();
    const q = $("search-input").value.trim();
    if (!q) return;
    $("autocomplete-list").classList.add("hidden");
    loadByCityName(q);
  });

  $("locate-btn").addEventListener("click", () => useGeolocation());
  function useGeolocation() {
    if (!navigator.geolocation) { toast("Geolocation not supported"); return; }
    showLoading();
    navigator.geolocation.getCurrentPosition(
      (pos) => loadByCoords(pos.coords.latitude, pos.coords.longitude),
      () => { showError("Location permission denied. Try searching instead."); },
      { timeout: 10000 }
    );
  }

  /* ================= Retry / Refresh ================= */
  $("retry-btn").addEventListener("click", () => {
    if (state.current) loadByCoords(state.current.place.latitude, state.current.place.longitude, state.current.place.name, state.current.place.country);
    else useGeolocation();
  });
  function refresh() {
    if (state.current) loadByCoords(state.current.place.latitude, state.current.place.longitude, state.current.place.name, state.current.place.country);
  }
  $("refresh-btn").addEventListener("click", refresh);
  $("settings-refresh-btn").addEventListener("click", refresh);

  /* ================= Favorite / Share / Copy / Screenshot ================= */
  $("favorite-btn").addEventListener("click", () => { if (state.current) toggleFavorite(state.current.place); });

  function buildReportText() {
    if (!state.current) return "";
    const { place, forecast } = state.current;
    const wmo = WMO[forecast.current.weather_code] || WMO[0];
    return `${place.name}${place.country ? ", " + place.country : ""} — ${fmtTemp(forecast.current.temperature_2m)}°${state.unit.toUpperCase()}, ${state.lang === "bn" ? wmo.bn : wmo.en}. Feels like ${fmtTemp(forecast.current.apparent_temperature)}°. Humidity ${forecast.current.relative_humidity_2m}%. via Skyline`;
  }

  $("share-btn").addEventListener("click", async () => {
    const text = buildReportText();
    if (navigator.share) {
      try { await navigator.share({ title: "Skyline Weather", text }); } catch (e) {}
    } else {
      await navigator.clipboard.writeText(text);
      toast("Copied to clipboard (share not supported)");
    }
  });

  $("copy-btn").addEventListener("click", async () => {
    try { await navigator.clipboard.writeText(buildReportText()); toast("Weather report copied"); }
    catch (e) { toast("Could not copy"); }
  });

  $("screenshot-btn").addEventListener("click", async () => {
    if (typeof html2canvas === "undefined") { toast("Screenshot tool not loaded"); return; }
    toast("Capturing…");
    const canvas = await html2canvas(document.getElementById("weather-content"), { backgroundColor: null });
    const link = document.createElement("a");
    link.download = "skyline-weather.png";
    link.href = canvas.toDataURL();
    link.click();
  });

  /* ================= Weather Sound Mode (synthesized, no audio files) ================= */
  function ensureAudioCtx() {
    if (!state.audioCtx) state.audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    return state.audioCtx;
  }
  function stopSound() {
    if (state.audioNodes) {
      state.audioNodes.forEach(n => { try { n.stop ? n.stop() : n.disconnect(); } catch (e) {} });
      state.audioNodes = null;
    }
  }
  function noiseBuffer(ctx) {
    const bufferSize = 2 * ctx.sampleRate;
    const buffer = ctx.createBuffer(1, bufferSize, ctx.sampleRate);
    const data = buffer.getChannelData(0);
    for (let i = 0; i < bufferSize; i++) data[i] = Math.random() * 2 - 1;
    return buffer;
  }
  function playRain(ctx) {
    const src = ctx.createBufferSource();
    src.buffer = noiseBuffer(ctx); src.loop = true;
    const filter = ctx.createBiquadFilter(); filter.type = "bandpass"; filter.frequency.value = 1800; filter.Q.value = 0.6;
    const gain = ctx.createGain(); gain.gain.value = 0.06;
    src.connect(filter); filter.connect(gain); gain.connect(ctx.destination);
    src.start();
    return [src, filter, gain];
  }
  function playWind(ctx) {
    const src = ctx.createBufferSource();
    src.buffer = noiseBuffer(ctx); src.loop = true;
    const filter = ctx.createBiquadFilter(); filter.type = "lowpass"; filter.frequency.value = 500;
    const gain = ctx.createGain(); gain.gain.value = 0.05;
    const lfo = ctx.createOscillator(); lfo.frequency.value = 0.15;
    const lfoGain = ctx.createGain(); lfoGain.gain.value = 150;
    lfo.connect(lfoGain); lfoGain.connect(filter.frequency);
    src.connect(filter); filter.connect(gain); gain.connect(ctx.destination);
    src.start(); lfo.start();
    return [src, filter, gain, lfo, lfoGain];
  }
  function playThunderOnce(ctx) {
    const src = ctx.createBufferSource();
    src.buffer = noiseBuffer(ctx);
    const filter = ctx.createBiquadFilter(); filter.type = "lowpass"; filter.frequency.value = 200;
    const gain = ctx.createGain();
    gain.gain.setValueAtTime(0, ctx.currentTime);
    gain.gain.linearRampToValueAtTime(0.35, ctx.currentTime + 0.05);
    gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + 1.8);
    src.connect(filter); filter.connect(gain); gain.connect(ctx.destination);
    src.start(); src.stop(ctx.currentTime + 1.8);
  }
  function startSoundForCode(code) {
    stopSound();
    const ctx = ensureAudioCtx();
    if (ctx.state === "suspended") ctx.resume();
    if (code >= 95) {
      state.audioNodes = playRain(ctx);
      state.thunderInterval = setInterval(() => playThunderOnce(ctx), 6000 + Math.random() * 6000);
    } else if ((code >= 51 && code <= 82)) {
      state.audioNodes = playRain(ctx);
    } else if (code >= 1 && code <= 3) {
      state.audioNodes = playWind(ctx);
    } else {
      state.audioNodes = playWind(ctx);
      state.audioNodes[2].gain.value = 0.02;
    }
  }
  $("sound-toggle").addEventListener("click", () => {
    state.soundOn = !state.soundOn;
    $("sound-toggle").textContent = state.soundOn ? "🔊" : "🔇";
    if (state.soundOn && state.current) {
      startSoundForCode(state.current.forecast.current.weather_code);
    } else {
      stopSound();
      if (state.thunderInterval) clearInterval(state.thunderInterval);
    }
  });

  /* ================= Voice Weather Report (Web Speech API, free/offline) ================= */
  $("voice-btn").addEventListener("click", () => {
    if (!("speechSynthesis" in window)) { toast("Voice not supported on this device"); return; }
    const text = buildAiSummaryText() || buildReportText();
    const utter = new SpeechSynthesisUtterance(text);
    utter.lang = state.lang === "bn" ? "bn-BD" : "en-US";
    utter.rate = 0.98;
    speechSynthesis.cancel();
    speechSynthesis.speak(utter);
  });

  /* ================= AI Smart Features (rule-based, on-device) ================= */
  function buildAiSummaryText() {
    if (!state.current) return "";
    return $("ai-summary").textContent;
  }

  function generateAiInsights(bundle) {
    const { forecast, aqi } = bundle;
    const cur = forecast.current;
    const tempC = cur.temperature_2m;
    const feelsC = cur.apparent_temperature;
    const humidity = cur.relative_humidity_2m;
    const wind = cur.wind_speed_10m;
    const code = cur.weather_code;
    const uv = forecast.daily.uv_index_max ? forecast.daily.uv_index_max[0] : 0;
    const rainChance = forecast.daily.precipitation_probability_max ? forecast.daily.precipitation_probability_max[0] : 0;
    const usAqi = aqi && aqi.current ? aqi.current.us_aqi : null;
    const isRainy = (code >= 51 && code <= 82) || code >= 95;
    const isHot = feelsC >= 35;
    const isCold = feelsC <= 10;

    let summary = `${Math.round(tempC)}°C, ${state.lang === "bn" ? (WMO[code] || WMO[0]).bn : (WMO[code] || WMO[0]).en}, feels like ${Math.round(feelsC)}°.`;
    if (isRainy) summary += " Expect wet conditions through the day.";
    else if (isHot) summary += " It's a hot one — stay hydrated.";
    else if (isCold) summary += " Bundle up, it's chilly out there.";
    else summary += " A fairly comfortable day overall.";

    let clothing;
    if (isCold) clothing = "Warm layers, jacket, and something to cover your ears.";
    else if (isHot) clothing = "Light, breathable fabrics and a hat — avoid dark colors.";
    else if (isRainy) clothing = "Water-resistant jacket and closed shoes.";
    else clothing = "Comfortable everyday clothing — no extremes needed.";

    let umbrella = rainChance >= 50 || isRainy
      ? "Yes — carry one, rain is likely today."
      : rainChance >= 25 ? "Maybe — pack a compact one just in case." : "Not needed today.";

    let activity;
    if (isRainy || wind > 40) activity = "Better for indoor plans today — conditions are rough outside.";
    else if (isHot) activity = "Stick to early morning or evening for outdoor activity; midday sun is intense.";
    else if (uv >= 8) activity = "Fine outdoors, but seek shade around midday (UV is very high).";
    else activity = "Good conditions for a walk, run, or outdoor plans.";

    let travel;
    if (code >= 95 || wind > 50) travel = "Delays possible — thunderstorms/high winds may affect flights and roads.";
    else if (isRainy) travel = "Roads may be slick; allow extra travel time.";
    else if ((forecast.hourly.visibility && forecast.hourly.visibility[0] < 2000)) travel = "Low visibility — drive carefully, especially in fog.";
    else travel = "Good travel conditions expected.";

    let farming;
    if (rainChance >= 60) farming = "Good natural irrigation expected — hold off on manual watering.";
    else if (isHot && humidity < 40) farming = "Hot and dry — crops may need extra watering today.";
    else farming = "Stable conditions — routine field work should be fine.";

    $("ai-summary").textContent = summary;
    $("ai-clothing").textContent = clothing;
    $("ai-umbrella").textContent = umbrella;
    $("ai-activity").textContent = activity;
    $("ai-travel").textContent = travel;
    $("ai-farming").textContent = farming;

    // Alerts
    const alertsBox = $("ai-alerts");
    alertsBox.innerHTML = "";
    const addAlert = (msg, level) => {
      const div = document.createElement("div");
      div.className = `ai-alert ${level}`;
      div.textContent = msg;
      alertsBox.appendChild(div);
    };
    if (feelsC >= 40) addAlert("🔥 Heatwave alert — feels-like temperature is dangerously high. Limit outdoor exposure.", "danger");
    else if (feelsC >= 35) addAlert("🌡️ Heat advisory — stay hydrated and avoid strenuous activity outdoors.", "warn");
    if (code >= 95) addAlert("⛈️ Storm warning — thunderstorms expected. Avoid open areas and tall isolated trees.", "danger");
    if (uv >= 8) addAlert("☀️ Very high UV — wear sunscreen and sunglasses, limit midday sun exposure.", "warn");
    if (usAqi != null && usAqi > 150) addAlert("😷 Unhealthy air quality — consider a mask outdoors and limit exertion.", "danger");
    else if (usAqi != null && usAqi > 100) addAlert("🌫️ Moderate-to-poor air quality — sensitive groups should take care outdoors.", "warn");
  }

  $("ai-share-btn").addEventListener("click", async () => {
    if (!state.current) return;
    const parts = [
      $("ai-summary").textContent, "👕 " + $("ai-clothing").textContent, "☂️ " + $("ai-umbrella").textContent,
      "🏃 " + $("ai-activity").textContent
    ];
    const text = parts.join("\n") + "\n— via Skyline";
    if (navigator.share) { try { await navigator.share({ title: "Skyline AI Weather Report", text }); } catch (e) {} }
    else { await navigator.clipboard.writeText(text); toast("AI report copied to clipboard"); }
  });

  /* ================= Theme / Unit / Language ================= */
  function applyTheme() {
    document.body.setAttribute("data-theme", state.theme);
    $("theme-toggle").textContent = state.theme === "dark" ? "🌙" : "☀️";
    localStorage.setItem("sky_theme", state.theme);
  }
  $("theme-toggle").addEventListener("click", () => {
    state.theme = state.theme === "dark" ? "light" : "dark";
    applyTheme();
  });

  document.querySelectorAll("[data-unit-btn]").forEach(btn => {
    btn.addEventListener("click", () => {
      state.unit = btn.getAttribute("data-unit-btn");
      localStorage.setItem("sky_unit", state.unit);
      document.querySelectorAll("[data-unit-btn]").forEach(b => b.classList.toggle("active", b === btn));
      applyI18n();
      if (state.current) render(state.current);
    });
  });

  document.querySelectorAll("[data-lang-btn]").forEach(btn => {
    btn.addEventListener("click", () => {
      state.lang = btn.getAttribute("data-lang-btn");
      localStorage.setItem("sky_lang", state.lang);
      document.querySelectorAll("[data-lang-btn]").forEach(b => b.classList.toggle("active", b === btn));
      applyI18n();
      renderRecent(); renderFavorites();
      if (state.current) render(state.current);
    });
  });

  $("auto-locate-toggle").addEventListener("change", (e) => {
    state.autoLocate = e.target.checked;
    localStorage.setItem("sky_autolocate", state.autoLocate);
  });

  /* ================= Offline handling ================= */
  window.addEventListener("offline", () => $("offline-banner").classList.remove("hidden"));
  window.addEventListener("online", () => { $("offline-banner").classList.add("hidden"); refresh(); });

  /* ================= PWA: service worker registration ================= */
  function registerServiceWorker() {
    if ("serviceWorker" in navigator) {
      window.addEventListener("load", () => {
        navigator.serviceWorker.register("./sw.js").catch(() => { /* offline install not available */ });
      });
    }
  }

  /* ================= Init ================= */
  function init() {
    registerServiceWorker();
    applyI18n();
    applyTheme();
    document.querySelector(`[data-unit-btn="${state.unit}"]`)?.classList.add("active");
    document.querySelectorAll("[data-unit-btn]").forEach(b => b.classList.toggle("active", b.getAttribute("data-unit-btn") === state.unit));
    document.querySelectorAll("[data-lang-btn]").forEach(b => b.classList.toggle("active", b.getAttribute("data-lang-btn") === state.lang));
    $("auto-locate-toggle").checked = state.autoLocate;

    renderPopular();
    renderRecent();
    renderFavorites();

    if (!navigator.onLine) $("offline-banner").classList.remove("hidden");

    const cached = localStorage.getItem("sky_last");
    if (state.autoLocate && navigator.geolocation) {
      navigator.geolocation.getCurrentPosition(
        (pos) => loadByCoords(pos.coords.latitude, pos.coords.longitude),
        () => {
          if (cached) { state.current = JSON.parse(cached); render(state.current); }
          else { loadByCityName("Dhaka"); }
        },
        { timeout: 8000 }
      );
    } else if (cached) {
      state.current = JSON.parse(cached);
      render(state.current);
    } else {
      loadByCityName("Dhaka");
    }
  }

  document.addEventListener("DOMContentLoaded", init);
})();
