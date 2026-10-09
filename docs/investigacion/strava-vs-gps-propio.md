# ¿Strava en la V1 o GPS propio?

Investigación del 9 de octubre de 2026 para la app de retos de running con premios en dinero.

## Resumen

**No recomiendo construir la V1 sobre Strava.** Sus términos actuales prohíben justo lo que la app necesita: mostrar los datos de un corredor a los demás (el leaderboard). Además, la cuota de atletas y la retención de datos están limitadas, y Strava puede cambiar las reglas sin aviso (lo ha hecho en nov. 2024, jun. 2026 y sep. 2026). Garmin tiene cerrado su programa de desarrolladores para nuevas apps desde la primavera de 2026.

**Recomendación para la V1:** leer las carreras desde **Apple Health (HealthKit) y Health Connect (Android)**, que reciben las actividades de Strava, Garmin, Nike Run Club, Apple Watch y el propio teléfono. No hay un tercero que pueda cerrar el acceso, el leaderboard es tuyo y no hay que construir el tracking GPS todavía. El GPS propio llega en la V2, empezando por los retos de "mejor tiempo en ruta", que son los que más se prestan a fraude.

## 1. Qué permite hoy la API de Strava

| Tema | Situación (oct. 2026) |
|---|---|
| Datos disponibles | Actividades con distancia, tiempo, ritmo, polyline GPS, tipo, si fue manual, dispositivo. Esfuerzos en segmentos. |
| Webhooks | Sí: aviso cuando un atleta crea, edita o borra una actividad, o revoca el acceso. |
| Cuota de atletas | 1 atleta al crear la app, 10 sin revisión. Más de 10 requiere revisión de Strava, sin plazo ni garantía. El nivel Standard llega a 9.999 atletas. |
| Costo | Desde junio de 2026 el desarrollador necesita una suscripción de Strava para el nivel Standard. |
| Límites de uso | 400 peticiones/15 min y 4.000/día en total para toda la app (no por usuario). |
| Retención | Caché limitada a 7 días. Un reto de un mes que guarde km acumulados choca con esto. |
| Segmentos | `segments/explore` ahora exige Extended Access (10.000+ atletas) y un permiso aparte. Los endpoints de clubes se eliminaron el 1 de sep. de 2026. |
| Intermediarios | Ya no se permite pasar datos de Strava por plataformas tipo agregador (p. ej. Terra). |
| IA | Prohibido usar datos de Strava en aplicaciones de IA, incluso anonimizados. |

### El bloqueo principal: el leaderboard

El API Agreement dice que los datos de un usuario "can only be displayed or disclosed in your Developer Application to that user" y que no se pueden mostrar datos de otros usuarios aunque sean públicos en Strava. También dice: "You may not create applications that compete with or replicate Strava functionality" (los leaderboards y segmentos son funcionalidad de Strava).

Un leaderboard público con tiempos o km de cada participante es justo lo que esa cláusula prohíbe. En el foro de desarrolladores de Strava hay preguntas recientes (oct. 2026) sobre retos con leaderboard y consentimiento explícito; la única respuesta, de un miembro de la comunidad, es que "user consent cannot overrule the Strava API policy". Strava no ha respondido públicamente.

Los términos no mencionan explícitamente premios o apuestas, pero prohíben usos ilegales o engañosos. Con dinero en juego, un cambio de reglas o una revocación de acceso a mitad de un reto sería grave.

## 2. Alternativas

| Opción | Ventajas | Problemas |
|---|---|---|
| **Strava API** | Muchos corredores ya lo usan; webhooks; datos ricos. | Prohíbe el leaderboard; cuota de 10 atletas sin revisión; retención de 7 días; reglas cambiantes; posible competencia directa (Strava tiene sus propios retos). |
| **Garmin Connect API** | Datos de reloj, más difíciles de falsificar. | Programa cerrado a nuevas apps desde primavera de 2026, sin fecha de reapertura. |
| **Apple HealthKit + Android Health Connect** | Gratis, en el dispositivo, sin aprobación de terceros. Agrega datos de Strava, Garmin, Nike, Apple Watch, etc. Incluye ruta GPS (con permiso del usuario) y marca si la actividad fue ingresada a mano. El leaderboard es tuyo. | Hay que integrar dos plataformas. La validez depende de la app de origen. Menos control antifraude que un GPS propio. |
| **Wahoo Cloud API** | Abierta, sin pausas reportadas. | Público pequeño, sobre todo ciclistas. |
| **GPS propio** | Control total del dato y de la validación; sin dependencia. | Más trabajo técnico (tracking en segundo plano, batería, precisión), y los corredores con reloj no quieren llevar el teléfono. |

## 3. Riesgos de fraude (con cualquier fuente)

Con premios en dinero, el fraude es el riesgo central:

- **GPS falso:** apps que simulan ubicación o GPX editados y subidos.
- **Medio de transporte:** correr parte en bici, moto o carro (ritmos imposibles).
- **Actividades manuales:** ingresadas a mano sin GPS.
- **Cuentas múltiples o prestadas:** otra persona corre por el participante.
- **Edición posterior:** cambiar la actividad tras el cierre del reto.

Controles mínimos para la V1:

1. Aceptar solo actividades con ruta GPS y no marcadas como manuales.
2. Filtros de ritmo y velocidad máxima por tramo (p. ej. < 2:30 min/km sostenido = revisión).
3. Coincidencia de la ruta con el trazado del reto (para "mejor tiempo en ruta").
4. Cadencia/frecuencia cardiaca cuando existan, como señal adicional.
5. Revisión manual de los puestos con premio antes de pagar, y un periodo de reclamos tras el cierre.
6. Verificación de identidad (KYC) para cobrar premios, que también ayuda con regulación de pagos.

Los retos de **km acumulados** son más fáciles de validar (sumar actividades legítimas) que los de **mejor tiempo en ruta**, donde un solo registro falso gana el premio.

## 4. Propuesta por fases

- **V1:** HealthKit + Health Connect. Arrancar con retos de km acumulados y algún reto de ruta con revisión manual del top 20. Leaderboard propio.
- **V2:** tracking GPS propio, obligatorio para retos de ruta con premios grandes. Mantener la importación para retos de km.
- **Después:** volver a evaluar Garmin si reabre su programa, o pedir a Strava permiso explícito por escrito para el caso de uso.

## Fuentes

- [Strava API Agreement](https://www.strava.com/legal/api)
- [Strava: límites de uso y capacidad de atletas](https://developers.strava.com/docs/rate-limits/)
- [Strava V3 API Changelog](https://developers.strava.com/docs/changelog/)
- [Terra: cambios de la API de Strava en 2026](https://tryterra.co/blog/strava-api-changes-2026)
- [Foro de intervals.icu: nuevos términos y suscripción requerida](https://forum.intervals.icu/t/strava-api-update-new-terms-subs-required-for-api-access/130240)
- [DC Rainmaker: cambios de Strava de nov. 2024](https://dcrainmaker.com/2024/11/stravas-changes-to-kill-off-apps.html)
- [Strava Community Hub: leaderboard de reto cerrado con consentimiento](https://communityhub.strava.com/developers-api-7/closed-team-challenge-leaderboard-with-opt-in-compliant-with-api-policy-13998)
- [Strava Community Hub: reto interno de un mes y retención de datos](https://communityhub.strava.com/developers-api-7/strava-api-for-a-1-month-internal-running-challenge-leaderboard-data-retention-14017)
- [the5krunner: Garmin pausa su API para desarrolladores](https://the5krunner.com/2026/09/14/garmin-developer-api-access-paused/)
- [Health Connect: rutas de ejercicio](https://developer.android.com/health-and-fitness/guides/health-connect/develop/exercise-routes)
