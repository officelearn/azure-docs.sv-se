---
title: IoT distribuera anpassade simulerade enheter – Azure | Microsoft-dokument
description: Den här programguiden visar hur du distribuerar anpassade simulerade enheter till lösningsacceleratorn för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66427562"
---
# <a name="deploy-a-new-simulated-device"></a>Distribuera en ny simulerad enhet

Med lösningsacceleratorerna för fjärrövervakning och enhetssimulering kan du definiera dina egna simulerade enheter. Den här artikeln visar hur du distribuerar en anpassad kylmaskinsenhetstyp och en ny glödlampa enhetstyp till remote monitoring lösningsacceleratorn.

Stegen i den här artikeln förutsätter att du har slutfört [skapa och testa en ny simulerad enhet hur-guide](iot-accelerators-remote-monitoring-create-simulated-device.md) och har de filer som definierar den anpassade kylmaskinen och nya glödlampa enhetstyper.

Stegen i den här guiden visar hur du:

1. Använd SSH för att komma åt filsystemet för den virtuella datorn som är värd för lösningsacceleratorn för fjärrövervakning.

1. Konfigurera Docker så att enhetsmodellerna läses in från en plats utanför Docker-behållaren.

1. Kör lösningsacceleratorn för fjärrövervakning med anpassade enhetsmodellfiler.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

För att kunna slutföra stegen i den här programguiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att följa den här guiden behöver du:

- En distribuerad instans av [lösningsacceleratorn för fjärrövervakning](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- En lokal **bash** skal `ssh` `scp` att köra och kommandon. På Windows, ett enkelt sätt att installera **bash** är att installera [git](https://git-scm.com/download/win).
- Din anpassade enhetsmodellfiler, till exempel de som beskrivs i [Skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurera Docker

I det här avsnittet konfigurerar du Docker så att enhetsmodellfilerna läses in från mappen **/tmp/devicemodels** i den virtuella datorn i stället för inifrån Docker-behållaren. Kör kommandona i det här avsnittet i ett **bash-skal** på din lokala dator:

I det här avsnittet konfigurerar du Docker så att enhetsmodellfilerna läses in från mappen **/tmp/devicemodels** i den virtuella datorn i stället för inifrån Docker-behållaren. Kör kommandona i det här avsnittet i ett **bash-skal** på din lokala dator:

1. Använd SSH för att ansluta till den virtuella datorn i Azure från din lokala dator. Följande kommando förutsätter att den offentliga IP-adressen för virtuell dator **vm-vikxv** är **104.41.128.108** - ersätta det här värdet med den offentliga IP-adressen för din virtuella dator från föregående avsnitt:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Följ anvisningarna för att logga in på den virtuella datorn med det lösenord du angav i föregående avsnitt.

1. Konfigurera enhetssimuleringstjänsten så att enhetsmodellerna läses in utanför behållaren. Öppna först dockerkonfigurationsfilen:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Leta reda på inställningarna för **devicesimulation-behållaren** och redigera **volyminställningen** enligt följande kodavsnitt:

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

1. Kopiera befintliga enhetsmodellfiler från behållaren till den nya platsen. Leta först reda på behållar-ID:et för enhetssimuleringsbehållaren:

    ```sh
    sudo docker ps
    ```

    Kopiera sedan enhetsmodellfilerna till **tmp-mappen** i den virtuella datorn. Följande kommando förutsätter att behållar-ID:t är c378d6878407 – ersätta det här värdet med enhetssimuleringscontainer-ID:

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
    ```

    Håll **bash-fönstret** med din SSH-session öppen.

1. Kopiera dina anpassade enhetsmodellfiler till den virtuella datorn. Kör det här kommandot i ett annat **bash-skal** på datorn där du skapade dina anpassade enhetsmodeller. Navigera först till den lokala mappen som innehåller din enhetsmodell JSON-filer. Följande kommandon förutsätter att den offentliga IP-adressen för den virtuella datorn är **104.41.128.108** – ersätta det här värdet med den offentliga IP-adressen för den virtuella datorn. Ange lösenordet för den virtuella datorn när du uppmanas:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Starta om dockerbehållaren för enhetssimulering för att använda de nya enhetsmodellerna. Kör följande kommandon i **bash** bash-skalet med den öppna SSH-sessionen till den virtuella datorn:

    ```sh
    sudo /app/start.sh
    ```

    Om du vill se status för de Docker-behållare som körs och deras behållar-ID använder du följande kommando:

    ```sh
    sudo docker ps
    ```

    Om du vill se loggen från enhetssimuleringsbehållaren kör du följande kommando. Byt ut behållar-ID:et mot ID:et för enhetssimuleringsbehållaren:

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Kör simulering

Nu kan du använda dina anpassade enhetsmodeller i lösningen för fjärrövervakning:

1. Starta instrumentpanelen för fjärrövervakning från [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Använd sidan **Enheter** för att lägga till simulerade enheter. När du lägger till en ny simulerad enhet kan du välja nya enhetsmodeller.

1. Du kan använda instrumentpanelen för att visa metoder för enhetstelemetri och anropsenhet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska ytterligare lämnar du den lösningsaccelerator för fjärrövervakning som har distribuerats.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att markera den och sedan på **Ta bort lösning**.

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du distribuerar anpassade enhetsmodeller till lösningsacceleratorn för fjärrövervakning. Det föreslagna nästa steget är att lära sig hur du [ansluter en riktig enhet till din lösning för fjärrövervakning](iot-accelerators-connecting-devices-node.md).
