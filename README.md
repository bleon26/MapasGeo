# 🧭 MapasGeo — Localizador de Puntos de Interés

App Flutter de la **Práctica Guiada Semana 10 · Geolocalización y Mapas** (Desarrollo Móvil Integral, UTXJ · IDGS).

Muestra tu ubicación en Google Maps en tiempo real, te deja crear destinos tocando el mapa, calcula la distancia en vivo hasta ellos y obtiene su dirección postal (calle y colonia).

**Autor:** Brandon Leon Cabrera

---

## ✅ Requisitos del reto

| Requisito | Dónde está |
|---|---|
| 1. Ubicación actual: centrar cámara en el usuario al iniciar | `MapScreen._onMapCreated → _centerOnMe` |
| 2. Marcador dinámico al tocar el mapa | `GoogleMap.onTap → _addPoi` (pines numerados, arrastrables) |
| 3. Distancia en vivo usuario ↔ destino | `Geolocator.distanceBetween` en `LocationService.distance` |
| 4. Dirección postal (calle y colonia) | `placemarkFromCoordinates` en `GeocodingService.reverse` |
| GPS en tiempo real + permisos | `Geolocator.getPositionStream` (distanceFilter 10 m) + `GateScreen` |
| Ciclo de vida | `dispose()` cancela el `StreamSubscription` y libera el `GoogleMapController` |
| API Key protegida | `android/local.properties` → Gradle → `manifestPlaceholders` (ignorado por Git) |

**Extras:** 3 estilos de mapa (Nocturno, Claro, Satélite), línea de trayecto, círculo de precisión GPS, rumbo/brújula, tiempo a pie, modo seguimiento, lista de puntos ordenada por cercanía (desliza para borrar) y copiar coordenadas.

## 📦 Dependencias

| Paquete | Uso |
|---|---|
| `google_maps_flutter` | Mapa, marcadores, polilíneas y círculos |
| `geolocator` | Permisos, stream de posición y cálculo de distancias |
| `geocoding` | Geocodificación inversa (coordenadas → dirección) |
| `google_fonts` | Tipografías de la interfaz |

---

## ▶️ Cómo ejecutarla

Requisitos: Flutter **3.29 o superior**, Android Studio o VS Code y un dispositivo con Google Play Services.

```bash
git clone https://github.com/bleon26/MapasGeo.git
cd MapasGeo

# 1) Genera los archivos de plataforma que faltan (íconos, gradlew, proyecto Xcode…).
#    NO sobrescribe los archivos ya configurados de este proyecto.
flutter create . --org com.utxj --project-name mapasgeo --platforms android,ios

# 2) Dependencias
flutter pub get

# 3) Ejecutar
flutter run
```

> Si `flutter create` genera un `android/app/build.gradle` (Groovy) además del `build.gradle.kts`, borra el de Groovy: tu Flutter es anterior a 3.29.

### 🔑 API Key de Google Maps

- **Android:** copia `android/local.properties.example` a `android/local.properties` y pon `maps.apikey=TU_LLAVE`.
- **iOS:** crea `ios/Flutter/Secrets.xcconfig` a partir de `Secrets.xcconfig.example` con `MAPS_API_KEY=TU_LLAVE`, luego `cd ios && pod install`.

Ambos archivos están en `.gitignore`, así que **la llave nunca llega a GitHub**. La llave se inyecta al compilar:

```
android/local.properties → build.gradle.kts (manifestPlaceholders) → AndroidManifest.xml
ios/Flutter/Secrets.xcconfig → Info.plist → AppDelegate.swift
```

En Google Cloud la llave está restringida por **paquete `com.utxj.mapasgeo` + SHA-1** y solo a las APIs Maps SDK for Android/iOS y Geocoding API.

Comprobación de que no se filtran secretos:

```bash
git check-ignore -v android/local.properties     # debe mostrar la regla del .gitignore
git grep -n "AIza" $(git rev-list --all)         # no debe devolver nada
```

---

## 🧪 Pruebas

```bash
flutter analyze   # análisis estático
flutter test      # pruebas unitarias de formato (m/km, punto cardinal, coordenadas)
```

---

## 🗂 Estructura

```
lib/
├── main.dart                     # Arranque y tema
├── app/theme.dart                # Paleta y tipografías
├── screens/
│   ├── gate_screen.dart          # Bienvenida, validación de GPS y permisos
│   └── map_screen.dart           # Mapa, stream GPS, marcadores, distancia
├── services/
│   ├── location_service.dart     # geolocator: permisos, stream, distanceBetween
│   └── geocoding_service.dart    # geocoding: coordenadas → calle/colonia (con caché)
├── map/
│   ├── map_styles.dart           # Estilos JSON propios del mapa
│   └── marker_factory.dart       # Pines dibujados en Canvas
├── models/                       # PointOfInterest, Address
├── widgets/                      # Paneles, chips, botones, radar
└── utils/format.dart             # Formato de distancias, coordenadas y rumbo
test/
└── format_test.dart              # Pruebas unitarias
```

## 🌿 Ramas

- `main` — rama principal (incluye la fusión de `MapasGeo`).
- `MapasGeo` — rama de desarrollo de la práctica.
