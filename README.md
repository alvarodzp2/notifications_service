README para el proyecto Reto 2: Comunicación entre microservicios con RabbitMQ

---

# Reto 2: Comunicación entre microservicios (RabbitMQ)

## Objetivo

Evaluar el diseño de microservicios desacoplados usando colas de mensajería.

El proyecto incluye dos microservicios:

1. orders_service: crea pedidos y publica mensajes en RabbitMQ.
2. notifications_service: escucha mensajes de RabbitMQ y muestra en consola los pedidos recibidos.

MongoDB se usa como base de datos y RabbitMQ como broker de mensajes.

---

## Estructura del proyecto

```
reto2-microservices/
├── orders_service/
│   ├── main.py
│   ├── routes/orders.py
│   ├── config/rabbit.py
│   ├── Dockerfile
│   └── requirements.txt
├── notifications_service/
│   ├── consumer.py
│   ├── config/rabbit.py
│   ├── Dockerfile
│   └── requirements.txt
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## Variables de entorno

Archivo `.env.example`:

```
MONGODB_URI=mongodb://localhost:27017/orders_db
RABBITMQ_URL=amqp://guest:guest@localhost:5672/
```

Cada servicio lee estas variables para conectarse a MongoDB y RabbitMQ.

---

## Levantar servicios localmente

1. Instalar Docker y Docker Compose en tu máquina.
2. Crear un archivo `.env` basado en `.env.example`.
3. Ejecutar en la raíz del proyecto:

```
docker-compose up --build
```

Esto levantará:

* RabbitMQ con interfaz web en [http://localhost:15672](http://localhost:15672)
* orders_service en [http://localhost:8000](http://localhost:8000)
* notifications_service escuchando mensajes desde RabbitMQ

---

## Probar el flujo

### Crear pedido con curl

```
curl -X POST http://localhost:8000/orders \
-H "Content-Type: application/json" \
-d "{\"cliente\":\"Juan Perez\",\"producto\":\"Teclado\",\"cantidad\":1,\"precio\":45.50,\"direccion\":\"Calle Falsa 123\"}"
```

### Crear pedido con Postman

* Método: POST
* URL: [http://localhost:8000/orders](http://localhost:8000/orders)
* Body: raw, JSON:

```
{
  "cliente": "Juan Perez",
  "producto": "Teclado",
  "cantidad": 1,
  "precio": 45.50,
  "direccion": "Calle Falsa 123"
}
```

### Verificar mensajes en notifications_service

* En la consola donde se ejecuta `notifications_service` deberías ver:

```
Nuevo pedido recibido: <order_id>
```

---

## Interfaz de documentación (Swagger)

* Orders Service expone Swagger en: [http://localhost:8000/docs](http://localhost:8000/docs)
* Puedes probar todos los endpoints desde esta interfaz.

---

## Notas adicionales

* RabbitMQ se puede monitorear en [http://localhost:15672](http://localhost:15672) (usuario: guest, contraseña: guest)
* Orders Service guarda los pedidos en MongoDB local (puede cambiar el URI si quieres usar otra base de datos)
* notifications_service solo imprime en consola los pedidos recibidos, no hace persistencia

---

## Flujo completo

1. Se crea un pedido en `orders_service` (POST /orders).
2. El pedido se guarda en MongoDB.
3. Se publica un mensaje en la cola `orders_queue` de RabbitMQ.
4. `notifications_service` recibe el mensaje y muestra en consola:

```
Nuevo pedido recibido: <order_id>
```

---

Eso cubre todo para ejecutar y probar localmente el reto.
