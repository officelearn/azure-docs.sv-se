---
title: Handledning av IoT Connected logistik | Microsoft-dokument
description: En självstudiekurs av anslutningslogistikapplikationsmall för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: eac43ae68b10436b3e45452c6b1d03bec3ae4c9c
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000562"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Självstudiekurs: Distribuera och gå igenom en mall för ansluten logistikprogram



Den här självstudien visar hur du kommer igång genom att distribuera en IoT **Central-ansluten** logistikprogrammall. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här självstudien får du lära dig att

* skapa uppkopplad logistikapplikation
* gå igenom applikationen 

## <a name="prerequisites"></a>Krav

* Inga specifika förutsättningar krävs för att distribuera den här appen
* Rekommenderas att ha Azure-prenumeration, men du kan även prova utan den

## <a name="create-connected-logistics-application-template"></a>Skapa mall för anslutna logistikprogram

Du kan skapa program med följande steg

1. Navigera till Azure IoT Central application manager webbplats. Välj **Skapa** i det vänstra navigeringsfältet och klicka sedan på fliken **Butik.**

    > [!div class="mx-imgBorder"]
    > ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Välj **Skapa app** under Connected **Logistics Application**

3. **Skapa app** öppnar Nytt ansökningsformulär och fyll i de begärda uppgifterna som visas nedan.
   * **Programnamn:** Du kan använda standardnamn eller ange ditt eget programnamn.
   * **URL**: Du kan använda föreslagen standard-URL eller ange din vänliga unika minnesvärda webbadress. Därefter rekommenderas standardinställningen om du redan har en Azure-prenumeration. Du kan börja med 7-dagars gratis testperiod prisplan och välja att konvertera till en standard prisplan när som helst innan den fria spåret löper ut.
   * **Faktureringsinformation**: Information om katalog, Azure-prenumeration och region krävs för att etablera resurserna.
   * **Skapa:** Välj skapa längst ned på sidan för att distribuera programmet.

    > [!div class="mx-imgBorder"]
    > ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![Faktureringsinformation för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Gå igenom programmet 

## <a name="dashboard"></a>Instrumentpanel

När du har distribuerat appmallen är standardinstrumentpanelen en fokuserad portal för ansluten logistikoperatör. Northwind Trader är en fiktiv logistikleverantör som förvaltar lastflotta i havet och på marken. I den här instrumentpanelen ser du två olika gateways som tillhandahåller telemetri om leveranser tillsammans med associerade kommandon, jobb och åtgärder som du kan göra. Den här instrumentpanelen är förkonfigurerad för att visa upp aktiviteten för kritiska logistikenheter.
Instrumentpanelen är logiskt fördelad mellan två olika gateway device management-åtgärder, 
   * Logistikrutten för lastbilstransporter och platsinformation om sjötransporten är en viktig del för alla multimodala transporter
   * Visa gatewaystatus & relevant information 

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Du kan enkelt spåra det totala antalet gateways, aktiva och okända taggar.
   * Du kan utföra enhetshanteringsåtgärder som uppdatering av inbyggd programvara, inaktivera sensor, aktivera sensor, tröskelvärde för uppdateringssensor, uppdatera telemetriintervall, & uppdatera enhetstjänstkontrakt.
   * Visa batteriförbrukning för enheten

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Enhetsmall

Klicka på fliken Enhetsmallar så visas hanterarens modell. En kapacitetsmodell är uppbyggd kring två olika gränssnitt **Gateway Telemetri & Property** and Gateway **Commands**

**Gateway Telemetri & Egenskap** - Det här gränssnittet representerar alla telemetri relaterade till sensorer, plats och enhetsinformation samt enhetstvillingegenskapskapacitet, till exempel sensortrösklar & uppdateringsintervall.

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Gateway-kommandon** - Det här gränssnittet organiserar alla kommandofunktioner för gateway

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Regler
Välj fliken Regler om du vill visa två olika regler som finns i den här programmallen. Dessa regler är konfigurerade för e-postmeddelanden till operatörerna för ytterligare undersökningar.
 
**Gateway stöld varning:** Denna regel utlöses när det finns oväntade ljusdetektering av sensorerna under resan. Operatörer måste underrättas ASAP för att undersöka eventuell stöld.
 
**Svarar inte gateway:** Den här regeln utlöses om gatewayen inte rapporterar till molnet under en längre period. Gateway kan inte svara på grund av låg batteriläge, förlust av anslutning, enhetens hälsa.

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Jobb
Välj fliken Jobb om du vill visa fem olika jobb som finns som en del av den här programmallen:

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Du kan använda jobbfunktionen för att utföra lösningsomfattande åtgärder. Här jobb använder enheten kommandon och dubbla funktioner för att göra uppgifter som att inaktivera specifika sensorer över hela gatewayen eller ändra sensorn tröskeln beroende på leveransläge och rutt. 
   * Det är en standardoperation för att inaktivera stötsensorer under sjötransporter för att spara på batteriet eller sänka temperaturtröskeln under transport av kallkedjan. 
 
   * Med jobb kan du utföra systemomfattande åtgärder, till exempel uppdatera inbyggd programvara på gateways eller uppdatera servicekontrakt för att hålla dig uppdaterad om underhållsaktiviteter.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte kommer att fortsätta att använda det här programmet tar **Delete**du bort programmallen genom att besöka inställningar för **Administrationsprogram** > **Application settings** och klicka på Ta bort .

> [!div class="mx-imgBorder"]
> ![Instrumentpanel för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om [uppkopplat logistikkoncept](./architecture-connected-logistics.md)
* Läs mer om andra [IoT Central-butiksmallar](./overview-iot-central-retail.md)
* Läs mer om [IoT Central översikt](../core/overview-iot-central.md)
