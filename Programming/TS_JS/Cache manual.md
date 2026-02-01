#programming #js #TS #cache #perplexity

# Cache manual

Para garantizar que siempre se cachee sin depender del ciclo de vida del Service Worker, usa la **Cache API directamente desde tu aplicación** [1][2]. La Cache API está disponible tanto en el Service Worker como en el thread principal del navegador.

## Solución: Cachear desde el frontend

Esta estrategia funciona **siempre**, incluso en hard reload o primera carga:

```javascript
// En tu aplicación Vue/JavaScript principal

// Función para cachear videos
async function cacheVideo(videoUrl) {
  try {
    const cache = await caches.open('video-cache-v1');
    
    // Verificar si ya está en caché
    const cached = await cache.match(videoUrl);
    if (cached) {
      console.log('Ya en caché:', videoUrl);
      return;
    }
    
    // Descargar y cachear
    const response = await fetch(videoUrl);
    await cache.put(videoUrl, response);
    console.log('Cacheado exitosamente:', videoUrl);
  } catch (error) {
    console.error('Error cacheando:', error);
  }
}

// Cachear múltiples videos al cargar la app
async function precacheVideos() {
  const videos = [
    '/videos/intro.mp4',
    '/videos/tutorial.mp4',
    '/videos/level1.mp4'
  ];
  
  for (const video of videos) {
    await cacheVideo(video);
  }
}

// Ejecutar al cargar la página
window.addEventListener('DOMContentLoaded', () => {
  if ('caches' in window) {
    precacheVideos();
  }
});
```

## Usar los videos cacheados

Recupera los videos desde la caché cuando los necesites [3]:

```javascript
async function getVideo(videoUrl) {
  const cache = await caches.open('video-cache-v1');
  const cachedResponse = await cache.match(videoUrl);
  
  if (cachedResponse) {
    console.log('Servido desde caché:', videoUrl);
    return cachedResponse;
  }
  
  // Si no está en caché, descargarlo
  console.log('Descargando desde red:', videoUrl);
  const response = await fetch(videoUrl);
  
  // Cachear para siguiente uso
  await cache.put(videoUrl, response.clone());
  return response;
}

// Usar en tu componente
async function playVideo(videoUrl) {
  const response = await getVideo(videoUrl);
  const blob = await response.blob();
  const videoElement = document.querySelector('video');
  videoElement.src = URL.createObjectURL(blob);
}
```

## Estrategia híbrida: Frontend + Service Worker

Combina ambos enfoques para máxima robustez [1]:

**Frontend (app principal):**
```javascript
// main.js
async function precacheImportantVideos() {
  const cache = await caches.open('video-cache-v1');
  const criticalVideos = ['/videos/intro.mp4'];
  
  for (const url of criticalVideos) {
    const response = await fetch(url);
    await cache.put(url, response);
  }
}

// Ejecutar inmediatamente
if ('caches' in window) {
  precacheImportantVideos();
}
```

**Service Worker:**
```javascript
// service-worker.js
self.addEventListener('install', (event) => {
  self.skipWaiting();
});

self.addEventListener('activate', (event) => {
  event.waitUntil(self.clients.claim());
});

// Cache-first para videos
self.addEventListener('fetch', (event) => {
  if (event.request.url.endsWith('.mp4') || 
      event.request.url.endsWith('.webp')) {
    event.respondWith(
      caches.match(event.request).then((cached) => {
        return cached || fetch(event.request).then((response) => {
          return caches.open('video-cache-v1').then((cache) => {
            cache.put(event.request, response.clone());
            return response;
          });
        });
      })
    );
  }
});
```

## Ventajas de cachear desde el frontend

- ✅ Funciona en **primera carga** inmediatamente [1]
- ✅ Funciona con **hard reload**
- ✅ No depende del ciclo de vida del Service Worker [2]
- ✅ Control total sobre cuándo y qué cachear
- ✅ Feedback inmediato al usuario

Con este enfoque, tus videos se cachean **siempre**, sin importar el estado del Service Worker [3][1].
