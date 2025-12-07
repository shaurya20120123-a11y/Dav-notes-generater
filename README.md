
git config user.email "shaurya20120123@gmail.com"
git config user.name "shaurya20120123-a11y"
git branch -M main
git remote add origin https://github.com/shaurya20120123-a11y/Dav-notes-generater.git
git add .
git commit -m "Initial commit"
git push -u origin main

<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>DAV Notes</title>
  <link rel="manifest" href="manifest.webmanifest" />
  <meta name="theme-color" content="#6200ea" />
</head>
<body style="font-family:system-ui,Arial,Helvetica,sans-serif;text-align:center;padding:2rem;">
  <h1>DAV Notes</h1>
  <p>Welcome — this is the PWA entry page.</p>
  <img src="icons/icon-192.png" alt="App icon" width="140" />
  <script>
    if ('serviceWorker' in navigator) {
      navigator.serviceWorker.register('/service-worker.js')
        .then(() => console.log('SW registered'))
        .catch(e => console.warn('SW failed', e));
    }
  </script>
</body>
</html>
{
  "name": "DAV Notes Generator",
  "short_name": "DAVNotes",
  "description": "Generate DAV-style notes and downloads.",
  "start_url": "/index.html",
  "scope": "/",
  "display": "standalone",
  "orientation": "portrait",
  "background_color": "#ffffff",
  "theme_color": "#6200ea",
  "icons": [
    {
      "src": "icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}

const CACHE_NAME = 'dav-notes-cache-v1';
const FILES_TO_CACHE = [
  '/',
  '/index.html',
  '/manifest.webmanifest',
  '/icons/icon-192.png',
  '/icons/icon-512.png'
];

// Install -> cache files
self.addEventListener('install', (evt) => {
  evt.waitUntil(
    caches.open(CACHE_NAME).then((cache) => cache.addAll(FILES_TO_CACHE))
  );
  self.skipWaiting();
});

// Activate -> cleanup old caches
self.addEventListener('activate', (evt) => {
  evt.waitUntil(
    caches.keys().then((keys) => Promise.all(
      keys.map(k => k !== CACHE_NAME ? caches.delete(k) : null)
    ))
  );
  self.clients.claim();
});

// Fetch -> serve from cache first, else network
self.addEventListener('fetch', (evt) => {
  evt.respondWith(
    caches.match(evt.request).then((resp) => resp || fetch(evt.request))
  );
});
