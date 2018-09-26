---
title: ta med fil
description: ta med fil
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 05/30/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 55920b6c147626f68f51b9e0479949330c71a748
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47103004"
---
> [!div class="op_single_selector"]
> * [C i Windows](../articles/iot-suite/iot-suite-v1-connecting-devices.md)
> * [C i Linux](../articles/iot-suite/iot-suite-v1-connecting-devices-linux.md)
> * [Node.js](../articles/iot-suite/iot-suite-v1-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>Scenarioöversikt
I det här scenariot skapar du en enhet som skickar följande telemetri till den [förkonfigurerade lösningen][lnk-what-are-preconfig-solutions] för fjärrövervakning:

* Extern temperatur
* Intern temperatur
* Fuktighet

För enkelhetens skull genererar koden på enheten exempelvärden men vi rekommenderar att du utökar exemplet genom att ansluta riktiga sensorer till enheten och skicka riktig telemetri.

Enheten kan även svara på metoderna som anropas från lösningens instrumentpanel och de önskade egenskapsvärden som är angivna i lösningens instrumentpanel.

Du behöver ett Azure-konto för att slutföra den här självstudiekursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [kostnadsfri utvärderingsversion av Azure][lnk-free-trial].

## <a name="before-you-start"></a>Innan du börjar
Innan du kan skriva kod för enheten måste du etablera din förkonfigurerade lösning för fjärrövervakning och etablera en ny anpassad enhet i lösningen.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Etablera din förkonfigurerade lösning för fjärrövervakning
Enheten som du skapar i den här självstudien skickar data till en instans av den förkonfigurerade lösningen för [fjärrövervakning][lnk-remote-monitoring]. Om du inte redan har etablerat den förkonfigurerade lösningen för fjärrövervakning i ditt Azure-konto använder du följande steg:

1. På den <https://www.azureiotsolutions.com/> klickar du på **+** att skapa en lösning.
2. Klicka på **Välj** på panelen **Fjärrövervakning** för att skapa din lösning.
3. På sidan **Create Remote monitoring solution** (Skapa fjärrövervakningslösning) anger du ett **Lösningsnamn** och väljer den **Region** du vill distribuera till samt väljer den Azure-prenumerationen som du vill använda. Klicka på **Skapa lösning**.
4. Vänta tills etableringsprocessen har slutförts.

> [!WARNING]
> De förkonfigurerade lösningarna använder fakturerbara Azure-tjänster. Se till att ta bort den förkonfigurerade lösningen från prenumerationen när du är färdig med den för att undvika onödiga kostnader. Du kan helt ta bort en förkonfigurerad lösning från din prenumeration genom att besöka den <https://www.azureiotsolutions.com/> sidan.
> 
> 

När etableringen av fjärrövervakningslösningen är klar klickar du på **Starta** för att öppna lösningens instrumentpanel i webbläsaren.

![Instrumentpanel för lösningen][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Etablera enheten i fjärrövervakningslösningen
> [!NOTE]
> Om du redan har etablerat en enhet i din lösning kan du hoppa över det här steget. Du behöver känna till enhetens autentiseringsuppgifter när du skapar klientprogrammet.
> 
> 

För att en enhet ska kunna ansluta till den förkonfigurerade lösningen måste den identifiera sig för IoT Hub med giltiga autentiseringsuppgifter. Du kan hämta enhetens autentiseringsuppgifter från lösningens instrumentpanel. Du kan inkludera enhetsautentiseringsuppgifterna i klientprogrammet senare i den här självstudien.

Om du vill lägga till en enhet till din fjärrövervakningslösning utför du följande steg i lösningens instrumentpanel:

1. Klicka på **Lägg till en enhet** i det nedre vänstra hörnet på instrumentpanelen.
   
   ![Lägg till en enhet][1]
2. I panelen **Anpassad enhet** klickar du på **Lägg till ny**.
   
   ![Lägg till en anpassad enhet][2]
3. Välj **Låt mig ange mitt eget enhets-ID**. Ange ett enhets-ID, t.ex. **mydevice** och klicka på **Kontrollera ID** för att kontrollera att namnet inte redan används. Klicka sedan på **Skapa** för att etablera enheten.
   
   ![Lägg till enhets-ID][3]
4. Notera enhetsautentiseringsuppgifterna (Enhets-ID, IoT Hub-värdnamn och Enhetsnyckel). Klientprogrammet behöver dessa värden för att ansluta till fjärrövervakningslösningen. Klicka sedan på **Klar**.
   
    ![Visa enhetsautentiseringsuppgifter][4]
5. Välj enheten i enhetslistan i lösningens instrumentpanel. Klicka sedan på **Aktivera enhet** i panelen **Enhetsinformation**. Statusen för din enhet är nu **Körs**. Fjärrövervakningslösningen kan nu ta emot telemetri från enheten och anropa metoder på enheten.

[img-dashboard]: ./media/iot-suite-v1-selector-connecting/dashboard.png
[1]: ./media/iot-suite-v1-selector-connecting/suite0.png
[2]: ./media/iot-suite-v1-selector-connecting/suite1.png
[3]: ./media/iot-suite-v1-selector-connecting/suite2.png
[4]: ./media/iot-suite-v1-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/