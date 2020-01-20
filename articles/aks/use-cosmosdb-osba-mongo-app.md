---
title: Integrera befintligt MongoDB-program med Azure Cosmos DB API för MongoDB och öppna Service Broker för Azure (OSBA)
description: I den här artikeln får du lära dig hur du integrerar ett befintligt Java-och MongoDB-program med Azure Cosmos DB API för MongoDB med hjälp av Open Service Broker for Azure (OSBA).
author: zr-msft
ms.service: azure-dev-spaces
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, öppna Service Broker, öppna Service Broker för Azure
ms.openlocfilehash: 3d0ab0b27d77e45d779227d30c5a8e4f824ba62a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277692"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrera befintligt MongoDB-program med Azure Cosmos DB API för MongoDB och öppna Service Broker för Azure (OSBA)

Azure Cosmos DB är en globalt distribuerad databas för flera datamodeller. Den ger också till gång till NoSQL-API: er med flera-API: er, inklusive för MongoDB Med Cosmos DB API för MongoDB kan du använda Cosmos DB med ditt befintliga MongoDB-program utan att behöva ändra programmets databas driv rutiner eller implementering. Du kan också etablera en Cosmos DB-tjänst med Open Service Broker för Azure.

I den här artikeln tar du ett befintligt Java-program som använder en MongoDB-databas och uppdaterar den för att använda en Cosmos DB databas med öppna Service Broker för Azure.

## <a name="prerequisites"></a>Krav

Innan du kan fortsätta måste du:
    
* Ett [Azure Kubernetes service-kluster](kubernetes-walkthrough.md) har skapats.
* [Öppna Service Broker för Azure installerat och konfigurerat på ditt AKS-kluster](integrate-azure.md). 
* Ha [service Catalog CLI](https://svc-cat.io/docs/install/) installerat och konfigurerat för att köra `svcat`-kommandon.
* Ha en befintlig [MongoDB](https://www.mongodb.com/) -databas. Du kan till exempel ha MongoDB som körs på [utvecklings datorn](https://docs.mongodb.com/manual/administration/install-community/) eller i en [virtuell Azure-dator](../virtual-machines/linux/install-mongodb.md).
* Ha ett sätt att ansluta till och fråga MongoDB-databasen, t. ex. [Mongo-gränssnittet](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Hämta programkod
    
I den här artikeln använder du [exempel programmet våren Music från Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) för att demonstrera ett program som använder en MongoDB-databas.
    
Klona programmet från GitHub och gå till programmets katalog:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Förbereda programmet för att använda din MongoDB-databas

Exempel programmet våren Music innehåller många alternativ för data källor. I den här artikeln konfigurerar du den så att den använder en befintlig MongoDB-databas. 

Lägg till YAML enligt slutet av *src/main/Resources/Application. yml*. Detta tillägg skapar en profil med namnet *MongoDB* och konfigurerar en URI och ett databas namn. Ersätt URI: n med anslutnings informationen till din befintliga MongoDB-databas. Att lägga till URI: n, som innehåller ett användar namn och lösen ord, är direkt till den här filen **endast för utvecklings användning** och **ska aldrig läggas till i versions kontrollen**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



När du startar programmet och instruerar det att använda *MongoDB* -profilen ansluter den till MongoDB-databasen och använder den för att lagra programmets data.

Så här skapar du ditt program:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Starta programmet och instruera det att använda *MongoDB* -profilen:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navigera till `http://localhost:8080` i webbläsaren.

![Spring-musikappen med standarddata](media/music-app.png)

Observera att programmet har fyllts med vissa [standard data](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interagera med den genom att ta bort några befintliga album och skapa några nya.

Du kan kontrol lera att ditt program använder din MongoDB-databas genom att ansluta till den och skicka en fråga till *musicdb* -databasen:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

I föregående exempel används [Mongo-gränssnittet](https://docs.mongodb.com/manual/mongo/) för att ansluta till MongoDB-databasen och fråga den. Du kan också kontrol lera att dina ändringar är sparade genom att stoppa programmet, starta om det och navigera tillbaka till det i webbläsaren. Observera att de ändringar du har gjort fortfarande finns där.


## <a name="create-a-cosmos-db-database"></a>Skapa en Cosmos DB-databas

Om du vill skapa en Cosmos DB databas i Azure med öppna Service Broker använder du kommandot `svcat provision`:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

Föregående kommando etablerar en Cosmos DB databas i Azure i resurs gruppen *MyResourceGroup* i regionen *östra* . Mer information om *resourceGroup*, *plats*och andra Azure-/regionsspecifika JSON-parametrar finns i [referens dokumentationen för Cosmos DB-modulen](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Kontrollera att databasen har slutfört etableringen med hjälp av kommandot `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

Databasen är klar när du ser *redo* under *status*.

När din databas har slutfört etableringen måste du binda dess metadata till en [Kubernetes-hemlighet](https://kubernetes.io/docs/concepts/configuration/secret/). Andra program kan sedan komma åt dessa data när de har bundits till en hemlighet. Om du vill binda metadata för databasen till en hemlighet använder du kommandot `svcat bind`:

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


## <a name="use-the-cosmos-db-database-with-your-application"></a>Använd Cosmos DB-databasen med ditt program

Om du vill använda Cosmos DB databasen med ditt program måste du känna till URI: n för att ansluta till den. Använd kommandot `kubectl get secret` för att hämta den här informationen:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

Föregående kommando hämtar *musicdb* -hemligheten och visar bara URI: n. Hemligheter lagras i base64-format så att föregående kommando också avkodar den.

Med hjälp av URI: n för Cosmos DB databasen uppdaterar du *src/main/Resources/Application. yml* för att använda den:

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

Uppdatering av URI: n, som innehåller ett användar namn och lösen ord, är direkt till den här filen **endast för utvecklings användning** och **ska aldrig läggas till i versions kontrollen**.

Återskapa och starta ditt program för att börja använda Cosmos DB-databasen:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Observera att programmet fortfarande använder *MongoDB* -profilen och en URI som börjar med *MongoDB://* för att ansluta till den Cosmos DB databasen. [Azure Cosmos DB API för MongoDB](../cosmos-db/mongodb-introduction.md) har den här kompatibiliteten. Det gör att ditt program kan fortsätta att köras som om det använder en MongoDB-databas, men det använder faktiskt Cosmos DB.

Navigera till `http://localhost:8080` i webbläsaren. Observera att standard data har återställts. Interagera med den genom att ta bort några befintliga album och skapa några nya. Du kan kontrol lera att dina ändringar är sparade genom att stoppa programmet, starta om det och navigera tillbaka till det i webbläsaren. Observera att de ändringar du har gjort fortfarande finns där. Ändringarna sparas i Cosmos DB som du skapade med öppna Service Broker för Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Kör ditt program på ditt AKS-kluster

Du kan använda [Azure dev Spaces](../dev-spaces/azure-dev-spaces.md) för att distribuera programmet till ditt AKS-kluster. Med Azure dev Spaces kan du generera artefakter som Dockerfiles-och Helm-diagram och distribuera och köra ett program i AKS.

Så här aktiverar du Azure dev Spaces i ditt AKS-kluster:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Använd verktyget Azure dev Spaces för att förbereda programmet för körning i AKS:

```cmd
azds prep --public
```

Det här kommandot genererar flera artefakter, inklusive en *diagram/* mapp, som är ditt Helm-diagram i projektets rot. Det här kommandot kan inte skapa en *Dockerfile* för det här projektet så du måste skapa det.

Skapa en fil i roten för projektet med namnet *Dockerfile* med det här innehållet:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Dessutom måste du uppdatera *konfigurationerna. utvecklar. Build* -egenskapen i *azds. yaml* till *false*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Du måste också uppdatera attributet *containerPort* till *8080* i *Charts/Spring-Music/templates/Deployment. yaml*:

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

Så här distribuerar du programmet till AKS:

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

Navigera till den URL som visas i loggarna. I föregående exempel skulle du använda *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/* . 

Kontrol lera att du ser programmet tillsammans med dina ändringar.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du uppdaterar ett befintligt program från att använda MongoDB för att använda Cosmos DB API för MongoDB. Den här artikeln innehåller också information om hur du etablerar en Cosmos DB tjänst med öppna Service Broker för Azure och hur du distribuerar programmet till AKS med Azure dev Spaces.

För ytterligare information om Cosmos DB, öppna Service Broker för Azure och Azure dev Spaces, se:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Öppna Service Broker för Azure](https://osba.sh)
* [Utveckla med dev Spaces](../dev-spaces/azure-dev-spaces.md)
