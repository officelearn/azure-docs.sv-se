---
title: Distribuera en anpassad avbildning i Enhetssimulering – Azure | Microsoft Docs
description: I den här guiden att lära dig hur du distribuerar en anpassad dockeravbildning av Enhetssimulering-lösning till Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51284952"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Distribuera en anpassad docker-avbildning för Enhetssimulering

Du kan ändra Enhetssimulering lösningen för att lägga till anpassade funktioner. Till exempel den [serialisera telemetri som använder Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) artikeln visar hur du lägger till en anpassad enhet i lösningen som använder Protocol Buffers (Protobuf) för att skicka telemetri. När du har testat ändringarna lokalt, är nästa steg att distribuera dina ändringar till din Enhetssimulering-instans i Azure. För att slutföra den här uppgiften, måste du skapa och distribuera en Docker-avbildning som innehåller den ändrade tjänsten.

Stegen i den här How-to-till-guiden visar dig hur du:

1. Förbered en utvecklingsmiljö
1. Generera en ny dockeravbildning
1. Konfigurera Enhetssimulering om du vill använda en ny Docker-avbildning
1. Kör en simulering med hjälp av den nya avbildningen

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att slutföra stegen i den här guiden:

* En distribuerad [Enhetssimulering](quickstart-device-simulation-deploy.md) instans.
* Docker. Ladda ned den [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) för din plattform.
* En [Docker Hub-konto](https://hub.docker.com/) där du kan ladda upp din Docker-avbildningar. I ditt Docker Hub-konto, skapar du en offentlig databas som heter **enhetssimulering**.
* A ändras och testas [Enhetssimulering lösning](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) på den lokala datorn. Du kan till exempel ändra lösningen [serialisera telemetri som använder Protocol Buffers](iot-accelerators-device-simulation-protobuf.md).
* Ett gränssnitt som kan köra SSH. Om du har installerat Git för Windows kan du använda den **bash** gränssnitt som ingår i e-installationen. Du kan också använda din [Azure Cloud Shell](https://shell.azure.com/).

Anvisningarna i den här artikeln förutsätter att du använder Windows. Om du använder ett annat operativsystem kan du behöva justera några av de sökvägar och kommandon för att de passar din miljö.

## <a name="create-a-new-docker-image"></a>Skapa en ny dockeravbildning

Om du vill distribuera dina egna ändringar till tjänsten Enhetssimulering, måste du redigera bygg- och skripten i **scripts\docker** mapp att ladda upp behållarna till docker hub-konto

### <a name="modify-the-docker-scripts"></a>Ändra docker-skript

Ändra Docker **build.cmd**, **publish.cmd**, och **run.cmd** skript i den **scripts\docker** mappen med ditt Docker Hub information om databasen. De här stegen förutsätter att du har skapat en offentlig databas som heter **enhetssimulering**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Uppdatera den **docker-compose.yml** så här:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Konfigurera lösningen om du vill inkludera alla nya filer

Om du har lagt till alla nya filer som enheten modellen måste du uttryckligen lägga till dem i lösningen. Lägg till en post som den **services/services.csproj** för varje fil som ska ingå. Exempel: Om du har slutfört den [serialisera telemetri som använder Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) how-to, Lägg till följande poster:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generera nya Docker-avbildningar och skicka till Docker Hub

Publicera den nya Docker-avbildningen till Docker Hub med ditt docker hub-konto:

1. Öppna en kommandotolk och navigera till den lokala kopian av databasen för device simulering.

1. Navigera till den **docker** mapp:

    ```cmd
    cd scripts\docker
    ```

1. Kör följande kommando för att skapa Docker-avbildningen:

    ```cmd
    build.cmd
    ```

1. Kör följande kommando för att publicera Docker-avbildningen till Docker Hub-lagringsplats. Logga in på Docker med Docker Hub-autentiseringsuppgifter:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Uppdatera tjänsten

Utför följande steg för att uppdatera Enhetssimulering-behållaren att använda den anpassade avbildningen:

* Använda SSH för att ansluta till den virtuella datorn som är värd för din Enhetssimulering-instans. Använd IP-adress och lösenord som du antecknade i föregående avsnitt:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navigera till den **App** directory:

    ```sh
    cd /app
    ```

* Redigera den **docker-compose.yml** fil:

    ```sh
    sudo nano docker-compose.yml
    ```

    Ändra den **bild** så att den pekar på anpassat **enhetssimulering** bilduppladdning till Docker Hub-lagringsplatsen:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Spara ändringarna.

* Kör följande kommando för att starta om mikrotjänster:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Kör dina simulering

Nu kan du köra en simulering med din anpassade Enhetssimulering-lösning:

1. Starta din Enhetssimulering webbgränssnitt från [Microsoft Azure IoT-Lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Du kan använda webbgränssnittet för att konfigurera och köra en simulering. Om du tidigare har slutfört [serialisera telemetri som använder Protocol Buffers](iot-accelerators-device-simulation-protobuf.md), du kan använda din anpassade enhetsmodell.

## <a name="next-steps"></a>Nästa steg

Nu du har lärt dig hur du distribuerar en anpassad avbildning för Enhetssimulering, kanske du vill lära dig hur du [använder en befintlig IoT-hubb med Enhetssimulering lösningsaccelerator](iot-accelerators-device-simulation-choose-hub.md).