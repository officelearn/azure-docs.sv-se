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
ms.openlocfilehash: d4b93685160250a5abe7c050db89978ae8ceb1a9
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555603"
---
> [!div class="op_single_selector"]
> * [C i Windows](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [C i Linux](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [C på Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)
> * [Node.js (generisk)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Node.js på Raspberry Pi](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [MXChip IoT DevKit](../articles/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2.md)

I den här självstudien implementerar du en **kyl** enhet som skickar följande telemetri till lösningen för fjärr styrnings [lösning](../articles/iot-accelerators/about-iot-accelerators.md):

* Temperatur
* Lufttryck
* Luftfuktighet

För enkelhetens skull genererar koden exempel på telemetri-värden för **kyl metoden**. Du kan utöka exemplet genom att ansluta riktiga sensorer till enheten och skicka riktiga telemetri.

Exempel enheten:

* Skickar metadata till lösningen för att beskriva dess funktioner.
* Svarar på åtgärder som utlöses från sidan **enheter** i lösningen.
* Svarar på konfigurations ändringar Skicka från sidan **enheter** i lösningen.

Du behöver ett Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Innan du börjar

Innan du skriver en kod för enheten kan du distribuera lösningen för fjärrövervakning och lägga till en ny verklig enhet i lösningen.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>Distribuera lösnings acceleratorn för fjärr styrning

Den **kylnings** enhet som du skapar i den här självstudien skickar data till en instans av lösningen för [fjärrövervakning](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md) . Om du inte redan har skapat lösningen för fjärrövervakning i ditt Azure-konto läser [du distribuera lösnings Accelerator för fjärrövervakning](../articles/iot-accelerators/quickstart-remote-monitoring-deploy.md)

När distributions processen för fjärr styrnings lösningen har slutförts klickar du på **Starta** för att öppna instrument panelen för lösningen i webbläsaren.

![Instrument panelen för lösningen](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>Lägg till din enhet i lösningen för fjärrövervakning

> [!NOTE]
> Om du redan har lagt till en enhet i lösningen kan du hoppa över det här steget. Nästa steg kräver dock enhets anslutnings strängen. Du kan hämta en enhets anslutnings sträng från [Azure Portal](https://portal.azure.com) eller använda IoT CLI-verktyget [AZ](/cli/azure/iot?view=azure-cli-latest) .

För att en enhet ska kunna ansluta till Solution Accelerator måste den identifiera sig för att IoT Hub använda giltiga autentiseringsuppgifter. Du har möjlighet att spara enhets anslutnings strängen som innehåller dessa autentiseringsuppgifter när du lägger till enheten i lösningen. Du tar med enhets anslutnings strängen i klient programmet senare i den här självstudien.

Om du vill lägga till en enhet i lösningen för fjärrövervakning utför du följande steg på sidan **Device Explorer** i lösningen:

1. Välj **+ ny enhet** och välj sedan **verklig** som **enhets typ**:

    ![Lägga till en riktig enhet](media/iot-suite-selector-connecting/devicesprovision.png)

1. Ange **fysikalisk-kylning** som enhets-ID. Välj alternativ för **symmetrisk nyckel** och **automatisk generering av nycklar** :

    ![Välj enhets alternativ](media/iot-suite-selector-connecting/devicesoptions.png)

1. Välj **Använd**. Anteckna värdena för **enhets-ID**, **primär nyckel** och **primär nyckel för anslutnings strängen** :

    ![Hämta autentiseringsuppgifter](media/iot-suite-selector-connecting/credentials.png)

Nu har du lagt till en riktig enhet i lösningen för fjärrövervakning och noterat enhets anslutnings strängen. I följande avsnitt implementerar du klient programmet som använder enhets anslutnings strängen för att ansluta till din lösning.

Klient programmet implementerar den inbyggda **kyl** enhets modellen. En enhets modell för Solution Accelerator anger följande om en enhet:

* De egenskaper enheten rapporterar till lösningen. En **kyl** enhet rapporterar till exempel information om den inbyggda program varan och platsen.
* De typer av telemetri som enheten skickar till lösningen. En **kyl** enhet skickar till exempel värdena temperatur, fuktighet och tryck.
* De metoder du kan schemalägga från lösningen som ska köras på enheten. Till exempel måste en **kyl** enhet implementera metoderna **reboot**, **FirmwareUpdate**, **EmergencyValveRelease** och **IncreasePressure** .