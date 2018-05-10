---
title: Kör ett Apache Spark-jobb med Azure-Kubernetes (AKS)
description: Använd Azure Kubernetes Service (AKS) för att köra ett Apache Spark-jobb
services: container-service
author: lenadroid
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: fbeddb60ce968ff0d32e2ddb6a4f62d2036d80aa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="running-apache-spark-jobs-on-aks"></a>Apache Spark jobb som körs på AKS

[Apache Spark] [ apache-spark] är en snabb motor för storskalig databearbetning. Med den [Spark 2.3.0 versionen][spark-latest-release], Apache Spark har stöd för integrering med Kubernetes kluster. Azure Kubernetes Service (AKS) är en hanterad Kubernetes miljö som körs i Azure. Det här dokumentet beskriver förbereda och Apache Spark jobb som körs i ett kluster med Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att kunna slutföra stegen i den här artikeln.

* Grundläggande förståelse för Kubernetes och [Apache Spark][spark-quickstart].
* [Docker-hubb] [ docker-hub] konto, eller en [Azure Container registret][acr-create].
* Azure CLI [installerat] [ azure-cli] i utvecklingssystemet.
* [JDK 8] [ java-install] installerad på datorn.
* Segregerade Barlasttankar ([Scala skapa verktyget][sbt-install]) installerad på datorn.
* Git kommandoradsverktyg installeras på datorn.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Spark används för storskalig databearbetning och kräver att Kubernetes noder storleken anpassas för att uppfylla kraven för Spark-resurser. Vi rekommenderar en minsta storlek på `Standard_D3_v2` för Azure Kubernetes Service (AKS)-noder.

Om du behöver ett AKS kluster som uppfyller den här rekommendationen om minsta, kör du följande kommandon.

Skapa en resursgrupp för klustret.

```azurecli
az group create --name mySparkCluster --location eastus
```

Skapa AKS-kluster med noder som har storlek `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Ansluta till AKS-kluster.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Om du använder Azure Container registret (ACR) för att lagra avbildningar för behållaren, konfigurera autentisering mellan AKS och ACR. Finns det [ACR autentisering dokumentationen] [ acr-aks] för de här stegen.

## <a name="build-the-spark-source"></a>Skapa Spark-datakälla

Innan du kör Spark jobb på ett AKS kluster, måste du skapa Spark källkoden och paketet till en behållare bild. Spark-källan innehåller skript som kan användas för att slutföra processen.

Klona lagringsplatsen för Spark-projekt att utvecklingssystemet.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Ändra till katalogen på klonade databasen och spara sökvägen till Spark-datakälla till en variabel.

```bash
cd spark
sparkdir=$(pwd)
```

Om du har flera JDK-versioner som är installerade, `JAVA_HOME` att använda version 8 för den aktuella sessionen.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Kör följande kommando för att skapa Spark källkod med stöd för Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Följande kommandon skapa Spark behållaren avbildningen och push-installera den till en behållare avbildningen registret. Ersätt `registry.example.com` med namnet på behållaren registret och `v1` med taggen du föredrar att använda. Om du använder Docker-hubb, är det här värdet registret-namn. Om du använder Azure Container registret (ACR), är det här värdet ACR server inloggningsnamnet.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Skicka behållaren avbildningen till behållaren image registret.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Förbereda ett Spark-jobb

Förbered ett Spark-jobb. Jar-filen används för att lagra Spark-jobb och krävs när du kör den `spark-submit` kommando. Jar kan åtkomlig via en offentlig URL eller före paketeras i en behållare avbildning. I det här exemplet skapas en exempel-jar för att beräkna värdet för Pi. Den här jar överförs sedan till Azure-lagring. Om du har en befintlig jar passa på att ersätta

Skapa en katalog där du vill skapa projektet för ett Spark-jobb.

```bash
mkdir myprojects
cd myprojects
```

Skapa ett nytt Scala-projekt från en mall.

```bash
sbt new sbt/scala-seed.g8
```

När du uppmanas, anger `SparkPi` för projektnamnet.

```bash
name [Scala Seed Project]: SparkPi
```

Navigera till den nyligen skapade projektkatalogen.

```bash
cd sparkpi
```

Kör följande kommandon för att lägga till en segregerade Barlasttankar plugin-programmet, vilket gör att paketera projekt som en jar-fil.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Köra dessa kommandon för att kopiera exempelkoden till det nya projektet och Lägg till alla nödvändiga beroenden.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11",/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Kör följande kommando för att paketera projektet till en jar.

```bash
sbt assembly
```

Efter lyckad paketering, bör du se utdata som liknar följande.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Kopiera till lagring

Skapa ett Azure storage-konto och en behållare för jar-filen.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Överför jar-filen till Azure storage-konto med följande kommandon.

```bash
CONTAINER_NAME=jars
BLOB_NAME=SparkPi-assembly-0.1.0-SNAPSHOT.jar
FILE_TO_UPLOAD=target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar

echo "Creating the container..."
az storage container create --name $CONTAINER_NAME
az storage container set-permission --name $CONTAINER_NAME --public-access blob

echo "Uploading the file..."
az storage blob upload --container-name $CONTAINER_NAME --file $FILE_TO_UPLOAD --name $BLOB_NAME

jarUrl=$(az storage blob url --container-name $CONTAINER_NAME --name $BLOB_NAME | tr -d '"')
```

Variabeln `jarUrl` innehåller nu offentligt tillgänglig sökvägen till jar-filen.

## <a name="submit-a-spark-job"></a>Skicka ett Spark-jobb

Starta kube-proxy i en separat kommandoraden med följande kod.

```bash
kubectl proxy
```

Gå tillbaka till roten i Spark-databasen.

```bash
cd $sparkdir
```

Skicka jobbet med `spark-submit`.

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Den här åtgärden startar Spark-jobbet som strömmas Jobbstatus i shell-sessionen. När jobbet körs, ser du Spark drivrutinen baljor och utföraren skida med hjälp av kubectl hämta skida kommando. Öppna en andra terminalsession för att köra dessa kommandon.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Du kan också använda Spark-UI medan jobbet körs. I den andra terminalsessionen använder den `kubectl port-forward` kommandot ge åtkomst till Spark-Användargränssnittet.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Om du vill komma åt Spark UI, öppna adressen `127.0.0.1:4040` i en webbläsare.

![Spark UI](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Hämta jobbresultaten och loggar

När jobbet har slutförts blir drivrutinen baljor i tillståndet ”slutförd”. Hämta namnet på baljor med följande kommando.

```bash
kubectl get pods --show-all
```

Resultat:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Använd den `kubectl logs` kommando för att hämta loggar från spark drivrutinen baljor. Ersätt namnet baljor med din drivrutinen baljor namn.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Du kan se resultatet av jobbet Spark, vilket är värdet för Pi i dessa loggar.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Paketet jar med behållaren avbildning

I exemplet ovan har Spark jar-filen överförts till Azure-lagring. Ett annat alternativ är att paketera jar-filen till specialbyggt Docker-avbildningar.

Det gör du genom att söka efter den `dockerfile` för Spark-avbildningen på `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` directory. Lägg till är `ADD` instruktionen för Spark jobbet `jar` mellan `WORKDIR` och `ENTRYPOINT` deklarationer.

Uppdatera jar-sökvägen till platsen för den `SparkPi-assembly-0.1.0-SNAPSHOT.jar` filen i utvecklingssystemet. Du kan också använda din egen anpassade jar-filen.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Skapa och push-avbildningen med inkluderade Spark-skript.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

När du kör jobbet i stället för som anger en fjärransluten jar-URL i `local://` schema kan användas med sökvägen till jar-filen Docker-bild.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Från Spark [dokumentationen][spark-docs]: ”Kubernetes Schemaläggaren är för närvarande experiment. I framtida versioner av kan det finnas förändringar i beteendet runt konfiguration, behållare avbildningar och entrypoints ”.

## <a name="next-steps"></a>Nästa steg

Checka ut Spark-dokumentationen för mer information.

> [!div class="nextstepaction"]
> [Spark-dokumentation][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
