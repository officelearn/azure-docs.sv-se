---
title: Självstudie över IoT Connectd logistik | Microsoft Docs
description: En själv studie kurs om den anslutna logistik program mal len för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 9d55eca3df8897650f78a6bd85b649349c806e95
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615255"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Självstudie: Distribuera och gå igenom en ansluten logistik-Programmall

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här självstudien visar hur du kommer igång genom att distribuera en IoT Central **ansluten logistik** -Programmall. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här självstudien får du lära dig att 
* skapa ett anslutet logistik program 
* gå igenom programmet 

## <a name="prerequisites"></a>Nödvändiga komponenter
* Inga särskilda krav krävs för att distribuera den här appen
* Vi rekommenderar att du har en Azure-prenumeration, men du kan även prova utan den

## <a name="create-connected-logistics-application-template"></a>Skapa mall för kopplad logistik-app
Du kan skapa program med följande steg
1. Gå till webbplatsen för Azure IoT Central Application Manager. Välj **Bygg** i det vänstra navigerings fältet och klicka sedan på fliken **detalj handel** .

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Välj **skapa app** under **ansluten logistik program**

3. **Skapa app** öppnar nytt program formulär och fyller i den begärda informationen som visas nedan.
   * **Program namn**: du kan använda det föreslagna standard namnet eller ange ett eget program namn.
   * **URL**: du kan använda den föreslagna standard-URL: en eller ange en egen, unik minnes adress. Sedan rekommenderas standardinställningen om du redan har en Azure-prenumeration. annars kan du börja med en kostnads fri utvärderings version på sju dagar och välja att konvertera betala per användning när som helst innan den kostnads fria giltighets tiden.
   * **Fakturerings information**: katalog, Azure-prenumeration och regions information krävs för att etablera resurserna.
   * **Skapa** : Välj Skapa längst ned på sidan för att distribuera programmet.

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

## <a name="walk-through-the-application"></a>gå igenom programmet 

## <a name="dashboard"></a>Instrumentpanel

När du har distribuerat app-mallen är standard instrument panelen en fokuserad portal som är fokuserad på logistik operatör. Northwind-näringsidkare är en fiktiv logistik leverantör som hanterar Last flottan i havet och på marken. På den här instrument panelen kan du se två olika gateways som tillhandahåller telemetri om leveranser tillsammans med tillhör ande kommandon, jobb och åtgärder som du kan utföra. Den här instrument panelen är förkonfigurerad för att demonstrera den kritiska logistik enhetens Operations-aktivitet.
Instrument panelen delas logiskt mellan två olika hanterings åtgärder för gateway-enheter, 
   * Logistik väg för leverans av laster och plats information för leverans av frakten är ett viktigt element för all den multi-modala transporten
   * Visa Gateway-status & relevant information 

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Du kan enkelt spåra det totala antalet gatewayer, aktiva och okända taggar.
   * Du kan utföra enhets hanterings åtgärder som uppdaterings-firmware, inaktivera sensor, aktivera sensor, uppdatera sensor tröskel, uppdatera telemetridata, & Uppdatera enhets tjänst kontrakt.
   * Visa enhetens batteri förbrukning

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Enhets mall

Klicka på fliken Mallar på enheten så visas kapacitets modellen för gateway. En kapacitets modell är strukturerad runt två olika gränssnitts- **Gateway-telemetri & egenskaper** och **Gateway-kommandon**

**& egenskap för gateway-telemetri** – det här gränssnittet representerar all telemetri som rör sensorer, plats och enhets information samt enhetens dubbla egenskaps funktioner, till exempel sensor tröskelvärden & uppdaterings intervall.

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Gateway-kommandon** – det här gränssnittet organiserar alla gatewayens kommando funktioner

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Regler
Välj fliken regler för att se två olika regler som finns i den här program mal len. Dessa regler är konfigurerade för e-postaviseringar till operatörerna för ytterligare undersökningar.
 
**Avisering om Gateway-stöld**: den här regeln utlöses när det finns oväntad ljus identifiering av sensorer under resan. Operatörer måste meddelas snart för att undersöka potentiell stöld.
 
**Svarar**inte på Gateway: den här regeln utlöses om gatewayen inte rapporterar till molnet under en längre period. Det gick inte att svara på gatewayen på grund av låg energi läge, förlust av anslutning, enhets hälsa.

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Jobb
Välj fliken jobb för att se fem olika jobb som finns som en del av den här program mal len:

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Du kan utnyttja jobb funktionen för att utföra åtgärder i hela lösningen. Följande jobb använder enhets kommandona & dubbla funktioner för att utföra åtgärder, till exempel inaktivera vissa sensorer i hela gatewayen eller ändra sensor tröskeln beroende på leverans läge och väg. 
   * Det är en standard åtgärd för att inaktivera stötande sensorer under skeppning av havet för att spara batteri eller minska temperatur tröskeln under transport av kall kedja. 
 
   * Med jobb kan du utföra systemomfattande åtgärder som att uppdatera den inbyggda program varan på gatewayerna eller uppdatera service kontraktet för att hålla dig uppdaterad om underhålls aktiviteter.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len genom att gå till **Administration** > **program inställningar** och klickar på **ta bort**.

> [!div class="mx-imgBorder"]
> ![ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [anslutna logistik konceptet](./architecture-connected-logistics-pnp.md)
* Läs mer om andra [IoT Central mallar för butik](./overview-iot-central-retail-pnp.md)
* Läs mer om [IoT Central översikt](../core/overview-iot-central-pnp.md)
