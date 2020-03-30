---
title: ta med fil
description: ta med fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ca4bd3d3b40934323bab8036f3ce72e9281f1de4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187779"
---
> [!div class="op_single_selector"]
> * [C i Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C i Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C på Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (generisk)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js på Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

I den här självstudien implementerar du en **Chiller-enhet** som skickar följande telemetri till [lösningsacceleratorn](../articles/iot-accelerators/about-iot-accelerators.md)för fjärrövervakning:

* Temperatur
* Tryck
* Fuktighet

För enkelhetens skull genererar koden exempeltelemetrivärden för **kylaren**. Du kan utöka provet genom att ansluta riktiga sensorer till enheten och skicka riktig telemetri.

Exempelenheten också:

* Skickar metadata till lösningen för att beskriva dess funktioner.
* Svarar på åtgärder som utlöses från sidan **Enheter** i lösningen.
* Svarar på konfigurationsändringar som skickas från sidan **Enheter** i lösningen.

Du behöver ett Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Innan du börjar

Innan du skriver någon kod för enheten kan du distribuera lösningsacceleratorn för fjärrövervakning och lägga till en ny riktig enhet i lösningen.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Distribuera lösningsacceleratorn för fjärrövervakning

Den **chiller-enhet** som du skapar i den här självstudien skickar data till en instans av lösningsacceleratorn [för fjärrövervakning.](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) Om du inte redan har etablerat lösningsacceleratorn för fjärrövervakning i ditt Azure-konto läser [du Distribuera lösningsacceleratorn för fjärrövervakning](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

När distributionsprocessen för lösningen för fjärrövervakning är klar klickar du på **Starta** för att öppna lösningsinstrumentpanelen i webbläsaren.

![Instrumentpanelen för lösningen](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Lägga till enheten i lösningen för fjärrövervakning

> [!NOTE]
> Om du redan har lagt till en enhet i din lösning kan du hoppa över det här steget. Nästa steg kräver dock enhetens anslutningssträng. Du kan hämta en enhets anslutningssträng från [Azure-portalen](https://portal.azure.com) eller använda az [iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI-verktyget.

För att en enhet ska kunna ansluta till lösningsacceleratorn måste den identifiera sig för IoT Hub med giltiga autentiseringsuppgifter. Du har möjlighet att spara enhetsanslutningssträngen som innehåller dessa autentiseringsuppgifter när du lägger till enheten i lösningen. Du inkluderar enhetsanslutningssträngen i klientprogrammet senare i den här självstudien.

Om du vill lägga till en enhet i lösningen för fjärrövervakning slutför du följande steg på sidan **Enhetsutforskaren** i lösningen:

1. Välj **+ Ny enhet**och välj sedan **Real** som **enhetstyp:**

    ![Lägga till en riktig enhet](media/iot-suite-selector-connecting/devicesprovision.png)

1. Ange **fysisk kylmaskin** som enhets-ID. Välj alternativ för **symmetrisk nyckel** och **generera tangenter automatiskt:**

    ![Välj enhetsalternativ](media/iot-suite-selector-connecting/devicesoptions.png)

1. Välj **Använd**. Anteckna sedan primärnyckelvärdena **för enhets-ID,** **primärnyckel**och **anslutningssträng:**

    ![Hämta autentiseringsuppgifter](media/iot-suite-selector-connecting/credentials.png)

Du har nu lagt till en riktig enhet i lösningsacceleratorn för fjärrövervakning och noterat dess enhetsanslutningssträng. I följande avsnitt implementerar du klientprogrammet som använder enhetsanslutningssträngen för att ansluta till din lösning.

Klientprogrammet implementerar den inbyggda chiller-enhetsmodellen. **Chiller** En lösningsacceleratorenhetsmodell anger följande om en enhet:

* Egenskaperna som enheten rapporterar till lösningen. En **kylare** rapporterar till exempel information om dess inbyggda programvara och plats.
* De typer av telemetri som enheten skickar till lösningen. En **kylmaskinsenhet** skickar till exempel temperatur-, fuktighets- och tryckvärden.
* De metoder som du kan schemalägga från lösningen för att köras på enheten. En **kylmaskinsenhet** måste till exempel implementera metoder för **Omstart**, **FirmwareUpdate**, **EmergencyValveRelease**och **IncreasePressure.**
