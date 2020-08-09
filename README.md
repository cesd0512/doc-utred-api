# doc-utred-api
This project contains description for integrations utred api


# TRYTON UTRED API

Esta guia describe los métodos para conectarse a la API de Tryton, creada por Presik Techonologies SAS.


## Endpoints


A toda petición deben agregar el token para que las request sean válidas.

Los Endpoints son las rutas para conectarse a la API, las rutas son:


A toda peticion se le debe pasar la api_key:

  args['headers']['authorization'] = api_key


### Consultas


#### Obtener información de un usuario

Esta ruta retorna la información del usuario del cual necesitamos el campo id y city_attention/id los cuales se utilizaran en el recurso de creación de PQRS:

```
method: POST

uri: /DB/search

```

Request:

{
    "model": "party.party",
    "domain": [["id_number", "=", "5711170"]],
    "context": {}
}

``` Donde el número en referencia 5711170 es la identificación del usuario
    El id_number debe ser sin puntos y sin comas
```

Response:

[
    {
        "addresses": [],
        "affiliation_state": "activo",
        "city_attention": {
            "id": 1143,
            "name": "San Gil"
        },
        "id": 242582,
        "id_number": "5711170",
        "name": "ABDENAGO  VILLAMIZAR ORTIZ",
        "phone": "",
        "rec_name": "ABDENAGO  VILLAMIZAR ORTIZ"
    }
]

Nota:
Tener en cuenta el estado de afiliación del usuario, debe estar en estado activo para que la creación de una pqr sea exitosa.


#### Obtener listado de Proveedores de Salud registrados

Esta ruta retorna la información correspondiente a los proveedores de salud con sus respectivos ids:

```
method: POST

uri: /DB/search

```

Request:

{
    "model": "crm.health_provider",
    "domain": [],
    "context": {}
}

Response:

[
    {
        "id": 111,
        "name": "FUNDACION AVANZAR FOS - SEDE BARBOSA",
        "rec_name": "FUNDACION AVANZAR FOS - SEDE BARBOSA"
    }
]


#### Obtener listado de detalles causales

Esta ruta retorna la información correspondiente a los casos o detalles causales el cual es obligatorio para crear una petición:

```
method: POST

uri: /DB/search

```

Request:

{
    "model": "crm.case",
    "domain": [],
    "context": {}
}

Response:

[
    {
        "id": 71,
        "name": "Ausencia de asignación del prestador primario de servicios de salud",
        "rec_name": "Ausencia de asignación del prestador primario de servicios de salud"
    },
    {
        "id": 39,
        "name": "Ausencia de dispensación de insumos",
        "rec_name": "Ausencia de dispensación de insumos"
    }
    ...
]


#### Obtener listado de receptores

Esta ruta retorna la información correspondiente a las entidades receptoras con su respectivo id el cual es obligatorio para crear una petición:

```
method: POST

uri: /DB/search

```

Request:

{
    "model": "crm.receiver_service",
    "domain": [],
    "context": {}
}

Response:

[
    {
        "id": 1,
        "name": "Comité de veedores",
        "rec_name": "Comité de veedores"
    },
    {
        "id": 2,
        "name": "Comité regional",
        "rec_name": "Comité regional"
    },
    ...
]


#### Creación de PQR

Esta ruta permite crear una PQR:

```
method: POST

uri: /DB/create

body: {object}

```

Los campos Object son:

* model: crm.customer_service (Texto) como esta en el ejemplo
* context: {} vacio por defecto como esta en el ejemplo
* record: objecto con los siguientes campos:
	* party: id del usuario obtenido de la consulta "Obtener información de un usuario" - (entero)
	* city_region: id de la ciudad de atención campo city_attention obtenido de la consulta "Obtener información de un usuario" - (entero)
	* customer: Nombre del usuario campo name obtenido de la consulta "Obtener información de un usuario" - (texto)
	* media: Las opciones habilitadas son: "web", "sede", "supersalud" (texto)
	* phone: Teléfono del usuario(texto)
	* email: Email donde recibira la noticación y respuesta (texto)
	* address: (texto)
	* health_provider: id del proveedor de salud obtenido de la consulta "Obtener listado de Proveedores de salud registrados"
	* case: id del detalle causal obtenido de la consulta "Obtener listado de detalles causales"
	* receiver: id del receptor obtenido de la consulta "Obtener listado de receptores"
	* description: descripción o detalle de la petición colocada (texto)
	* attach_customer_1: (texto)
	* attach_customer_2: (texto)



Request:

{
    "model": "crm.customer_service",
    "context": {},
    "record": {
        "party": 238190,
        "city_region": 1138,
        "customer": "USUARIO PRUEBA",
        "media": "web",
        "phone": "3114443333",
        "email": "prueba@gmail.com",
        "address": "CALLE 12 #34-21",
        "health_provider": 105,
        "case": 67,
        "receiver": 8,
        "description": "ESTO ES UNA PRUEBA",
        "attach_customer_1": "https://res.cloudinary.com/utred/image/upload/v1597008300/herra_tawi60.jpg",
        "attach_customer_2": ""
    }

}

Response 200 (exitoso):

{
    "address": "CALLE 12 #34-21",
    "case": {
        "id": 67,
        "name": "Petición de certificación de la afiliación a servicios de salud "
    },
    "city": null,
    "company": {
        "id": 1,
        "name": "UT RED INTEGRADA FOSCAL CUB"
    },
    "cs_date": "2020-08-09T22:25:18",
    "customer": "USUARIO PRUEBA",
    "department_region": {
        "id": 54,
        "name": "Santander"
    },
    "description": "ESTO ES UNA PRUEBA",
    "effective_date": null,
    "health_provider": {
        "id": 105,
        "name": "FUNDACION AVANZAR FOS - SEDE BUCARAMANGA"
    },
    "id": 143,
    "party": {
        "id": 238190,
        "name": "ABELINO  CENTENO "
    },
    "phone": "3202921664",
    "rec_name": "None",
    "receiver": {
        "id": 8,
        "name": "Prestador de Salud"
    },
    "region": {
        "id": 7,
        "name": "Región_7"
    },
    "response": null,
    "state": "draft"
}



