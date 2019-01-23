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
ms.openlocfilehash: 64cbf8f8704dbb216a15247caf741ff43690496a
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54457058"
---
> [!div class="op_single_selector"]
> * [C i Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C i Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C på Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (generisk)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js på Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md)

I den här självstudien får du implementera en **kylaggregat** enhet som skickar följande telemetri till den fjärrövervakning [lösningsaccelerator](../articles/iot-accelerators/about-iot-accelerators.md):

* Temperatur
* Tryck
* Fuktighet

För enkelhetens skull genererar koden exempelvärden telemetri för den **kylaggregat**. Du kan utöka exemplet genom att ansluta riktiga sensorer till enheten och skicka riktig telemetri.

Exemplet enheten också:

* Skickar metadata till lösningen att beskriva dess funktioner.
* Svarar på åtgärder som utlöses från den **enheter** sidan i lösningen.
* Svarar på ändringar i konfigurationen skicka från den **enheter** sidan i lösningen.

Du behöver ett Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Innan du börjar

Innan du kan skriva kod för enheten, distribuerar lösningsacceleratorn för fjärrövervakning och lägga till en ny riktig enhet i lösningen.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Distribuera lösningsacceleratorn för fjärrövervakning

Den **kylaggregat** enheten som du skapar i den här självstudien skickar data till en instans av den [fjärrövervakning](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) lösningsaccelerator. Om du inte redan har etablerat lösningsacceleratorn för fjärrövervakning i ditt Azure-konto, se [distribuerar lösningsacceleratorn för fjärrövervakning](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

När distributionsprocessen för lösningen för fjärrövervakning är klar klickar du på **starta** att öppna instrumentpanelen för lösningen i din webbläsare.

![Instrumentpanelen för lösningen](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Lägga till din enhet i lösningen för fjärrövervakning

> [!NOTE]
> Om du har redan lagt till en enhet i din lösning, kan du hoppa över det här steget. Nästa steg kräver dock enhetens anslutningssträng. Du kan hämta anslutningssträngen för en enhet från den [Azure-portalen](https://portal.azure.com) eller med hjälp av den [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI-verktyget.

För en enhet att ansluta till lösningsaccelerator kan identifiera den sig för IoT Hub med giltiga autentiseringsuppgifter. Du har möjlighet att spara enhetens anslutningssträng som innehåller de här autentiseringsuppgifterna när du lägger till enheten i lösningen. Du kan inkludera enhetens anslutningssträng i klientprogrammet senare i den här självstudien.

Om du vill lägga till en enhet till din fjärrövervakningslösning utför du följande steg på den **enheter** sidan i lösningen:

1. Välj **+ ny enhet**, och välj sedan **fysiska** som den **enhetstyp**:

    ![Lägga till en riktig enhet](media/iot-suite-selector-connecting/devicesprovision.png)

1. Ange **fysiska kylaggregat** som enhetens ID. Välj den **symmetrisk nyckel** och **generera nycklar automatiskt** alternativ:

    ![Välj alternativ för enhet](media/iot-suite-selector-connecting/devicesoptions.png)

1. Välj **gäller**. Skriv ned den **enhets-ID**, **primärnyckel**, och **Connection string primärnyckel** värden:

    ![Hämta autentiseringsuppgifter](media/iot-suite-selector-connecting/credentials.png)

Du har nu lagt till en riktig enhet att lösningsacceleratorn för fjärrövervakning och anges dess enhetens anslutningssträng. I följande avsnitt kan du implementera klientprogram som använder enhetens anslutningssträng för att ansluta till din lösning.

Klientprogrammet implementerar inbyggt **kylaggregat** enhetsmodell. En enhetsmodell för solution accelerator anger följande om en enhet:

* Egenskaperna enheten rapporterar till lösningen. Till exempel en **kylaggregat** enheten rapporterar information om den inbyggda programvaran och placering.
* Typer av telemetri som enheten skickar till lösningen. Till exempel en **kylaggregat** enheten skickar temperatur, fuktighet och värden för tryck.
* De metoder som du kan schemalägga från lösningen ska köras på enheten. Till exempel en **kylaggregat** enheten måste implementera **omstart**, **FirmwareUpdate**, **EmergencyValveRelease**, och  **IncreasePressure** metoder.
