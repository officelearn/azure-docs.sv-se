---
title: Självstudie över hantering av Smart inventering i IoT | Microsoft Docs
description: En själv studie kurs om program mal len för smart inventerings hantering för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 8360db3e31899dc7ca0a2b502d0036358f0858d6
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615235"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Självstudie: Distribuera och gå igenom en mall för hantering av Smart inventerings program

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här självstudien visar hur du kommer igång genom att distribuera en IoT Central Mall för **Smart inventerings hantering** . Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här självstudien får du lära dig att 
* Skapa program för smart inventerings hantering 
* gå igenom programmet 

## <a name="prerequisites"></a>Nödvändiga komponenter
* Inga särskilda krav krävs för att distribuera den här appen
* Vi rekommenderar att du har en Azure-prenumeration, men du kan även prova utan den

## <a name="create-smart-inventory-management-application-template"></a>Skapa program mal len för smart inventerings hantering

Du kan skapa program med följande steg
1. Gå till webbplatsen för Azure IoT Central Application Manager. Välj **Bygg** i det vänstra navigerings fältet och klicka sedan på fliken **detalj handel** .

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Välj fliken **detalj handel** och välj **skapa app** under * * Smart inventerings hantering * *

3. **Skapa app** öppnar nytt program formulär och fyller i den begärda informationen som visas nedan.
   **Program namn**: du kan använda det föreslagna standard namnet eller ange ett eget program namn.
   **URL**: du kan använda den föreslagna standard-URL: en eller ange en egen, unik minnes adress. Sedan rekommenderas standardinställningen om du redan har en Azure-prenumeration eller om du kan börja med en kostnads fri utvärderings version på sju dagar och välja att konvertera betala per användning när som helst innan det kostnads fria förfallo datumet.
   **Fakturerings information**: katalog, Azure-prenumeration och regions information krävs för att etablera resurserna.
   **Skapa**: Välj Skapa längst ned på sidan för att distribuera programmet.

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

## <a name="walk-through-the-application"></a>gå igenom programmet 

### <a name="dashboard"></a>Instrumentpanel 
När du har distribuerat app-mallen är standard instrument panelen en fokuserad Portal för smart inventerings hantering. Northwind-näringsidkare är en fiktiv Smart Inventory Provider för hantering av smarta lager med Bluetooth låg energi (Bell) och Retail Store med radio frekvens identifiering (RFID). På den här instrument panelen kan du se två olika gateways som tillhandahåller telemetri om inventering tillsammans med tillhör ande kommandon, jobb och åtgärder som du kan utföra. Den här instrument panelen är förkonfigurerad för att visa den kritiska aktiviteten för enhets åtgärder för smart inventerings hantering.
Instrument panelen delas logiskt mellan två olika hanterings åtgärder för gateway-enheter, 
   * Lagret distribueras med en fast Bell-Gateway & Bell-taggar på lastpallar för att spåra & spårnings inventering vid en större anläggning
   * Butiks lager implementeras med en fast RFID-Gateway & RFID-taggar på enskilda objekt nivåer för att spåra och spåra lagret i ett lager uttag
   * Visa Gateway-platsen, status & relaterad information 

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Du kan enkelt spåra det totala antalet gatewayer, aktiva och okända taggar.
   * Du kan utföra enhets hanterings åtgärder som uppdaterings-firmware, inaktivera sensor, aktivera sensor, uppdatera sensor tröskel, uppdatera telemetri & Uppdatera enhets tjänst kontrakt
   * Gateway-enheter kan utföra lager hantering på begäran med en fullständig eller stegvis genomsökning.

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Enhets mall
Klicka på fliken Mallar på enheten så visas kapacitets modellen för gateway. En kapacitets modell är strukturerad runt två olika gränssnitts- **Gateway-telemetri & egenskaper** och **Gateway-kommandon**

**& egenskap för gateway-telemetri** – det här gränssnittet representerar all telemetri som rör sensorer, plats, enhets information samt enhetens dubbla egenskaps funktion, till exempel gateway-tröskelvärden & uppdaterings intervall.

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Gateway-kommandon** – det här gränssnittet organiserar alla gatewayens kommando funktioner

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regler
Välj fliken regler för att se två olika regler som finns i den här program mal len. Dessa regler är konfigurerade för e-postaviseringar till operatörerna för ytterligare undersökningar.

**Gateway offline**: den här regeln utlöses om gatewayen inte rapporterar till molnet under en längre period. Det gick inte att svara på gatewayen på grund av låg energi läge, förlust av anslutning, enhets hälsa.

**Okända Taggar**: det är viktigt att spåra alla RFID-& Bell-taggar som är kopplade till en till gång. Om gatewayen identifierar för många okända taggar, är det här en indikation på utmaningarna för synkronisering med tag-käll program.

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Jobb
Välj fliken jobb för att se fem olika jobb som finns som en del av den här program mal len: du kan utnyttja jobb funktionen för att utföra åtgärder i hela lösningen. Dessa lager hanterings jobb använder enhets kommandona & dubbla funktioner för att utföra uppgifter som,
   * Inaktivera läsare över hela gatewayen
   * ändrar tröskelvärdet för telemetri mellan 
   * utför lager genomsökning på begäran i hela lösningen.

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len genom att gå till **Administration** > **program inställningar** och klickar på **ta bort**.

> [!div class="mx-imgBorder"]
> ![Smart Inventory Management-instrumentpanel](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om Smart Inventory Management- [koncept för smart inventering](./architecture-smart-inventory-management-pnp.md)
* Läs mer om andra [IoT Central mallar för butik](./overview-iot-central-retail-pnp.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../core/overview-iot-central-pnp.md)
