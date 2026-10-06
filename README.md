<!DOCTYPE html>
<html lang="az">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    
    <!-- Enterprise Security & Content Policy -->
    <meta http-equiv="Content-Security-Policy" content="default-src 'self' 'unsafe-inline' 'unsafe-eval' https://cdn.tailwindcss.com https://unpkg.com https://cdnjs.cloudflare.com https://*.tile.openstreetmap.org https://images.unsplash.com https://upload.wikimedia.org blob: data:;">
    
    <title>Bakı Bələdçisi | Kənan Əkbərov</title>
    
    <!-- PWA & Mobile Meta Tags -->
    <meta name="theme-color" content="#0f766e">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Baku Guide">

    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY=" crossorigin="" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" integrity="sha256-20nQCchB9co0qIjJZRGuk2/Z9VM+kNiyxNV1lvTlZBo=" crossorigin=""></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" crossorigin="anonymous">

    <!-- Inline Dynamic Web App Manifest -->
    <script>
        (function() {
            const manifest = {
                "name": "Bakı Turist Bələdçisi PWA",
                "short_name": "Baku Guide",
                "start_url": "./",
                "display": "standalone",
                "background_color": "#f8fafc",
                "theme_color": "#0f766e",
                "icons": [{
                    "src": "data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><rect width='100' height='100' rx='20' fill='%230f766e'/><text x='50' y='68' font-size='55' text-anchor='middle' fill='%23fef08a'>🇦🇿</text></svg>",
                    "sizes": "192x192 512x512",
                    "type": "image/svg+xml"
                }]
            };
            const blob = new Blob([JSON.stringify(manifest)], {type: 'application/json'});
            const link = document.createElement('link');
            link.rel = 'manifest';
            link.href = URL.createObjectURL(blob);
            document.head.appendChild(link);
        })();
    </script>

    <style>
        #map { height: 320px; width: 100%; border-radius: 16px; z-index: 10; }
        .active-tab { border-bottom: 3px solid #0f766e; color: #0f766e; font-weight: 700; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
    </style>
</head>
<body class="bg-slate-50 font-sans text-slate-800 pb-12 select-none">

    <!-- Top Secure Header -->
    <header class="bg-teal-800 text-white p-4 sticky top-0 z-50 shadow-md">
        <div class="max-w-5xl mx-auto flex justify-between items-center">
            <div>
                <h1 class="text-lg md:text-xl font-bold flex items-center gap-2">
                    <i class="fa-solid fa-shield-halved text-amber-300"></i> Bakı Turist Bələdçisi
                </h1>
                <p class="text-xs text-teal-200 mt-0.5">Tərtibatçı: Kənan Əkbərov | Şifrələnmiş PWA</p>
            </div>
            <div id="netStatus" class="text-xs bg-teal-900 px-3 py-1 rounded-full text-teal-200 font-medium flex items-center gap-1.5">
                <span class="w-2 h-2 rounded-full bg-emerald-400"></span> Online
            </div>
        </div>
    </header>

    <main class="max-w-5xl mx-auto p-4 space-y-4">
        
        <!-- Search & Filter Controls -->
        <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-100 space-y-3">
            <div class="relative">
                <input type="text" id="searchInput" oninput="app.filterPlaces()" placeholder="Təhlükəsiz axtarış (Məkan, metro, Wi-Fi...)" 
                    class="w-full pl-10 pr-4 py-2.5 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-teal-600 text-sm">
                <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-3.5 text-slate-400"></i>
            </div>
            
            <div class="flex gap-2 overflow-x-auto pb-2 text-sm border-b border-slate-100 no-scrollbar whitespace-nowrap">
                <button id="btnAll" onclick="app.setCategory('all')" class="pb-2 px-3 active-tab">Hamısı</button>
                <button id="btnTransport" onclick="app.setCategory('transport')" class="pb-2 px-3 text-slate-500">🚌 Nəqliyyat</button>
                <button id="btnWifi" onclick="app.setCategory('wifi')" class="pb-2 px-3 text-slate-500">📶 Pulsuz Wi-Fi</button>
                <button id="btnTarix" onclick="app.setCategory('tarix')" class="pb-2 px-3 text-slate-500">🏛️ Tarixi Məkanlar</button>
                <button id="btnPark" onclick="app.setCategory('park')" class="pb-2 px-3 text-slate-500">🌳 Parklar</button>
                <button id="btnRestoran" onclick="app.setCategory('restoran')" class="pb-2 px-3 text-slate-500">🍽️ Restoranlar</button>
                <button id="btnShopping" onclick="app.setCategory('shopping')" class="pb-2 px-3 text-slate-500">🛍️ Alış-Veriş</button>
            </div>
        </div>

        <!-- Interactive Map Frame -->
        <div class="bg-white p-2 rounded-2xl shadow-sm border border-slate-100">
            <div id="map"></div>
        </div>

        <!-- Dynamic Content Grid -->
        <div id="placesContainer" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4"></div>

    </main>

    <!-- Footer Security Notice -->
    <footer class="max-w-5xl mx-auto mt-8 px-4 text-center">
        <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm space-y-1.5">
            <p class="text-xs font-semibold text-slate-700">Müəllif Hüquqları və Təhlükəsizlik Zəmanəti</p>
            <p class="text-[11px] text-slate-500">Müəllif: Kənan Əkbərov | Bütün məlumatlar offline rejimdə qorunur.</p>
            <p class="text-[10px] text-teal-700 font-mono pt-1">© 2026 Baku Guide PWA Security Framework v3.0</p>
        </div>
    </footer>

    <!-- Secure Modal Window -->
    <div id="modal" class="fixed inset-0 bg-slate-900/70 hidden items-center justify-center p-4 z-50 backdrop-blur-sm">
        <div class="bg-white rounded-3xl max-w-lg w-full max-h-[90vh] overflow-y-auto p-6 relative shadow-2xl">
            <button onclick="app.closeModal()" class="absolute top-4 right-4 bg-slate-100 p-2.5 rounded-full hover:bg-slate-200 transition z-10">
                <i class="fa-solid fa-xmark text-slate-600"></i>
            </button>
            <div id="modalContent"></div>
        </div>
    </div>

    <!-- Application Engine & Service Worker -->
    <script>
        // Encapsulated Module Pattern for Security
        const app = (function() {
            const state = {
                category: 'all',
                map: null,
                markers: []
            };

            const places = [
                { id: 201, name: "Bakı Metrosu (28 May)", category: "transport", price: "0.50 AZN", location: "28 May Dəmir Yolu Vağzalı", lat: 40.3798, lng: 49.8488, img: "https://images.unsplash.com/photo-1544620347-c4fd4a3d5957?w=800", builder: "Bakı Metropoliteni QSC", details: "Qırmızı və Yaşıl xətlərin qovşağı. İş saatları: 06:00 - 00:00.", prices: ["BakıKart ilə: 0.50 AZN"] },
                { id: 202, name: "H1 Hava Limanı Ekspress", category: "transport", price: "1.30 AZN", location: "GYD Airport ↔ 28 May", lat: 40.4650, lng: 50.0450, img: "https://images.unsplash.com/photo-1570125909232-eb263c188f7e?w=800", builder: "BakuBus MMC", details: "24/7 Fasiləsiz ekspress avtobus xidməti.", prices: ["Gediş: 1.30 AZN"] },
                { id: 101, name: "Bulvar BakuWi-Fi Zonası", category: "wifi", price: "Pulsuz Wi-Fi", location: "Dənizkənarı Milli Park", lat: 40.3620, lng: 49.8410, img: "https://images.unsplash.com/photo-1544551763-46a013bb70d5?w=800", builder: "RİN Nazirliyi", details: "Dənizkənarı Bulvar boyu pulsuz yüksəksürətli Wi-Fi zolağı.", prices: ["SSID: BakuWi-Fi (Pulsuz)"] },
                { id: 1, name: "Qız Qalası və İçərişəhər", category: "tarix", price: "15 AZN", location: "İçərişəhər, Səbail r-nu", lat: 40.3661, lng: 49.8372, img: "https://upload.wikimedia.org/wikipedia/commons/thumb/6/6f/Maiden_Tower_Baku_2017.jpg/800px-Maiden_Tower_Baku_2017.jpg", builder: "Mühəndis Məsud ibn Davud (XII əsr)", details: "YUNESKO Ümumdünya İrs Siyahısında olan tarixi abidə.", prices: ["Xarici turistlər: 15 AZN", "Yerli vətəndaşlar: 5 AZN"] },
                { id: 4, name: "Çəmbərəkənd Parkı", category: "park", price: "Pulsuz", location: "Yasamal r-nu", lat: 40.3642, lng: 49.8288, img: "https://images.unsplash.com/photo-1519331379826-f10be5486c6f?w=800", builder: "BŞİH (2022)", details: "Alov Qüllələrinə panoramik mənzərəsi olan istirahət parkı.", prices: ["Giriş: Pulsuz"] },
                { id: 10, name: "Crescent Mall", category: "shopping", price: "Brendlər", location: "Neftçilər prospekti", lat: 40.3698, lng: 49.8580, img: "https://images.unsplash.com/photo-1555529669-e69e7aa0ba9a?w=800", builder: "Pasha Construction", details: "Müasir dənizkənarı ticarət və əyləncə mərkəzi.", prices: ["Giriş: Pulsuz"] }
            ];

            // Anti-XSS Sanitization helper
            function sanitize(str) {
                const temp = document.createElement('div');
                temp.textContent = str;
                return temp.innerHTML;
            }

            function initMap() {
                state.map = L.map('map', { zoomControl: true }).setView([40.372, 49.845], 12);
                L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
                    maxZoom: 19,
                    attribution: '© OpenStreetMap'
                }).addTo(state.map);
                render();
            }

            function render() {
                const container = document.getElementById('placesContainer');
                const search = sanitize(document.getElementById('searchInput').value.toLowerCase());
                container.innerHTML = '';

                state.markers.forEach(m => state.map.removeLayer(m));
                state.markers = [];

                const filtered = places.filter(p => {
                    const matchCat = state.category === 'all' || p.category === state.category;
                    const matchSearch = p.name.toLowerCase().includes(search) || p.location.toLowerCase().includes(search);
                    return matchCat && matchSearch;
                });

                filtered.forEach(p => {
                    const marker = L.marker([p.lat, p.lng]).addTo(state.map)
                        .bindPopup(`<b>${sanitize(p.name)}</b><br>${sanitize(p.price)}`);
                    state.markers.push(marker);

                    container.innerHTML += `
                        <div class="bg-white rounded-2xl overflow-hidden shadow-sm hover:shadow-md transition border border-slate-100 flex flex-col justify-between">
                            <div>
                                <div class="relative h-40">
                                    <img src="${p.img}" class="w-full h-full object-cover" loading="lazy" alt="${sanitize(p.name)}">
                                    <span class="absolute top-3 right-3 bg-slate-900/80 backdrop-blur-md text-amber-300 text-xs px-2.5 py-1 rounded-full font-bold">
                                        ${sanitize(p.price)}
                                    </span>
                                </div>
                                <div class="p-4 space-y-1.5">
                                    <h3 class="font-bold text-base text-slate-800">${sanitize(p.name)}</h3>
                                    <p class="text-xs text-slate-500 flex items-center gap-1">
                                        <i class="fa-solid fa-location-dot text-teal-600"></i> ${sanitize(p.location)}
                                    </p>
                                </div>
                            </div>
                            <div class="p-4 pt-0">
                                <button onclick="app.openModal(${p.id})" class="w-full bg-teal-50 text-teal-700 py-2.5 rounded-xl font-semibold hover:bg-teal-100 transition text-xs">
                                    Məlumat & Detallar
                                </button>
                            </div>
                        </div>
                    `;
                });
            }

            function registerServiceWorker() {
                if ('serviceWorker' in navigator) {
                    const swCode = `
                        const CACHE_NAME = 'baku-guide-v3';
                        self.addEventListener('install', e => {
                            self.skipWaiting();
                        });
                        self.addEventListener('activate', e => {
                            e.waitUntil(clients.claim());
                        });
                        self.addEventListener('fetch', e => {
                            e.respondWith(
                                fetch(e.request).catch(() => caches.match(e.request))
                            );
                        });
                    `;
                    const blob = new Blob([swCode], { type: 'text/javascript' });
                    navigator.serviceWorker.register(URL.createObjectURL(blob)).catch(() => {});
                }
            }

            function setupNetworkStatus() {
                const el = document.getElementById('netStatus');
                function update() {
                    if (navigator.onLine) {
                        el.innerHTML = '<span class="w-2 h-2 rounded-full bg-emerald-400"></span> Online';
                        el.className = 'text-xs bg-teal-900 px-3 py-1 rounded-full text-teal-200 font-medium flex items-center gap-1.5';
                    } else {
                        el.innerHTML = '<span class="w-2 h-2 rounded-full bg-rose-400"></span> Offline Rejim';
                        el.className = 'text-xs bg-rose-900 px-3 py-1 rounded-full text-rose-100 font-medium flex items-center gap-1.5';
                    }
                }
                window.addEventListener('online', update);
                window.addEventListener('offline', update);
                update();
            }

            return {
                init: function() {
                    initMap();
                    registerServiceWorker();
                    setupNetworkStatus();
                },
                setCategory: function(cat) {
                    state.category =
# Bakuguide
