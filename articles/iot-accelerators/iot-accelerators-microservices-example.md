---
title: Ändra och distribuera om en mikrotjänst – Azure | Microsoft-dokument
description: Den här självstudien visar hur du ändrar och distribuerar om en mikrotjänst i fjärrövervakning
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: 1552c54afe2195d58a032e9cc7bfa5aa70c844b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447649"
---
# <a name="customize-and-redeploy-a-microservice"></a>Anpassa och distribuera om en mikrotjänst

Den här självstudien visar hur du redigerar en av [mikrotjänsterna](https://azure.com/microservices) i lösningen för fjärrövervakning, skapar en avbildning av din mikrotjänst, distribuerar avbildningen till docker-hubben och använder den sedan i lösningen för fjärrövervakning. För att introducera det här konceptet använder självstudien ett grundläggande scenario där du anropar ett api för mikrotjänst och ändrar statusmeddelandet från "Alive and Well" till "Nya redigeringar som görs här!"

Remote Monitoring-lösningen använder mikrotjänster som är byggda med dockeravbildningar som hämtas från en dockernav. 

I den här självstudiekursen får du lära du dig att:

>[!div class="checklist"]
> * Redigera och skapa en mikrotjänst i lösningen för fjärrövervakning
> * Skapa en dockeravbildning
> * Driva en dockeravbildning till dockerhubben
> * Dra den nya dockerbilden
> * Visualisera ändringarna 

## <a name="prerequisites"></a>Krav

För att följa den här självstudien behöver du:

>[!div class="checklist"]
> * [Distribuera lösningsacceleratorn för fjärrövervakning lokalt](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Ett Docker-konto](https://hub.docker.com/)
> * [Brevbärare](https://www.getpostman.com/) - Behövs för att visa API-svaret

## <a name="call-the-api-and-view-response-status"></a>Anropa API- och visa svarsstatus

I den här delen anropar du standard-IoT hub manager-api:et för i-hubbhanteraren. API:et returnerar ett statusmeddelande som du ändrar senare genom att anpassa mikrotjänsten.

1. Kontrollera att lösningen för fjärrövervakning körs lokalt på datorn.
2. Leta reda på var du hämtade Postman och öppna den.
3. I Postman anger du följande `http://localhost:8080/iothubmanager/v1/status`i GET: .
4. Visa returen och du bör se, "Status": "OK: Alive and Well".

    ![Levande och väl Postman Meddelande](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Ändra status och skapa avbildningen

Nu ändra statusmeddelandet för Iot Hub Manager mikrotjänst till "Nya redigeringar made here!" och sedan bygga om dockeravbildningen med den här nya statusen. Om du stöter på problem här läser du avsnittet [Felsökning.](#Troubleshoot)

1. Kontrollera att terminalen är öppen och ändras till katalogen där du har klonat lösningen för fjärrövervakning. 
1. Ändra din katalog till "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/Services".
1. Öppna StatusService.cs i alla textredigerare eller IDE som du gillar. 
1. Leta upp följande kod:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    och ändra den till koden nedan och spara den.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Gå tillbaka till din terminal men nu ändra till följande katalog: "azure-iot-pcs-remote-monitoring-dotnet/services/iothub-manager/scripts/docker".
6. Om du vill skapa den nya dockerbilden skriver du

    ```sh
    sh build
    ```
    
    eller i Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Om du vill kontrollera att den nya avbildningen har skapats skriver du

    ```cmd/sh
    docker images 
    ```

Databasen ska vara "azureiotpcs/iothub-manager-dotnet".

![Lyckad dockeravbildning](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Tagga och överföra avbildningen
Innan du kan skicka den nya dockeravbildningen till en dockernav förväntar sig Docker att dina avbildningar ska taggas. Om du stöter på problem här läser du avsnittet [Felsökning.](#Troubleshoot)

1. Leta reda på bild-ID för docker-avbildningen som du skapade genom att skriva:

    ```cmd/sh
    docker images
    ```

2. Så här taggar du bilden med typen "testning"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Om du vill skicka den nyligen taggade bilden till dockerhubben skriver du

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Öppna din webbläsare och gå till [din docker hub](https://hub.docker.com/) och logga in.
5. Du bör nu se din nyligen skjutna docker-avbildning på dockerhubben.
![Docker-bild i dockernav](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Uppdatera lösningen för fjärrövervakning
Nu måste du uppdatera din lokala docker-compose.yml för att hämta den nya dockeravbildningen från dockerhubben. Om du stöter på problem här läser du avsnittet [Felsökning.](#Troubleshoot)

1. Gå tillbaka till terminalen och ändra till följande katalog: "azure-iot-pcs-remote-monitoring-dotnet/services/scripts/local".
2. Öppna docker-compose.yml i någon textredigerare eller IDE som du gillar.
3. Leta upp följande kod:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    och ändra den för att se ut som bilden nedan och spara den.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Visa den nya svarsstatusen
Slutför genom att distribuera om en lokal instans av lösningen för fjärrövervakning och visa det nya statussvaret i Postman.

1. Gå tillbaka till din terminal och ändra till följande katalog: "azure-iot-pcs-remote-monitoring-dotnet/scripts/local".
2. Starta den lokala instansen av fjärrövervakningslösningen genom att skriva följande kommando i terminalen:

    ```cmd/sh
    docker-compose up
    ```

3. Leta reda på var du hämtade Postman och öppna den.
4. I Postman anger du följande begäran `http://localhost:8080/iothubmanager/v1/status`i GET: . Du bör nu se, "Status": "OK: Nya redigeringar som gjorts här!".

![Nya redigeringar Made Here brevbärare meddelande](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Felsöka

Om du stöter på problem kan du prova att ta bort dockeravbildningar och behållare på den lokala datorn.

1. Om du vill ta bort alla behållare måste du först stoppa alla behållare som körs. Öppna din terminal och skriv

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Om du vill ta bort alla bilder öppnar du terminalen och skriver 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Du kan kontrollera om det finns några behållare på maskinen genom att skriva

    ```cmd/sh
    docker ps -aq 
    ```

    Om du har tagit bort alla behållare ska ingenting dyka upp.

4. Du kan kontrollera om det finns några bilder på maskinen genom att skriva

    ```cmd/sh
    docker images
    ```

    Om du har tagit bort alla behållare ska ingenting dyka upp.

## <a name="next-steps"></a>Nästa steg

I den här guiden såg du hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Redigera och skapa en mikrotjänst i lösningen för fjärrövervakning
> * Skapa en dockeravbildning
> * Driva en dockeravbildning till dockerhubben
> * Dra den nya dockerbilden
> * Visualisera ändringarna 

Nästa sak att prova är att [anpassa enheten simulator mikrotjänst i remote monitoring-lösningen](iot-accelerators-microservices-example.md)

Mer information om lösningen för fjärrövervakning finns i:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

