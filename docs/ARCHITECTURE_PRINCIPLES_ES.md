# Principios de Arquitectura

## Estructura del módulo

Cada módulo está compuesto por:

```text
*.Module.Contracts
*.Module
```

## Contracts

Responsabilidades:

- Interfaces públicas
- DTOs públicos (Request/Response)
- Atributos de validación simples y autocontenidos

Reglas:

- No debe referenciar ningún otro módulo
- No debe referenciar otro proyecto Contracts
- No debe contener lógica de implementación
- Debe evitar dependencias orientadas a la implementación

Los Contratos son nodos hoja en el grafo de dependencias.

## Módulo

Responsabilidades:

- Funcionalidades
- Componentes compartidos
- Infraestructura
- Registro de endpoints
- Implementaciones de contratos

Reglas:

- Referencia su propio proyecto Contracts
- Puede referenciar otros proyectos Contracts
- No debe referenciar otro Módulo

-------------------------------
### Visibilidad
-------------------------------

Todo dentro de un Módulo debe ser interno por defecto.

El único punto de entrada público es Bootstrap.

De esta forma tenemos aislamiento total sobre el proyecto.

-------------------------------
### Bootstrap
-------------------------------

Bootstrap es el punto de integración entre un módulo y una aplicación anfitriona.

Posibles responsabilidades:

- Registro de inyección de dependencias
- Registro de endpoints
- Registro de servicios hospedados
- Registro de middleware
- Otras preocupaciones de integración específicas del anfitrión

No se impone ninguna interfaz o patrón de implementación requerido.

Esto es debido a que la aplicacion anfitriona puede ser desde un function, api, service, console, etc.

> [!NOTE]
> Un Bootstrap puede contener compatibilidad con multiples formatos de aplicacion anfitriona.

-------------------------------
### Features
-------------------------------

Una Feature representa una capacidad funcional o caso de uso.

Ejemplos:

```text
GetOrder
GetOrders
CreateOrder
CancelOrder
```

esta arquitectura no impone ninguna estructura interna en las Funcionalidades para que el equipo decidad si mantenerlo simple, implementar una estructura de Command/Handler o implementar algun otro diseño.

-------------------------------
### Infrastructure
-------------------------------

Infrastructure contiene las integraciones y detalles técnicos necesarios para que el módulo interactúe con recursos externos o mecanismos de persistencia.

Su responsabilidad es aislar la implementación técnica del resto del módulo.

Cada integracion se debe considerar como una Feature por lo cual todo lo relacionado debe estar contenido en la misma carpeta.

Ejemplo:

```text
Infrastructure
|└─Database
|   └─ IOrderRepository
|   └─ OrderDbContext
└─Notification
|   └─ INotificationService
|   └─ NotificationService
```

-------------------------------
### Shared
-------------------------------

Shared contiene elementos reutilizados por múltiples Funcionalidades dentro del mismo módulo.

Si varias Funcionalidades dependen de la misma implementación, esa implementación debe promoverse a Shared.

>[!TIP]
> No creen Shared por defecto, crearlo cuando tenga logica que quieren usar en varios casos de usos como Helpers o Utils.


## Remote

Cuando un módulo requiere escalar independientemente o ser extraído a un repositorio separado, la implementación local puede reemplazarse por una implementación remota.

`*.Module.Remote` tiene la misma responsabilidad funcional y reglas que `*.Module`, pero su implementación delega la ejecución a un servicio remoto.

Puede contener conceptos como

```text
Clients
Serialization
Authentication
Retries
Resilience Policies
Remote Configuration
```

Como los otros `*.Module` consumen el `*.Module.Contract` no se enteran del cambio y solamente la aplicacion anfitriona debe actualizar su dependencia y consumir un nuevo Boostrap que ofrece `*.Module.Remote`.

Este modulo debe respetar la regla de que todo sea `internal` a excepcion del Boostrap que se ofrece para la aplicacion anfitriona.

>[!TIP]
> Si el equipo es muy maduro en vez de este enfoque pueden optar por sacar Contracts a un paquete nugget y la implementacion a otro paquete eliminando del proyecto el 100% de la informacion del modulo y pasar a ser una caja negra nuggetizada.


## Reglas de dependencias

Permitido:

```text
Module -> Module.Contracts
Module -> Other.Module.Contracts
API -> Module
API -> Module.Remote
```

Prohibido:

```text
Module -> Other.Module
Module -> Other.Module.Remote
Contracts -> Anything
Contracts -> Other.Contracts
```

## Diagramas de dependencias

```mermaid
flowchart TB

subgraph Module
    Contract["*.Module.Contracts"]

    Local["*.Module"]
    Remote["*.Module.Remote"]

    Local --> Contract
    Remote --> Contract
end

Consumer["Another Module"]

Consumer -.-> Contract

EntryPoint["API"]

EntryPoint -.-> Local
EntryPoint -.-> Remote
EntryPoint -.-> Consumer
```