---
title: Distribuera en anpassad enhetssimuleringsavbildning – Azure| Microsoft-dokument
description: I den här programguiden får du lära dig hur du distribuerar en anpassad Docker-avbildning av enhetssimuleringslösningen till Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61448412"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Distribuera en anpassad dockeravbildning för enhetssimulering

Du kan ändra enhetssimuleringslösningen för att lägga till anpassade funktioner. I artikeln [Serialize telemetry med hjälp av protokollbuffertar](iot-accelerators-device-simulation-protobuf.md) visas till exempel hur du lägger till en anpassad enhet i lösningen som använder Protobuf (Protocol Buffers) för att skicka telemetri. När du har testat dina ändringar lokalt är nästa steg att distribuera dina ändringar till din Enhetssimuleringsinstans i Azure. För att slutföra den här uppgiften måste du skapa och distribuera en Docker-avbildning som innehåller den ändrade tjänsten.

Stegen i den här guiden visar hur du:

1. Förbereda en utvecklingsmiljö
1. Generera en ny Docker-avbildning
1. Konfigurera enhetssimulering så att den använder den nya Docker-avbildningen
1. Köra en simulering med den nya bilden

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här guiden behöver du:

* En distribuerad [enhetssimuleringsinstans.](quickstart-device-simulation-deploy.md)
* Docker. Ladda ned [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) för din plattform.
* Ett [Docker Hub-konto](https://hub.docker.com/) där du kan ladda upp Docker-avbildningar. Skapa en offentlig databas som kallas **enhetssimulering**i Docker Hub-kontot .
* En modifierad och testad [enhetssimuleringslösning](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) på din lokala dator. Du kan till exempel ändra lösningen för att [serialisera telemetri med hjälp av protokollbuffertar](iot-accelerators-device-simulation-protobuf.md).
* Ett skal som kan köra SSH. Om du installerar Git för Windows **bash** kan du använda bash-skalet som ingår i installationen. Du kan också använda ditt [Azure Cloud Shell](https://shell.azure.com/).

Instruktionerna i den här artikeln förutsätter att du använder Windows. Om du använder ett annat operativsystem kan du behöva justera några av filsökvägarna och kommandona så att de passar din miljö.

## <a name="create-a-new-docker-image"></a>Skapa en ny Docker-avbildning

Om du vill distribuera dina egna ändringar i tjänsten Enhetssimulering måste du redigera bygg- och distributionsskripten i **mappen scripts\docker** för att ladda upp behållarna till ditt docker-hub-konto

### <a name="modify-the-docker-scripts"></a>Ändra dockerskripten

Ändra Docker **build.cmd,** **publish.cmd**och **run.cmd-skript** i **mappen scripts\docker** med databasinformationen för Docker Hub. De här stegen förutsätter att du har skapat en offentlig databas som kallas **enhetssimulering:**

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Uppdatera **filen docker-compose.yml** enligt följande:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Konfigurera lösningen så att den innehåller nya filer

Om du har lagt till nya enhetsmodellfiler måste du uttryckligen inkludera dem i lösningen. Lägg till en post i **services/services.csproj** för varje ytterligare fil som ska inkluderas. Om du till exempel har slutfört [telemetrin Serialize med hjälp av instruktioner för protokollbuffertar](iot-accelerators-device-simulation-protobuf.md) lägger du till följande poster:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generera nya Docker-avbildningar och gå till Docker Hub

Publicera den nya Docker-avbildningen i Docker Hub med ditt docker-hub-konto:

1. Öppna en kommandotolk och navigera till den lokala kopian av enhetssimuleringsdatabasen.

1. Navigera till **dockermappen:**

    ```cmd
    cd scripts\docker
    ```

1. Kör följande kommando för att skapa Docker-avbildningen:

    ```cmd
    build.cmd
    ```

1. Kör följande kommando för att publicera Docker-avbildningen i Docker Hub-databasen. Logga in på Docker med autentiseringsuppgifterna för Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Uppdatera tjänsten

Så här uppdaterar du behållaren för enhetssimulering för att använda din anpassade avbildning:

* Använd SSH för att ansluta till den virtuella datorn som är värd för din enhetssimuleringsinstans. Använd IP-adressen och lösenordet som du har noterat i föregående avsnitt:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Navigera till **katalogen /app:**

    ```sh
    cd /app
    ```

* Redigera **filen docker-compose.yml:**

    ```sh
    sudo nano docker-compose.yml
    ```

    Ändra **bilden** så att den anpassade **enhetssimuleringsbilden** som du har laddat upp till Docker Hub-databasen:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Spara ändringarna.

* Kör följande kommando för att starta om mikrotjänsterna:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Kör din simulering

Du kan nu köra en simulering med din anpassade enhetssimuleringslösning:

1. Starta webbgränssnittet för enhetssimulering från [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Använd webbgränssnittet för att konfigurera och köra en simulering. Om du tidigare har slutfört [serialisera telemetri med hjälp av protokollbuffertar](iot-accelerators-device-simulation-protobuf.md)kan du använda den anpassade enhetsmodellen.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du distribuerar en anpassad enhetssimuleringsavbildning, du kanske vill lära dig hur [du använder en befintlig IoT-hubb med device simulation-lösningsacceleratorn](iot-accelerators-device-simulation-choose-hub.md).