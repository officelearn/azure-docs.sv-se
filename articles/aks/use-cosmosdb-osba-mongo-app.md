---
title: Integrera befintliga MongoDB-program med Azure Cosmos DB API för MongoDB och Open Service Broker for Azure (OSBA)
description: I den här artikeln får du lära dig hur du integrerar ett befintligt Java och MongoDB-program med Azure Cosmos DB API för MongoDB med Open Service Broker for Azure (OSBA).
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB och öppna Service Broker, Open Service Broker for Azure
ms.openlocfilehash: 3146d11f33809391d93305d63bad757ed281fb70
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55977023"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrera befintliga MongoDB-program med Azure Cosmos DB API för MongoDB och Open Service Broker for Azure (OSBA)

Azure Cosmos DB är en globalt distribuerad databas för flera datamodeller. Det ger också under överföring protokollkompatibilitet med flera NoSQL APIs inklusive för MongoDB. API för MongoDB i Cosmos DB kan du använda Cosmos DB med din befintliga MongoDB-program utan att behöva ändra programmets databasdrivrutiner eller implementering. Du kan också etablera ett Cosmos DB-tjänsten med Open Service Broker för Azure.

I den här artikeln får du ta ett befintligt Java-program som använder en MongoDB-databas och uppdatera den om du vill använda med Open Service Broker för Azure Cosmos DB-databasen.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan fortsätta måste du:
    
* Har en [Azure Kubernetes Service-kluster](kubernetes-walkthrough.md) skapas.
* Har [Open Service Broker for Azure installeras och konfigureras på AKS-klustret](integrate-azure.md). 
* Har den [CLI för Tjänstkatalogen](https://svc-cat.io/docs/install/) installeras och konfigureras för att köra `svcat` kommandon.
* Har en befintlig [MongoDB](https://www.mongodb.com/) databas. Du kan till exempel ha MongoDB som körs på din [utvecklingsdator](https://docs.mongodb.com/manual/administration/install-community/) eller i en [Azure VM](../virtual-machines/linux/install-mongodb.md).
* Ha ett sätt att ansluta till och fråga MongoDB-databas som den [mongo-gränssnittet](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Hämta programkod
    
I den här artikeln använder du den [spring musik exempelprogrammet från Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) att visa ett program som använder en MongoDB-databas.
    
Klona programmet från GitHub och gå till programmets katalog:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Förbereda programmet till att använda MongoDB-databasen

Exempelprogrammet spring musik ger många alternativ för datakällor. I den här artikeln får konfigurera du den för att använda en befintlig MongoDB-databas. 

Lägg till följande YAML till slutet av *src/main/resources/application.yml*. Detta skapar en profil med namnet *mongodb* och konfigurerar ett namn för URI: N och databasen. Ersätt URI: N med anslutningsinformationen till din befintliga MongoDB-databas. Att lägga till den URI som innehåller ett användarnamn och lösenord, direkt till den här filen är för **använder endast** och **aldrig ska läggas till versionskontrollen**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



När du startar programmet och berätta det för att använda den *mongodb* profil, ansluter den till din MongoDB-databas och använda den för att lagra programdata.

Att skapa ditt program:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Starta appen och berätta det för att använda den *mongodb* profil:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Gå till http://localhost:8080 i din webbläsare.

![Spring-musikappen med standarddata](media/music-app.png)

Observera att programmet har fyllts med några [standard data](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interagera med den genom att ta bort några befintliga album och skapa några nya.

Du kan kontrollera att ditt program använder MongoDB-databasen genom att ansluta till den och frågar den *musicdb* databasen:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

I föregående exempel används den [mongo-gränssnittet](https://docs.mongodb.com/manual/mongo/) att ansluta till MongoDB-databasen och frågar den. Du kan också kontrollera sparas dina ändringar genom att stoppa programmet, starta om den och gå tillbaka till den i webbläsaren. Observera att du har gjort ändringarna är kvar.


## <a name="create-a-cosmos-db-database"></a>Skapa en Cosmos DB-databas

Du kan skapa en Cosmos DB-databas i Azure via Open Service Broker med den `svcat provision` kommando:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Föregående kommando etablerar en Cosmos DB-databas i Azure i resursgruppen *MyResourceGroup* i den *eastus* region. Mer information om *resourceGroup*, *plats*, och andra Azure-specifika JSON-parametrar är tillgänglig i den [Cosmos DB-modulen referensdokumentation](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Kontrollera att databasen har slutfört etableringen med hjälp av kommandot `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Din databas är klar när ser *redo* under *STATUS*.

När databasen har slutfört etablering, måste du binda dess metadata till en [Kubernetes-hemlighet](https://kubernetes.io/docs/concepts/configuration/secret/). Andra program kan sedan komma åt dessa data när den har bundits till en hemlighet. Om du vill binda metadata för din databas till en hemlighet, använda den `svcat bind` kommando:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>Använda Cosmos DB-databas med ditt program

Om du vill använda Cosmos DB-databas med ditt program, som du behöver veta URI: N att ansluta till den. Hämta den här informationen med den `kubectl get secret` kommando:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Föregående kommando hämtar den *musicdb* hemlighet och visar endast URI: N. Hemligheter lagras i base64-format så att det föregående kommandot avkodar också.

Med hjälp av URI: N för Cosmos DB-databasen, uppdatera *src/main/resources/application.yml* att använda den:

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

Uppdaterar den URI som innehåller ett användarnamn och lösenord, direkt till den här filen är för **använder endast** och **aldrig ska läggas till versionskontrollen**.

Återskapa och starta programmet att börja använda Cosmos DB-databasen:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Lägg märke till ditt program använder fortfarande den *mongodb* profil och en URI som börjar med *mongodb: / /* att ansluta till Cosmos DB-databasen. Den [Azure Cosmos DB API för MongoDB](../cosmos-db/mongodb-introduction.md) ger den här kompatibilitet. Det gör att programmet och fortsätt att arbeta som om den använder en MongoDB-databas, men den faktiskt använder Cosmos DB.

Gå till http://localhost:8080 i din webbläsare. Observera att dessa standarddata har återställts. Interagera med den genom att ta bort några befintliga album och skapa några nya. Du kan verifiera dina ändringar sparas genom att stoppa programmet, starta om den och gå tillbaka till den i webbläsaren. Observera att du har gjort ändringarna är kvar. Ändringarna har sparats till Cosmos-DB som du skapade med Open Service Broker för Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Kör din app på AKS-kluster

Du kan använda [Azure Dev blanksteg](../dev-spaces/azure-dev-spaces.md) att distribuera programmet till AKS-kluster. Azure Dev blanksteg kan du generera artefakter, till exempel Dockefiles och Helm-diagram och distribuera och köra ett program i AKS.

Aktivera Azure Dev blanksteg i AKS-klustret:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Använd lagringsutrymmen för utveckling av Azure-verktyg för att förbereda programmet att köras i AKS:

```cmd
azds prep --public
```

Det här kommandot genererar flera artefakter, inklusive en *diagram /* -mappen, som är din Helm-diagrammet i roten av projektet. Det här kommandot kan inte generera en *Dockerfile* för den här specifika projekt så att du behöver att skapa den.

Skapa en fil i roten av projektet med namnet *Dockerfile* med det här innehållet:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Dessutom kan du behöva uppdatera den *configurations.develop.build* -egenskapen i *azds.yaml* till *FALSKT*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Du måste också uppdatera den *containerPort* attributet *8080* i *charts/spring-music/templates/deployment.yaml*:

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

För att distribuera programmet till AKS:

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

Gå till den URL som visas i loggarna. I föregående exempel skulle du använda *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*. 

Kontrollera att du ser programmet tillsammans med dina ändringar.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du uppdaterar ett befintligt program från att använda MongoDB till Cosmos DB API för MongoDB. Den här artikeln beskrivs också hur du etablerar en Cosmos DB-tjänsten med Open Service Broker för Azure och distribuera programmet till AKS med Azure Dev blanksteg.

Mer information om Cosmos DB Se Open Service Broker for Azure och Azure Dev blanksteg:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Öppna Service Broker for Azure](https://osba.sh)
* [Utveckla med utveckling blanksteg](../dev-spaces/azure-dev-spaces.md)
