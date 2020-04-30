---
title: Självstudie över IoT Connectd logistik | Microsoft Docs
description: En själv studie kurs om den anslutna logistik program mal len för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: eac43ae68b10436b3e45452c6b1d03bec3ae4c9c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000562"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Självstudie: Distribuera och gå igenom en ansluten logistik-Programmall



Den här självstudien visar hur du kommer igång genom att distribuera en IoT Central **ansluten logistik** -Programmall. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här självstudien får du lära dig hur du,

* skapa ett anslutet logistik program
* gå igenom programmet 

## <a name="prerequisites"></a>Krav

* Inga särskilda krav krävs för att distribuera den här appen
* Vi rekommenderar att du har en Azure-prenumeration, men du kan även prova utan den

## <a name="create-connected-logistics-application-template"></a>Skapa mall för kopplad logistik-app

Du kan skapa program med följande steg

1. Gå till webbplatsen för Azure IoT Central Application Manager. Välj **Bygg** i det vänstra navigerings fältet och klicka sedan på fliken **detalj handel** .

    > [!div class="mx-imgBorder"]
    > ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Välj **skapa app** under **ansluten logistik program**

3. **Skapa app** öppnar nytt program formulär och fyller i den begärda informationen som visas nedan.
   * **Program namn**: du kan använda det föreslagna standard namnet eller ange ett eget program namn.
   * **URL**: du kan använda den föreslagna standard-URL: en eller ange en egen, unik minnes adress. Sedan rekommenderas standardinställningen om du redan har en Azure-prenumeration. Du kan börja med en kostnads fri utvärderings prenumerations plan på 7 dagar och välja att konvertera till en standard pris plan när som helst innan det kostnads fria slutet går ut.
   * **Fakturerings information**: katalog, Azure-prenumeration och regions information krävs för att etablera resurserna.
   * **Skapa**: Välj Skapa längst ned på sidan för att distribuera programmet.

    > [!div class="mx-imgBorder"]
    > ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![Fakturerings information för ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Gå igenom programmet 

## <a name="dashboard"></a>Instrumentpanel

När du har distribuerat app-mallen är standard instrument panelen en fokuserad portal som är fokuserad på logistik operatör. Northwind-näringsidkare är en fiktiv logistik leverantör som hanterar Last flottan i havet och på marken. På den här instrument panelen kan du se två olika gateways som tillhandahåller telemetri om leveranser tillsammans med tillhör ande kommandon, jobb och åtgärder som du kan göra. Den här instrument panelen är förkonfigurerad för att demonstrera den kritiska logistik enhetens Operations-aktivitet.
Instrument panelen delas logiskt mellan två olika hanterings åtgärder för gateway-enheter, 
   * Logistik väg för leverans av laster och plats information för leverans av frakten är ett viktigt element för all den multi-modala transporten
   * Visa Gateway-status & relevant information 

> [!div class="mx-imgBorder"]
> ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Du kan enkelt spåra det totala antalet gatewayer, aktiva och okända taggar.
   * Du kan utföra enhets hanterings åtgärder, till exempel uppdatera inbyggd program vara, inaktivera sensor, aktivera sensor, uppdatera sensor tröskel, uppdatera telemetridata, & uppdatera enhets Service kontrakt.
   * Visa enhetens batteri förbrukning

> [!div class="mx-imgBorder"]
> ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Enhets mall

Klicka på fliken Mallar på enheten så visas kapacitets modellen för gateway. En kapacitets modell är strukturerad runt två olika gränssnitts- **Gateway-telemetri & egenskaper** och **Gateway-kommandon**

**& egenskap för gateway-telemetri** – det här gränssnittet representerar all telemetri som rör sensorer, plats och enhets information samt enhetens dubbla egenskaps funktion, till exempel sensor tröskelvärden & uppdaterings intervall.

> [!div class="mx-imgBorder"]
> ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Gateway-kommandon** – det här gränssnittet organiserar alla gatewayens kommando funktioner

> [!div class="mx-imgBorder"]
> ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Regler
Välj fliken regler för att se två olika regler som finns i den här program mal len. Dessa regler är konfigurerade för e-postaviseringar till operatörerna för ytterligare undersökningar.
 
**Avisering om Gateway-stöld**: den här regeln utlöses när det finns oväntad ljus identifiering av sensorer under resan. Operatörer måste meddelas snart för att undersöka potentiell stöld.
 
**Svarar**inte på Gateway: den här regeln utlöses om gatewayen inte rapporterar till molnet under en längre period. Det gick inte att svara på gatewayen på grund av låg energi läge, anslutningens hälso status och enhetens hälso tillstånd.

> [!div class="mx-imgBorder"]
> ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Jobb
Välj fliken jobb för att se fem olika jobb som finns som en del av den här program mal len:

> [!div class="mx-imgBorder"]
> ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Du kan använda jobb funktionen om du vill utföra åtgärder i hela lösningen. Följande jobb använder enhets kommandona och den dubbla funktionen för att utföra åtgärder, till exempel inaktivera vissa sensorer i gatewayen eller ändra sensor tröskeln beroende på leverans läge och väg. 
   * Det är en standard åtgärd för att inaktivera stötande sensorer under skeppning av havet för att spara batteri eller minska temperatur tröskeln under transport av kall kedja. 
 
   * Med jobb kan du utföra systemomfattande åtgärder som att uppdatera den inbyggda program varan på gatewayerna eller uppdatera service kontraktet för att hålla dig uppdaterad om underhålls aktiviteter.

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len genom att gå till **administrations** > **program inställningar** och klicka på **ta bort**.

> [!div class="mx-imgBorder"]
> ![Ansluten logistik instrument panel](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [anslutna logistik konceptet](./architecture-connected-logistics.md)
* Läs mer om andra [IoT Central mallar för butik](./overview-iot-central-retail.md)
* Läs mer om [IoT Central översikt](../core/overview-iot-central.md)
