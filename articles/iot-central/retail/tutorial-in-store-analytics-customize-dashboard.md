---
title: Självstudiekurs - Anpassa operatörsinstrumentpanelen i Azure IoT Central
description: Den här självstudien visar hur du anpassar operatörsinstrumentpanelen i ett IoT Central-program och hanterar enheter.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 47edaec54a2470e9b657b2f214be923439e1e8a2
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000121"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Självstudiekurs: Anpassa operatörsinstrumentpanelen och hantera enheter i Azure IoT Central


I den här självstudien får du lära dig hur du anpassar operatörsinstrumentpanelen i ditt Azure IoT Central-analysprogram i butik. Programoperatorer kan använda den anpassade instrumentpanelen för att köra programmet och hantera de anslutna enheterna.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Ändra instrumentpanelens namn
> * Anpassa bildpaneler på instrumentpanelen
> * Ordna paneler för att ändra layouten
> * Lägga till telemetripaneler för att visa förhållanden
> * Lägga till egenskapspaneler för att visa enhetsinformation
> * Lägga till kommandopaneler för att köra kommandon

## <a name="prerequisites"></a>Krav

Byggaren bör slutföra självstudien för att skapa Azure IoT Central i butik analytics program och lägga till enheter:

* [Skapa ett analysprogram i butik i Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (obligatoriskt)

## <a name="change-the-dashboard-name"></a>Ändra instrumentpanelens namn
Om du vill anpassa operatörsinstrumentpanelen måste du redigera standardinstrumentpanelen i programmet. Du kan också skapa ytterligare nya instrumentpaneler. Det första steget för att anpassa instrumentpanelen i ditt program är att ändra namnet.

1. Navigera till [Azure IoT Central application manager](https://aka.ms/iotcentral) webbplats.

1. Öppna det tillståndsövervakningsprogram som du skapade i [programmet Skapa ett analysprogram i Azure IoT Central.](./tutorial-in-store-analytics-create-app.md)

1. Välj **Redigera** i instrumentpanelsverktygsfältet. I redigeringsläge kan du anpassa instrumentpanelens utseende, layout och innehåll.

    ![Azure IoT Central redigera instrumentpanel](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. Du kan också dölja den vänstra rutan. Om du döljer den vänstra rutan får du ett större arbetsområde för redigering av instrumentpanelen.

1. Ange ett eget namn för instrumentpanelen i **Instrumentpanelsnamnet.** Den här självstudien använder ett fiktivt företag med namnet Contoso och exempelinstrumentpanelens namn är *Contoso-instrumentpanelen*. 

1. Välj **Spara**. Ändringarna sparas på instrumentpanelen och redigeringsläget är inaktiverat.

    ![Azure IoT Central ändra instrumentpanelsnamn](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Anpassa bildpaneler på instrumentpanelen
Instrumentpanelen för Ett Azure IoT Central-program består av en eller flera paneler. En panel är en rektangulär behållare för att visa innehåll på en instrumentpanel. Du associerar olika typer av innehåll med paneler och du drar, släpper och ändrar storlek på paneler för att anpassa en instrumentpanelslayout. Det finns flera typer av paneler för att visa innehåll. Bildpaneler innehåller bilder och du kan lägga till en URL som gör det möjligt för användare att klicka på bilden. Etikettpaneler visar oformaterad text. Markdown-paneler innehåller formaterat innehåll och låter dig ange en bild, en URL, en rubrik och markeringskod som återges som HTML. Telemetri-, egenskaps- eller kommandopaneler visar enhetsspecifika data. 

I det här avsnittet får du lära dig hur du anpassar bildpaneler på instrumentpanelen.

Så här anpassar du bildpanelen som visar en varumärkesbild på instrumentpanelen:

1. Välj **Redigera** i instrumentpanelsverktygsfältet. 

1. Välj **Konfigurera** på bildpanelen som visar varumärkesbilden Northwind. 

    ![Azure IoT Central redigera varumärkesbild](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Ändra **rubriken**. Titeln visas när en användare hovrar över bilden.

1. Välj **Bild**. En dialogruta öppnas och du kan ladda upp en anpassad bild. 

1. Du kan också ange en URL för bilden.

1. Välj **Uppdatera konfiguration**. Knappen **Uppdatera konfiguration** sparar ändringar på instrumentpanelen och lämnar redigeringsläget aktiverat.

    ![Azure IoT Central spara varumärkesavbildning](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Du kan också välja **Konfigurera** på panelen **Dokumentation**och ange en URL för supportinnehåll. 

Så här anpassar du bildpanelen som visar en karta över sensorzonerna i butiken:

1. Välj **Konfigurera** på bildpanelen som visar standardkartan för butikszon. 

1. Välj **Bild**och använd dialogrutan för att ladda upp en anpassad bild av en karta över butikszoner. 

1. Välj **Uppdatera konfiguration**.

    ![Azure IoT Central spara butikskarta](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    Exemplet Contoso butikskarta visar fyra zoner: två kassazoner, en zon för kläder och personlig vård, och en zon för livsmedel och deli. I den här självstudien associerar du sensorer med dessa zoner för att tillhandahålla telemetri.

    ![Azure IoT Centrala butikszoner](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Välj **Spara**. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Ordna paneler för att ändra layouten
Ett viktigt steg i anpassningen av en instrumentpanel är att ordna om panelerna för att skapa en användbar vy. Programoperatörer använder instrumentpanelen för att visualisera enhetstelemetri, hantera enheter och övervaka förhållanden i en butik. Azure IoT Central förenklar programverktygets uppgift att skapa en instrumentpanel. Med redigeringsläget för instrumentpanelen kan du snabbt lägga till, flytta, ändra storlek på och ta bort paneler. Programmallen **I butik – utcheckningsprogrammet** förenklar också uppgiften att skapa en instrumentpanel. Det ger en fungerande instrumentpanelslayout, med anslutna sensorer och paneler som visar kassalinjeantal och miljöförhållanden.

I det här avsnittet ordnar du om instrumentpanelen i programmallen **För butiksanalys – utcheckningsprogram** för att skapa en anpassad layout.

Så här tar du bort paneler som du inte tänker använda i programmet:

1. Välj **Redigera** i instrumentpanelsverktygsfältet. 

1. Välj **X Ta bort** om du vill ta bort följande paneler: Tillbaka till alla **zoner,** **Besök butiksinstrumentpanelen,** **Väntetiden**och alla tre paneler som är associerade med **Kassan 3**. Instrumentpanelen contoso-arkivet använder inte dessa paneler. 

    ![Ta bort azure IoT Central-paneler](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Bläddra för att visa de återstående instrumentpanelspanelerna.

1. Välj **X Ta bort** om du vill ta bort följande paneler: **Uppvärmningsutcheckningszon,** **Nedkylningsdiskutcheckningszon,** **Inställningar för beläggningssensor,** **Termostatsensorinställningar**och **Miljöförhållanden**. 

   ![Azure IoT Central ta bort återstående paneler](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Välj **Spara**. Om du tar bort oanvända paneler frigörs utrymme på redigeringssidan och instrumentpanelsvyn förenklas för operatorer.

1. Visa ändringarna på instrumentpanelen.

   ![Azure IoT Central efter att ha tagit bort paneler](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

När du har tagit bort oanvända paneler ordnar du om de återstående panelerna för att skapa en ordnad layout. Den nya layouten innehåller utrymme för paneler som du lägger till i ett senare steg.

Så här ordnar du om de återstående panelerna:

1. Välj **Redigera**.

1. Välj panelen **För inflyttningsanslagsanslag** och dra den till höger om batteripanelen **för beläggning.**

1. Välj **thermostat firmware** kakel och dra den till höger om **Termostat** batteri kakel.

1. Välj **Spara**.

1. Visa dina layoutändringar. 

    ![Azure IoT Central firmware batteripaneler](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Lägga till telemetripaneler för att visa förhållanden
När du har anpassat instrumentpanelslayouten är du redo att lägga till paneler för att visa telemetri. Om du vill skapa en telemetripanel markerar du en enhetsmall och enhetsinstans och väljer sedan enhetsspecifik telemetri som ska visas i panelen. Mallen **För analys i butik – utcheckningsprogram** innehåller flera telemetripaneler på instrumentpanelen. De fyra brickorna i de två utcheckningszonerna visar telemetri från den simulerade beläggningssensorn. Trafikpanelen **Kontakter** visar antalet i de två utcheckningszonerna. 

I det här avsnittet lägger du till ytterligare två telemetripaneler för att visa miljötelemetri från RuuviTag-sensorerna som du lade till i [analytics-programmet Skapa i butik i Azure IoT](./tutorial-in-store-analytics-create-app.md) Central-självstudien. 

Så här lägger du till paneler för att visa miljödata från RuuviTag-sensorerna:

1. Välj **Redigera**.

1. Markera `RuuviTag` i listan **Enhetsmall.** 

1. Välj en **enhetsinstans** av en av de två RuuviTag-sensorerna. I exemplet Contoso-arkivet väljer du `Zone 1 Ruuvi` att skapa en telemetripanel för zon 1. 

1. Markera `Relative humidity` `temperature` och i listan **Telemetri.** Det här är de telemetriobjekt som visas för varje zon på panelen.

1. Välj **Kombinera**. 

    ![Azure IoT Central lägga RuuviTag kakel 1](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    En ny bricka visas för att visa kombinerad luftfuktighet och temperaturtelemetri för den valda sensorn. 

1. Välj **Konfigurera** på den nya panelen för RuuviTag-sensorn. 

1. Ändra **miljön Rubrik** till *zon 1*. 

1. Välj **Uppdatera konfiguration**.

1. Upprepa föregående steg för att skapa en panel för den andra sensorinstansen. Ange miljön **Rubrik** till *zon 2* och välj sedan **Uppdatera konfiguration.**

1. Dra panelen med titeln **Zon 2-miljö** under **termostatblinkomsan.** 

1. Dra panelen **Zon 1-miljön** under **trafikpanelen Kontakter.** 

1. Välj **Spara**. Instrumentpanelen visar zontelemetri i de två nya panelerna.

    ![Azure IoT Central alla RuuviTag-paneler](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

Så här redigerar du **panelen Kontakter trafik** för att visa telemetri för endast två utcheckningszoner:

1. Välj **Redigera**. 

1. Välj **Konfigurera** på **persontrafikpanelen.**

1. I **Telemetri** väljer **du antal 1**, antal **2**och **antal 3**. 

1. Välj **Uppdatera konfiguration**. Den rensar den befintliga konfigurationen på panelen. 

1. Välj **Konfigurera** igen på **persontrafikpanelen.**

1. I **Telemetri** väljer du **antal 1**och **antal 2**. 

1. Välj **Uppdatera konfiguration**. 

1. Välj **Spara**.  Den uppdaterade instrumentpanelen visar räknas för endast dina två kassazoner, som baseras på den simulerade beläggningssensorn.

    ![Azure IoT Central personer trafik två körfält](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Lägga till egenskapspaneler för att visa enhetsinformation
Programoperatorer använder instrumentpanelen för att hantera enheter och övervaka status. Lägg till en panel för varje RuuviTag så att operatörerna kan visa programvaruversionen. 

Så här lägger du till en egenskapspanel för varje RuuviTag:

1. Välj **Redigera**.

1. Markera `RuuviTag` i listan **Enhetsmall.** 

1. Välj en **enhetsinstans** av en av de två RuuviTag-sensorerna. I exemplet Contoso-arkivet väljer du `Zone 1 Ruuvi` att skapa en telemetripanel för zon 1. 

1. Välj **Egenskaper > Programvaruversion**.

1. Välj **Kombinera**. 

1. Välj **Konfigurera** på den nyskapade panelen Med namnet **Software version**. 

1. Ändra **titeln** till *Ruuvi 1 mjukvaruversion*.

1. Välj **Uppdatera konfiguration**. 

1. Dra kakel med titeln **Ruuv 1 mjukvaruversion** under **Zon 1 miljö** kakel.

1. Upprepa föregående steg för att skapa en programvaruversionsegenskapspanel för den andra RuuviTag. 

1. Välj **Spara**.  

    ![Azure IoT Central RuuviTag egenskapspaneler](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Lägga till kommandopaneler för att köra kommandon
Programoperatorer använder också instrumentpanelen för att hantera enheter genom att köra kommandon. Du kan lägga till kommandopaneler på instrumentpanelen som kör fördefinierade kommandon på en enhet. I det här avsnittet lägger du till en kommandopanel för att göra det möjligt för operatörer att starta om Rigado-gatewayen. 

Så här lägger du till en kommandopanel för att starta om gatewayen:

1. Välj **Redigera**. 

1. Markera `C500` i listan **Enhetsmall.** Det är mallen för Rigado C500 gateway. 

1. Välj gateway-instans i **Enhetsinstans**.

1. Välj **Kommando > Starta om** och dra det till instrumentpanelen bredvid butikskartan. 

1. Välj **Spara**. 

1. Visa den färdiga Contoso-instrumentpanelen. 

    ![Azure IoT Central fullständig anpassning av instrumentpanelen](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Du kan också välja panelen **Starta om** för att köra omstartskommandot på gatewayen.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

* Ändra instrumentpanelens namn
* Anpassa bildpaneler på instrumentpanelen
* Ordna paneler för att ändra layouten
* Lägga till telemetripaneler för att visa förhållanden
* Lägga till egenskapspaneler för att visa enhetsinformation
* Lägga till kommandopaneler för att köra kommandon

Nu när du har anpassat instrumentpanelen i ditt Azure IoT Central-analysprogram i butik är detta det föreslagna nästa steget:

> [!div class="nextstepaction"]
> [Exportera data och visualisera insikter](./tutorial-in-store-analytics-export-data-visualize-insights.md)
