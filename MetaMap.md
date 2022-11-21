# METAMAP
## Onboarding: EndPoint para conocer resultados de verificaciones (MetaMap) -> HU-DI883

### **PROBLEMATICA**
**NECESIDAD**: Se requiere un EndPoint que permita conocer los resultados de verificaciones de Metamap

Metamap enviará información por cada flujo de verificación iniciado, enviando la actualización de status de cada uno de los pasos realizados. Se debe almacenar esta información en una tabla de encolamiento.

### **SOLUCION**

Se realizo un nuevo endpoint en el proyecto "**TiendaAmigaApi**" en el cual se creo un nuevo controlador para cualquier endpoint relacionado con **METAMAP**

![Controller](Direccion%20Controller.png?lang=es)

El url del nuevo endpoint de tipo **POST** es:

[ApiTiendaAmiga/METAMAP/VALIDATIONS](https://test.apitiendaamiga.com/api/MetaMap/Validations)

>Recibe como parametros **(json)**:
```json
{
  "resources": "https://api.getmati.com/v2/verifications/63759cb2a540f3001d4ab3a5%22",
  "flowid": "6375625eae9931001c574baa",
  "eventName": "verification_completed",
  "timestamp": "2022-11-17T02:35:38.392Z"
}
```
#### **Base de datos**
El objetivo de Endpoint es encolar todos los eventos obtenidos del servicio de **Metamap**

> Se creo una Tabla en la Base de datos  **[TA_Integrations].[MM].[MetaMapVerification]**

```SQL
USE TA_Integrations
GO

CREATE SCHEMA MM
GO

Create Table MM.MetaMapVerification 
(
	Id Bigint IDENTITY(1,1) PRIMARY KEY,
	IdMetaMap varchar(100) NOT NULL,
	StatusMetaMapId varchar(50) NOT NULL,
	Resource varchar(100) NOT NULL,
	TimeStamap varchar(50) NOT NULL,
	FlowId varchar(100) NOT NULL,
	SyncFailed bit NULL DEFAULT 0,
	Retries tinyint NULL DEFAULT 0,
	LastError varchar(2000) DEFAULT NULL,
	CreateDate datetime NOT NULL DEFAULT GETDATE(),
	UpdateDate datetime NULL
)
```
> Diccionario de datos

| Campo | Descripcion |
|--- |--- |
| Id | Usado como identificador de filas |
| IdMetaMap | Identificador de procesos obtenido de Metamap |
|StatusMetaMapId | Muestra el estado de cada proceso obtenido de Metamap |
|Resource |Url para obtener datos del cliente |
|TimeStamap | Fecha y hora del proceso obtenido de Metamap|
|FlowId |Identificador relacionados con el flujo al que pertenece la verificación.|
|SyncFailed |Identificador de servicio fallido |
|Retries |Numero de reintentos fallidos |
|LastError |Descripcion del error |
|CreateDate |Fecha del registro |
|UpdateDate |Ultima modificacion del registro |

>### **Implementacion del Endpoint en Metamap a traves de Webhook**

Una vez publicado el nuevo endpoint en el servidor, se implementa en cada Metamaps que se desee.

1. Entramos en el metamapa que  deseemos implementar el webhook.
![Metamapas](Metamapas.png?lang=es)

2. Damos Click en la rueda de configuracion de webhooks
![Metamapas](Metamapas2.png?lang=es)

3. Introducimos la url del nuevo Endpoint que se creo y generamos un nuevo secreto.
![Metamapas](Metamapas3.png?lang=es)













