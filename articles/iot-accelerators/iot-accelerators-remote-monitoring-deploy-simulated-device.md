---
title: IoT distribuera anpassade simulerade enheter – Azure | Microsoft Docs
description: Den här instruktions guiden visar hur du distribuerar anpassade simulerade enheter till en lösnings Accelerator för fjärr styrning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "66427562"
---
# <a name="deploy-a-new-simulated-device"></a>Distribuera en ny simulerad enhet

Med hjälp av styrenhets acceleratorer för fjärrövervakning och enhets simulering kan du definiera dina egna simulerade enheter. I den här artikeln lär du dig hur du distribuerar en anpassad typ av kyl enhet och en ny glöd lampan-enhets typ till en lösnings Accelerator för fjärr styrning.

I den här artikeln förutsätter vi att du har slutfört guiden [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md) med instruktioner och har de filer som definierar de anpassade kyl-och glöd lampan enhets typerna.

Stegen i den här instruktions guiden visar hur du:

1. Använd SSH för att få åtkomst till fil systemet på den virtuella datorn som är värd för lösningen för fjärr styrnings lösning.

1. Konfigurera Docker för att läsa in enhets modeller från en plats utanför Docker-behållaren.

1. Kör lösnings acceleratorn för fjärrövervakning med anpassade enhets modell filer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

För att slutföra stegen i den här instruktions guiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här instruktions guiden behöver du:

- En distribuerad instans av [lösnings acceleratorn för fjärrövervakning](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Ett lokalt **bash** -gränssnitt för att köra- `ssh` och- `scp` kommandon. I Windows är ett enkelt sätt att installera **bash** är att installera [git](https://git-scm.com/download/win).
- Dina anpassade enhets modell filer, till exempel de som beskrivs i [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurera Docker

I det här avsnittet konfigurerar du Docker för att läsa in enhets modellens filer från mappen **/tmp/devicemodels** på den virtuella datorn i stället för inifrån Docker-behållaren. Kör kommandona i det här avsnittet i ett **bash** -gränssnitt på den lokala datorn:

I det här avsnittet konfigurerar du Docker för att läsa in enhets modellens filer från mappen **/tmp/devicemodels** på den virtuella datorn i stället för inifrån Docker-behållaren. Kör kommandona i det här avsnittet i ett **bash** -gränssnitt på den lokala datorn:

1. Använd SSH för att ansluta till den virtuella datorn i Azure från den lokala datorn. Följande kommando förutsätter att den offentliga IP-adressen för den virtuella datorn **VM-vikxv** är **104.41.128.108** --Ersätt detta värde med den offentliga IP-adressen för den virtuella datorn från föregående avsnitt:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Följ anvisningarna för att logga in på den virtuella datorn med lösen ordet som du angav i föregående avsnitt.

1. Konfigurera enhets simulerings tjänsten för att läsa in enhets modellerna utanför behållaren. Öppna Docker-konfigurations filen först:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Leta upp inställningarna för behållaren **devicesimulation** och redigera inställningen för **volymer** enligt följande kodfragment:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Spara ändringarna.

1. Kopiera de befintliga enhets modell filerna från behållaren till den nya platsen. Börja med att leta upp container-ID: t för enhets simulerings behållaren:

    ```sh
    sudo docker ps
    ```

    Kopiera sedan enhets modellens filer till mappen **tmp** på den virtuella datorn. Följande kommando förutsätter att behållar-ID är c378d6878407--Ersätt det här värdet med enhets simuleringens behållare-ID:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Behåll **bash** -fönstret med din SSH-session öppen.

1. Kopiera dina anpassade enhets modell filer till den virtuella datorn. Kör det här kommandot i ett annat **bash** -gränssnitt på den dator där du skapade dina anpassade enhets modeller. Börja med att navigera till den lokala mappen som innehåller dina JSON-filer för enhets modellen. Följande kommandon förutsätter att den virtuella datorns offentliga IP-adress är **104.41.128.108** -Ersätt det här värdet med den offentliga IP-adressen för den virtuella datorn. Ange lösen ordet för den virtuella datorn när du uppmanas:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Starta om enhets simuleringens Docker-behållare för att använda de nya enhets modellerna. Kör följande kommandon i **bash** -gränssnittet med den öppna SSH-sessionen till den virtuella datorn:

    ```sh
    sudo /app/start.sh
    ```

    Använd följande kommando om du vill se status för de körde Docker-behållare och deras behållar-ID:

    ```sh
    sudo docker ps
    ```

    Kör följande kommando om du vill se loggen från behållaren för enhets simulering. Ersätt container-ID: t med ID: t för enhets simulerings behållaren:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Kör simulering

Nu kan du använda dina anpassade enhets modeller i lösningen för fjärrövervakning:

1. Starta instrument panelen för fjärrövervakning från [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Använd sidan **enheter** för att lägga till simulerade enheter. När du lägger till en ny simulerad enhet är dina nya enhets modeller tillgängliga för att välja.

1. Du kan använda instrument panelen för att Visa telemetri för enheter och anropa enhets metoder.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska ytterligare lämnar du lösningen för Fjärrövervaknings lösning distribuerad.

Om du inte längre behöver Solution Accelerator tar du bort den från sidan [etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att markera den och sedan klicka på **ta bort lösning**.

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du distribuerar anpassade enhets modeller till Accelerator-lösningen för fjärrövervakning. Det föreslagna nästa steg är att lära dig hur du [ansluter en riktig enhet till din lösning för fjärr styrning](iot-accelerators-connecting-devices-node.md).
