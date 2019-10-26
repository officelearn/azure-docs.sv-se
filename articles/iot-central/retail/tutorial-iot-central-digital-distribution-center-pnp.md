---
title: Själv studie kurs om IoT Digital Distribution Center | Microsoft Docs
description: En själv studie kurs om program mal len för digital distributions Center för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 20ed04efc1d10e419148cb4f6c75c3eab4ab40a6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957918"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Självstudie: Distribuera och gå igenom en app-mall för digital distributions Center

Den här självstudien visar hur du kommer igång genom att distribuera en IoT Central **digital distributions Center** -Programmall. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

## <a name="details"></a>Information

I den här självstudien får du lära dig att 
* Skapa ett program för digital distributions Center 
* gå igenom programmet 

## <a name="prerequisites"></a>Krav
* Inga särskilda krav krävs för att distribuera den här appen
* Vi rekommenderar att du har en Azure-prenumeration, men du kan även prova utan den

## <a name="create-digital-distribution-center-application-template"></a>Skapa app-mall för digital distributions Center

Du kan skapa program med följande steg

1. Gå till webbplatsen för Azure IoT Central Application Manager. Välj **Bygg** i det vänstra navigerings fältet och klicka sedan på fliken **detalj handel** .

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Välj fliken **butik** och välj **skapa app** under * * Digital distributions Center program * *

3. **Skapa app** öppnar nytt program formulär och fyller i den begärda informationen som visas nedan.
   **Program namn**: du kan använda det föreslagna standard namnet eller ange ett eget program namn.
   **URL**: du kan använda den föreslagna standard-URL: en eller ange en egen, minnes värt URL. Sedan rekommenderas standardinställningen om du redan har en Azure-prenumeration. Annars kan du börja med en kostnads fri utvärderings period på sju dagar och välja att konvertera betala per användning när som helst innan det kostnads fria förfallo datumet.
   **Fakturerings information**: katalog, Azure-prenumeration och regions information krävs för att etablera resurserna.
   **Skapa**: Välj Skapa längst ned på sidan för att distribuera programmet.

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-create.png)

## <a name="walk-through-the-application"></a>gå igenom programmet 

## <a name="dashboard"></a>Instrumentpanel 

När du har distribuerat app-mallen är standard instrument panelen en distributions Center operatör med fokus på portalen. Northwind-näringsidkare är en fiktiv distributions Center lösnings leverantör som hanterar transport band system. 

På den här instrument panelen visas en Edge-gateway och en kamera som fungerar som en IoT-enhet. Gateway tillhandahåller telemetri om paket som giltig, ogiltig, oidentifierad och storlek tillsammans med tillhör ande enhets dubbla egenskaper. Alla underordnade kommandon körs på IoT-enheter, till exempel en kamera. Den här instrument panelen är förkonfigurerad för att demonstrera den kritiska distributions Center-aktiviteten för enhets åtgärder.

Instrument panelen är logiskt strukturerad för att Visa enhets hanterings funktionerna i Azure IoT Edge gateway och IoT-enheten.  
   * Du kan utföra kommandot Edge Gateway & kontroll uppgifter
   * Hantera alla kameror som ingår i lösningen. 

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Enhets mall

Klicka på fliken **mallar på enheten** så visas de distinkta enhets mallarna för Azure IoT Edge & kamera. 

Enhets mal len Azure IoT Edge Gateway representerar en skiss som definierar egenskaperna och beteendet för en Edge gateway-enhet. Några av komponenterna i en gräns enhet är,
   * Distributionsmanifest
   * Relation med underordnade enheter
   * Program varu moduler
   * Moduler egenskaper & kommandon 

Distributions manifestet innehåller en lista över de programmoduler som ska köras i modulen Azure IoT Edge Device & som har kon figurer ATS med önskade egenskaper. Använd distributions manifestet som är en del av enhets mal len, Azure IoT Edge kör tid vet vilka moduler som ska installeras och hur de ska konfigureras så att de fungerar tillsammans.

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

I den här enhets mal len kan du se modulens enhets kapacitets modeller genereras från distributions manifestet. 

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-devicetemplate2.png)

Här kan du lägga till underordnade enhets relationer, moln egenskap, skapa vyer längs anpassa modul och enhets funktioner.

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-moduletemplate.png)

Klicka på fliken **enhet** och välj den **ddcgateway** -enhet som är kopplad till Digital distributions Center Azure IoT Edge. Här visas anpassade Edge-moduler & underordnad kamera som är del av enhets relationen. IoT Edge moduler är den minsta beräknings enheten, och den kan innehålla Azure-tjänster (till exempel Azure Stream Analytics, AI-moduler) eller din lösnings bara kod.

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-modules.png)

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-downstream.png)

## <a name="gateway-commands"></a>Gateway-kommandon
Det här gränssnittet ordnar alla funktioner i Gateway-kommandot

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regler
Välj fliken regler för att se två olika regler som finns i den här program mal len. Dessa regler är konfigurerade för e-postaviseringar till operatörerna för ytterligare undersökningar.
 **Avisering om för många ogiltiga paket** – den här regeln utlöses när kameran identifierar ett stort antal ogiltiga paket som flödar genom transport systemet.
 
**Stort paket** – den här regeln utlöses om kameran identifierar enorma paket som inte kan kontrol leras för kvaliteten. 

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Jobb
Välj fliken jobb för att se fem olika jobb som finns som en del av den här program mal len: du kan utnyttja jobb funktionen för att utföra åtgärder i hela lösningen. Här är digitala distributions Center jobb använder enhets kommandona & dubbla funktioner för att utföra uppgifter som,
   * Kalibrera kameran innan du påbörjar paket identifieringen 
   * regelbundet uppdatera inbyggd kamera
   * ändra intervallet för telemetri för att hantera data uppladdning

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len genom att gå till **Administration** > **program inställningar** och klickar på **ta bort**.

> [!div class="mx-imgBorder"]
> ![Digital distributions Center](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om digital distributions Center lösnings arkitektur för [digital distributions Center](./architecture-digital-distribution-center-pnp.md)
* Läs mer om andra [IoT Central mallar för butik](./overview-iot-central-retail-pnp.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../core/overview-iot-central-pnp.md)
