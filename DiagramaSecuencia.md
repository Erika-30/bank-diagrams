# A. API BUSINESS CUSTOMER V1

### A.1.1 Registra un cliente

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-customer-v1
  participant DB as DB

  Note right of USER: Se envía datos del cliente.
  
  USER ->> API_CUS: POST <br> /customer/v1/customers

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta si hay un cliente registrado con el DNI
    API_CUS ->> API_CUS: Valida DNI único del cliente 
    rect rgb(199, 206, 234)  
      alt NO SE ENCONTRÓ UN CLIENTE REGISTRADO CON EL DNI
        DB -->> API_CUS: Responde sin un cliente

        API_CUS ->> DB: INSERT <br> Registra al cliente
        DB -->> API_CUS: Responde un cliente

        API_CUS -->> USER: HttpStatus 201 - Responde el ID del cliente

      else SE ENCONTRÓ UN CLIENTE REGISTRADO CON EL DNI
        DB -->> API_CUS: Responde un cliente
        
        API_CUS -->> USER: HttpStatus 409 - Responde una api exception
        Note right of API_CUS: Responde un api exception indicando que existe un cliente registrado con el DNI.
      end 
    end
  deactivate DB

```

### A.1.2 Obtiene clientes

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-customer-v1
  participant DB as DB

  USER ->> API_CUS: GET <br> /customer/v1/customers

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta clientes registrados
    API_CUS ->> API_CUS: Valida existencia de clientes 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ CLIENTES
        DB -->> API_CUS: Responde clientes

        API_CUS -->> USER: HttpStatus 200 - Responde clientes

      else NO SE ENCONTRÓ CLIENTES
        DB -->> API_CUS: Responde cero clientes
        
        API_CUS -->> USER: HttpStatus 204 - Responde cero clientes
      end 
    end
  deactivate DB

```

### A.1.3 Obtiene un cliente por ID

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-customer-v1
  participant DB as DB

  USER ->> API_CUS: GET <br> /customer/v1/customers/:id

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta si existe un cliente registrado con el ID
    API_CUS ->> API_CUS: Valida si existe el cliente 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ UN CLIENTE REGISTRADO CON EL ID
        DB -->> API_CUS: Responde un cliente

        API_CUS -->> USER: HttpStatus 200 - Responde datos del cliente

      else NO SE ENCONTRÓ UN CLIENTE REGISTRADO CON EL ID
        DB -->> API_CUS: Responde cero clientes
        
        API_CUS -->> USER: HttpStatus 204 - Responde vacio
      end 
    end
  deactivate DB

```

### A.1.4 Actualiza un cliente

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-customer-v1
  participant DB as DB

  Note right of USER: Se envía datos del cliente.
  
  USER ->> API_CUS: PUT <br> /customer/v1/customers/:id

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta si existe un cliente registrado con el ID
    API_CUS ->> API_CUS: Valida si existe el cliente 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ UN CLIENTE REGISTRADO CON EL ID
        DB -->> API_CUS: Responde un cliente

        API_CUS ->> API_CUS: Carga los nuevos datos al cliente

        API_CUS ->> DB: UPDATE <br> Actualiza al cliente
        DB -->> API_CUS: Responde un cliente

        API_CUS -->> USER: HttpStatus 200 - Responde el ID del cliente

      else NO SE ENCONTRÓ UN CLIENTE REGISTRADO CON EL ID
        DB -->> API_CUS: Responde cero clientes
        
        API_CUS -->> USER: HttpStatus 409 - Responde una excepción
        Note right of API_CUS: Responde un api exception indicando que no existe un cliente registrado con el ID.
      end 
    end
  deactivate DB

```

### A.1.5 Elimina un cliente

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-customer-v1
  participant DB as DB
  participant API_ACC as business-account-v1

  Note right of USER: Se envía datos del cliente.
  
  USER ->> API_CUS: DELETE <br> /customer/v1/customers/:id

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta si existe un cliente registrado con el ID
    DB -->> API_CUS: Responde cero o un cliente
    API_CUS ->> API_CUS: Valida si existe el cliente 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ UN CLIENTE REGISTRADO CON EL ID
        
        API_CUS ->> API_ACC: GET <br> /account/v1/accounts/:customerId/accounts
        API_ACC -->> API_CUS: Responde cero o la menos una cuenta del cliente
        API_CUS ->> API_CUS: Valida si el cliente tiene cuentas
        rect rgb(199, 206, 234)  
            alt NO SE ENCONTRÓ PARA CLIENTE CUENTAS

                API_CUS ->> DB: DELETE <br> Elimina al cliente
                DB -->> API_CUS: Responde cero clientea

                API_CUS -->> USER: HttpStatus 204 - Responde cero contenido

            else SE ENCONTRÓ PARA CLIENTE CUENTAS
                
                API_CUS -->> USER: HttpStatus 409 - Responde una excepción
                Note right of API_CUS: Responde una excepción de API que el cliente tiene cuentas y no puede eliminarse.
            end 
        end

      else NO SE ENCONTRÓ UN CLIENTE REGISTRADO CON EL ID
        
        API_CUS -->> USER: HttpStatus 409 - Responde una excepción
        Note right of API_CUS: Responde un api exception indicando que no existe un cliente registrado con el ID.
      end 
    end
  deactivate DB

```


# B. API BUSINESS ACCOUNT V1

### B.1.1 Registra una cuenta

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-customer-v1
  participant DB as DB

  Note right of USER: Se envía datos de la cuenta
  
  USER ->> API_CUS: POST <br> /account/v1/accounts

  API_CUS ->> DB: INSERT <br> Registra al cuenta
  DB -->> API_CUS: Responde una cuenta

  API_CUS -->> USER: HttpStatus 201 - Responde el ID de la cuenta

```

### B.1.1 Obtener cuentas

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-account-v1
  participant DB as DB

  USER ->> API_CUS: GET <br> /account/v1/accounts

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta cuentas registrados
    DB -->> API_CUS: Responde cero o n cuentas
    API_CUS ->> API_CUS: Valida existencia de cuentas 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ CUENTAS
        
        API_CUS -->> USER: HttpStatus 200 - Responde cuentas

      else NO SE ENCONTRÓ CUENTAS
        
        API_CUS -->> USER: HttpStatus 204 - Responde cero cuentas
      end 
    end
  deactivate DB

```

### B.1.3 Obtiene una cuenta por ID

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-account-v1
  participant DB as DB

  USER ->> API_CUS: GET <br> /account/v1/accounts/:id

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta si existe una cuenta registrado con el ID
    DB -->> API_CUS: Responde cero o una cuenta
    API_CUS ->> API_CUS: Valida si existe la cuenta 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ UN cuenta REGISTRADO CON EL ID

        API_CUS -->> USER: HttpStatus 200 - Responde datos de la cuenta

      else NO SE ENCONTRÓ UN cuenta REGISTRADO CON EL ID
        
        API_CUS -->> USER: HttpStatus 204 - Responde vacio
      end 
    end
  deactivate DB

```

### B.1.4 Realizar un depósito en una cuenta bancaria 

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-account-v1
  participant DB as DB

  Note right of USER: Se envía datos del cuenta.
  
  USER ->> API_CUS: PUT <br> /account/v1/accounts/:id/deposit

  API_CUS ->> API_CUS: Valida que el saldo se mayor a cero

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta si existe un cuenta registrado con el ID
    DB -->> API_CUS: Responde cero o una cuenta
    API_CUS ->> API_CUS: Valida si existe el cuenta 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ UN cuenta REGISTRADO CON EL ID
        
        API_CUS ->> API_CUS: Carga los nuevos datos de la cuenta

        API_CUS ->> DB: UPDATE <br> Actualiza la cuenta
        DB -->> API_CUS: Responde una cuenta

        API_CUS -->> USER: HttpStatus 200 - Responde el ID de la cuenta

      else NO SE ENCONTRÓ UN cuenta REGISTRADO CON EL ID
        
        API_CUS -->> USER: HttpStatus 409 - Responde una excepción
        Note right of API_CUS: Responde un api exception indicando que no existe una cuenta registrado con el ID.
      end 
    end
  deactivate DB

```

### B.1.4 Realizar un retiro de una cuenta bancaria 

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-account-v1
  participant DB as DB

  Note right of USER: Se envía datos del cuenta.
  
  USER ->> API_CUS: PUT <br> /account/v1/accounts/:id/withdraw

  API_CUS ->> API_CUS: Valida que el saldo de retiro sea mayor a cero

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta si existe un cuenta registrado con el ID
    DB -->> API_CUS: Responde cero o una cuenta
    API_CUS ->> API_CUS: Valida si existe el cuenta 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ UN CUENTA REGISTRADO CON EL ID

        API_CUS ->> API_CUS: Verifica que el saldo disponible sea mayor o igual al saldo a retirar
        
        alt SALDO DISPONIBLE
            
            API_CUS ->> API_CUS: Carga los nuevos datos de la cuenta

            API_CUS ->> DB: UPDATE <br> Actualiza la cuenta
            DB -->> API_CUS: Responde una cuenta

            API_CUS -->> USER: HttpStatus 200 - Responde el ID de la cuenta

        else SALDO INDISPONIBLE
            
            API_CUS -->> USER: HttpStatus 409 - Responde una excepción
            Note right of API_CUS: Responde un api exception indicando que no existe saldo suficiente para el retiro.
        end 

      else NO SE ENCONTRÓ UN CUENTA REGISTRADO CON EL ID
        
        API_CUS -->> USER: HttpStatus 409 - Responde una excepción
        Note right of API_CUS: Responde un api exception indicando que no existe una cuenta registrado con el ID.
      end 
    end
  deactivate DB

```

### B.1.5 Elimina un cuenta

```mermaid
%%{init: { 'theme': 'forest' } }%%
sequenceDiagram
  autonumber
  participant USER as user
  participant API_CUS as business-account-v1
  participant DB as DB

  Note right of USER: Se envía datos del cuenta.
  
  USER ->> API_CUS: DELETE <br> /account/v1/accounts/:id

  activate DB
    API_CUS ->> DB: QUERY <br> Consulta si existe un cuenta registrado con el ID
    DB -->> API_CUS: Responde cero o una cuenta
    API_CUS ->> API_CUS: Valida si existe la cuenta 
    rect rgb(199, 206, 234)  
      alt SE ENCONTRÓ UN CUENTA REGISTRADO CON EL ID
        
        API_CUS ->> DB: DELETE <br> Elimina la cuenta
        DB -->> API_CUS: Responde cero cuentas

        API_CUS -->> USER: HttpStatus 204 - Responde vacio

      else NO SE ENCONTRÓ UN CUENTA REGISTRADO CON EL ID
        
        API_CUS -->> USER: HttpStatus 409 - Responde una excepción
        Note right of API_CUS: Responde un api exception indicando que no existe una cuenta registrado con el ID.
      end 
    end
  deactivate DB

```