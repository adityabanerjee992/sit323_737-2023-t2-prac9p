# Adding MongoDB to our Cloud-Native Application

This documentation details the process of adding a MongoDB database to our cloud-native application deployed on Kubernetes.

## MongoDB Database Configuration

Our MongoDB setup uses a stateful set to ensure data persistence across pod lifecycle. This setup includes the following key configuration files:

1. `createStorageClass.yaml` - Defines the storage class for the MongoDB volume.
2. `createPersistentVolume.yaml` - Creates the Persistent Volume that the MongoDB database will use.
3. `createPersistentVolumeClaim.yaml` - Claims the created Persistent Volume for MongoDB use.
4. `createConfigMap.yaml` - Defines MongoDB specific configurations.
5. `createMongoDbSecret.yaml` - Secures sensitive information such as database username and password.
6. `createStatefulSet.yaml` - Describes a MongoDB stateful application.
7. `createHeadlessService.yaml` - Enables network access to MongoDB.
8. `createPod.yaml` - Defines the MongoDB pod configuration.
9. `createService.yaml` - Exposes MongoDB on a network port for communication with other services.

## Steps for Implementation

1. First, apply the `createStorageClass.yaml` using the command: `kubectl apply -f createStorageClass.yaml`.
2. Follow up with `kubectl apply -f createPersistentVolume.yaml` and `kubectl apply -f createPersistentVolumeClaim.yaml`.
3. Apply `createConfigMap.yaml` and `createMongoDbSecret.yaml` using similar commands.
4. Now, apply the `createStatefulSet.yaml` with `kubectl apply -f createStatefulSet.yaml`. This step creates the MongoDB replica set.
5. After this, apply `createHeadlessService.yaml` and `createService.yaml`.
6. Finally, check that everything is running correctly by using `kubectl get all`.

## Testing and Verification

To confirm successful deployment, we execute commands to each MongoDB pod (mongo-0, mongo-1, and mongo-2) and check their status. Additionally, we ensure the application is capable of performing CRUD operations.

For instance, we used the command `db.mycollection.insertOne({ name:'Bob', age:30 })` to insert a record into the MongoDB database. Then, we tested that this data was replicated correctly across all pods by entering the MongoDB shell for each pod and checking the inserted data.

Your `mongo-0` pods can be shown by the `rs.status()` command output. You can now write data to the `mongo-0` pod as only primaries can accept write operations in a MongoDB replica set.

To test the replication, you can insert data into `mongo-0` and then check if the data has been replicated to `mongo-1` and `mongo-2`.

You can use the following commands:

To initialize the replica set, you need to use the `rs.initiate()` command from the mongo shell in one of your MongoDB instances (preferably, mongo-0). Here is an example of the command and its structure:

```sell

```

Here's how you can perform these steps:

1. Use the following command to get a shell into the mongo-0 pod:

```shell
kubectl exec -it mongo-0 -- /bin/bash
```

2. Start the mongo shell:

```shell
mongo
```

3. Use the `rs.initiate()` command as shown above.

4. It might take a few seconds for the replica set to elect a primary. You can use the command `rs.status()` to check if the mongo-0 has become primary.


After this, you should be able to use `rs.status()` to check the status of your replica set and confirm that the configuration has been applied.

1. Insert data into `mongo-0`:

```bash
db.mycollection.insertOne({ name:'Bob', age:30 })
```

2. Check that the data was inserted:

```bash
db.mycollection.find()
```

Then, you can switch to the `mongo-1` and `mongo-2` pods and check if the data has been replicated:

1. Make `mongo-1` and `mongo-2` pods able to read data:

```bash
rs.slaveOk()
```

2. Find the data:

```bash
db.mycollection.find()
```
