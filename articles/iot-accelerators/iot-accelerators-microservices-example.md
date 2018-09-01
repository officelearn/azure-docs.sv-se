---
title: Ändra och distribuera om en mikrotjänst | Microsoft Docs
description: Den här självstudiekursen visar hur du ändrar och distribuera om en mikrotjänst i fjärrövervakning
author: giyeh
manager: hegate
ms.author: giyeh
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.openlocfilehash: e15e17a499ad33a270b220fa7483d96c2945f6bb
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338085"
---
# <a name="customize-and-redeploy-a-microservice"></a>Anpassa och distribuera om en mikrotjänst

Den här självstudien visar hur du redigerar en av mikrotjänster i lösningen för fjärrövervakning, skapa en avbildning av din mikrotjänst, distribuera avbildningen till docker hub och sedan använda lösningen för fjärrövervakning. För att införa detta begrepp används i självstudiekursen ett enkelt scenario där du kan anropa en mikrotjänst API och ändrar statusmeddelandet från ”Alive och bra” till ”New redigerar Made här”!

Remote Monitoring-lösningen använder mikrotjänster som skapas med hjälp av docker-avbildningar som hämtas från en docker-hubb. 

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Redigera och skapa en mikrotjänst i lösningen för fjärrövervakning
> * Skapa en docker-avbildning
> * Överför en docker-avbildningen till docker hub
> * Hämta den nya docker-avbildningen
> * Visualisera ändringarna 

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här självstudien behöver du:

>[!div class="checklist"]
> * [Distribuera lösningsacceleratorn för fjärrövervakning lokalt](iot-accelerators-remote-monitoring-deploy-local.md)
> * [En Docker-konto](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) – krävs för att visa API-svar

## <a name="call-the-api-and-view-response-status"></a>Anropa API: et och visa svarsstatusen

I den här delen kan du anropa standard IoT hub manager mikrotjänst API. API: et returnerar ett statusmeddelande som du senare ändras genom att anpassa mikrotjänst.

1. Kontrollera att lösningen för fjärrövervakning körs lokalt på din dator.
2. Leta upp som du laddade ned Postman och öppna den.
3. I Postman, anger du följande i GET: http://localhost:8080/iothubmanager/v1/status.
4. Visa avkastningen och du bör se, ”Status” ”: OK: Alive och väl”.
![Alive och väl Postman meddelande](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Ändra status och skapa avbildningen

Ändra nu statusmeddelande för Iot Hub-hanterare mikrotjänst till ”nya ändringar görs här”! och sedan återskapa docker-avbildning med den nya statusen. Om du stöter på problem här, se vår [felsökning](#Troubleshoot) avsnittet.

1. Kontrollera att din terminal är öppen och ändra till den katalog där du har klonade av lösningen för fjärrövervakning. 
2. Ändra katalogen till ”.. azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/WebService/v1/Controllers ”.
3. Öppna StatusController.cs i valfri textredigerare eller IDE som helst. 
4. Leta upp följande kod:

    ```javascript
    return new StatusApiModel(true, "Alive and well");
    ```

    och ändra den till koden nedan och spara den.

    ```javascript
    return new StatusApiModel(true, "New Edits Made Here!");
    ```

5. Gå tillbaka till terminalen men nu ändras till följande katalog ”:... azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/scripts/docker”.
6. Om du vill skapa en ny docker-avbildning, skriver du:

    ```cmd/sh
    sh build
    ```

7. Så här kontrollerar du din nya avbildningen har skapats

    ```cmd/sh
    docker images 
    ```

Databasen ska vara ”azureiotpcs/iothub-manager-dotnet”.

![Lyckad docker-avbildning](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Tagga och överföra avbildningen
Innan du kan skicka en ny docker-avbildning till en docker-hubb, förväntas bilderna taggas med Docker. Om du stöter på problem här, se vår [felsökning](#Troubleshoot) avsnittet.

1. Leta upp avbildnings-ID för docker-avbildning som du skapade genom att skriva:

    ```cmd/sh
    docker images
    ```

2. Tagga avbildningen med typen ”testning”

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Om du vill skicka din nyligen taggade avbildning på docker-hubben, skriver du:

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Öppna webbläsaren och gå till din [docker hub](https://hub.docker.com/) och logga in.
5. Du bör nu se nyligen publicerade docker-avbildning på docker hub.
![Docker-avbildning i docker hub](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Uppdatera lösningen för fjärrövervakning
Nu måste du uppdatera din lokala docker-compose.yml om du vill hämta en ny docker-avbildning från docker hub. Om du stöter på problem här, se vår [felsökning](#Troubleshoot) avsnittet.

1. Gå tillbaka till terminalen och ändra till följande katalog ”:... Azure-IOT-PCs-Remote-Monitoring-DotNet/Scripts/Local ”.
2. Öppna docker-compose.yml i valfri textredigerare eller IDE som helst.
3. Leta upp följande kod:

    ```docker
    image: azureiotpcs/pcs-auth-dotnet:testing
    ```

    och ändra den så att den ser ut som på bilden nedan och spara den.

    ```cmd/sh
    image: [docker ID]/pcs-auth-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Visa den nya svarsstatusen
Slutför genom att omdistribuera en lokal instans av lösningen för fjärrövervakning och visa nya statussvar i Postman.

1. Gå tillbaka till terminalen och ändra till följande katalog ”:... Azure-IOT-PCs-Remote-Monitoring-DotNet/Scripts/Local ”.
2. Starta din lokala instans av lösningen för fjärrövervakning genom att skriva följande kommando i terminalen:

    ```cmd/sh
    docker-compose up
    ```

3. Leta upp som du laddade ned Postman och öppna den.
4. Ange följande GET-begäran i Postman: http://localhost:8080/iothubmanager/v1/status. Du bör nu se, ”Status” ”: OK: nya ändringar som görs här”!.

![Nytt redigerar gjorts här postman meddelande](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>Felsöka

Om du kör i problem, tar du bort docker-avbildningar och behållare på den lokala datorn.

1. Om du vill ta bort alla behållare, behöver du först stoppa alla behållare som körs. Öppna terminalen och typ

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Öppna terminalen och typ för att ta bort alla avbildningar 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Du kan kontrollera om det finns inga behållare på datorn genom att skriva

    ```cmd/sh
    docker ps -aq 
    ```

    Om du har tagit bort alla behållare bör ingenting visas.

4. Du kan kontrollera om det finns bilder på datorn genom att skriva

    ```cmd/sh
    docker images
    ```

    Om du har tagit bort alla behållare bör ingenting visas.

## <a name="next-steps"></a>Nästa steg

I den här självstudien fick du se hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Redigera och skapa en mikrotjänst i lösningen för fjärrövervakning
> * Skapa en docker-avbildning
> * Överför en docker-avbildningen till docker hub
> * Hämta den nya docker-avbildningen
> * Visualisera ändringarna 

Nästa sak att försöka är [anpassa enheten simulatorn mikrotjänst i lösningen för fjärrövervakning](iot-accelerators-microservices-example.md)

Mer information för utvecklare om lösningen för fjärrövervakning, finns:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

