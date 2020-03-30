---
title: Handledning av IoT Digital Distribution Center | Microsoft-dokument
description: En självstudiekurs av programmall för digital distributionscenter för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 93a77d73b5cc249c39609f98f055a7b1927dd6ed
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77025494"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Självstudiekurs: Distribuera och gå igenom en programmall för ett program för digitala distributionscenter



Den här självstudien visar hur du kommer igång genom att distribuera en IoT Central **digital distributionscenter** programmall. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här självstudien lär du dig att 
* Skapa program för digitala distributionscenter 
* Gå igenom programmet 

## <a name="prerequisites"></a>Krav
* Inga specifika förutsättningar krävs för att distribuera den här appen
* Rekommenderas att ha Azure-prenumeration, men du kan även prova utan den

## <a name="create-digital-distribution-center-application-template"></a>Skapa programmall för programmall för digital distributionscenter

Du kan skapa program med följande steg

1. Navigera till Azure IoT Central application manager webbplats. Välj **Skapa** i det vänstra navigeringsfältet och klicka sedan på fliken **Butik.**

    > [!div class="mx-imgBorder"]
    > ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Välj **fliken Butik** och välj Skapa **app** under program för **digitala distributionscenter**

3. **Skapa app** öppnar Nytt ansökningsformulär och fyll i de begärda uppgifterna som visas nedan.
   **Programnamn:** Du kan använda standardnamn eller ange ditt eget programnamn.
   **URL**: Du kan använda föreslagen standard-URL eller ange din vänliga unika minnesvärda webbadress. Därefter rekommenderas standardinställningen om du redan har en Azure-prenumeration. Du kan börja med 7-dagars gratis testperiod prisplan och välja att konvertera till en standard prisplan när som helst innan den fria spåret löper ut.
   **Faktureringsinformation**: Information om katalog, Azure-prenumeration och region krävs för att etablera resurserna.
   **Skapa:** Välj skapa längst ned på sidan för att distribuera programmet.

    > [!div class="mx-imgBorder"]
    > ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-create.png)

    > [!div class="mx-imgBorder"]
    > ![Faktureringsinformation för digital distribution](./media/tutorial-iot-central-ddc/ddc-create-billinginfo.png)

## <a name="walk-through-the-application-dashboard"></a>Gå igenom programinstrumentpanelen 

När du har distribuerat appmallen är standardinstrumentpanelen en portal för distributionscenteroperatör. Northwind Trader är en fiktiv distributionscentral lösning leverantör hantera transportörsystem. 

I den här instrumentpanelen ser du en gateway och en kamera som fungerar som en IoT-enhet. Gateway tillhandahåller telemetri om paket som giltiga, ogiltiga, oidentifierade och storlek tillsammans med associerade enhetstvillingegenskaper. Alla nedströmskommandon körs på IoT-enheter, till exempel en kamera. Den här instrumentpanelen är förkonfigurerad för att visa upp aktiviteten för kritiska distributionscenterenheter.

Instrumentpanelen är logiskt ordnad för att visa enhetshanteringsfunktionerna för Azure IoT-gatewayen och IoT-enheten.  
   * Du kan utföra gateway-kommando & kontrolluppgifter
   * Hantera alla kameror som ingår i lösningen. 

> [!div class="mx-imgBorder"]
> ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Enhetsmall

Klicka på fliken Enhetsmallar så visas hanterarens modell. En kapacitetsmodell är uppbyggd kring två olika gränssnitt **Kamera** och **Digital Distribution Gateway**

> [!div class="mx-imgBorder"]
> ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** - Det här gränssnittet organiserar alla kameraspecifika kommandofunktioner 

> [!div class="mx-imgBorder"]
> ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Digital Distribution Gateway** - Det här gränssnittet representerar all telemetri som kommer från kamera, molndefinierade enhetstvillingegenskaper och gatewayinformation.

> [!div class="mx-imgBorder"]
> ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Gateway-kommandon
Det här gränssnittet organiserar alla kommandofunktioner för gateway

> [!div class="mx-imgBorder"]
> ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regler
Välj fliken Regler om du vill visa två olika regler som finns i den här programmallen. Dessa regler är konfigurerade för e-postmeddelanden till operatörerna för ytterligare undersökningar.

 **För många ogiltiga paketavisering** - Den här regeln utlöses när kameran upptäcker ett stort antal ogiltiga paket som flödar genom transportörsystemet.
 
**Stort paket** - Den här regeln utlöser om kameran upptäcker ett enormt paket som inte kan inspekteras för kvaliteten. 

> [!div class="mx-imgBorder"]
> ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Jobb
Välj fliken jobb om du vill visa fem olika jobb som finns som en del av den här programmallen: Du kan utnyttja jobbfunktionen för att utföra lösningsomfattande åtgärder. Här använder jobb för digitala distributionscenter enhetskommandon & dubbla funktioner för att utföra uppgifter som
   * kalibrera kameran innan paketdetekteringen påbörjas 
   * regelbundet uppdatera kamerans firmware
   * ändra telemetriintervallet för att hantera dataöverföring

> [!div class="mx-imgBorder"]
> ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte kommer att fortsätta att använda det här programmet tar **Delete**du bort programmallen genom att besöka inställningar för **Administrationsprogram** > **Application settings** och klicka på Ta bort .

> [!div class="mx-imgBorder"]
> ![Digitalt distributionscenter](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om digital distributionscenter lösning arkitektur [digital distribution center koncept](./architecture-digital-distribution-center-pnp.md)
* Läs mer om andra [IoT Central-butiksmallar](./overview-iot-central-retail-pnp.md)
* Läs mer om IoT Central se [IoT Central översikt](../core/overview-iot-central.md)
