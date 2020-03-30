---
title: Integrera befintliga MongoDB-program med Azure Cosmos DB API för MongoDB och Open Service Broker för Azure (OSBA)
description: I den här artikeln får du lära dig hur du integrerar ett befintligt Java- och MongoDB-program med Azure Cosmos DB API för MongoDB med Open Service Broker for Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker för Azure
ms.openlocfilehash: ddaa3b9aa198bc142e1bcbcab6b7b1e028eff2aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247924"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrera befintliga MongoDB-program med Azure Cosmos DB API för MongoDB och Open Service Broker för Azure (OSBA)

Azure Cosmos DB är en globalt distribuerad databas för flera datamodeller. Det ger också trådprotokollkompatibilitet med flera NoSQL API:er, inklusive för MongoDB. Cosmos DB API för MongoDB kan du använda Cosmos DB med din befintliga MongoDB ansökan utan att behöva ändra programmets databasdrivrutiner eller genomförande. Du kan också etablera en Cosmos DB-tjänst med Open Service Broker för Azure.

I den här artikeln tar du ett befintligt Java-program som använder en MongoDB-databas och uppdaterar den för att använda en Cosmos DB-databas med Open Service Broker för Azure.

## <a name="prerequisites"></a>Krav

Innan du kan fortsätta måste du:
    
* Har ett [Azure Kubernetes-tjänstkluster](kubernetes-walkthrough.md) skapat.
* Ha [Open Service Broker för Azure installerat och konfigurerat på aks-klustret](integrate-azure.md). 
* Låt [CLI:en](https://svc-cat.io/docs/install/) ha installerat och `svcat` konfigurerat servicekatalogen för att köra kommandon.
* Har en befintlig [MongoDB-databas.](https://www.mongodb.com/) Du kan till exempel ha MongoDB som körs på [utvecklingsdatorn](https://docs.mongodb.com/manual/administration/install-community/) eller i en [Azure VM](../virtual-machines/linux/install-mongodb.md).
* Har ett sätt att ansluta till och fråga MongoDB-databasen, till exempel [mongo-skalet](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Hämta programkod
    
I den här artikeln använder du [exempelprogrammet vårens musik från Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) för att demonstrera ett program som använder en MongoDB-databas.
    
Klona programmet från GitHub och gå till programmets katalog:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Förbered programmet för att använda din MongoDB-databas

Vårens musikprovsprogram innehåller många alternativ för datakällor. I den här artikeln konfigurerar du den för att använda en befintlig MongoDB-databas. 

Lägg till YAML följande till slutet av *src/main/resources/application.yml*. Det här tillägget skapar en profil som heter *mongodb* och konfigurerar ett URI- och databasnamn. Ersätt URI:n med anslutningsinformationen till din befintliga MongoDB-databas. Lägga till URI, som innehåller ett användarnamn och lösenord, direkt till den här filen är endast för **utveckling användning** och bör aldrig läggas **till versionskontroll**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



När du startar programmet och be den att använda *mongodb-profilen* ansluter den till din MongoDB-databas och använder den för att lagra programmets data.

Så här skapar du ditt program:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Starta programmet och säg åt den att använda *profilen mongodb:*

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navigera `http://localhost:8080` till i webbläsaren.

![Spring-musikappen med standarddata](media/music-app.png)

Observera att programmet har fyllts i med vissa [standarddata](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interagera med den genom att ta bort några befintliga album och skapa några nya.

Du kan kontrollera att ditt program använder din MongoDB-databas genom att ansluta till den och fråga *musicdb-databasen:*

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

I föregående exempel används [mongo-skalet](https://docs.mongodb.com/manual/mongo/) för att ansluta till MongoDB-databasen och fråga det. Du kan också kontrollera att ändringarna sparas genom att stoppa programmet, starta om det och navigera tillbaka till det i webbläsaren. Observera att de ändringar du har gjort finns kvar.


## <a name="create-a-cosmos-db-database"></a>Skapa en Cosmos DB-databas

Om du vill skapa en Cosmos DB-databas `svcat provision` i Azure med Öppna tjänstmäklare använder du kommandot:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Föregående kommando innehåller bestämmelser om en Cosmos DB-databas i Azure i resursgruppen *MyResourceGroup* i *regionen eastus.* Mer information om *resourceGroup,* *plats*och andra Azure-specifika JSON-parametrar finns i [referensdokumentationen för Cosmos DB-modul](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Kontrollera att databasen har slutfört etableringen med hjälp av kommandot `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Databasen är klar när du ser *Klar* under *STATUS*.

När databasen har etablerats måste du binda dess metadata till en [Kubernetes-hemlighet](https://kubernetes.io/docs/concepts/configuration/secret/). Andra program kan sedan komma åt dessa data efter att de har bundits till en hemlighet. Om du vill binda databasens metadata `svcat bind` till en hemlighet använder du kommandot:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Använda Cosmos DB-databasen med ditt program

Om du vill använda Cosmos DB-databasen med ditt program måste du känna till URI:n för att ansluta till den. Använd kommandot för att `kubectl get secret` hämta den här informationen:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Föregående kommando får *musicdb* hemlighet och visar bara URI. Hemligheter lagras i base64-format så föregående kommando avkodar också det.

Med hjälp av URI i Cosmos DB-databasen uppdaterar du *src/main/resources/application.yml* för att använda den:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

Uppdatera URI, som innehåller ett användarnamn och lösenord, direkt till den här filen är endast för **utvecklingsanvändning** och **bör aldrig läggas till versionskontroll**.

Återskapa och starta programmet för att börja använda Cosmos DB-databasen:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Observera att ditt program fortfarande använder *mongodb-profilen* och en URI som börjar med *mongodb://* för att ansluta till Cosmos DB-databasen. [Azure Cosmos DB API för MongoDB](../cosmos-db/mongodb-introduction.md) ger den här kompatibiliteten. Det gör att ditt program att fortsätta att fungera som om det använder en MongoDB databas, men det är faktiskt använder Cosmos DB.

Navigera `http://localhost:8080` till i webbläsaren. Observera att standarddata har återställts. Interagera med den genom att ta bort några befintliga album och skapa några nya. Du kan kontrollera att ändringarna sparas genom att stoppa programmet, starta om det och navigera tillbaka till det i webbläsaren. Observera att de ändringar du har gjort finns kvar. Ändringarna sparas i Cosmos DB som du skapade med Öppna tjänstmäklare för Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Kör ditt program på AKS-klustret

Du kan använda [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) för att distribuera programmet till AKS-klustret. Azure Dev Spaces hjälper dig att generera artefakter, till exempel Dockerfiles och Helm-diagram, och distribuera och köra ett program i AKS.

Så här aktiverar du Azure Dev Spaces i AKS-klustret:

```azurecli
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Använd Azure Dev Spaces-verktygen för att förbereda ditt program så att det körs i AKS:

```cmd
azds prep --public
```

Det här kommandot genererar flera artefakter, inklusive en *diagram /* mapp, som är ditt Helm-diagram, vid roten av projektet. Det här kommandot kan inte generera en *Dockerfile* för det här specifika projektet, så du måste skapa den.

Skapa en fil i roten till projektet *Dockerfile* med det här innehållet:

```dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Dessutom måste du uppdatera egenskapen *configurations.develop.build* i *azds.yaml* till *false:*
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Du måste också uppdatera *attributet containerPort* till *8080* i *diagram/spring-music/templates/deployment.yaml*:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Så här distribuerar du ditt program till AKS:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Navigera till webbadressen som visas i loggarna. I föregående exempel använder *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* du . 

Kontrollera att du ser programmet tillsammans med dina ändringar.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du uppdaterar ett befintligt program från att använda MongoDB till att använda Cosmos DB API för MongoDB. Den här artikeln omfattade också hur du etablerar en Cosmos DB-tjänst med Open Service Broker för Azure och distribuerar programmet till AKS med Azure Dev Spaces.

Mer information om Cosmos DB, Open Service Broker för Azure och Azure Dev Spaces finns i:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker for Azure](https://osba.sh)
* [Utveckla med Dev Spaces](../dev-spaces/azure-dev-spaces.md)
