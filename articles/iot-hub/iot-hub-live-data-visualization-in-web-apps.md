---
title: Data i realtid visualisering av sensordata från Azure IoT hub – Web Apps | Microsoft Docs
description: Använda funktionen Web Apps i Microsoft Azure App Service för att visualisera data för temperaturen och fuktigheten som samlats in från sensorn och skickas till din Iot hub.
author: rangv
manager: ''
keywords: realtid datavisualisering, live datavisualisering sensor datavisualisering
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 445bc4b693c8129c4faf42fee3b2be34d90846ae
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160259"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Visualisera sensordata i realtid från Azure IoT-hubben med hjälp av funktionen Web Apps i Azure App Service

![Slutpunkt till slutpunkt-diagram](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

I den här självstudien får lära du att visualisera sensordata i realtid som din IoT-hubb tar emot genom att köra ett webbprogram som finns på en webbapp. Om du vill försöka att visualisera data i IoT-hubben med hjälp av Power BI, se [Använd Power BI för att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Vad du gör

- Skapa en webbapp i Azure-portalen.
- Förbereda din IoT-hubb för åtkomst till data genom att lägga till en konsumentgrupp.
- Konfigurera webbappen för att läsa sensordata från IoT hub.
- Ladda upp ett webbprogram som ska köras av webbappen.
- Öppna appen om du vill se temperatur och fuktighet realtidsdata från IoT hub.

## <a name="what-you-need"></a>Vad du behöver

- [Konfigurera din enhet](iot-hub-raspberry-pi-kit-node-get-started.md), som omfattar följande krav:
  - En aktiv Azure-prenumeration
  - En Iot-hubb i din prenumeration
  - Ett klientprogram som skickar meddelanden till din Iot-hubb
- [Hämta Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Skapa en webbapp

1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **skapa en resurs** > **webb + mobilt** > **Webbapp**.
2. Ange ett unikt jobbnamn, kontrollera prenumerationen, ange en resursgrupp och en plats, väljer **fäst på instrumentpanelen**, och klicka sedan på **skapa**.

   Vi rekommenderar att du väljer på samma plats som resursgruppen. Detta hjälper till med bearbetningshastigheten och minskar kostnaden för dataöverföring.

   ![Skapa en webbapp](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Konfigurera webbappen för att läsa data från IoT hub

1. Öppna webbappen du precis har etablerat.
2. Klicka på **programinställningar**, och under **appinställningar**, Lägg till följande nyckel/värde-par:

   | Nyckel                                   | Värde                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Hämtas från Azure CLI                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | Namnet på konsumentgrupp som du lägger till din IoT-hubb  |

   ![Lägga till inställningar till webbappen med nyckel/värde-par](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

3. Klicka på **programinställningar**under **allmänna inställningar**, växla den **Web sockets** alternativ och klickar sedan på **spara**.

   ![Ändra alternativet för Web sockets](media/iot-hub-live-data-visualization-in-web-apps/10_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Ladda upp ett webbprogram kan vara värd för webbappen

På GitHub, har vi gjort tillgängliga ett webbprogram som visar sensordata i realtid från IoT hub. Allt du behöver göra är att konfigurera webbappen för att arbeta med en Git-lagringsplats, ladda ned webbprogrammet från GitHub och överför den till Azure för web-app till värden.

1. I webbappen, klickar du på **distributionsalternativ** > **Välj källa** > **lokal Git-lagringsplats**, och klicka sedan på **OK**.

   ![Konfigurera din distribution av webbappar om du vill använda den lokala Git-lagringsplatsen](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Klicka på **Distributionsbehörigheterna**, skapa ett användarnamn och lösenord för att ansluta till Git-lagringsplats i Azure och klicka sedan på **spara**.

3. Klicka på **översikt**, och anteckna värdet för **url för Git-klon**.

   ![Hämta URL: en för Git-klon på din webbapp](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

4. Öppna ett kommando eller ett terminalfönster på din lokala dator.

5. Ladda ned webbappen från GitHub och överför det till Azure för web-app till värden. Gör du följande kommandon:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<URL för Git-klonen\> är URL: en för Git-lagringsplats som finns på den **översikt** sidan i webbappen.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Öppna appen om du vill se temperatur och fuktighet realtidsdata från IoT hub

På den **översikt** sidan i webbappen, klickar du på URL: en för att öppna webbappen.

![Hämta URL för din webbapp](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Du bör se temperatur och fuktighet realtidsdata från IoT hub.

![Web app-sidan som visar i realtid temperatur och fuktighet](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Kontrollera exempelprogrammet som körs på din enhet. Om inte, du får ett tomt diagram, kan du referera till självstudier under [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Nästa steg
Du har använt din webbapp för att visualisera sensordata i realtid från IoT hub.

Ett annat sätt att visualisera data från Azure IoT Hub, se [Använd Power BI för att visualisera sensordata i realtid från IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
