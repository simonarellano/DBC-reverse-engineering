# Transmisión — VW Passat CC 2012

Documentación de los IDs y señales CAN relacionados con la transmisión automática DSG (DQ250).

> **Estado:** En progreso — las señales marcadas con ⚠️ aún no han sido validadas completamente.

---

## IDs identificados

### ID `0x540` — Marcha actual y modo de transmisión

| Bit inicio | Longitud | Factor | Offset | Unidad | Señal |
|---|---|---|---|---|---|
| 0 | 4 | 1 | 0 | — | CurrentGear |
| 4 | 4 | 1 | 0 | — | TargetGear |
| 8 | 2 | 1 | 0 | — | TransmissionMode |

**Valores de `TransmissionMode`:**

| Valor | Descripción |
|---|---|
| 0 | Park / Neutral |
| 1 | Drive |
| 2 | Sport |
| 3 | Manual (Tiptronic) |

---

### ID `0x560` — Temperatura del aceite de caja ⚠️

| Bit inicio | Longitud | Factor | Offset | Unidad | Señal |
|---|---|---|---|---|---|
| 0 | 8 | 1 | -40 | °C | GearboxOilTemp |

---

### ID `0x580` — Posición del selector ⚠️

| Bit inicio | Longitud | Factor | Offset | Unidad | Señal |
|---|---|---|---|---|---|
| 0 | 8 | 1 | 0 | — | ShiftLeverPosition |

**Valores conocidos:** `0x50` = P, `0x60` = R, `0x70` = N, `0x80` = D

---

## Notas

- La transmisión es una DSG de 6 velocidades (DQ250) con doble embrague húmedo.
- Pendiente: identificar señal de par transmitido.
- Pendiente: validar IDs de temperatura del embrague.

---

## Historial de cambios

| Fecha | Cambio |
|---|---|
| 2024-01 | Identificación inicial de marcha actual y modo |
