---
title: Ändra och distribuera en mikrotjänster | Microsoft Docs
description: Den här kursen visar hur du ändrar och distribuera en mikrotjänster i Fjärrövervaknings
services: ''
suite: iot-suite
author: giyeh
manager: hegate
ms.author: giyeh
ms.service: iot-suite
ms.date: 04/19/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: b9be74b4ef5a1239f6ce753ebf41af6b5dbacb5e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="customize-and-redeploy-a-microservice"></a>Anpassa och distribuera en mikrotjänster

Den här kursen visar hur du redigera ett mikrotjänster i lösningen Fjärrövervaknings, skapa en avbildning av din mikrotjänster, distribuera avbildningen till docker-NAV och sedan använda den i Fjärrövervaknings lösning. För att införa detta begrepp används i självstudiekursen ett enkelt scenario där du anropa mikrotjänster API och ändra statusmeddelande från ”Alive och väl” till ”ny redigerar Made här”!

Fjärråtkomst övervakning lösningen använder mikrotjänster som har skapats med docker-avbildningar som hämtas från en docker-hubb. 

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Redigera och skapa en mikrotjänster i Fjärrövervaknings-lösning
> * Skapa en docker-bild
> * Push-en docker-avbildning till docker-hubb
> * Hämta den nya docker-avbildningen
> * Visualisera ändringarna 

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här kursen behöver du:

>[!div class="checklist"]
> * [Distribuera fjärråtkomst övervakning förkonfigurerade lösningen lokalt](../iot-accelerators/iot-accelerators-remote-monitoring-deploy-local.md)
> * [En Docker-konto](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) - behövs för att visa API-svar

## <a name="call-the-api-and-view-response-status"></a>Anropa svarsstatusen API och visa

I den här delen kan anropa du standard IoT-hubb manager mikrotjänster API. API: N returnerar ett statusmeddelande som du senare ändras genom att anpassa mikrotjänster.

1. Kontrollera att lösningen Fjärrövervaknings körs lokalt på datorn.
2. Leta upp dit du hämtade Postman och öppna den.
3. I Postman, anger du följande i GET: http://localhost:8080/iothubmanager/v1/status.
4. Visa tillbaka och du bör se, ”Status” ”: OK: Alive och väl”.
![Alive och korrekt Postman meddelande](media/iot-suite-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Ändra status och skapa avbildningen

Ändra statusmeddelande för Iot Hub Manager mikrotjänster till ”nya ändringar görs här”! och sedan återskapa docker-avbildningen med den nya statusen. Om du stöter på problem här, finns våra [felsökning](#Troubleshoot) avsnitt.

1. Kontrollera att terminalen är öppen och ändra till den katalog där du har klonas Fjärrövervaknings-lösning. 
2. Ändra katalogen till ”... azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/WebService/v1/Controllers ”.
3. Öppna StatusController.cs i valfri textredigerare eller IDE som du vill använda. 
4. Leta upp följande kod:

    ```javascript
    return new StatusApiModel(true, "Alive and well");
    ```

    Ändra till koden nedan och sparar den.

    ```javascript
    return new StatusApiModel(true, "New Edits Made Here!");
    ```

5. Gå tillbaka till terminalen men nu ändra till följande katalog ”:... azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/scripts/docker”.
6. Om du vill skapa den nya docker-bilden skriver du:

    ```cmd/sh
    sh build
    ```

7. Så här kontrollerar du den nya bilden har skapats

    ```cmd/sh
    docker images 
    ```

Databasen måste vara ”azureiotpcs/iothub-manager-dotnet”.

![Lyckad docker-bild](media/iot-suite-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Taggen och push avbildningen
Innan du kan pressa ny docker-avbildning till en docker-hubben, förväntas Docker bilderna taggas. Om du stöter på problem här, finns våra [felsökning](#Troubleshoot) avsnitt.

1. Leta upp avbildnings-ID för docker-avbildningen som du skapade genom att skriva:

    ```cmd/sh
    docker images
    ```

2. Att tagga avbildningen med typen ”testa”

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. För att vidarebefordra nyligen märkt avbildningen till hubben docker, skriver du:

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Öppna webbläsaren och gå till din [docker-hubb](https://hub.docker.com/) och logga in.
5. Du bör nu se nyligen intryckt docker-avbildningen på docker-hubben.
![Docker-avbildningen i docker-hubb](media/iot-suite-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Uppdatera din lösning för övervakning av fjärråtkomst
Du måste nu uppdatera din lokala docker-compose.yml om du vill dra ny docker-avbildning från docker-hubben. Om du stöter på problem här, finns våra [felsökning](#Troubleshoot) avsnitt.

1. Gå tillbaka till terminalen och ändra till följande katalog ”:... Azure-IOT-PCs-Remote-Monitoring-DotNet/Scripts/Local ”.
2. Öppna docker-compose.yml i valfri textredigerare eller IDE som du vill använda.
3. Leta upp följande kod:

    ```docker
    image: azureiotpcs/pcs-auth-dotnet:testing
    ```

    och ändra den så att den ser ut som på bilden nedan och sparar den.

    ```cmd/sh
    image: [docker ID]/pcs-auth-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Visa den nya svarsstatusen
Avsluta med att omdistribuera en lokal instans av Fjärrövervaknings-lösningen och visa nya Statussvaret i Postman.

1. Gå tillbaka till terminalen och ändra till följande katalog ”:... Azure-IOT-PCs-Remote-Monitoring-DotNet/Scripts/Local ”.
2. Starta din lokal instans av Fjärrövervaknings lösningen genom att skriva följande kommando i terminalen:

    ```cmd/sh
    docker-compose up
    ```

3. Leta upp dit du hämtade Postman och öppna den.
4. I Postman, anger du följande GET-begäran: http://localhost:8080/iothubmanager/v1/status. Du bör nu se, ”Status” ”: OK: nya ändringar görs här”!.

![Nytt redigerar gjorts här postman meddelande](media/iot-suite-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Felsöka

Om du kör till problem, tar du bort docker-bilder och behållare på den lokala datorn.

1. Om du vill ta bort alla behållare måste du först du stoppa alla behållare som körs. Öppna terminal och typ

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Öppna terminal och typ för att ta bort alla bilder 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Du kan kontrollera om det finns inga behållare på datorn genom att skriva

    ```cmd/sh
    docker ps -aq 
    ```

    Om du har tagit bort alla behållare ska ingenting visas.

4. Du kan kontrollera om det finns några bilder på datorn genom att skriva

    ```cmd/sh
    docker images
    ```

    Om du har tagit bort alla behållare ska ingenting visas.

## <a name="next-steps"></a>Nästa steg

I kursen får du sett hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Redigera och skapa en mikrotjänster i Fjärrövervaknings-lösning
> * Skapa en docker-bild
> * Push-en docker-avbildning till docker-hubb
> * Hämta den nya docker-avbildningen
> * Visualisera ändringarna 

Det nästa försök är [anpassa enheten simulatorn mikrotjänster i Fjärrövervaknings-lösning](iot-suite-remote-monitoring-test.md)

Utvecklare om lösningen Fjärrövervaknings, Läs mer:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

