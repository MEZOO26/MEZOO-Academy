const CACHE_NAME = "me zoo-academy-v2".replace(" ", "-");

const APP_FILES = [
"./",
"./index.html",
"./manifest.json"
];

self.addEventListener("install", (event) => {
event.waitUntil(
caches.open(CACHE_NAME).then(async (cache) => {
await Promise.all(
APP_FILES.map(async (file) => {
try {
await cache.add(file);
} catch (error) {
console.warn("Could not cache:", file);
}
})
);
})
);

self.skipWaiting();
});

self.addEventListener("activate", (event) => {
event.waitUntil(
caches.keys().then((keys) =>
Promise.all(
keys
.filter((key) => key.startsWith("me-zoo-academy-") &&
key !== CACHE_NAME)
.map((key) => caches.delete(key))
)
)
);

self.clients.claim();
});

self.addEventListener("fetch", (event) => {
const request = event.request;

if (request.method !== "GET") return;

const url = new URL(request.url);

if (url.origin !== self.location.origin) return;

event.respondWith(
caches.match(request).then(async (cached) => {
if (cached) return cached;

  try {
    const response = await fetch(request);

    if (response.ok && response.type === "basic") {
      const cache = await caches.open(CACHE_NAME);
      cache.put(request, response.clone()).catch(() => {});
    }

    return response;
  } catch (error) {
    if (request.mode === "navigate") {
      return (await caches.match("./index.html")) ||
        new Response("أنت غير متصل بالإنترنت. افتح المنصة مرة أخرى بعد الاتصال بالإنترنت.", {
          status: 503,
          headers: { "Content-Type": "text/plain; charset=utf-8" }
        });
    }

    return new Response("", { status: 503 });
  }
})

);
});
