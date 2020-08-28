---
title: Ändra och distribuera om en mikrotjänst – Azure | Microsoft Docs
description: Den här självstudien visar hur du ändrar och distribuerar om en mikrotjänst i fjärrövervakning
author: dominicbetts
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 04/19/2018
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ff3e12721628e244f247e174af101e71ea91191
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998330"
---
# <a name="customize-and-redeploy-a-microservice"></a>Anpassa och distribuera om en mikrotjänst

Den här självstudien visar hur du redigerar en av [mikrotjänster](https://azure.com/microservices) i lösningen för fjärrövervakning, skapar en avbildning av mikrotjänsten, distribuerar avbildningen till Docker-hubben och använder den i lösningen för fjärrövervakning. För att införa det här konceptet använder självstudien ett grundläggande scenario där du anropar ett mikrotjänst-API och ändrar status meddelandet från "Alive" till "nya ändringar som görs här!"

Lösningen för fjärrövervakning använder mikrotjänster som har skapats med Docker-avbildningar som hämtas från en Docker-hubb. 

I den här guiden får du lära dig att:

>[!div class="checklist"]
> * Redigera och bygg en mikrotjänst i lösningen för fjärrövervakning
> * Bygg en Docker-avbildning
> * Push-överför en Docker-avbildning till Docker-hubben
> * Hämta den nya Docker-avbildningen
> * Visualisera ändringarna 

## <a name="prerequisites"></a>Förutsättningar

För att följa den här självstudien behöver du:

>[!div class="checklist"]
> * [Distribuera lösnings acceleratorn för fjärrövervakning lokalt](iot-accelerators-remote-monitoring-deploy-local.md)
> * [Ett Docker-konto](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) – krävs för att Visa API-svaret

## <a name="call-the-api-and-view-response-status"></a>Anropa API: et och Visa svars status

I den här delen anropar du standard-API: t för IoT Hub Manager. API: et returnerar ett status meddelande som du senare ändrar genom att anpassa mikrotjänsten.

1. Kontrol lera att lösningen för fjärrövervakning körs lokalt på datorn.
2. Leta reda på var du laddade ned Postman och öppna den.
3. I Postman anger du följande i avsnittet GET: `http://localhost:8080/iothubmanager/v1/status` .
4. Visa returen och du bör se, "status": "OK: Alive".

    ![Meddelande om Alive och bra Postman](./media/iot-accelerators-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>Ändra status och bygga avbildningen

Ändra nu status meddelandet för tjänsten IoT Hub Manager till "nya ändringar som gjorts här!" och återskapa Docker-avbildningen med den nya statusen. Om du stöter på problem här läser du avsnittet om [fel sökning](#Troubleshoot) .

1. Se till att terminalen är öppen och ändras till den katalog där du har klonat lösningen för fjärrövervakning. 
1. Ändra katalogen till "Azure-IoT-PC-Remote-Monitoring-dotNet/Services/iothub-Manager/Services".
1. Öppna StatusService.cs i valfri text redigerare eller IDE som du vill. 
1. Leta upp följande kod:

    ```csharp
    var result = new StatusServiceModel(true, "Alive and well!");
    ```

    och ändra den till koden nedan och spara den.

    ```csharp
    var result = new StatusServiceModel(true, "New Edits Made Here!");
    ```

5. Gå tillbaka till terminalen, men ändra nu till följande katalog: "Azure-IoT-PC-Remote-Monitor-dotNet/Services/iothub-Manager/scripts/Docker".
6. Om du vill bygga den nya Docker-avbildningen skriver du

    ```sh
    sh build
    ```
    
    eller i Windows:
    
    ```cmd
    ./build.cmd
    ```

7. Om du vill kontrol lera att den nya avbildningen har skapats skriver du

    ```cmd/sh
    docker images 
    ```

Lagrings platsen ska vara "azureiotpcs/iothub-Manager-dotNet".

![Lyckad Docker-avbildning](./media/iot-accelerators-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>Tagga och överföra avbildningen
Innan du kan push-överföra den nya Docker-avbildningen till en Docker-hubb förväntar dock dina bilder som taggade. Om du stöter på problem här läser du avsnittet om [fel sökning](#Troubleshoot) .

1. Leta upp bild-ID: t för den Docker-avbildning som du skapade genom att skriva:

    ```cmd/sh
    docker images
    ```

2. Tagga din avbildning med "test typ"

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. Om du vill skicka den nyligen taggade avbildningen till Docker-hubben skriver du

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. Öppna din webbläsare och gå till din [Docker-hubb](https://hub.docker.com/) och logga in.
5. Nu bör du se den nyligen publicerade Docker-avbildningen på Docker-hubben.
![Docker-avbildning i Docker Hub](./media/iot-accelerators-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>Uppdatera din lösning för fjärr styrning
Nu måste du uppdatera din lokala filen Docker. yml för att hämta den nya Docker-avbildningen från Docker-hubben. Om du stöter på problem här läser du avsnittet om [fel sökning](#Troubleshoot) .

1. Gå tillbaka till terminalen och ändra till följande katalog: "Azure-IoT-PC-Remote-Monitoring-dotNet/Services/scripts/Local".
2. Öppna filen Docker. yml i valfri text redigerare eller IDE som du vill.
3. Leta upp följande kod:

    ```yml
    image: azureiotpcs/iothub-manager-dotnet:testing
    ```

    ändra den så att den ser ut som på bilden nedan och spara den.

    ```yml
    image: [docker ID]/iothub-manager-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>Visa den nya svars statusen
Slutför genom att distribuera om en lokal instans av lösningen för fjärrövervakning och visa det nya status svaret i Postman.

1. Gå tillbaka till terminalen och ändra till följande katalog: "Azure-IoT-PC-Remote-Monitoring-dotNet/scripts/Local".
2. Starta din lokala instans av lösningen för fjärrövervakning genom att skriva följande kommando i terminalen:

    ```cmd/sh
    docker-compose up
    ```

3. Leta reda på var du laddade ned Postman och öppna den.
4. I Postman anger du följande begäran i avsnittet GET: `http://localhost:8080/iothubmanager/v1/status` . Nu bör du se "status": "OK: nya ändringar har gjorts här!".

![Nya ändringar gjorda här Postman-meddelande](./media/iot-accelerators-microservices-example/new-postman-message.png)

## <a name="troubleshoot"></a><a name="Troubleshoot"></a>Felsöka

Om du stöter på problem kan du försöka med att ta bort Docker-avbildningarna och behållarna på den lokala datorn.

1. Om du vill ta bort alla behållare måste du först stoppa alla behållare som körs. Öppna terminalen och skriv

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. Om du vill ta bort alla avbildningar öppnar du terminalen och skriver 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. Du kan kontrol lera om det finns behållare på datorn genom att skriva

    ```cmd/sh
    docker ps -aq 
    ```

    Om du har tagit bort alla behållare bör inget visas.

4. Du kan kontrol lera om det finns några bilder på datorn genom att skriva

    ```cmd/sh
    docker images
    ```

    Om du har tagit bort alla behållare bör inget visas.

## <a name="next-steps"></a>Nästa steg

I den här självstudien fick du lära dig att:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Redigera och bygg en mikrotjänst i lösningen för fjärrövervakning
> * Bygg en Docker-avbildning
> * Push-överför en Docker-avbildning till Docker-hubben
> * Hämta den nya Docker-avbildningen
> * Visualisera ändringarna 

Nästa sak är att [Anpassa enhets Simulatorns mikrotjänst i lösningen för fjärrövervakning](iot-accelerators-microservices-example.md)

Mer information om utvecklings lösningen finns i:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

