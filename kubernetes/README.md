# UrbanCode Velocity

## Pre-requisite

- Kubernetes 1.7+ with Beta APIs enabled
- Helm v2.6  (version might vary, which ever is compatible with your Kubernetes cluster).

## Installing the Chart and images

1. Get the required helm charts.

  ```sh
  $ helm init
  $ helm repo add urbancode-se https://raw.githubusercontent.com/IBM/velocity-se/master/kubernetes/repo
  $ helm repo add ibm-stable https://raw.githubusercontent.com/IBM/charts/master/repo/stable/
  $ helm repo update
  ```
2. If you do not have a MongoDB database installed, you can install one now. Go to MongoDB GitHub repository (https://github.com/kubernetes/charts/tree/master/stable/mongodb) to install. Below is a sample command for installing the MongoDB. Based on your environment, you might need to modify or add to the sample command.

  ```sh
    $ helm install   \
      --set database.password=mongo \
      --set database.user=mongo \
      --set database.name=velocity \
      --name velocity-mongo ibm-stable/ibm-mongodb-dev
  ```

3. Refer to the Configuration section below to customize the deployment. The following step (step 3) shows the values at minimal that are required to be set.

4. Deploy Velocity-RabbitMQ.

  ```sh
  $ helm install  \
    --set rabbitmq.username=rabbit \
    --set rabbitmq.password=carrot  \
    --name velocity-rabbitmq urbancode-se/rabbitmq
  ```

5. Deploy Velocity-SE. `access.key` and `hostname` are required, refer Configuration section below for description of parameters.

  ```sh
  $ helm install \
    --set access.key=<access_key> \
    --set url.domain=<hostname> \
    --set url.nodePort=32443 \
    --set mongo.url=mongodb://mongo:mongo@velocity-mongo-ibm-mongodb-dev:27017/admin \
    --set rabbitmq.url=amqp://rabbit:carrot@velocity-rabbitmq:5672/ \
    --name v2 urbancode-se/velocity
  ```

6. Read the NOTES section from the result of the previous command.

## Configuration

The following tables lists the configurable parameters of the Velocity chart and their default values.

Parameter                     | Description
----------------------------- | ---------------------------------------------------------------------------------------------------
access.key                    | This field is pre-populated with a valid access key.
url.protocol                  | `https` https only
url.domain                    | This is usually the hostname of your Kubernetes master node or ingress hostname. <br/>  If you have any reverse proxy in front of your Kubernetes cluster, that becomes your domain. <br/>
url.port                      | `32443` Usually same as same as `url.nodePort`. <br/>  If you have any reverse proxy in front of your kubernetes cluster, use that port number for url.port <br/>
url.nodePort                  | `32443` The NodePort on which Velocity will be accessible outside the cluster.
apitoken                      | A random string or a GUID that is used verify the the authenticity of API calls and data.
ciphertoken                   | 32-byte Hex that is used verify the the authenticity of API calls and data.
hmackey                       | 32-byte Hex that is used verify the the authenticity of API calls and data.
mongo.url                     | `mongodb://<mongo-username>:<mongo-password>@<mongo-service-name/URL>:<mongo-port>/<database-name>`
rabbitmq.url                  | `amqp://<rabbit-username>:<rabbit-password>@<rabbit-service-name/URL>:5672/<optional-v_host>`

## Uninstalling the release

NOTE: this will remove all Velocity pod deployments, services and ingress rules (if enabled) that were installed on your Kube cluster as part of this release.

```sh
$ helm delete my-release --purge
```
