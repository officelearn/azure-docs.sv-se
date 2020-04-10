---
title: Handledning av IoT Smart lagerhantering | Microsoft-dokument
description: En självstudiekurs av programmall för smart lagerhantering för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 430f477422e040e0e7e28fd69a1cbc18bce7d656
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000548"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Självstudiekurs: Distribuera och gå igenom en programmall för smart lagerhantering



Den här självstudien visar hur du kommer igång genom att distribuera en IoT Central **smart lagerhantering** programmall. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här självstudien lär du dig att 
* skapa ett program för smart lagerhantering 
* gå igenom applikationen 

## <a name="prerequisites"></a>Krav

* Inga specifika förutsättningar krävs för att distribuera den här appen
* Rekommenderas att ha Azure-prenumeration, men du kan även prova utan den

## <a name="create-smart-inventory-management-application-template"></a>Skapa programmall för smart lagerhantering

Du kan skapa program med följande steg

1. Navigera till Azure IoT Central application manager webbplats. Välj **Skapa** i det vänstra navigeringsfältet och klicka sedan på fliken **Butik.**

    > [!div class="mx-imgBorder"]
    > ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Välj **fliken Butik** och välj Skapa **app** under **smart lagerhantering**

3. **Skapa app** öppnar Nytt ansökningsformulär och fyll i de begärda uppgifterna som visas nedan.
   **Programnamn:** Du kan använda standardnamn eller ange ditt eget programnamn.
   **URL**: Du kan använda föreslagen standard-URL eller ange din vänliga unika minnesvärda webbadress. Därefter rekommenderas standardinställningen om du redan har en Azure-prenumeration. Du kan börja med 7-dagars gratis testperiod prisplan och välja att konvertera till en standard prisplan när som helst innan den fria spåret löper ut.
   **Faktureringsinformation**: Information om katalog, Azure-prenumeration och region krävs för att etablera resurserna.
   **Skapa:** Välj skapa längst ned på sidan för att distribuera programmet.

    > [!div class="mx-imgBorder"]
    > ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

    > [!div class="mx-imgBorder"]
    > ![Faktureringsinformation för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Gå igenom programmet 

### <a name="dashboard"></a>Instrumentpanel 

När du har distribuerat appmallen är standardinstrumentpanelen en fokuserad portal för smart lagerhanteringshantering. Northwind Trader är en fiktiv smart lagerleverantör som hanterar lager med Bluetooth low energy (BLE) och butik med radiofrekvensidentifiering (RFID). I den här instrumentpanelen ser du två olika gateways som tillhandahåller telemetri om lager tillsammans med associerade kommandon, jobb och åtgärder som du kan utföra. Den här instrumentpanelen är förkonfigurerad för att visa upp den kritiska aktiviteten för hantering av smarta lagerhanteringsenheter.
Instrumentpanelen är logiskt fördelad mellan två olika gateway device management-åtgärder, 
   * Lagret distribueras med en fast BLE-gateway & BLE-taggar på lastpallar för att spåra & spårningsinventering på en större anläggning
   * Butik implementeras med en fast RFID-gateway & RFID-taggar på enskild artikelnivå för att spåra och spåra lagret i ett butiksuttag
   * Visa gateway-plats, status & relaterad information 

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Du kan enkelt spåra det totala antalet gateways, aktiva och okända taggar.
   * Du kan utföra enhetshanteringsåtgärder som uppdatering av inbyggd programvara, inaktivera sensor, aktivera sensor, tröskelvärde för uppdateringssensor, uppdatera telemetriintervall & uppdatera enhetstjänstkontrakt
   * Gateway-enheter kan utföra lagerhantering på begäran med en fullständig eller inkrementell genomsökning.

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Enhetsmall
Klicka på fliken Enhetsmallar så visas hanterarens modell. En kapacitetsmodell är uppbyggd kring två olika gränssnitt **Gateway Telemetri & Property** and Gateway **Commands**

**Gateway Telemetri & Egenskap** - Det här gränssnittet representerar alla telemetri relaterade till sensorer, plats, enhetsinformation och enhetstvillingegenskapsfunktioner, till exempel tröskelvärden för gateway och uppdateringsintervall.

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Gateway-kommandon** - Det här gränssnittet organiserar alla kommandofunktioner för gateway

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regler
Välj fliken Regler om du vill visa två olika regler som finns i den här programmallen. Dessa regler är konfigurerade för e-postmeddelanden till operatörerna för ytterligare undersökningar.

**Gateway offline**: Den här regeln utlöses om gatewayen inte rapporterar till molnet under en längre period. Gateway kan inte svara på grund av låg batteriläge, förlust av anslutning, enhetens hälsa.

**Okända taggar**: Det är viktigt att spåra alla RFID-& BLE-taggar som är associerade med en tillgång. Om gatewayen identifierar för många okända taggar är det en indikation på synkroniseringsutmaningar med tagginköpsprogram.

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Jobb
Välj fliken jobb om du vill visa fem olika jobb som finns som en del av den här programmallen: Du kan använda jobbfunktionen för att utföra lösningsomfattande åtgärder. Här använder lagerhanteringsjobb enhetskommandon och dubbla funktioner för att utföra uppgifter som
   * inaktivera läsare i alla gateway
   * ändra telemetritröskeln mellan 
   * utföra lagerskanning på begäran över hela lösningen.

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar **Delete**du bort programmallen genom att besöka inställningar för **Administrationsprogram** > **Application settings** och klicka på Ta bort .

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för smart lagerhantering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om smart koncept för smart lagerhantering [smart lagerhantering](./architecture-smart-inventory-management.md)
* Läs mer om andra [IoT Central-butiksmallar](./overview-iot-central-retail.md)
* Läs mer om IoT Central se [IoT Central översikt](../core/overview-iot-central.md)
