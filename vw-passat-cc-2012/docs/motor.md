# 🔧 Motor — VW Passat CC 2012

Documentación de los IDs y señales CAN relacionados con el sistema de motor.

> **Estado:** 🔬 En progreso — las señales marcadas con ⚠️ aún no han sido validadas completamente.

---

## IDs identificados

### ID `0x280` — RPM y carga del motor

| Bit inicio | Longitud | Factor | Offset | Unidad | Señal |
|---|---|---|---|---|---|
| 24 | 16 | 0.25 | 0 | rpm | EngineRPM |
| 8  | 8  | 0.392 | 0 | % | EngineLoad |

**Ejemplo de trama:**
```
0x280  8  00 00 1F 40 00 64 00 00
```
→ RPM = `0x1F40` × 0.25 = **2000 rpm**
→ Carga = `0x64` × 0.392 ≈ **38.8 %**

---

### ID `0x320` — Temperatura del refrigerante ⚠️

| Bit inicio | Longitud | Factor | Offset | Unidad | Señal |
|---|---|---|---|---|---|
| 0 | 8 | 1 | -40 | °C | CoolantTemp |

---

### ID `0x380` — Velocidad del vehículo

| Bit inicio | Longitud | Factor | Offset | Unidad | Señal |
|---|---|---|---|---|---|
| 0 | 16 | 0.01 | 0 | km/h | VehicleSpeed |

---

## Notas

- La captura se realizó en modo ralentí y durante aceleración en 2ª marcha.
- Pendiente: identificar señal de posición del acelerador (APP).
- Pendiente: validar ID de presión del turbo.

---

## Historial de cambios

| Fecha | Cambio |
|---|---|
| 2024-01 | Identificación inicial de RPM y velocidad |
