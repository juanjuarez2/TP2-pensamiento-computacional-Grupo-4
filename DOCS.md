## 1. Objetivos

### Objetivo General

Desarrollar un prototipo de software para la gestión integral del ciclo de vida de proyectos, que permita la planificación, asignación de recursos, ejecución, monitoreo y cierre, proveyendo métricas avanzadas para la evaluación de su desempeño basadas en estándares de gestión de valor ganado.

### Objetivos Específicos

- **Facilitar** la definición y estructuración de proyectos mediante la creación y organización de tareas con validación de dependencias temporales.
- **Permitir** la asignación eficiente de recursos humanos y materiales con verificación de disponibilidad y detección de conflictos.
- **Habilitar** el registro y seguimiento del progreso con captura automática de fechas reales de inicio y finalización.
- **Proveer** mecanismos de control con indicadores de desempeño (CPI, SPI) y alertas tempranas de desviaciones.
- **Automatizar** el cierre de proyectos con generación de informes que incluyan análisis de valor ganado y lecciones aprendidas.
- **Garantizar** la trazabilidad completa de cambios en recursos, costos y cronogramas.

---

## 2. Análisis

### Requerimientos Funcionales (RF)

#### Gestión de Proyectos

- **RF-001:** El sistema debe permitir el registro de nuevos proyectos especificando nombre, descripción, fecha de inicio, fecha de fin planificada y presupuesto.
- **RF-002:** El sistema debe registrar automáticamente la fecha de cierre real al cerrar un proyecto.
- **RF-003:** El sistema debe permitir modificar datos de proyectos en estado "Planificación" únicamente.
- **RF-004:** El sistema debe permitir consultar el historial completo de proyectos cerrados con sus métricas finales.

#### Gestión de Tareas

- **RF-005:** El sistema debe permitir la creación de tareas con descripción, fechas estimadas de inicio y fin, y tiempo estimado en horas.
- **RF-006:** El sistema debe validar que la fecha de fin estimada sea posterior a la fecha de inicio estimada.
- **RF-007:** El sistema debe registrar automáticamente la fecha de inicio real al primer registro de horas en la tarea.
- **RF-008:** El sistema debe registrar la fecha de fin real cuando el porcentaje de avance alcance el 100%.
- **RF-009:** El sistema debe permitir modificar o eliminar tareas que no tengan horas registradas.

#### Gestión de Usuarios y Licencias

- **RF-010:** El sistema debe permitir el registro de usuarios con nombre, rol y costo por hora.
- **RF-011:** El sistema debe permitir modificar el costo por hora de usuarios manteniendo historial de cambios.
- **RF-012:** El sistema debe permitir el registro de licencias de software especificando nombre, costo mensual y cupo máximo de usuarios.
- **RF-013:** El sistema debe validar que las licencias no excedan su cupo máximo al asignarlas.

#### Asignación de Recursos

- **RF-014:** El sistema debe permitir asignar uno o más usuarios a una tarea específica.
- **RF-015:** El sistema debe validar la disponibilidad del usuario antes de asignarlo, detectando conflictos con otras tareas simultáneas.
- **RF-016:** El sistema debe permitir asignar una o más licencias a una tarea validando disponibilidad de cupos.
- **RF-017:** El sistema debe permitir reasignar recursos sin perder el historial de trabajo previo.

#### Ejecución y Monitoreo

- **RF-018:** El sistema debe permitir a los usuarios registrar las horas trabajadas en sus tareas asignadas.
- **RF-019:** El sistema debe validar que la suma de horas diarias por usuario no exceda la jornada laboral (8 horas por defecto, configurable).
- **RF-020:** El sistema debe permitir actualizar el porcentaje de avance de una tarea (0-100%).
- **RF-021:** El sistema debe calcular automáticamente el porcentaje de avance general del proyecto basado en el avance ponderado de sus tareas.

#### Control y Reportes

- **RF-022:** El sistema debe calcular el costo real acumulado incluyendo horas trabajadas y costos de licencias asignadas.
- **RF-023:** El sistema debe calcular el índice de desempeño de costos (CPI = Valor Ganado / Costo Real).
- **RF-024:** El sistema debe calcular el índice de desempeño del cronograma (SPI = Valor Ganado / Valor Planificado).
- **RF-025:** El sistema debe generar alertas cuando el costo real supere el 90% del presupuesto.
- **RF-026:** El sistema debe generar reportes de avance con métricas de desempeño y proyecciones.

#### Cierre y Evaluación

- **RF-027:** El sistema debe permitir cerrar un proyecto únicamente si todas sus tareas están al 100% de avance.
- **RF-028:** El sistema debe generar un informe final que incluya: duración total real, costo total real (horas + licencias), desviaciones de presupuesto y cronograma, CPI, SPI, y horas totales trabajadas.
- **RF-029:** El sistema debe almacenar permanentemente los informes de cierre para consulta histórica.

## DER 

```mermaid
erDiagram
    PROYECTO {
        int id_proyecto PK
        string nombre
        string descripcion
        date fecha_inicio
        date fecha_fin_planificada
        date fecha_cierre
        decimal presupuesto
        string estado "Planificacion, EnEjecucion, EnPausa, Cerrado"
    }

    TAREA {
        int id_tarea PK
        int id_proyecto FK
        string nombre
        string descripcion
        date fecha_inicio_est
        date fecha_fin_est
        date fecha_inicio_real
        date fecha_fin_real
        decimal tiempo_estimado_horas
        int porcentaje_avance
    }

    USUARIO {
        int id_usuario PK
        string nombre
        string rol
        decimal costo_por_hora_actual
    }

    HISTORIAL_COSTO_USUARIO {
        int id_historial PK
        int id_usuario FK
        decimal costo_por_hora
        date fecha_vigencia_inicio
        date fecha_vigencia_fin
    }

    LICENCIA {
        int id_licencia PK
        string nombre
        decimal costo_mensual
        int cupo_maximo
    }

    ASIGNACION_USUARIO {
        int id_asignacion_usuario PK
        int id_tarea FK
        int id_usuario FK
        date fecha_asignacion
        date fecha_desasignacion
        boolean activa
    }

    ASIGNACION_LICENCIA {
        int id_asignacion_licencia PK
        int id_tarea FK
        int id_licencia FK
        date fecha_asignacion
        date fecha_desasignacion
        boolean activa
    }

    REGISTRO_HORAS {
        int id_registro PK
        int id_tarea FK
        int id_usuario FK
        decimal horas_trabajadas
        date fecha_registro
        decimal costo_hora_aplicado
    }

    INFORME_CIERRE {
        int id_informe PK
        int id_proyecto FK
        decimal costo_total
        decimal costo_horas
        decimal costo_licencias
        decimal duracion_real_dias
        decimal desviacion_costo
        decimal desviacion_tiempo_dias
        decimal cpi
        decimal spi
        decimal horas_totales
        date fecha_generacion
    }

    ALERTA {
        int id_alerta PK
        int id_proyecto FK
        string tipo "PresupuestoExcedido, TareaAtrasada, ConflictoRecurso"
        string mensaje
        date fecha_generacion
        boolean leida
    }

    PROYECTO ||--|{ TAREA : "contiene"
    PROYECTO ||--o| INFORME_CIERRE : "genera"
    PROYECTO ||--|{ ALERTA : "emite"
    
    TAREA ||--|{ ASIGNACION_USUARIO : "tiene"
    USUARIO ||--|{ ASIGNACION_USUARIO : "participa en"
    
    TAREA ||--|{ ASIGNACION_LICENCIA : "utiliza"
    LICENCIA ||--|{ ASIGNACION_LICENCIA : "se asigna a"
    
    TAREA ||--|{ REGISTRO_HORAS : "registra"
    USUARIO ||--|{ REGISTRO_HORAS : "reporta"
    
    USUARIO ||--|{ HISTORIAL_COSTO_USUARIO : "tiene historial"
```

## Data Flow Diagram

```mermaid
graph TD
    subgraph Sistema
        A[Sistema de Gestión de Proyectos]
    end
    GP[Gerente de Proyecto]
    ME[Miembro de Equipo]
    AD[Administrador]

    GP -- Planificación y Asignaciones --> A
    A -- Informes y Alertas --> GP
    ME -- Horas y Avance --> A
    A -- Tareas Asignadas --> ME
    AD -- Gestión de Usuarios y Licencias --> A
    A -- Configuración del Sistema --> AD
```


```mermaid
graph TD

subgraph Sistema de Gestión de Proyectos

P1["1.0<br/>Planificar<br/>Proyecto"]

P2["2.0<br/>Asignar<br/>Recursos"]

P3["3.0<br/>Ejecutar y<br/>Monitorear"]

P4["4.0<br/>Controlar<br/>Avances"]

P5["5.0<br/>Cerrar<br/>Proyecto"]

P6["6.0<br/>Gestionar<br/>Maestros"]

end

  

GP[Gerente de Proyecto]

ME[Miembro de Equipo]

AD[Administrador]

  

DS_P[("D1: Proyectos")]

DS_T[("D2: Tareas")]

DS_U[("D3: Usuarios")]

DS_L[("D4: Licencias")]

DS_AU[("D5: Asignaciones<br/>Usuario")]

DS_AL[("D6: Asignaciones<br/>Licencia")]

DS_R[("D7: Registros<br/>de Horas")]

DS_I[("D8: Informes<br/>Cierre")]

DS_A[("D9: Alertas")]

DS_H[("D10: Historial<br/>Costos")]

  

GP -- Datos Proyecto/Tareas --> P1

P1 -- Escribe --> DS_P

P1 -- Escribe --> DS_T

P1 -- Lee --> DS_P

  

GP -- Solicitud Asignación --> P2

P2 -- Lee --> DS_T

P2 -- Lee --> DS_U

P2 -- Lee --> DS_L

P2 -- Escribe --> DS_AU

P2 -- Escribe --> DS_AL

P2 -- Lee --> DS_AU

P2 -- Validación --> P4

  

ME -- Horas y Avance --> P3

P3 -- Escribe --> DS_R

P3 -- Actualiza --> DS_T

P3 -- Lee --> DS_AU

P3 -- Lee --> DS_H

P3 -- Datos Avance --> P4

  

GP -- Solicitud Reporte --> P4

P4 -- Lee --> DS_P

P4 -- Lee --> DS_T

P4 -- Lee --> DS_R

P4 -- Lee --> DS_U

P4 -- Lee --> DS_AL

P4 -- Lee --> DS_L

P4 -- Escribe --> DS_A

P4 -- Informe --> GP

  

GP -- Solicitud Cierre --> P5

P5 -- Lee --> DS_T

P5 -- Lee --> DS_P

P5 -- Lee --> DS_R

P5 -- Lee --> DS_U

P5 -- Lee --> DS_AL

P5 -- Lee --> DS_L

P5 -- Actualiza --> DS_P

P5 -- Escribe --> DS_I

P5 -- Reporte Final --> GP

  

AD -- Gestión Usuarios/Licencias --> P6

P6 -- Escribe/Lee --> DS_U

P6 -- Escribe/Lee --> DS_L

P6 -- Escribe --> DS_H
```

### Notas
Proceso 1.0: Planificar Proyecto
Este proceso es el punto de partida para cualquier proyecto.

Responsable: Gerente de Proyecto.

Entrada de datos: El Gerente introduce los Datos Proyecto/Tareas, que incluyen el nombre, presupuesto, fechas planificadas del proyecto y los detalles de cada tarea (nombre, fechas estimadas, tiempo estimado).

Acciones que realiza:

Toma los datos del Gerente.

Valida la coherencia de los datos (por ejemplo, que la fecha de fin sea posterior a la de inicio, como se especifica en el pseudocódigo).

Escribe la información en los almacenes de datos.

Salida de datos:

Crea un nuevo registro en el almacén D1: Proyectos.

Crea uno o varios registros asociados en el almacén D2: Tareas.

Proceso 2.0: Asignar Recursos
Una vez que el proyecto y sus tareas están definidos, este proceso se encarga de asignar el personal y las herramientas.

Responsable: Gerente de Proyecto.

Entrada de datos: El Gerente envía una Solicitud de Asignación (ej: "asignar a este usuario y esta licencia a tal tarea").

Acciones que realiza:

Lee los datos de las tareas (D2), usuarios (D3) y licencias (D4) para saber qué recursos están disponibles.

Valida la disponibilidad de esos recursos (RF-015, RF-016). Esto es crucial: verifica si un usuario ya tiene conflictos de agenda o si una licencia ha alcanzado su cupo máximo.

Si la validación es exitosa, escribe las nuevas asignaciones.

Salida de datos:

Crea nuevos registros en D5: Asignaciones Usuario y D6: Asignaciones Licencia, vinculando los recursos con las tareas.

Envía información de Validación al proceso 4.0 Controlar Avances. Esto significa que al asignar un recurso con conflictos, el sistema podría generar una alerta de forma proactiva a través del proceso de control.

Proceso 3.0: Ejecutar y Monitorear
Este es el proceso del día a día, donde se registra el trabajo real.

Responsable: Miembro de Equipo.

Entrada de datos: El miembro del equipo introduce Horas y Avance (ej: "trabajé 4 horas en la tarea X y ahora está al 30%").

Acciones que realiza:

Lee de D5 para confirmar que el usuario está asignado a la tarea que reporta.

Lee de D10: Historial Costos para saber qué costo por hora aplicar a esas horas trabajadas en esa fecha específica (RF-011).

Escribe un nuevo registro en D7: Registros de Horas con las horas y el costo aplicado.

Actualiza el porcentaje_avance en el registro correspondiente del almacén D2: Tareas.

Salida de datos:

Envía Datos Avance al proceso 4.0 Controlar Avances, informándole que el estado del proyecto ha cambiado y que las métricas deben ser recalculadas.

Proceso 4.0: Controlar Avances
Este es el cerebro analítico del sistema. No crea datos de proyecto, sino que los procesa para generar inteligencia.

Responsable: Gerente de Proyecto.

Entrada de datos: Recibe una Solicitud de Reporte del Gerente, y también recibe datos continuamente de los procesos P2 y P3.

Acciones que realiza:

Lee datos de múltiples almacenes:

D1: Proyectos (para el presupuesto y fechas).

D2: Tareas (para el avance y tiempos estimados).

D7: Registros de Horas y D3: Usuarios (para calcular el costo real de las horas).

D6: Asignaciones Licencia y D4: Licencias (para calcular el costo real de las licencias).

Calcula las métricas clave: Costo Real (AC), Valor Ganado (EV), CPI y SPI (RF-022, RF-023, RF-024).

Verifica si se cumplen las condiciones para generar alertas (ej: costo > 90% del presupuesto, como en RF-025). Si es así, escribe en D9: Alertas.

Salida de datos:

Genera un Informe que se muestra al Gerente de Proyecto.

Proceso 5.0: Cerrar Proyecto
Este proceso formaliza la finalización de un proyecto.

Responsable: Gerente de Proyecto.

Entrada de datos: El Gerente envía una Solicitud de Cierre.

Acciones que realiza:

Lee de D2: Tareas para verificar que todas están al 100% (RF-027). Si no, el proceso se detiene.

Si todo está completo, lee de todos los almacenes de datos relevantes (D1, D7, D6, etc.) para hacer un cálculo final de costos, duración y métricas de desempeño.

Escribe un registro permanente en D8: Informes Cierre (RF-029).

Actualiza el estado en D1: Proyectos a "Cerrado" y registra la fecha de cierre real (RF-002).

Salida de datos:

Entrega un Reporte Final al Gerente de Proyecto.

Proceso 6.0: Gestionar Maestros
Este proceso es administrativo y se encarga de gestionar los datos base que el sistema utiliza en todos los proyectos.

Responsable: Administrador.

Entrada de datos: El Administrador introduce Gestión Usuarios/Licencias (ej: crear un nuevo usuario, cambiar el costo por hora de uno existente, añadir una nueva licencia).

Acciones que realiza:

Lee y Escribe directamente en los almacenes D3: Usuarios y D4: Licencias.

Cuando se modifica el costo de un usuario, escribe un registro en D10: Historial Costos para mantener la trazabilidad de los cambios (RF-011).

Salida de datos:

No produce informes, simplemente mantiene la integridad de los datos maestros del sistema.

## creación de proyecto

```mermaid
stateDiagram-v2

[*] --> Planificacion: crear proyecto

Planificacion --> EnEjecucion: iniciar [recursos asignados]

Planificacion --> [*]: eliminar [sin tareas]

EnEjecucion --> EnPausa: pausar

EnPausa --> EnEjecucion: reanudar

EnEjecucion --> Cerrado: cerrar [todas tareas 100%]

EnEjecucion --> EnEjecucion: registrar avance

Cerrado --> [*]

note right of Planificacion

Tareas creadas

Sin horas registradas

Modificable

end note

note right of EnEjecucion

Al menos 1 hora registrada

Genera alertas

Calcula métricas

end note

note right of Cerrado

Genera InformeCierre

Inmutable

Solo consulta

end note
```

## Diagramas de Estado

```mermaid
stateDiagram-v2
    [*] --> Pendiente: crear tarea
    
    Pendiente --> EnProgreso: registrar primera hora
    Pendiente --> [*]: eliminar [sin horas]
    
    EnProgreso --> EnProgreso: registrar horas/avance
    EnProgreso --> Completada: marcar 100%
    
    Completada --> [*]
    
    note right of Pendiente
        Sin fecha inicio real
        Modificable
    end note
    
    note right of EnProgreso
        Tiene fecha inicio real
        Sin fecha fin real
    end note
    
    note right of Completada
        Tiene fecha fin real
        Inmutable
    end note
```




```mermaid
sequenceDiagram
    actor GP as Gerente
    participant UI as Interfaz
    participant SA as Servicio Asignación
    participant VR as Validador Recursos
    participant RT as Repo Tarea
    participant RU as Repo Usuario
    participant RL as Repo Licencia
    
    GP->>UI: asignarRecursos(idTarea, recursos[])
    UI->>SA: asignarRecursos(idTarea, recursos[])
    
    SA->>RT: obtenerTarea(idTarea)
    RT-->>SA: tarea
    
    loop por cada usuario
        SA->>RU: obtenerUsuario(idUsuario)
        RU-->>SA: usuario
        SA->>VR: validarDisponibilidad(usuario, tarea)
        VR->>RU: obtenerTareasSimultaneas(usuario, fechas)
        RU-->>VR: tareas[]
        
        alt usuario disponible
            VR-->>SA: true
            SA->>RU: crearAsignacion(tarea, usuario)
        else conflicto detectado
            VR-->>SA: false
            SA-->>UI: error("Conflicto: usuario ocupado")
        end
    end
    
    loop por cada licencia
        SA->>RL: obtenerLicencia(idLicencia)
        RL-->>SA: licencia
        SA->>VR: validarCupoLicencia(licencia)
        
        alt cupo disponible
            VR-->>SA: true
            SA->>RL: crearAsignacion(tarea, licencia)
        else sin cupo
            VR-->>SA: false
            SA-->>UI: error("Licencia sin cupo")
        end
    end
    
    SA-->>UI: asignacionExitosa
    UI-->>GP: confirmarAsignacion()
```
## Diagrama de Sequencia para cierre de proyecto

```mermaid
sequenceDiagram
    actor GP as Gerente
    participant UI as Interfaz
    participant SC as ServicioCierre
    participant CM as CalculadorMetricas
    participant RP as RepoProyecto
    participant RT as RepoTarea
    participant RH as RepoHoras
    participant RI as RepoInforme
    
    GP->>UI: solicitarCierre(idProyecto)
    UI->>SC: cerrarProyecto(idProyecto)
    SC->>RP: obtenerProyecto(idProyecto)
    RP-->>SC: proyecto
    
    SC->>RT: obtenerTareas(idProyecto)
    RT-->>SC: tareas[]
    
    loop validar cada tarea
        SC->>SC: verificar(tarea.porcentaje == 100)
    end
    
    alt todas completas
        SC->>RH: calcularCostoHoras(idProyecto)
        RH-->>SC: costoHoras
        
        SC->>SC: calcularCostoLicencias(idProyecto)
        SC->>SC: costoTotal = costoHoras + costoLicencias
        
        SC->>CM: calcularCPI(proyecto, costoTotal)
        CM-->>SC: cpi
        
        SC->>CM: calcularSPI(proyecto)
        CM-->>SC: spi
        
        SC->>SC: generarInforme()
        SC->>RI: guardarInforme(informe)
        SC->>RP: actualizarEstado(idProyecto, "Cerrado")
        
        SC-->>UI: InformeCierre
        UI-->>GP: mostrarInforme(informe)
        
    else hay tareas incompletas
        SC-->>UI: error("Tareas pendientes")
        UI-->>GP: listarTareasPendientes()
    end
```


