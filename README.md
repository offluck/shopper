# Shopper

## Architecture

### Schema

![Architecture](https://github.com/offluck/shopper/blob/master/documents/arch.drawio.svg)

### Description

Platform services:
- *HTTP-GateWay*: Handles HTTP request and, processes its contents and passes resulting gRPC request to other services
- *Auth*: Gives JWT over Bearer, based on user's credentials

Product services:
- *CheckOut*: Handles with user's "shopping cart"
- *OMS (Order Management Service)*: Hadles user's orders
- *Billing*: Handles user's financial tasks

## API

This is an API description. Source file can be found in `./{service-name}/api` folder

### HTTP

      - Status code 200 (OK)
      - Status code 201 (Created)
      - Status code 204 (No Content)
      - Status code 400 (Bad Request)
      - Status code 401 (Unauthorized)
      - Status code 403 (Forbidden)
      - Status code 404 (Not Found)
      - Status code 500 (Internal Server Error)

- ***Auth-HTTP-GateWay***:
  - **POST /api/v1/registration**
    - *Request*:
      - Body:
        ```json
        {
          "login": "<login>",
          "password": "<password>",
          "first_name": "<first_name>",
          "last_name": "<last_name>",
          "email": "<email>",
          "phone_number": "<phone_number>"
        }
        ```
    - *Response*:
      - Status code 201 (Created):
        ```json
        {
          "token": "<token>"
        }
        ```
      - Status code 400 (Bad Request)
      - Status code 500 (Internal Server Error)
  - **POST /api/v1/login**
    - *Request*:
      - Body:
        ```json
        {
          "login": "<login>",
          "password": "<password>"
        }
        ```
    - *Response*:
      - Status code 200 (OK):
        - Body:
          ```json
          {
            "token": "<token>"
          }
          ```
      - Status code 400 (Bad Request)
      - Status code 500 (Internal Server Error)

- ***CheckOut-HTTP-GateWay***:
  - **GET /api/v1/{user_id}/cart/items**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
    - *Response*:
  - **PATCH /api/v1/{user_id}/cart/items**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
      - Body:
        ```json
        {
          "": "<>"
        }
        ```
    - *Response*:
  - **DELETE /api/v1/{user_id}/cart/items**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
      - Body:
        ```json
        {
          "": "<>"
        }
        ```
    - *Response*:
  - **POST /api/v1/{user_id}/cart/order**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
      - Body:
        ```json
        {
          "": "<>"
        }
        ```
    - *Response*:

- ***OMS-HTTP-GateWay***:
  - **GET /api/v1/{user_id}/orders/{order_id}**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
    - *Response*:
  - **DELETE /api/v1/{user_id}/orders/{order_id}**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
    - *Response*:
  - **POST /api/v1/{user_id}/orders/{order_id}/pay**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
      - Body:
        ```json
        {
          "": "<>"
        }
        ```
    - *Response*:

- ***Billing-HTTP-GateWay***:
  - **GET /api/v1/{user_id}/balance**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
    - *Response*:
  - **POST /api/v1/{user_id}/balance/top-up**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
      - Body:
        ```json
        {
          "": "<>"
        }
        ```
    - *Response*:
  - **POST /api/v1/{user_id}/balance/withdraw**
    - *Request*:
      - Headers:
        - `"Authorization": "Bearer <token>"`
      - Body:
        ```json
        {
          "": "<>"
        }
        ```
    - *Response*:


### gRPC

- ***Auth***:
  - **Register**: Registration of a new user
    - *Request*:
      - Username: string
      - Password: string
      - FirstName: string
      - LastName: string
      - Email: string
      - PhoneNumber: string
    - *Responses*:
      - Status code 0 (OK)
        - Token: string
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 6 (ALREADY_EXISTS)
      - Status code 13 (INTERNAL)

  - **Login**: User login
    - *Request*:
      - Username: string
      - Password: string
    - *Responses*:
      - Status code 0 (OK)
        - Token: string
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 5 (NOT_FOUND)
      - Status code 13 (INTERNAL)

- ***CheckOut***:
  - **AddToCart**: Adds product to cart
    - *Request*:
      - UserID: uint64
      - SKU: uint32
      - Count: uint32
    - *Responses*:
      - Status code 0 (OK)
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 5 (NOT_FOUND)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **DeleteFromCart**: Deletes product from cart
    - *Request*:
      - UserID: uint64
      - SKU: uint32
      - Count: uint32
    - *Responses*:
      - Status code 0 (OK)
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 5 (NOT_FOUND)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 9 (FAILED_PRECONDITION)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **ListCart**: Shows cart info
    - *Request*:
      - UserID: string
    - *Responses*:
      - Status code 0 (OK)
        - []Items
          - SKU: uint32
          - Count: uint32
          - Name: string
          - Price: uint32
        - TotalPrice: uint32
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **Order**: Sends order request
    - *Request*:
      - UserID: uint64
    - *Responses*:
      - Status code 0 (OK)
        - OrderID: uint64
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)

- ***OMS***:
  - **CreateOrder**:
    - *Request*:
      - UserID: uint64
      - []Items
        - SKU: uint32
        - Count: uint32
      - TotalPrice: uint32
    - *Responses*:
      - Status code 0 (OK)
        - Token: string
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **CancelOrder**:
    - *Request*:
      - OrderID: uint64
    - *Responses*:
      - Status code 0 (OK)
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 5 (NOT_FOUND)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **ListOrder**:
    - *Request*:
      - OrderID: uint64
    - *Responses*:
      - Status code 0 (OK)
        - Status:
          - UNSPECIFIED
          - NEW
          - AWAITING_PAYMENT
          - FAILED
          - PAYED
          - CANCELED
        - UserID: uint64
        - []Items
          - SKU: uint32
          - Count: uint32
        - TotalPrice: uint32
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 5 (NOT_FOUND)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **PayOrder**:
    - *Request*:
      - OrderID: uint64
    - *Responses*:
    - Status code 0 (OK)
      - Status:
        - UNSPECIFIED
        - NEW
        - AWAITING_PAYMENT
        - FAILED
        - PAYED
        - CANCELED
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 5 (NOT_FOUND)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)

- ***Billing***:
  - **PayOrder**:
    - *Request*:
      - OrderID: uint64
      - TotalPrice: uint32
      - UserID: uint64
    - *Responses*:
      - Status code 0 (OK)
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 9 (FAILED_PRECONDITION)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **GetBalance**:
    - *Request*:
      - UserID: uint64
    - *Responses*:
      - Status code 0 (OK)
        - Balance: float64
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **TopUpAccount**:
    - *Request*:
      - UserID: uint64
      - Amount: float64
    - *Responses*:
      - Status code 0 (OK)
        - NewBalance: float64
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 5 (NOT_FOUND)
      - Status code 6 (ALREADY_EXISTS)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
  - **WithdrawMoney**:
    - *Request*:
      - UserID: uint64
      - Amount: float64
    - *Responses*:
      - Status code 0 (OK)
        - NewBalance: float64
      - Status code 1 (CANCELLED)
      - Status code 2 (UNKOWN)
      - Status code 3 (INVALID_ARGUMENT)
      - Status code 4 (DEADLINE_EXCEEDED)
      - Status code 5 (NOT_FOUND)
      - Status code 6 (ALREADY_EXISTS)
      - Status code 7 (PERMISSION_DENIED)
      - Status code 13 (INTERNAL)
      - Status code 14 (UNAVAILABLE)
