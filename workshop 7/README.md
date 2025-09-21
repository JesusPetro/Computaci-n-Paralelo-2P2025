# COMPUTACION EN PARALELO 2P

## Reto de Programación 7 — SAVIO

**Challenge:** Producer-Consumer Weather Data Pipeline with Condition Variables

### Descripción

En este reto se debe extender el clásico problema Productor-Consumidor integrando datos del mundo real.  
En lugar de producir ítems artificiales, los productores consultarán datos de clima y calidad del aire desde la API de **Open-Meteo**.  
Los consumidores unificarán y almacenarán los resultados en un archivo CSV de manera **horaria**.

Este ejercicio simula un _data pipeline_ en tiempo real, donde múltiples productores recolectan datos de servicios externos y múltiples consumidores los agregan en un dataset estructurado.

---

### Instrucciones para los estudiantes

#### 1. Estructura de datos compartida

- Implementar una cola _thread-safe_ donde los productores insertarán respuestas de la API y los consumidores las extraerán.

#### 2. Hilos productores

- Cada hilo productor debe solicitar datos de clima y calidad del aire desde **Open-Meteo** para una subregión colombiana (identificada por código postal).
- Antes de la petición, convertir **códigos postales → coordenadas LAT/LON** (con un diccionario estático o función de búsqueda).
- Insertar la respuesta (JSON o diccionario parseado) en la cola.
- Si la cola está llena, el productor deberá esperar con una _condition variable_.

#### 3. Hilos consumidores

- Cada consumidor espera hasta que haya datos en la cola.
- Una vez disponibles, extraen los campos relevantes (ej. temperatura, humedad, AQI).
- Los resultados deben agregarse por hora y guardarse en un archivo `weather_data.csv`.
- Si la cola está vacía, el consumidor deberá esperar con una _condition variable_.

#### 4. Sincronización

- Usar _condition variables_ para coordinar la comunicación:
  - Productores notifican a consumidores cuando hay datos nuevos.
  - Consumidores notifican a productores cuando hay espacio disponible.

#### 5. Requerimientos de datos

Recolectar desde **Open-Meteo**:

- Clima: temperatura, precipitación, velocidad del viento.
- Calidad del aire: PM2.5, PM10, ozono (O₃).

Cada registro en el CSV debe contener:

- Timestamp de recolección
- Región (código postal)
- Latitud, Longitud
- Valores de clima
- Valores de calidad del aire

#### 6. Validación y pruebas de rendimiento

- Implementar primero una versión **serial** (sin hilos).
- Validar que los resultados coincidan con la versión paralela.
- Comparar tiempos de ejecución y discutir el _speedup_ observado.

#### 7. Discusión

- Explicar los retos de usar _condition variables_ en un pipeline real (deadlocks, señales perdidas, _race conditions_).
- Justificar la importancia de la sincronización en la ingesta de datos en tiempo real.

---

### Ejemplo de flujo

1. Productor 1 obtiene datos de Bogotá.
2. Productor 2 obtiene datos de Medellín.
3. La cola se llena con objetos JSON.
4. Consumidor 1 agrega datos cada hora y escribe en `weather_data.csv`.
5. Consumidor 2 valida consistencia o prepara información para procesamiento adicional.

---

### Consideraciones extra

- Manejar múltiples APIs (clima + calidad del aire por separado).
- Agregar logging: productores/consumidores deben imprimir cuando esperan, notifican o procesan datos.
- Implementar apagado controlado cuando los productores dejen de recolectar datos.
- Cada estudiante debe elegir **diferentes zonas subregionales** (mínimo tres).

---

### Referencias

- [Open-Meteo](https://open-meteo.com)
- [Google Maps Geocoding API](https://developers.google.com/maps/documentation/geocoding/overview)
- [Códigos postales de Colombia — Wikipedia](https://es.wikipedia.org/wiki/Anexo:C%C3%B3digos_postales_de_Colombia)
