---
title: Realtidsdata visualisering av sensordata från din Azure IoT-hubb – Web Apps | Microsoft Docs
description: Använd funktionen Web Apps i Microsoft Azure App Service visualisera temperatur- och fuktighetskonsekvens data som samlas in från sensorn och skickas till din Iot-hubb.
author: rangv
manager: ''
keywords: realtid datavisualisering, realtidsdata visualiseringen sensor datavisualisering
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 3d127afa94b761d96db17bcb59700a275a44a265
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633748"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Visualisera sensordata i realtid från Azure IoT-hubben med hjälp av funktionen Web Apps i Azure App Service

![Diagram för slutpunkt till slutpunkt](media/iot-hub-get-started-e2e-diagram/5.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Detta får du får lära dig

I kursen får lära du att visualisera sensordata i realtid som din IoT-hubb som tar emot genom att köra ett program som finns på en webbapp. Om du vill försöka visualisera data i din IoT-hubb med hjälp av Power BI, se [Använd Power BI att visualisera sensordata i realtid från Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Vad du gör

- Skapa en webbapp i Azure-portalen.
- Förbereda din IoT-hubb för åtkomst till data genom att lägga till en konsumentgrupp.
- Konfigurera webbappen för att läsa sensordata från IoT-hubb.
- Ladda upp ett webbprogram kan hanteras på webbprogrammet.
- Öppna webbapp om du vill se temperatur- och fuktighetskonsekvens realtidsdata från din IoT-hubb.

## <a name="what-you-need"></a>Vad du behöver

- [Konfigurera din enhet](iot-hub-raspberry-pi-kit-node-get-started.md), som omfattar följande krav:
  - En aktiv Azure-prenumeration
  - En Iot-hubb i din prenumeration
  - Ett klientprogram som skickar meddelanden till din Iot-hubb
- [Hämta Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Skapa en webbapp

1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **skapar du en resurs** > **webb + mobilt** > **Web App**.
2. Ange ett unikt jobbnamn, kontrollera prenumerationen, ange en resursgrupp och en plats, väljer **fäst på instrumentpanelen**, och klicka sedan på **skapa**.

   Vi rekommenderar att du väljer på samma plats som resursgruppen. Gör detta hjälper till med bearbetningshastigheten och minskar kostnaden för dataöverföring.

   ![Skapa en webbapp](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Konfigurera webbappen för att läsa data från IoT-hubb

1. Öppna webbapp som du precis har etablerats.
2. Klicka på **programinställningar**, och under **appinställningar**, Lägg till följande nyckel/värde-par:

   | Nyckel                                   | Värde                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Hämtas från iothub explorer                                |
   | Azure.IoT.IoTHub.ConsumerGroup        | Namnet på konsumentgrupp som du lägger till din IoT-hubb  |

   ![Lägger till inställningarna i ditt webbprogram med nyckel/värde-par](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

3. Klicka på **programinställningar**under **allmänna inställningar**, växla den **Web sockets** alternativ och klickar sedan på **spara**.

   ![Växla alternativet sockets](media/iot-hub-live-data-visualization-in-web-apps/10_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Ladda upp ett webbprogram kan hanteras på webbprogrammet

På GitHub, har vi gjort tillgängliga ett webbprogram som visar sensordata i realtid från din IoT-hubb. Allt du behöver göra är att konfigurera webbprogram för att arbeta med Git-lagringsplatsen, hämta webbprogrammet från GitHub och överföra det till Azure för webbprogrammet till värden.

1. I webbappen, klickar du på **distributionsalternativ** > **Välj källa** > **lokal Git-lagringsplats**, och klicka sedan på **OK**.

   ![Konfigurera web app-distribution för att använda lokal Git-lagringsplats](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Klicka på **Distributionsbehörigheterna**, skapa ett användarnamn och lösenord som ska användas för att ansluta till Git-lagringsplats i Azure och klicka sedan på **spara**.

3. Klicka på **översikt**, och anteckna värdet för **url för Git-klon**.

   ![Hämta URL för Git-klon av ditt webbprogram](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

4. Öppna ett kommando eller ett terminalfönster på den lokala datorn.

5. Hämta webbprogrammet från GitHub och överföra det till Azure för webbprogrammet till värden. Det gör du genom att köra följande kommandon:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<URL för Git-klon\> är URL för Git-lagringsplats som hittades på den **översikt** sidan i webbprogrammet.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Öppna webbapp om du vill se temperatur- och fuktighetskonsekvens realtidsdata från din IoT-hubb

På den **översikt** sidan av ditt webbprogram, klicka på Webbadressen för att öppna webbapp.

![Hämta URL för ditt webbprogram](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Du bör se temperatur- och fuktighetskonsekvens realtidsdata från din IoT-hubb.

![Appen webbsidan visar realtid temperatur- och fuktighetskonsekvens](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Kontrollera exempelprogrammet som körs på enheten. Om inte, får du ett tomt diagram, kan du referera till självstudier under [konfigurera enheten](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Nästa steg
Du har har använt ditt webbprogram för att visualisera sensordata i realtid från din IoT-hubb.

Ett annat sätt att visualisera data från Azure IoT Hub, se [Använd Power BI att visualisera sensordata i realtid från din IoT-hubb](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
