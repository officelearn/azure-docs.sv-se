---
title: Köra ett Apache Spark-jobb med Azure Kubernetes Service (AKS)
description: Använda Azure Kubernetes Service (AKS) för att köra ett Apache Spark-jobb
author: lenadroid
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: alehall
ms.custom: mvc
ms.openlocfilehash: 4b3248cb9ab61a158f70b5a2d6ae9dd846501816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473633"
---
# <a name="running-apache-spark-jobs-on-aks"></a>Köra Apache Spark-jobb på AKS

[Apache Spark][apache-spark] är en snabb motor för storskalig databehandling. Från och med [Spark 2.3.0-versionen][spark-latest-release]stöder Apache Spark inbyggd integrering med Kubernetes-kluster. Azure Kubernetes Service (AKS) är en hanterad Kubernetes-miljö som körs i Azure. Den här dokumentinformationen som förbereder och kör Apache Spark-jobb på ett AKS-kluster (Azure Kubernetes Service).

## <a name="prerequisites"></a>Krav

För att kunna slutföra stegen i den här artikeln behöver du följande.

* Grundläggande förståelse av Kubernetes och [Apache Spark][spark-quickstart].
* [Docker Hub-konto][docker-hub] eller ett [Azure Container-register][acr-create].
* Azure CLI [installerat][azure-cli] på ditt utvecklingssystem.
* [JDK 8][java-install] installerat på ditt system.
* SBT ([Scala Build Tool][sbt-install]) installerat på ditt system.
* Git kommandoradsverktyg installerade på datorn.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Spark används för storskalig databehandling och kräver att Kubernetes-noder har storlek för att uppfylla Spark-resurskraven. Vi rekommenderar en `Standard_D3_v2` minsta storlek för dina AKS-noder (Azure Kubernetes Service).

Om du behöver ett AKS-kluster som uppfyller den här minimirekommendationen kör du följande kommandon.

Skapa en resursgrupp för klustret.

```azurecli
az group create --name mySparkCluster --location eastus
```

Skapa ett tjänsthuvudnamn för klustret. När den har skapats behöver du appen Service Principal och lösenord för nästa kommando.

```azurecli
az ad sp create-for-rbac --name SparkSP
```

Skapa AKS-klustret med noder som är av storlek `Standard_D3_v2`och värden för appId och lösenord som skickas som parametrar för tjänsthuvudnamn och klienthemlighet.

```azurecli
az aks create --resource-group mySparkCluster --name mySparkCluster --node-vm-size Standard_D3_v2 --generate-ssh-keys --service-principal <APPID> --client-secret <PASSWORD>
```

Anslut till AKS-klustret.

```azurecli
az aks get-credentials --resource-group mySparkCluster --name mySparkCluster
```

Om du använder Azure Container Registry (ACR) för att lagra behållaravbildningar konfigurerar du autentisering mellan AKS och ACR. Mer om du vill använda dokumentationen för ACR-autentisering finns i dokumentationen för [ACR-autentisering.][acr-aks]

## <a name="build-the-spark-source"></a>Bygg Spark-källan

Innan du kör Spark-jobb i ett AKS-kluster måste du skapa Spark-källkoden och paketera den till en behållaravbildning. Spark-källan innehåller skript som kan användas för att slutföra den här processen.

Klona Spark-projektarkivet till ditt utvecklingssystem.

```bash
git clone -b branch-2.4 https://github.com/apache/spark
```

Byt till katalogen för den klonade databasen och spara sökvägen för Spark-källan till en variabel.

```bash
cd spark
sparkdir=$(pwd)
```

Om du har flera JDK-versioner installerade ställer du in på `JAVA_HOME` att använda version 8 för den aktuella sessionen.

```bash
export JAVA_HOME=`/usr/libexec/java_home -d 64 -v "1.8*"`
```

Kör följande kommando för att skapa Spark-källkoden med stöd för Kubernetes.

```bash
./build/mvn -Pkubernetes -DskipTests clean package
```

Följande kommandon skapar miniatyrbehållarens avbildning och skickar den till ett containeravbildningsregister. Ersätt `registry.example.com` med namnet på behållarregistret och `v1` med den tagg du föredrar att använda. Om du använder Docker Hub är det här värdet registernamnet. Om du använder Azure Container Registry (ACR) är det här värdet ACR-inloggningsserverns namn.

```bash
REGISTRY_NAME=registry.example.com
REGISTRY_TAG=v1
```

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG build
```

Skicka behållaravbildningen till behållaravbildningsregistret.

```bash
./bin/docker-image-tool.sh -r $REGISTRY_NAME -t $REGISTRY_TAG push
```

## <a name="prepare-a-spark-job"></a>Förbereda ett Spark-jobb

Förbered sedan ett Spark-jobb. En jar-fil används för att hålla spark-jobbet och behövs när `spark-submit` kommandot körs. Burken kan göras tillgänglig via en offentlig WEBBADRESS eller förpackas i en behållaravbildning. I det här exemplet skapas en provburk för att beräkna värdet för Pi. Den här burken överförs sedan till Azure-lagring. Om du har en befintlig burk är du välkommen att ersätta

Skapa en katalog där du vill skapa projektet för ett Spark-jobb.

```bash
mkdir myprojects
cd myprojects
```

Skapa ett nytt Scala-projekt från en mall.

```bash
sbt new sbt/scala-seed.g8
```

Ange för `SparkPi` projektnamnet när du uppmanas att göra det.

```bash
name [Scala Seed Project]: SparkPi
```

Navigera till den nyskapade projektkatalogen.

```bash
cd sparkpi
```

Kör följande kommandon för att lägga till en SBT plugin, som gör att förpackningen projektet som en jar-fil.

```bash
touch project/assembly.sbt
echo 'addSbtPlugin("com.eed3si9n" % "sbt-assembly" % "0.14.10")' >> project/assembly.sbt
```

Kör dessa kommandon för att kopiera exempelkoden till det nyligen skapade projektet och lägga till alla nödvändiga beroenden.

```bash
EXAMPLESDIR="src/main/scala/org/apache/spark/examples"
mkdir -p $EXAMPLESDIR
cp $sparkdir/examples/$EXAMPLESDIR/SparkPi.scala $EXAMPLESDIR/SparkPi.scala

cat <<EOT >> build.sbt
// https://mvnrepository.com/artifact/org.apache.spark/spark-sql
libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.0" % "provided"
EOT

sed -ie 's/scalaVersion.*/scalaVersion := "2.11.11"/' build.sbt
sed -ie 's/name.*/name := "SparkPi",/' build.sbt
```

Om du vill paketera projektet i en burk kör du följande kommando.

```bash
sbt assembly
```

Efter lyckad förpackning bör du se utdata som liknar följande.

```bash
[info] Packaging /Users/me/myprojects/sparkpi/target/scala-2.11/SparkPi-assembly-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[success] Total time: 10 s, completed Mar 6, 2018 11:07:54 AM
```

## <a name="copy-job-to-storage"></a>Kopiera jobb till lagring

Skapa ett Azure-lagringskonto och en behållare för att lagra jar-filen.

```azurecli
RESOURCE_GROUP=sparkdemo
STORAGE_ACCT=sparkdemo$RANDOM
az group create --name $RESOURCE_GROUP --location eastus
az storage account create --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT --sku Standard_LRS
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $RESOURCE_GROUP --name $STORAGE_ACCT -o tsv`
```

Ladda upp jar-filen till Azure-lagringskontot med följande kommandon.

```azurecli
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

Variabeln `jarUrl` innehåller nu den allmänt tillgängliga sökvägen till jar-filen.

## <a name="submit-a-spark-job"></a>Skicka in ett Spark-jobb

Starta kube-proxy på en separat kommandorad med följande kod.

```bash
kubectl proxy
```

Navigera tillbaka till roten i Spark-databasen.

```bash
cd $sparkdir
```

Skapa ett tjänstkonto som har tillräcklig behörighet för att köra ett jobb.

```bash
kubectl create serviceaccount spark
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

Skicka jobbet `spark-submit`med .

```bash
./bin/spark-submit \
  --master k8s://http://127.0.0.1:8001 \
  --deploy-mode cluster \
  --name spark-pi \
  --class org.apache.spark.examples.SparkPi \
  --conf spark.executor.instances=3 \
  --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
  --conf spark.kubernetes.container.image=$REGISTRY_NAME/spark:$REGISTRY_TAG \
  $jarUrl
```

Den här åtgärden startar Spark-jobbet, som strömmar jobbstatus till din skalsession. Medan jobbet körs kan du se Spark-drivrutinskapslar och executorpoddar med kommandot kubectl get pods. Öppna en andra terminalsession för att köra dessa kommandon.

```console
kubectl get pods
```

```output
NAME                                               READY     STATUS     RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   1/1       Running    0          16s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-1   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-2   0/1       Init:0/1   0          4s
spark-pi-2232778d0f663768ab27edc35cb73040-exec-3   0/1       Init:0/1   0          4s
```

Medan jobbet körs kan du också komma åt Spark-användargränssnittet. I den andra terminalsessionen `kubectl port-forward` använder du kommandot som ger åtkomst till Spark UI.

```bash
kubectl port-forward spark-pi-2232778d0f663768ab27edc35cb73040-driver 4040:4040
```

Öppna adressen `127.0.0.1:4040` i en webbläsare om du vill komma åt Spark UI.

![Spark UI](media/aks-spark-job/spark-ui.png)

## <a name="get-job-results-and-logs"></a>Få jobbresultat och loggar

När jobbet är klart kommer drivrutinsenheten att vara i tillståndet "Slutförd". Hämta namnet på kapseln med följande kommando.

```bash
kubectl get pods --show-all
```

Resultat:

```output
NAME                                               READY     STATUS      RESTARTS   AGE
spark-pi-2232778d0f663768ab27edc35cb73040-driver   0/1       Completed   0          1m
```

Använd `kubectl logs` kommandot för att hämta loggar från spark driver pod. Ersätt pod-namnet med drivrutinsenhetens namn.

```bash
kubectl logs spark-pi-2232778d0f663768ab27edc35cb73040-driver
```

I dessa loggar kan du se resultatet av Spark-jobbet, som är värdet för Pi.

```output
Pi is roughly 3.152155760778804
```

## <a name="package-jar-with-container-image"></a>Paketburk med behållaravbildning

I exemplet ovan laddades Spark-jar-filen upp till Azure-lagring. Ett annat alternativ är att paketera jar-filen i specialbyggda Docker-avbildningar.

Det gör du `dockerfile` genom att leta `$sparkdir/resource-managers/kubernetes/docker/src/main/dockerfiles/spark/` reda på miniatyravbildningen som finns i katalogen. Lägg `ADD` till am-sats `WORKDIR` för `ENTRYPOINT` Spark-jobbet `jar` någonstans mellan och deklarationer.

Uppdatera jar-sökvägen till `SparkPi-assembly-0.1.0-SNAPSHOT.jar` platsen för filen i utvecklingssystemet. Du kan också använda din egen anpassade jar-fil.

```bash
WORKDIR /opt/spark/work-dir

ADD /path/to/SparkPi-assembly-0.1.0-SNAPSHOT.jar SparkPi-assembly-0.1.0-SNAPSHOT.jar

ENTRYPOINT [ "/opt/entrypoint.sh" ]
```

Skapa och tryck på avbildningen med de medföljande Spark-skripten.

```bash
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> build
./bin/docker-image-tool.sh -r <your container repository name> -t <tag> push
```

När du kör jobbet, i stället för `local://` att ange en url för fjärrburk, kan schemat användas med sökvägen till jar-filen i Docker-avbildningen.

```bash
./bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=3 \
    --conf spark.kubernetes.authenticate.driver.serviceAccountName=spark \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///opt/spark/work-dir/<your-jar-name>.jar
```

> [!WARNING]
> Från Spark [dokumentation][spark-docs]: "Den Kubernetes scheduler är för närvarande experimentell. I framtida versioner kan det finnas beteendeförändringar kring konfiguration, behållaravbildningar och entrypoints".

## <a name="next-steps"></a>Nästa steg

Kolla in Spark dokumentation för mer information.

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
