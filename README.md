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
# Servicios
# VehicleServiceImpl

Este servicio se encarga de gestionar la información relacionada con los vehículos disponibles para el alquiler y los extras que se pueden añadir a dichos vehículos.

# Dependencias

Este servicio depende de las siguientes clases y paquetes:

- VehicleMapper: Se utiliza para interactuar con la capa de persistencia y realizar operaciones relacionadas con los vehículos y sus extras.
- WrongParamsException: Una excepción personalizada que se lanza cuando ocurren errores relacionados con los parámetros incorrectos.

# Funcionalidades
## Obtener Vehículos
``` 
public List<Vehicle> getVehicles(String brand, String color, String model, Double minBaseFee, Double maxBaseFee)  
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
public String addExtraToVehicle(long vehicleId, long extraId)
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

# Funcionalidades
## Crear Solicitud de Alquiler

```
public RentingRequest createRentingRequest(RentingRequest rentingRequest)
```
Esta función permite crear una nueva solicitud de alquiler. Si la solicitud está vacía, se lanza una EmptyRentingRequestException. La solicitud es evaluada para determinar si se aprueba automáticamente antes de ser guardada.

## Actualizar Estado de Solicitud de Alquiler
```
public RentingRequest updateRentingRequestStatus(long rentingRequestId, String status)
```
Esta función permite actualizar el estado de una solicitud de alquiler. Si la solicitud no se encuentra, se lanza una RentingRequestNotFoundException.

## Obtener Solicitud de Alquiler
```
public RentingRequest getRentingRequest(long rentingRequestId)
```
Esta función permite obtener una solicitud de alquiler por su identificador. Si la solicitud no se encuentra, se lanza una RentingRequestNotFoundException.

## Obtener Solicitudes de Alquiler Filtradas
```
public List<RentingRequest> getFilteredRentingRequests(String rentingRequestStatus)
```
Esta función permite obtener una lista de solicitudes de alquiler filtradas por su estado.

# Eliminar Solicitud de Alquiler
```
public boolean deleteRentingRequest(long rentingRequestId)
```
Esta función permite eliminar una solicitud de alquiler por su identificador. Si la solicitud no se encuentra, se lanza una RentingRequestNotFoundException.

# Reglas

En este paquete encontramos las reglas de aprobación o denegación para una Renting Request, así como el servicio de Preaprobación, que se encarga de asignar un estado a la request (PENDIENTE, PREAPROVADO Y PREDENEGADO) según si se cumplen las reglas.




----------------------------------------------------------------------------------------------------

## Approbations
Contiene el `ApprobationRulesService`, que comprueba si se cumplen las reglas de aprobación. 

Cuando esta clase se instancia, recibe la lista de reglas de aprobación y al usar el método `approveRules()`, itera las reglas y comprueba que si se cumplen para una request específica.

```java
    public boolean approveRules(RentingRequest rentingRequestDto){
        for(ApprobationRule rule: approbationRuleList){
            if(!rule.approve(rentingRequestDto)){
                return false;
            }
        }
        return true;
    }
```

Cada regla implementa la interfaz `ApprobationRule`, aplicando el método:

```java
boolean approve(RentingRequest request);
```
### Implementaciones ApprobationRules
Con cada mapper, recupera datos de la BDD y aplica la lógica adecuada.
- Regla 2 - **GrossIncomeRule**: Usa el `EmployeeMapper`, comprueba si el cliente es asalariado ó si, en caso de ser autónomo, la inversión de la request sea menor o igual a los ingresos brutos*3. 
    > [!CAUTION]
    > Hace la consulta a BDD del ingreso bruto antes de tener en cuenta si es autónomo o no.
- Regla 3 - **Debt rule**: Usa el `ClientMapper` y comprueba que la deuda del cliente sea menor que la cuota de la request. 
- Regla 4 - **EmploymentSeniorityRule**: Usa el `EmployeeMapper` y comprueba que el cliente lleve 3 o más años en su empresa.
- Regla 11 - **GuarantorVerificationRule**: Con el `ClientMapper`, verifica si el cliente es nuevo ó si NO es garante (no tiene garantías)

------------------------------------------------------------

## Aprobacion Renting

#### Neet In come Rule
* Regla de aprobacion 4

```
public boolean approve(RentingRequest request) { 
  double inversion = 0;
        for (Vehicle vehicleDto : request.getVehicles()){
            inversion += vehicleDto.getPrice();
        }
        Calendar cal = Calendar.getInstance(TimeZone.getTimeZone("Europe/Paris"));
        cal.setTime(request.getRentingRequestDate());
        long averageSalary = mapper.getAverageSalary(request.getClientId(),
                cal.get(Calendar.YEAR));
        return inversion <= averageSalary;
  ....}
```
Recupera la lista de vehiculos del renting para hacer un acumulado del precio, y comprueba que la inversion sea inferior al la media de salario.

#### NIF Rule
* Regla de aprobacion 10
* El CIF/NIF de la empresa donde trabaja el cliente se encuentra en INFORMA con un resultado antes de impuesto medio de los últimos 3 años superior a 150.000 €
  * Solo aplica a asalariados
  * Aplican los años de los que se disponga, si solo se dispone de un año se coge ese año. Si se dispone de 2 años se hace la media de esos dos años.
  * Si el último balance es de hace más de dos años se considera que no está en INFORMA.

```java
    private static final float TAX_LIMIT = 150000;
    private final EmployeeMapper employeeMapper;
    private final InformaMapper informaMapper;
```
```java
    @Override
    public boolean approve(RentingRequest request) {
        Salaried salaried = employeeMapper.isSalaried(request.getClientId());

        if (salaried == null) {
            return true;
        }

        if(existCifInEnterpise(salaried.getCif())){
            float tax = informaMapper.getEnterpriseIncomeOverThreeYears(salaried.getCif());
            return tax > TAX_LIMIT;
        }

        return false;
    }

    private boolean existCifInEnterpise(String enterpiseCif){
        return informaMapper.countEnterpise(enterpiseCif) > 0;
    }
```
1. Verifica si el cliente es asalariado.
2. Verifica si tiene el CIF de la empresa y retorna si supera el limite de ingresos en los ultimos 3 años.
3. Usa informaMapper para obtener el CIF en la tabla INFORMA

### NonApprovedWithWarrantyRule
