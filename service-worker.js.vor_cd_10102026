const VERSION = 'v1';
const APP_CACHE = 'mein-jahr-app-' + VERSION;
const FONT_CACHE = 'mein-jahr-fonts-' + VERSION;

const APP_DATEIEN = [
  './',
  './index.html',
  './manifest.webmanifest',
  './icon-192.png',
  './icon-512.png',
];

self.addEventListener('install', (e) => {
  e.waitUntil(
    caches.open(APP_CACHE)
      .then((c) => c.addAll(APP_DATEIEN))
      .then(() => self.skipWaiting())
  );
});

self.addEventListener('activate', (e) => {
  e.waitUntil(
    caches.keys()
      .then((namen) => Promise.all(
        namen
          .filter((n) => n !== APP_CACHE && n !== FONT_CACHE)
          .map((n) => caches.delete(n))
      ))
      .then(() => self.clients.claim())
  );
});

self.addEventListener('fetch', (e) => {
  const req = e.request;
  if (req.method !== 'GET') return;

  const url = new URL(req.url);
  const istSchrift = url.hostname === 'fonts.googleapis.com' ||
                     url.hostname === 'fonts.gstatic.com';

  // Schriften: erst Cache, sonst Netz - und dann fuer offline behalten.
  if (istSchrift) {
    e.respondWith(
      caches.match(req).then((treffer) => treffer || fetch(req).then((res) => {
        const kopie = res.clone();
        caches.open(FONT_CACHE).then((c) => c.put(req, kopie));
        return res;
      }).catch(() => new Response('', { status: 504 })))
    );
    return;
  }

  if (url.origin !== self.location.origin) return;

  // Seitenaufrufe: Netz zuerst, damit Updates sofort ankommen; offline aus dem Cache.
  if (req.mode === 'navigate') {
    e.respondWith(
      fetch(req)
        .then((res) => {
          const kopie = res.clone();
          caches.open(APP_CACHE).then((c) => c.put('./index.html', kopie));
          return res;
        })
        .catch(() => caches.match('./index.html').then((t) => t || caches.match('./')))
    );
    return;
  }

  e.respondWith(
    caches.match(req).then((treffer) => treffer || fetch(req))
  );
});
