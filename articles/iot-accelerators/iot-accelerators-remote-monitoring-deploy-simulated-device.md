---
title: IoT distribuera anpassade simulerade enheter – Azure | Microsoft Docs
description: Den här guiden visar hur du distribuerar anpassade simulerade enheter till lösningsacceleratorn för fjärrövervakning.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: f073637810e9ed1acdf37b0e541ca3f1d518de2a
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345049"
---
# <a name="deploy-a-new-simulated-device"></a>Distribuera en ny simulerad enhet

Fjärrövervakning och Enhetssimulering Lösningsacceleratorer båda kan du definiera egna simulerade enheter. Den här artikeln visar hur du distribuerar en anpassad kylaggregat enhetstyp och en ny typ av enhet glödlampan till lösningsacceleratorn för fjärrövervakning.

Stegen i den här artikeln förutsätter att du har slutfört den [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md) how-to leder och innehåller filer som definierar anpassade kylaggregat och nya glödlampan enhetstyper.

Stegen i den här guiden visar dig hur du:

1. Använda SSH för att få åtkomst till filsystemet för den virtuella datorn som är värd för lösningsacceleratorn för fjärrövervakning.

1. Konfigurera Docker för att läsa in enhetsmodeller från en plats utanför Docker-behållaren.

1. Kör den lösningsacceleratorn för fjärrövervakning använder anpassade modellfiler.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

För att slutföra stegen i den här guiden behöver du en aktiv Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa den här guiden behöver du:

- En distribuerad instans av den [lösningsacceleratorn för fjärrövervakning](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- En lokal **bash** gränssnittet för att köra den `ssh` och `scp` kommandon. På Windows, ett enkelt sätt att installera **bash** är att installera [git](https://git-scm.com/download/win).
- Din anpassade modell-filer, till exempel de som beskrivs i [skapa och testa en ny simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Konfigurera Docker

I det här avsnittet ska du konfigurera Docker för att läsa in modellfiler enheten från den **/tmp/devicemodels** mapp på den virtuella datorn i stället för från i Docker-behållaren. Kör kommandon i det här avsnittet i en **bash** skal på den lokala datorn:

I det här avsnittet ska du konfigurera Docker för att läsa in modellfiler enheten från den **/tmp/devicemodels** mapp på den virtuella datorn i stället för från i Docker-behållaren. Kör kommandon i det här avsnittet i en **bash** skal på den lokala datorn:

1. Använda SSH för att ansluta till den virtuella datorn i Azure från din lokala dator. Följande kommando förutsätter offentliga IP-adress för virtuell dator **vm-vikxv** är **104.41.128.108** --Ersätt detta värde med den offentliga IP-adressen för den virtuella datorn i föregående avsnitt:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Följ anvisningarna för att logga in på den virtuella datorn med det lösenord som anges i föregående avsnitt.

1. Konfigurera enheten simulering tjänsten för att läsa in enhetsmodeller från utanför behållaren. Först öppna konfigurationsfilen Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Leta upp inställningarna för den **devicesimulation** behållare och redigera den **volymer** inställning som du ser i följande kodavsnitt:

    ```yml
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_IOTHUB_CONNSTRING
        - PCS_STORAGEADAPTER_WEBSERVICE_URL=http://storageadapter:9022/v1
        - PCS_AUTH_ISSUER
        - PCS_AUTH_AUDIENCE
        - PCS_AUTH_REQUIRED
        - PCS_CORS_WHITELIST
        - PCS_APPLICATION_SECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Spara ändringarna.

1. Kopiera de befintliga enheten modellfilerna från behållaren till den nya platsen. Ta först reda behållar-ID för enheten simulering behållaren:

    ```sh
    docker ps
    ```

    Kopiera sedan enheten modellfilerna till den **tmp** mapp på den virtuella datorn. Följande kommando förutsätter behållar-ID är c378d6878407, Ersätt detta värde med din enhet simulering behållar-ID:

    ```sh
    docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    ```

    Behåll den **bash** fönster med SSH-sessionen öppen.

1. Kopiera dina anpassade modellfiler till den virtuella datorn. Kör det här kommandot i en annan **bash** skal på datorn där du skapade dina anpassade modeller. Navigera först till den lokala mappen som innehåller enheten modellen JSON-filer. Följande kommandon förutsätter att den offentliga IP-adressen för den virtuella datorn är **104.41.128.108** --Ersätt detta värde med den offentliga IP-adressen för den virtuella datorn. Ange lösenordet för virtuell dator när du uppmanas till detta:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Starta om enheten simulering Docker-behållare för att använda de nya modellerna för enheten. Kör följande kommandon den **bash** skal med öppna SSH-sessionen till den virtuella datorn:

    ```sh
    sudo /app/start.sh
    ```

    Om du vill se status för de Docker-behållarna som körs och deras behållare ID: N använder du följande kommando:

    ```sh
    docker ps
    ```

    Om du vill se loggen från simulering enhetsbehållare kör du följande kommando. Ersätt behållar-ID med ID: T för din enhet simulering behållare:

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Kör simulering

Du kan nu använda dina modeller med anpassad enhet i lösningen för fjärrövervakning:

1. Starta instrumentpanelen för fjärrövervakning från [Microsoft Azure IoT-Lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Använd den **enheter** sidan för att lägga till simulerade enheter. När du lägger till en ny simulerad enhet är din nya enhetsmodeller kan väljas.

1. Du kan använda instrumentpanelen för att visa enhetstelemetri och anropa enhetsmetoder.

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker utforska ytterligare lämna den lösningsacceleratorn för fjärrövervakning distribueras.

Om du inte längre behöver lösningsaccelerator kan ta bort den från den [etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) sidan genom att markera den och sedan på **ta bort lösningen**.

## <a name="next-steps"></a>Nästa steg

Den här guiden visar dig distribuera anpassade modeller till lösningsacceleratorn för fjärrövervakning. Föreslagna nästa steg är att lära dig hur du [ansluta en fysisk enhet till lösningen för fjärrövervakning](iot-accelerators-connecting-devices-node.md).
