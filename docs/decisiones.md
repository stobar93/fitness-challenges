# Decisiones

Registro de lo decidido sobre el producto. Lo más reciente va arriba.

## 2026-10-09

### Ciudad de lanzamiento
Cali, Colombia.

### Modelo de negocio
Suscripción mensual en lugar de un fee por reto. Cada plan permite un número limitado de retos, y hay eventos ocasionales con premios.

**Por qué:** el modelo original de "pozo" (las inscripciones forman el premio) puede leerse como una apuesta sobre el propio rendimiento, que sí regula la Ley 643. Con suscripción y premios pagados por la plataforma el reto queda como competencia de habilidad. Ver [regulación de premios en Colombia](investigacion/regulacion-premios-colombia.md).

### Validación de carreras
- **V1:** leer las carreras desde Apple Health (HealthKit) y Health Connect (Android).
- **V2:** tracking GPS propio, empezando por los retos de mejor tiempo en ruta.

**Por qué:** la API de Strava prohíbe mostrar datos de un usuario a otros (el leaderboard) y Garmin tiene cerrado su programa para apps nuevas. Ver [Strava vs GPS propio](investigacion/strava-vs-gps-propio.md).

Pocas apps escriben la ruta GPS en Apple Health o Health Connect (Garmin no la envía) y ~79 % de los teléfonos en Colombia son Android. Ver [apps que reportan a Apple Health y Health Connect](investigacion/apps-apple-health-health-connect.md).

### Tipos de reto
- Mejor tiempo en una ruta.
- Total de kilómetros en una ventana de tiempo (semana o mes).

### Premios
- Para los primeros puestos (por ejemplo, top 20).
- Premios grandes para el top 3 y decrecientes después.
- El premio mínimo es la entrada gratis a un próximo reto.
- Todo premio por mérito, sin sorteos.
- Los premios los paga la plataforma; no dependen del número de inscritos.
- Los eventos con premio están abiertos a cualquier suscripción de pago.
