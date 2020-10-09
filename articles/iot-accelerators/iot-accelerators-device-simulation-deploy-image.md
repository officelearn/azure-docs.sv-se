---
title: Distribuera en anpassad enhets simulerings avbildning – Azure | Microsoft Docs
description: I den här instruktions guiden får du lära dig hur du distribuerar en anpassad Docker-avbildning av enhets simulerings lösningen till Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "61448412"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Distribuera en egen Docker-avbildning av enhets simulering

Du kan ändra enhets simulerings lösningen om du vill lägga till anpassade funktioner. I artikeln om att [serialisera telemetri med hjälp av protokoll](iot-accelerators-device-simulation-protobuf.md) , visas till exempel hur du lägger till en anpassad enhet till den lösning som använder Protocol Buffers (protobuf) för att skicka telemetri. När du har testat dina ändringar lokalt är nästa steg att distribuera dina ändringar till din enhets simulerings instans i Azure. För att slutföra den här uppgiften måste du skapa och distribuera en Docker-avbildning som innehåller den ändrade tjänsten.

Stegen i den här instruktions guiden visar hur du:

1. Förbered en utvecklings miljö
1. Generera en ny Docker-avbildning
1. Konfigurera enhets simuleringen så att den använder den nya Docker-avbildningen
1. Kör en simulering med den nya avbildningen

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här instruktions guiden behöver du:

* En distribuerad [enhets simulerings](quickstart-device-simulation-deploy.md) instans.
* Docker. Hämta [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) för din plattform.
* Ett [Docker Hub-konto](https://hub.docker.com/) där du kan ladda upp Docker-avbildningar. I ditt Docker Hub-konto skapar du ett offentligt lagrings lager som heter **Device-simulering**.
* En modifierad och testad [lösning för enhets simulering](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) på den lokala datorn. Du kan till exempel ändra lösningen om du vill [serialisera telemetri med hjälp av protokoll buffertar](iot-accelerators-device-simulation-protobuf.md).
* Ett gränssnitt som kan köra SSH. Om du installerar git för Windows kan du använda **bash** -gränssnittet som ingår i installationen. Du kan också använda din [Azure Cloud Shell](https://shell.azure.com/).

I instruktionerna i den här artikeln förutsätter vi att du använder Windows. Om du använder ett annat operativ system kan du behöva justera några av fil Sök vägarna och kommandona för att passa din miljö.

## <a name="create-a-new-docker-image"></a>Skapa en ny Docker-avbildning

Om du vill distribuera dina egna ändringar i enhets simulerings tjänsten måste du redigera skripten för att bygga och distribuera i **scripts\docker** -mappen för att överföra behållarna till ditt Docker Hub-konto

### <a name="modify-the-docker-scripts"></a>Ändra Docker-skript

Ändra den Docker **build. cmd**-, **Publish. cmd**-och **Run. cmd** -skript i mappen **scripts\docker** med din Docker-Hubbs lagrings information. De här stegen förutsätter att du har skapat ett offentligt lager med namnet **enhets simulering**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Uppdatera filen **filen Docker. yml** på följande sätt:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Konfigurera lösningen så att den inkluderar alla nya filer

Om du har lagt till nya enhets modell filer måste du uttryckligen inkludera dem i lösningen. Lägg till en post i **tjänsterna/Services. CSPROJ** för varje ytterligare fil som ska tas med. Om du till exempel har slutfört [serialiserad telemetri med hjälp av Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) lägger du till följande poster:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generera nya Docker-avbildningar och push-överför till Docker Hub

Publicera den nya Docker-avbildningen till Docker Hub med ditt Docker Hub-konto:

1. Öppna en kommando tolk och navigera till din lokala kopia av enhets simuleringens lagrings plats.

1. Navigera till **Docker** -mappen:

    ```cmd
    cd scripts\docker
    ```

1. Kör följande kommando för att skapa Docker-avbildningen:

    ```cmd
    build.cmd
    ```

1. Kör följande kommando för att publicera Docker-avbildningen till Docker Hub-lagringsplatsen. Logga in på Docker med dina autentiseringsuppgifter för Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Uppdatera tjänsten

Utför följande steg för att uppdatera behållaren för enhets simulering för att använda din anpassade avbildning:

* Använd SSH för att ansluta till den virtuella dator som är värd för enhets simulerings instansen. Använd IP-adress och lösen ord som du har gjort en anteckning om i föregående avsnitt:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navigera till **/app** -katalogen:

    ```sh
    cd /app
    ```

* Redigera filen **filen Docker. yml** :

    ```sh
    sudo nano docker-compose.yml
    ```

    Ändra **avbildningen** så att den pekar på den anpassade **enhets simulerings** avbildningen som du laddade upp till Docker Hub-lagringsplatsen:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Spara ändringarna.

* Kör följande kommando för att starta om mikrotjänster:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Kör simuleringen

Nu kan du köra en simulering med din anpassade lösning för enhets simulering:

1. Starta webb gränssnittet för enhets simulering från [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Använd webb gränssnittet för att konfigurera och köra en simulering. Om du tidigare har slutfört [serialiseringen av telemetri med hjälp av protokoll buffertar](iot-accelerators-device-simulation-protobuf.md)kan du använda din anpassade enhets modell.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar en anpassad enhets simulerings avbildning kanske du vill lära dig hur du [använder en befintlig IoT-hubb med enhets simulerings lösnings Accelerator](iot-accelerators-device-simulation-choose-hub.md).