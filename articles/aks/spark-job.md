---
title: Köra ett Apache Spark jobb med Azure Kubernetes service (AKS)
description: Använda Azure Kubernetes service (AKS) för att köra ett Apache Spark jobb
services: container-service
author: lenadroid
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/15/2018
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 647cb0573922bb53232dbce3f3a7a2557553d47d
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263892"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Köra Apache Spark jobb på AKS

[Apache Spark][apache-spark] är en snabb motor för storskalig data bearbetning. Från och med [Spark 2.3.0-versionen][spark-latest-release]stöder Apache Spark inbyggd integrering med Kubernetes-kluster. Azure Kubernetes service (AKS) är en hanterad Kubernetes-miljö som körs i Azure. Den här dokument informationen förbereder och kör Apache Spark jobb i ett AKS-kluster (Azure Kubernetes service).

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här artikeln behöver du följande.

* Grundläggande förståelse för Kubernetes och [Apache Spark][spark-quickstart].
* [Docker Hub][docker-hub] -konto eller en [Azure Container Registry][acr-create].
* Azure CLI [installerat][azure-cli] i utvecklings systemet.
* [JDK 8][java-install] är installerat i systemet.
* SBT ([Scala build Tool][sbt-install]) installerat i systemet.
* Git kommando rads verktyg som är installerade på systemet.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Spark används för storskalig data bearbetning och kräver att Kubernetes-noderna är så stora att de uppfyller kraven för Spark-resurser. Vi rekommenderar en minimal storlek på `Standard_D3_v2` för dina Azure Kubernetes service-noder (AKS).

Om du behöver ett AKS-kluster som uppfyller den här minsta rekommendationen kör du följande kommandon.

Skapa en resurs grupp för klustret.

```azurecli
az group create --name mySparkCluster --location eastus
```

Skapa AKS-klustret med noder som är i storlek `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Anslut till AKS-klustret.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Om du använder Azure Container Registry (ACR) för att lagra behållar avbildningar konfigurerar du autentisering mellan AKS och ACR. Se [dokumentationen för ACR-autentisering][acr-aks] för de här stegen.

## <a name="build-the-spark-source"></a>Bygg Spark-källan

Innan du kör Spark-jobb på ett AKS-kluster måste du bygga Spark-källkoden och paketera den i en behållar avbildning. Spark-källan innehåller skript som kan användas för att slutföra den här processen.

Klona Spark-projektfilen till utvecklings systemet.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Ändra till katalogen för den klonade lagrings platsen och spara sökvägen till Spark-källan till en variabel.

```bash
cd spark
sparkdir=$(pwd)
```

Om du har flera JDK-versioner installerade anger du `JAVA_HOME` om du vill använda version 8 för den aktuella sessionen.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Kör följande kommando för att skapa Spark-källkoden med stöd för Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Följande kommandon skapar Spark container-avbildningen och push-överför den till ett register för behållar avbildningar. Ersätt `registry.example.com` med namnet på ditt behållar register och `v1` med den tagg som du vill använda. Om du använder Docker-hubb är det här värdet register namnet. Om du använder Azure Container Registry (ACR) är det här värdet namnet på ACR-inloggnings servern.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Push-överför behållar avbildningen till ditt behållar avbildnings register.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Förbereda ett Spark-jobb

Förbered sedan ett Spark-jobb. En jar-fil används för att hålla Spark-jobbet och behövs när du kör kommandot `spark-submit`. Jar kan göras tillgänglig via en offentlig URL eller förpackas i en behållar avbildning. I det här exemplet skapas en jar-exempel för att beräkna värdet för PI. Den här jar-filen överförs sedan till Azure Storage. Om du har en befintlig jar är du välkommen att ersätta

Skapa en katalog där du vill skapa projektet för ett Spark-jobb.

```bash
mkdir myprojects
cd myprojects
```

Skapa ett nytt Scala-projekt från en mall.

```bash
sbt new sbt/scala-seed.g8
```

När du uppmanas anger du `SparkPi` som projekt namn.

```bash
name [Scala Seed Project]: SparkPi
```

Navigera till den nyligen skapade projekt katalogen.

```bash
cd sparkpi
```

Kör följande kommandon för att lägga till ett SBT-plugin-program som gör det möjligt att paketera projektet som en jar-fil.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.6")' >> project/assembly.sbt
```

Kör dessa kommandon för att kopiera exempel koden till det nyskapade projektet och lägga till alla nödvändiga beroenden.

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

Om du vill paketera projektet i en jar kör du följande kommando.

```bash
sbt assembly
```

Efter en lyckad paketering bör du se utdata som liknar följande.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Kopiera jobb till lagring

Skapa ett Azure Storage-konto och en behållare som ska innehålla jar-filen.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Överför jar-filen till Azure Storage-kontot med följande kommandon.

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

Variabeln `jarUrl` innehåller nu den offentligt tillgängliga sökvägen till jar-filen.

## <a name="submit-a-spark-job"></a>Skicka ett Spark-jobb

Starta Kube-proxy i en separat kommando rad med följande kod.

```bash
kubectl proxy
```

Gå tillbaka till roten av Spark-lagringsplatsen.

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

Den här åtgärden startar Spark-jobbet som strömmar jobb status till din Shell-session. Medan jobbet körs kan du se Spark-drivrutinen Pod och utförar poddar med kommandot kubectl get poddar. Öppna en andra terminalserversession för att köra dessa kommandon.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

När jobbet körs kan du också komma åt Spark-ANVÄNDARGRÄNSSNITTET. I den andra terminalfönstret använder du kommandot `kubectl port-forward` för att ge åtkomst till Spark-ANVÄNDARGRÄNSSNITTET.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Om du vill komma åt Spark-ANVÄNDARGRÄNSSNITTET öppnar du adressen `127.0.0.1:4040` i en webbläsare.

![Spark-användargränssnitt](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Hämta jobb resultat och loggar

När jobbet har slutförts kommer driv Rutinens pod att ha statusen "slutfört". Hämta namnet på Pod med följande kommando.

```bash
kubectl get pods --show-all
```

Resultat:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Använd kommandot `kubectl logs` för att hämta loggar från Spark-drivrutinen pod. Ersätt namnet på Pod med driv rutins Pod namn.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

I dessa loggar kan du se resultatet av Spark-jobbet, vilket är värdet PI.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Paket jar med behållar avbildning

I exemplet ovan överfördes Spark-jar-filen till Azure Storage. Ett annat alternativ är att paketera jar-filen i anpassade Docker-avbildningar.

Det gör du genom att leta upp `dockerfile` för Spark-avbildningen som finns i `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/`-katalogen. Lägg till am `ADD`-satsen för Spark-jobbet `jar` någonstans mellan `WORKDIR`-och `ENTRYPOINT`-deklarationer.

Uppdatera jar-sökvägen till platsen för `SparkPi-assembly-0.1.0-SNAPSHOT.jar`-filen i utvecklings systemet. Du kan också använda en egen anpassad jar-fil.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Bygg och skicka avbildningen med de inkluderade Spark-skripten.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

När jobbet körs, i stället för att ange en jar-URL, kan `local://`-schemat användas med sökvägen till jar-filen i Docker-avbildningen.

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
> Från Spark- [dokumentationen][spark-docs]: "Kubernetes Scheduler är för närvarande experimentell. I framtida versioner kan det uppstå funktions förändringar runt konfiguration, behållar avbildningar och entrypoints ".

## <a name="next-steps"></a>Nästa steg

Se Spark-dokumentationen för mer information.

> [!div class="nextstepaction"]
> [Spark-dokumentation][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://aka.ms/azure-jdks
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html


<!-- LINKS - internal -->
[acr-aks]: cluster-container-registry-integration.md
[acr-create]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: https://docs.microsoft.com/azure/aks/
[azure-cli]: https://docs.microsoft.com/cli/azure/?view=azure-cli-latest
[storage-account]: https://docs.microsoft.com/azure/storage/common/storage-azure-cli
