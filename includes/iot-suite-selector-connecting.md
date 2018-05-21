---
title: ta med fil
description: ta med fil
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 500e335d0b2eddc56cdfb9828236bc4676d9b6aa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
> [!div class="op_single_selector"]
> * [C i Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C i Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [Node.js (generisk)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js på Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [C på Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)

I kursen får du implementerar en **kylaggregat** enhet som skickar följande telemetri för fjärråtkomst övervakning [lösningsaccelerator](../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md):

* Temperatur
* Tryck
* Fuktighet

För enkelhetens skull koden genererar exempelvärden telemetri för den **kylaggregat**. Du kan utöka exemplet genom att ansluta verkliga sensorer till enheten och skicka verkliga telemetri.

Exempel enheten också:

* Skickar metadata i lösningen för att beskriva dess funktioner.
* Svarar på åtgärder som utlöses från den **enheter** sida i lösningen.
* Svarar på konfigurationsändringar skicka från den **enheter** sida i lösningen.

Du behöver ett Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Innan du börjar

Innan du skriva kod för enheten kan du distribuera din fjärråtkomst övervakning solution accelerator och lägger till en ny fysisk enhet i lösningen.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Distribuera din fjärråtkomst övervakning solution accelerator

Den **kylaggregat** enhet som du skapar i den här självstudiekursen skickar data till en instans av den [fjärrövervaknings](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) lösningsaccelerator. Om du inte redan har etablerats fjärråtkomst övervakning solution accelerator i ditt Azure-konto, se [Distribuera fjärråtkomst övervakning solution accelerator](../articles/iot-accelerators/iot-accelerators-remote-monitoring-deploy.md)

När distributionsprocessen för fjärranslutna övervakningslösning är klar klickar du på **starta** att öppna lösningen instrumentpanelen i webbläsaren.

![Infopanelen lösning](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Lägg till din enhet i fjärranslutna övervakningslösning

> [!NOTE]
> Om du redan har lagt till en enhet i din lösning kan du hoppa över det här steget. Nästa steg kräver dock anslutningssträngen enhet. Du kan hämta anslutningssträngen för en enhet från den [Azure-portalen](https://portal.azure.com) eller med hjälp av den [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI-verktyget.

För en enhet kan ansluta till solution accelerator, måste den identifiera sig för IoT-hubb med giltiga autentiseringsuppgifter. Du har möjlighet att spara anslutningssträngen för enheten som innehåller dessa autentiseringsuppgifter när du lägger till enheten lösningen. Du inkludera enheten anslutningssträngen i ditt klientprogram senare i den här kursen.

Lägg till en enhet i din lösning för fjärråtkomst övervakning, utför följande steg på den **enheter** sida i lösningen:

1. Välj **+ ny enhet**, och välj sedan **fysiska** som den **enhetstyp**:

    ![Lägg till en fysisk enhet](media/iot-suite-selector-connecting/devicesprovision.png)

1. Ange **fysiska kylaggregat** som enhets-ID. Välj den **symmetrisk nyckel** och **automatiskt generera nycklar** alternativ:

    ![Välj alternativ för enheten](media/iot-suite-selector-connecting/devicesoptions.png)

1. Välj **tillämpa**. Sedan anteckna den **enhets-ID**, **primärnyckel**, och **anslutning sträng primärnyckel** värden:

    ![Hämta autentiseringsuppgifter](media/iot-suite-selector-connecting/credentials.png)

Du har nu lagt till en fysisk enhet till fjärråtkomst övervakning solution accelerator och anges sin enhet anslutningssträng. I följande avsnitt kan du implementera klientprogram som använder enheten anslutningssträngen för att ansluta till din lösning.

Klientprogrammet implementerar inbyggt **kylaggregat** enhetsmodell. En solution accelerator enhetsmodell anger följande om en enhet:

* Egenskaperna enheten rapporterar till lösningen. Till exempel en **kylaggregat** enheten rapporterar information om den inbyggda programvaran och plats.
* Vilka typer av telemetri enheten skickar till lösningen. Till exempel en **kylaggregat** enheten skickar temperatur, fuktighet och tryckvärden.
* Metoderna som du kan schemalägga från lösningen ska köras på enheten. Till exempel en **kylaggregat** enheten måste implementera **omstart**, **FirmwareUpdate**, **EmergencyValveRelease**, och  **IncreasePressure** metoder.