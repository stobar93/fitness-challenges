# ¿Qué apps de running envían carreras a Apple Health y Health Connect?

Investigación del 9 de octubre de 2026. Complementa [strava-vs-gps-propio.md](strava-vs-gps-propio.md), que recomendó leer las carreras desde Apple Health (iPhone) y Health Connect (Android) en la V1.

## Resumen

1. **Casi todas las apps grandes escriben la actividad (tipo, duración, distancia, calorías, pulso), pero muy pocas escriben la ruta GPS.** Garmin, la marca de reloj más común entre corredores serios, **no envía la ruta** ni a Apple Health ni a Health Connect.
2. **Huawei no escribe en Health Connect** de forma oficial, y en iPhone su envío de entrenamientos a Apple Health es limitado. En Colombia Huawei es una marca de reloj fuerte, así que es el hueco más grande.
3. **En Colombia ~79 % de los teléfonos son Android** (StatCounter, sep. 2026: Android 78,5 %, iOS 21,5 %). Health Connect pesa más que Apple Health para la base de usuarios.
4. Ambas plataformas permiten **distinguir entradas manuales** y saber **qué app y qué dispositivo** generó cada registro.

**Consecuencia para el producto:** la regla del informe anterior ("aceptar solo actividades con ruta GPS") dejaría por fuera a casi todos los usuarios de Garmin, Samsung, Coros y Huawei. Propuesta:

- **Retos de km acumulados:** aceptar actividades sin ruta si vienen de un dispositivo o app de confianza, no son manuales y pasan los filtros de ritmo.
- **Retos de mejor tiempo en ruta:** exigir ruta. En la V1 solo la tienen Apple Watch/iPhone, Strava (grabado en Strava), Polar y pocos más; por eso conviene adelantar el **GPS propio** para estos retos o limitarlos a esas fuentes.

## Tabla por app

Leyenda: ✅ sí, documentado · ❌ no · ⚠️ parcial o con condiciones · ❓ no documentado (hay que probarlo con un dispositivo real).

| App / reloj | Apple Health: actividad | Apple Health: ruta GPS | Health Connect: actividad | Health Connect: ruta GPS | Notas |
|---|---|---|---|---|---|
| **Apple Watch / app Entrenamiento (iPhone)** | ✅ | ✅ | n/a | n/a | Fuente nativa. La ruta llega como `HKWorkoutRoute`. Es el reloj más usado en Strava a nivel mundial (Strava Year in Sport 2024). |
| **Strava** | ✅ distancia, tiempo, calorías, pulso | ⚠️ sí si se grabó en Strava; las subidas de Garmin/Zwift normalmente llegan sin ruta | ✅ tiempo, distancia y calorías de actividades GPS (solo app Android) | ❓ su ayuda no menciona la ruta | En 2022 Strava apagó y luego reactivó el envío de actividades de terceros a Apple Health; puede volver a cambiarlo. |
| **Garmin Connect** | ✅ entrenamiento con distancia, calorías, pulso | ❌ (no aparece como fuente de rutas) | ✅ desde jul. 2025: distancia, velocidad, pulso, pasos, calorías, desnivel | ❌ no está en la lista de datos compartidos | Envío en un solo sentido. No envía cadencia de carrera ni potencia. |
| **Samsung Health / Galaxy Watch** | n/a (no tiene app de iPhone que escriba en Health) | n/a | ✅ sesión de ejercicio + distancia | ❓ no está en la tabla oficial de Samsung | Hay reportes de usuarios de ejercicios que dejan de sincronizar tras actualizaciones. |
| **Huawei Health** | ⚠️ limitado; usuarios piden en el foro de Huawei poder enviar entrenamientos | ❌ | ❌ sin integración oficial (verificado sep. 2026) | ❌ | Solo vía apps puente de terceros (Health Sync), que sí pueden copiar actividades con ubicación. |
| **COROS** | ✅ (pasos, pulso, entrenamientos, sueño) | ❓ | ✅ aparece como app compatible | ❓ | Sin detalle oficial de qué campos envía. |
| **Polar Flow** | ✅ tipo, duración, distancia, energía, pulso medio | ❓ no se menciona | ✅ sesión, vueltas, distancia, velocidad | ✅ "cuando hay ruta disponible" | La integración más completa con Health Connect entre las marcas de relojes. |
| **Suunto** | ✅ entrenamientos y pulso | ❓ | ❓ no documentado | ❓ | Health Sync puede servir de puente. |
| **Amazfit / Zepp** | ✅ ritmo, distancia y calorías de actividades GPS | ❓ | ✅ (desde 2025, entrenamientos) | ❓ | Marca de precio bajo con buena presencia en Colombia. |
| **Xiaomi (Mi Fitness)** | ⚠️ pasos y básicos | ❓ | ✅ pasos, sueño, pulso, entrenamientos | ❓ | |
| **Nike Run Club** | ✅ entrenamientos, distancia, pulso, energía | ❓ | ❓ no hay documentación oficial | ❓ | |
| **adidas Running** | ✅ | ❓ | ❓ | ❓ | adidas cerró oficinas de Runtastic y cortó integraciones de terceros en 2025; riesgo de que la app desaparezca. Prioridad baja. |

## Cómo detectar la fuente y las entradas manuales

| Señal | Apple Health (HealthKit) | Health Connect |
|---|---|---|
| Entrada manual | Metadato `HKMetadataKeyWasUserEntered` | `metadata.recordingMethod` = entrada manual (`Metadata.manualEntry()`) vs. grabado activamente |
| App de origen | `sourceRevision` (bundle id de la app) | `metadata.dataOrigin` (paquete de la app) |
| Dispositivo | `device` del registro (reloj vs. teléfono) | `metadata.device` (tipo: reloj, teléfono…) |
| Ruta | `HKWorkoutRoute` asociada al entrenamiento | `ExerciseRoute` dentro de `ExerciseSessionRecord` |

**Detalle importante de Health Connect:** leer la ruta de otra app requiere el permiso `READ_EXERCISE_ROUTES` **y un consentimiento del usuario por cada sesión**, mostrado desde la app en primer plano. En segundo plano la lectura devuelve "consentimiento requerido". El flujo tendría que ser: el usuario abre la app → "Registrar esta carrera en el reto" → acepta compartir la ruta. Para retos de km sin ruta, la lectura de sesiones y distancia sí puede hacerse en segundo plano.

**Riesgo de fraude a tener en cuenta:** apps puente como Health Sync, o cualquier app con permiso de escritura, pueden crear entrenamientos con ruta. Conviene mantener una **lista blanca de apps de origen** (`dataOrigin` / `sourceRevision`) y tratar con más sospecha lo que venga de puentes.

## Prioridad para Colombia

No encontré datos públicos recientes de qué apps usan los corredores en Colombia (el dato de Euromonitor más citado es de 2019: Apple 45 %, Samsung 32 %, Huawei 12 % en wearables). Con lo que hay, el orden sugerido para probar con dispositivos reales:

1. **Garmin → Health Connect y Apple Health** (corredores de carreras y clubes).
2. **Strava (grabado en el teléfono) → ambas plataformas** (la app gratuita más usada).
3. **Apple Watch / iPhone** (fuente más completa, con ruta).
4. **Samsung Health / Galaxy Watch → Health Connect** (Android dominante).
5. **Huawei** (resolver si se acepta vía Strava o Health Sync, o se deja para el GPS propio).
6. Amazfit/Xiaomi, COROS, Polar, Nike Run Club.

Vale la pena una encuesta corta a clubes de running de Cali preguntando qué reloj y qué app usan; eso cambiaría el orden.

## Pendiente de verificar con dispositivo real

Las casillas ❓ no tienen documentación oficial. Una prueba de un día con 4 o 5 dispositivos (Garmin, Galaxy Watch, Apple Watch, un Huawei y un teléfono con Strava) resolvería la mayoría: grabar una carrera corta y revisar qué llega a cada plataforma, con o sin ruta, y con qué metadatos.

## Fuentes

- [Tom's Guide: lista de datos de Garmin en Health Connect (jul. 2025)](https://www.tomsguide.com/wellness/fitness-trackers/garmin-clarifies-what-data-its-sharing-with-google-health-connect-heres-the-full-list)
- [Notebookcheck: Garmin confirma soporte de Health Connect (jul. 2025)](https://www.notebookcheck.net/Garmin-officially-reveals-Google-Health-Connect-support-for-wearables.1054577.0.html)
- [Foro de Garmin: Garmin Connect no sincroniza rutas a Apple Health](https://forums.garmin.com/apps-software/mobile-apps-web/f/garmin-connect-mobile-ios/352961/garmin-connect-does-not-sync-workout-routes-i-e-gps-data-to-apple-health-app)
- [Strava: Health Connect y Strava](https://support.strava.com/en-us/articles/15401554-health-connect-and-strava)
- [Sonar Health: qué envía Strava a Apple Health](https://www.sonarhealth.co/blog/sync-strava-to-apple-health/)
- [DC Rainmaker: Strava apaga y reactiva el envío a Apple Health (2022)](https://dcrainmaker.com/2022/03/strava-abruptly-health.html)
- [Samsung Developers: datos de Samsung Health en Health Connect](https://developer.samsung.com/health/blog/en/accessing-samsung-health-data-through-health-connect)
- [FitMesh: Huawei Health y Health Connect (2026)](https://www.fitmesh.fit/en/blog/huawei-health-health-connect-sync)
- [Comunidad Huawei: pedido de sincronizar entrenamientos con Apple Health](https://consumer.huawei.com/en/community/details/topicId-179150/)
- [Polar: Flow y Health Connect](https://support.polar.com/en/flow-app-health-connect)
- [Polar: Flow y Apple Health](https://support.polar.com/en/support/connecting_polar_flow_with_apple_health)
- [COROS: apps compatibles](https://support.coros.com/hc/en-us/articles/360040256531-Supported-3rd-Party-Apps)
- [Lifestack: COROS con Apple Health y Health Connect](https://lifestack.ai/blog/apps-to-use-with-coros)
- [Constant: Suunto y Apple Health](https://trainconstant.com/blog/suunto-apple-health)
- [Constant: Amazfit/Zepp y Apple Health](https://trainconstant.com/blog/amazfit-zepp-apple-health)
- [Notebookcheck: Amazfit agrega Health Connect](https://www.notebookcheck.net/Amazfit-smartwatches-get-new-Health-Connect-data-sync-feature.951489.0.html)
- [Reaction Club: Xiaomi y Health Connect](https://www.reaction-club.com/guides/xiaomi-health-connect)
- [Nike: conectar NRC con Apple Health](https://www.nike.com/ca/help/a/connect-nrc-health-app)
- [Smashrun: adidas corta integraciones (2025)](https://blog.smashrun.com/2025/03/20/adidas-integration-discontinued)
- [Health Sync: fuentes y destinos](https://healthsync.app/about/)
- [Android Developers: rutas de ejercicio en Health Connect](https://developer.android.com/health-and-fitness/health-connect/features/exercise-routes)
- [Apple Developer: HKWorkoutRoute](https://developer.apple.com/documentation/healthkit/hkworkoutroute)
- [StatCounter: Android vs iOS en Colombia](https://gs.statcounter.com/os-market-share/mobile/colombia)
- [Tech Advisor: relojes más usados en Strava (2024)](https://www.techadvisor.com/article/2550334/why-this-smartwatch-is-more-popular-vs-garmin-for-strava-runners.html)
- [La República: mercado de wearables en Colombia (2019)](https://www.larepublica.co/empresas/apple-lidera-el-mercado-de-smart-wearables-en-colombia-con-45-de-participacion-2839285)
