# JpegInMongoDB
In MongoDB, you can use the BSON binary type to store any kind of binary data. This repo is to understand how to store it.

# 1. Publish a large number of messages via DLX
Put the number of 1000 jpeg images in /home/vagrant/Downloads/. <br>
No problem even if they are all copied file from the same image file. But the names of file should be image1.jpg, image2.jpg, ..., image1000.jpg.

```
export RABBITMQ_IPADDR="192.168.33.220"
export RABBITMQ_DLX="dlx.employee-queue"
export RABBITMQ_MESSAGECOUNT="1000"
```
```
$ cd rabbitMQ_KEDA_Csharp/sendImage-to-rabbitmqDLX
$ dotnet run
```

After issue dotnet run, you can find 1000 of messages on the employee-queue which are waiting to be consumed by consumer.

# 2. Consume them thru the DLX aware App
```
$ https://github.com/developer-onizuka/rabbitMQ_KEDA_Csharp
$ cd rabbitMQ_KEDA_Csharp/rabbitmq-to-hybridCloud
```
```
$ cat <<EOF > local.settings.json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "FUNCTIONS_WORKER_RUNTIME": "dotnet",
        "RabbitMQConnection": "amqp://user:PASSWORD@rabbitmq.default.svc.cluster.local:5672",
        "RabbitMQ_IPaddress": "rabbitmq",
        "RabbitMQ_DLX": "dlx.employee-queue-failure",
        "PrimaryConnection": "mongodb://mongo-0:27017,mongo-1:27017,mongo-2:27017/?replicaSet=myReplicaSet",
        "SecondaryConnection": "mongodb://mongo-0:27017,mongo-1:27017,mongo-2:27017/?replicaSet=myReplicaSet"
    }
}
EOF
```
```
$ func kubernetes delete --name rabbitmq-to-hybridcloud --registry 192.168.1.5:5000 --max-replicas 16 --polling-interval 5 --cooldown-period 30
```

# 3. Bring up each micro service in kubernetes

> https://github.com/developer-onizuka/hybridCloud


# 4. Find Data in MongoDB
You can see the Base64 Encoded string. It is the Jpeg file which you put in /home/vagrant/Downloads and sent to rabbitMQ.

<img src="https://github.com/developer-onizuka/JpegInMongoDB/blob/main/jpegInMongoDB1.png" width="840"> <br>


# 5. Search Employee by EmployeeID

<img src="https://github.com/developer-onizuka/JpegInMongoDB/blob/main/jpegInMongoDB2.png" width="360"> <br>

# 5. Put a jpeg image individually thru app
You can find the container of [developeronizuka/emploee2](https://hub.docker.com/repository/registry-1.docker.io/developeronizuka/employee2/general) in dockerHub.<br>

<img src="https://github.com/developer-onizuka/JpegInMongoDB/blob/main/jpegInMongoDB3.png" width="360"> <br>
