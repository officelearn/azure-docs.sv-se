---
title: Kör ett Apache Spark-jobb med Azure Kubernetes Service (AKS)
description: Använd Azure Kubernetes Service (AKS) för att köra ett Apache Spark-jobb
services: container-service
author: rockboyfor
manager: digimobile
ms.service: container-service
ms.topic: article
origin.date: 03/15/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.custom: mvc
ms.openlocfilehash: ddaff590fd493b430a72c30dd35cb1b891b80d84
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104973"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Kör Apache Spark-jobb på AKS

[Apache Spark] [ apache-spark] är en snabb motor för storskalig databearbetning. Från och med den [Spark 2.3.0 versionen][spark-latest-release], Apache Spark har stöd för inbyggd integration med Kubernetes-kluster. Azure Kubernetes Service (AKS) är en hanterad Kubernetes-miljö som körs i Azure. Det här dokumentet beskriver förbereder och Apache Spark-jobb som körs på ett kluster i Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att kunna slutföra stegen i den här artikeln.

* Grundläggande kunskaper om Kubernetes och [Apache Spark][spark-quickstart].
* [Docker Hub] [ docker-hub] konto, eller en [Azure Container Registry][acr-create].
* Azure CLI [installerat] [ azure-cli] i utvecklingssystemet.
* [JDK 8] [ java-install] installerat på datorn.
* Segregerade Barlasttankar ([Scala skapa verktyget][sbt-install]) installerat på datorn.
* Git kommandoradsverktyg installeras på datorn.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Spark används för att bearbeta storskaliga och kräver att Kubernetes-noderna är storlek för att uppfylla kraven för Spark-resurser. Vi rekommenderar en minsta storlek på `Standard_D3_v2` för Azure Kubernetes Service (AKS)-noder.

Om du behöver ett AKS-kluster som uppfyller den här minsta rekommendationen, kör du följande kommandon.

Skapa en resursgrupp för klustret.

```azurecli
az group create --name mySparkCluster --location chinaeast2
```

Skapa AKS-kluster med noder som är av storleken `Standard_D3_v2`.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2
```

Ansluta till AKS-klustret.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Om du använder Azure Container Registry (ACR) för att lagra behållaravbildningar, konfigurerar du autentisering mellan AKS och ACR. Se den [ACR-autentisering dokumentation] [ acr-aks] för de här stegen.

## <a name="build-the-spark-source"></a>Skapa Spark-källa

Innan du kör Spark-jobb på ett AKS-kluster, måste du skapa Spark källkoden och paketera den i en behållaravbildning. Spark-källan innehåller skript som kan användas för att slutföra den här processen.

Klona databasen för Spark-projektet till utvecklingssystemet.

```bash
git clone -b branch-2.3 https://github.com/apache/spark
```

Ändra till katalogen på den klonade lagringsplatsen och spara sökvägen för Spark-källan i en variabel.

```bash
cd spark
sparkdir=$(pwd)
```

Om du har flera JDK-versioner som är installerade, ange `JAVA_HOME` att använda version 8 för den aktuella sessionen.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Kör följande kommando för att skapa Spark källkoden med stöd för Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Följande kommandon skapa Spark-behållaravbildning och push-överföra den till ett behållarregister för avbildningen. Ersätt `registry.example.com` med namnet på behållarregistret och `v1` med taggen du föredrar att använda. Om du använder Docker Hub, är det här värdet registernamnet. Om du använder Azure Container Registry (ACR), är det här värdet namnet på ACR-inloggningsservern.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Överföra behållaravbildningen till avbildningen till behållarregistret.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Förbereda ett Spark-jobb

Förbered ett Spark-jobb. Jar-filen används för att lagra Spark-jobbet och när du kör den `spark-submit` kommando. Jar kan vara åtkomlig via en offentlig URL eller tillsammans i en behållaravbildning. I det här exemplet skapas en exempel-jar för att beräkna värdet för Pi. Den här jar överförs sedan till Azure storage. Om du har en befintlig jar passa på att ersätta

Skapa en katalog där du vill skapa projektet för ett Spark-jobb.

```bash
mkdir myprojects
cd myprojects
```

Skapa ett nytt Scala-projekt från en mall.

```bash
sbt new sbt/scala-seed.g8
```

När du uppmanas, anger `SparkPi` som projektnamn.

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

Kör dessa kommandon för att kopiera exempelkoden till det nyskapade projektet och Lägg till alla nödvändiga beroenden.

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

Om du vill paketera projektet till en jar, kör du följande kommando.

```bash
sbt assembly
```

Du bör se utdata som liknar följande när du har lyckats paketering.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Kopiera jobb till storage

Skapa ett Azure storage-konto och en behållare för jar-filen.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location chinaeast2
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Ladda upp jar-filen till Azure storage-konto med följande kommandon.

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

Starta kube-proxy i en separat kommandoradsverktyget med följande kod.

```bash
kubectl proxy
```

Gå tillbaka till roten Spark-lagringsplatsen.

```bash
cd $sparkdir
```

Skicka till jobbet med `spark-submit`.

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

Den här åtgärden startar Spark-jobb som strömmar Jobbstatus i shell-sessionen. När jobbet körs, kan du se Spark-drivrutinen pod och executor poddar med hjälp av kubectl hämta poddar kommando. Öppna en andra terminalsession för att köra dessa kommandon.

```console
$ kubectl get pods

NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Du kan även använda Spark-Användargränssnittet medan jobbet körs. I den andra terminalsessionen använder den `kubectl port-forward` kommandot ger åtkomst till Spark-Gränssnittet.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

För att komma åt Användargränssnittet för Spark, öppna adressen `127.0.0.1:4040` i en webbläsare.

![Spark UI](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Hämta jobbresultat och loggar

När jobbet har slutförts blir drivrutinen pod med tillståndet ”slutfört”. Hämta namnet på en pod med följande kommando.

```bash
kubectl get pods --show-all
```

Utdata:

```bash
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Använd den `kubectl logs` kommando för att hämta loggar från spark-drivrutinen pod. Ersätt pod-namnet med ditt podnamn för drivrutinen.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

Du kan se resultatet av Spark-jobbet, vilket är värdet för Pi inom dessa loggar.

```bash
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Paketet jar med behållaravbildning

I exemplet ovan har Spark jar-filen överförts till Azure storage. Ett annat alternativ är att paketera jar-filen till specialbyggda Docker-avbildningar.

Du gör detta genom att hitta den `dockerfile` för Spark-avbildningen finns på `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` directory. Lägg till är `ADD` instruktionen för Spark-jobbet `jar` någonstans mellan `WORKDIR` och `ENTRYPOINT` deklarationer.

Uppdatera jar-sökvägen till platsen för den `SparkPi-assembly-0.1.0-SNAPSHOT.jar` filen i utvecklingssystemet. Du kan också använda dina egna anpassade jar-filen.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Skapa och överför avbildningen med inkluderade Spark-skript.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

När du kör jobbet, i stället för som anger en fjärransluten jar-URL i `local://` schemat kan användas med sökvägen till jar-filen i Docker-avbildningen.

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
> Från Spark [dokumentation][spark-docs]: ”Kubernetes-scheduler är för närvarande experimentella. I framtida versioner kan finnas det förändringar i beteendet runt konfiguration, behållaravbildningar och entrypoints ”.

## <a name="next-steps"></a>Nästa steg

Kolla in Spark-dokumentationen för mer information.

> [!div class="nextstepaction"]
> [Spark-dokumentation][spark-docs]

<!-- LINKS - external -->
[apache-spark]: https://spark.apache.org/
[docker-hub]: https://docs.docker.com/docker-hub/
[java-install]: https://docs.azure.cn/zh-cn/java/java-supported-jdk-runtime?view=azure-java-stable
[sbt-install]: https://www.scala-sbt.org/1.0/docs/Setup.html
[spark-docs]: https://spark.apache.org/docs/latest/running-on-kubernetes.html
[spark-latest-release]: https://spark.apache.org/releases/spark-release-2-3-0.html
[spark-quickstart]: https://spark.apache.org/docs/latest/quick-start.html

<!-- LINKS - internal -->
[acr-aks]: /container-registry/container-registry-auth-aks
[acr-create]: /container-registry/container-registry-get-started-azure-cli
[aks-quickstart]: /aks/
[azure-cli]: https://docs.azure.cn/zh-cn/cli/?view=azure-cli-latest?view=azure-cli-latest
[storage-account]: /storage/common/storage-azure-cli