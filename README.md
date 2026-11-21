# App web Alistamiento y Anticorrosivo

**App:** https://ubba2408-dotcom.github.io/alistamiento-costasur/  
**Registro Entra ID:** Alistamiento Costa Sur Web · Id. de cliente `4bbf6529-109e-4f15-bf40-a5f206613444` · permisos delegados `User.Read` y `Sites.ReadWrite.All` (no requiere consentimiento de administrador).

## Puesta en marcha

Aplicación de una sola página (`index.html`) que usa **listas de SharePoint** del sitio **POSVENTACSACOSTASUR** como base de datos y se publica gratis en **GitHub Pages**. Si `CLIENT_ID` está vacío, abre en **modo demostración** con datos de ejemplo.

## Flujo del proceso
1. **Administrativa (Vehículos):** registra el vehículo con los mismos campos del cuadro de control.
2. **Jefe de alistamiento (Enviar a proceso):** selecciona uno o varios vehículos y los envía a **anticorrosivo**, a **alistamiento** o a **ambos**, con fecha.
3. **Puestos (Mi puesto):**
   - **Cabina 1** y **Cabina 2** (anticorrosivo) y **Alistador** tocan **INICIAR** al empezar y **FINALIZAR** al terminar.
   - El orden es libre: un vehículo puede ir primero a anticorrosivo y luego a alistamiento, o al revés.
   - La app impide que un vehículo esté en las dos etapas a la vez, que dos puestos tomen el mismo vehículo y que un puesto tenga dos vehículos al tiempo.
4. **Tablero (Harold, Laura Buelvas y Ubaldo):**
   - Estado real de cada vehículo: en cabina de anticorrosivo (con número de cabina), en alistamiento, en espera, o proceso completo.
   - Estado de cada puesto: libre u ocupado.
   - Tiempos promedio de cada etapa.
   - Se actualiza solo cada 30 segundos.
5. **Trazabilidad (solo Ubaldo y Harold):** historial de eventos con fecha, hora, responsable y duración. Se exporta a Excel con dos botones: eventos y tiempos por vehículo.

## Base de datos en SharePoint (ya creada y cargada)
| Lista | Contenido |
|---|---|
| **VehiculosControl** | 497 vehículos importados del Excel *CONTROL ALISTAMIENTO - CARTAGENA TC* con sus 21 columnas, más 10 columnas de proceso: `AC_Estado`, `AC_Cabina`, `AC_Programado`, `AC_Inicio`, `AC_Fin`, `AL_Estado`, `AL_Tecnico` (puesto "Alistador"), `AL_Programado`, `AL_Inicio`, `AL_Fin`. |
| **TrazabilidadProceso** | Un registro por acción: inventario, etapa, acción (Enviado a proceso / Inicio / Fin / Registro / Edición / Quitado de espera), responsable, usuario, fecha y hora, duración en minutos. |

Notas de la importación:
- El Excel tenía 36 inventarios repetidos; de cada uno se conservó la fila más reciente.
- ESTADO, ESTADO PLAZO y PRIORIDAD eran fórmulas en el Excel. En SharePoint quedaron como valores del día de la importación y se actualizan desde la pestaña Vehículos.
- Los vehículos importados no traen etapas de proceso. El jefe los envía desde "Enviar a proceso".
- La lista **ColaPintura** (de la app Power Apps) no se modifica.

## Paso 1 — Publicar en GitHub Pages (10 min)
1. En github.com cree un repositorio **público**, por ejemplo `alistamiento-costasur`. El código no contiene contraseñas ni datos; los datos están en SharePoint y piden inicio de sesión corporativo.
2. Suba `index.html` con **Add file → Upload files → Commit**.
3. Vaya a **Settings → Pages → Branch: main / (root) → Save**.
4. Queda en `https://SU-USUARIO.github.io/alistamiento-costasur/`.

## Paso 2 — Registrar la app en Microsoft Entra ID (15 min)
Puede requerir permisos de TI.
1. Entre a https://entra.microsoft.com y luego a **Aplicaciones → Registros de aplicaciones → Nuevo registro**.
2. Configure el registro:
   - Nombre: `Alistamiento Costa Sur Web`.
   - Tipos de cuenta: **solo este directorio organizativo**.
   - URI de redirección: plataforma **Aplicación de página única (SPA)**, con la URL del paso 1 (con la `/` final).
3. Copie el **Id. de aplicación (cliente)**.
4. Vaya a **Permisos de API → Microsoft Graph → Permisos delegados** y marque `Sites.ReadWrite.All` y `User.Read`. Si su organización lo exige, pida **Conceder consentimiento de administrador**.
5. En `index.html`, dentro de `CONFIG`, pegue el Id en `CLIENT_ID` y vuelva a subir el archivo a GitHub.

## Paso 3 — Permisos en SharePoint
- **Jefatura y administrativa:** edición en VehiculosControl y TrazabilidadProceso.
- **Cabinas y alistador:** edición en VehiculosControl y TrazabilidadProceso. La app solo les muestra Mi puesto.

| Persona | Pestañas visibles |
|---|---|
| Ubaldo (umsaenz) y Harold (hemartinez2) | Tablero, Enviar a proceso, Vehículos, Trazabilidad, Mi puesto |
| Laura Buelvas (lvbuelvas) | Tablero, Enviar a proceso, Vehículos, Mi puesto |
| Cualquier otra cuenta (cabinas y alistador) | Mi puesto |

La app oculta las pestañas según el correo (listas `JEFES` y `TRAZA` en `CONFIG`). La protección de fondo la dan los permisos de SharePoint.

## Ajustes frecuentes (en `CONFIG`, dentro de index.html)
- **Agregar otro puesto:** añada una línea en `PUESTOS`, por ejemplo `{id:"Alistador 2", tipo:"AL", color:"var(--teal)"}` o `{id:"Cabina 3", tipo:"AC", color:"var(--blue)"}`.
- **Dar acceso a la trazabilidad:** añada el correo en `TRAZA`.
- **Agregar una persona de jefatura:** añada su correo en `JEFES`.
