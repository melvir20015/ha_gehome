# Pruebas manuales: modos HVAC con calefacción / Manual QA: HVAC modes with heating

## Objetivo / Goal

Confirmar que los climatizadores con calefacción expongan el modo **Calor / Heat** cuando el electrodoméstico lo reporte en `AC_AVAILABLE_MODES`, y que los cambios de la UI y del servicio `climate.set_hvac_mode` escriban correctamente `0x03` en `AC_OPERATION_MODE` sin afectar los modos **cool/auto/fan_only**.

## Pre-requisitos / Prerequisites

- Un dispositivo de aire acondicionado compatible que reporte `ErdAcAvailableModes.has_heat = True`.
- Registro de depuración habilitado para el dominio `ge_home` (p. ej. en `configuration.yaml`: `logger: default: info; logs: ge_home: debug`).
- Entidad de clima ya disponible en Home Assistant.

## Pasos de verificación / Verification steps

1. **Validar opciones de la UI / Validate UI options**
   - Abrir el panel de la entidad de clima del aire acondicionado.
   - Revisar la lista de modos HVAC y confirmar que incluye **Calor / Heat** además de **Cool / Auto / Fan Only** cuando el dispositivo reporta calefacción.

2. **Cambiar a calefacción desde la UI / Switch to heat from UI**
   - Seleccionar el modo **Calor / Heat** en la tarjeta de la entidad.
   - En los registros de depuración, confirmar una escritura hacia `AC_OPERATION_MODE` con el valor `0x03`.

3. **Cambiar a calefacción por servicio / Switch to heat via service**
   - Abrir *Developer Tools → Services* y llamar a `climate.set_hvac_mode` con el payload:
     ```yaml
     entity_id: climate.<nombre_de_entidad>
     hvac_mode: heat
     ```
   - Verificar en los registros que `AC_OPERATION_MODE` se actualiza a `0x03`.

4. **Comprobar modos existentes / Check existing modes**
   - Volver a **cool**, **auto** y **fan_only** desde la UI o el servicio.
   - Confirmar que cada cambio sigue reflejándose en `AC_OPERATION_MODE` con los valores esperados y sin errores adicionales en los registros.

## Resultados esperados / Expected results

- El modo **Calor / Heat** aparece en la lista de `hvac_modes` solo cuando el equipo reporta calefacción.
- Las escrituras de cambio de modo a **Heat** muestran `AC_OPERATION_MODE = 0x03`.
- Los modos **cool/auto/fan_only** continúan funcionando sin variaciones frente a versiones anteriores.
