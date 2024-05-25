- [Controladores](#controllers)
- [Entidades](#entidades)
- [Servicios](#servicios)
  - [Aprobacion renting](#aprobacion-renting)
  - [Denegación renting](#denegacion-renting)
  - [Client Service](#aprobacion-renting)
  - [Country Service](#aprobacion-renting)
  - [Fee Calculation Service](#aprobacion-renting)
  - [Province Service](#aprobacion-renting)
  - [Renting Request Service](#aprobacion-renting)
  - [Vehicle Service](#aprobacion-renting)

## DTOs Incluidos

### *ClientDto*

La clase ClientDto representa la información completa de un cliente. Incluye detalles como:

- DNI del cliente

- Nombre y apellidos

- Scoring de crédito

- Fecha de nacimiento

- País y código de provincia

- Ingresos netos y brutos anuales

- Antigüedad laboral

- Información del empleador

### *ClientUpdateDto*

La clase ClientUpdateDto se utiliza para actualizar ciertos datos de un cliente existente. Incluye:

- Ingresos netos y brutos anuales

- Antigüedad laboral

- País

- CIF de la empresa empleadora

- IDs del empleado y del asalariado

### *RentingRequestDto*

La clase RentingRequestDto representa una solicitud de renting. Incluye:

- ID del cliente solicitante

- Fechas de solicitud, inicio y resolución

- Cantidad y detalles de los vehículos solicitados

- Inversión total y cuota mensual

- Plazo en meses y estado de la solicitud

### *RentingRequestStatusDto*

La clase RentingRequestStatusDto se utiliza para representar el estado de una solicitud de renting. Incluye:

- Estado de la solicitud (por ejemplo, PENDIENTE, APROBADA, RECHAZADA)

### *VehicleDto*

La clase VehicleDto contiene la información de un vehículo solicitado en una solicitud de renting. Incluye:

- Marca y modelo del vehículo

- Precio

- Cilindrada y potencia

- Número de plazas

- Cuota base y color

### *WarrantyDto*

La clase WarrantyDto representa una garantía asociada a un renting. Incluye:

- NIF del avalista

- Valor de la garantía

- Descripción y tipo de garantía

### *Uso*

Estos DTOs se utilizan principalmente en la comunicación entre el cliente y el servidor, asegurando que los datos se transfieran de manera eficiente y estructurada. Las anotaciones de Swagger ayudan a generar documentación automática de la API, facilitando la comprensión y uso de los endpoints.

## Mappers

En nuestro proyecto, utilizamos MyBatis, un framework de persistencia en Java, para gestionar la interacción con la base de datos de manera eficiente y segura. Los mappers son interfaces que definen métodos que corresponden a operaciones SQL específicas. Cada método en un mapper está asociado con una consulta SQL que se ejecuta cuando el método es llamado.

# Ignacio

# VehicleServiceImpl

Este servicio se encarga de gestionar la información relacionada con los vehículos disponibles para el alquiler y los extras que se pueden añadir a dichos vehículos.

# Dependencias

Este servicio depende de las siguientes clases y paquetes:

- VehicleMapper: Se utiliza para interactuar con la capa de persistencia y realizar operaciones relacionadas con los vehículos y sus extras.
- WrongParamsException: Una excepción personalizada que se lanza cuando ocurren errores relacionados con los parámetros incorrectos.

# Funcionalidades
## Obtener Vehículos
``` 
@Override
public List<Vehicle> getVehicles(String brand, String color, String model, Double minBaseFee, Double maxBaseFee) {
    return vehicleMapper.getVehicles(brand, color, model, minBaseFee, maxBaseFee); 
```

Esta función permite obtener una lista de vehículos filtrada según los siguientes parámetros:

- brand: Marca del vehículo.
- color: Color del vehículo.
- model: Modelo del vehículo.
- minBaseFee: Tarifa base mínima del vehículo.
- maxBaseFee: Tarifa base máxima del vehículo.
La función devuelve una lista de objetos Vehicle que cumplen con los criterios especificados.

## Añadir Extra a un Vehículo
```
@Override
public String addExtraToVehicle(long vehicleId, long extraId) throws WrongParamsException {
    int rowsAffected = vehicleMapper.addExtraToVehicle(vehicleId, extraId);
```
Esta función permite añadir un extra a un vehículo específico. Los parámetros necesarios son:

- vehicleId: Identificador del vehículo.
- extraId: Identificador del extra.
Si el extra se añade correctamente, la función devuelve el id del extra.

# RentingRequestServiceImpl

Este servicio se encarga de gestionar las solicitudes de alquiler de coches, incluyendo su creación, actualización, obtención, filtrado y eliminación.

# Dependencias

Este servicio depende de las siguientes clases y paquetes:

- RentingRequestMapper: Se utiliza para interactuar con la capa de persistencia y realizar operaciones relacionadas con las solicitudes de alquiler.
- PreApprobationService: Servicio que calcula la pre-aprobación de las solicitudes de alquiler.
- EmptyRentingRequestException: Excepción personalizada que se lanza cuando una solicitud de alquiler está vacía.
- RentingRequestNotFoundException: Excepción personalizada que se lanza cuando una solicitud de alquiler no se encuentra.


