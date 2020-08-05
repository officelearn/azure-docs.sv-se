---
title: Azure IoT Connector för FHIR (för hands version) – fel söknings guide och anvisningar
description: Så här felsöker du vanliga Azure IoT Connector för FHIR (förhands granskning) fel meddelanden och villkor och kopiera mappnings-filer
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jasteppe
ms.openlocfilehash: b404fa5322d3afa8cbf71741382d44dd0433d49c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553690"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Fel söknings guide för Azure IoT Connector för FHIR (för hands version)

Den här artikeln innehåller steg för att felsöka vanliga Azure IoT-anslutningar för FHIR * fel meddelanden och villkor.  

Du lär dig också hur du skapar kopior av Azure IoT Connector för FHIR JSON-Mapping-filer för redigering och arkivering utanför Azure Portal eller för att tillhandahålla teknisk support för Azure när du öppnar ett support ärende. 

## <a name="error-messages-and-fixes"></a>Fel meddelanden och korrigeringar

|Meddelande   |Villkor  |Åtgärda         |
|----------|-----------|------------|
|Ogiltigt mappnings namn, mappnings namn ska vara enhets-eller FHIR|Den angivna mappnings typen är inte enhets-eller FHIR|Använd en av de två mappnings typer som stöds (till exempel: enhet eller FHIR)|
|Återskapa nyckel parametrar har inte definierats|Återskapa nyckel förfrågan|Inkludera parametrarna i begäran om att skapa en nyckel|
|Det maximala antalet IoT Connector *-instanser som kan tillhandahållas i den här prenumerationen har uppnåtts|Prenumerations kvoten för Azure IoT Connector för FHIR har nåtts (standard är (2) per prenumeration)|Ta bort en av de befintliga instanserna av Azure IoT Connector för FHIR, Använd en annan prenumeration som inte har nått prenumerations kvoten eller begär en ökad prenumerations kvot|
|Flytt av resurs stöds inte för IoT Connector aktiverat Azure API för FHIR-resursen|Försöker utföra en flyttnings åtgärd på en Azure API för FHIR-resurs som har en eller flera instanser av Azure IoT Connector för FHIR|Ta bort befintliga instanser av Azure IoT Connector för att FHIR ska kunna utföras och slutföra flyttnings åtgärden|
|IoT-kopplingen är inte etablerad|Försök att använda underordnade tjänster (anslutningar & mappningar) när överordnad (Azure IoT Connector för FHIR) inte har etablerats|Etablera en Azure IoT-anslutning för FHIR|
|Begäran stöds inte|En speciell API-begäran stöds inte|Använd rätt API-begäran|
|Kontot finns inte|Försök att lägga till en Azure IoT-anslutning för FHIR och Azure API för FHIR-resursen finns inte|Skapa Azure API för FHIR-resursen och försök sedan igen|
|Azure API för FHIR Resource FHIR-versionen stöds inte för IoT Connector|Försök att använda en Azure IoT-anslutning för FHIR med en inkompatibel version av Azure API för FHIR-resursen|Skapa en ny Azure API för FHIR-resurs (version R4) eller Använd en befintlig Azure API för FHIR-resurs (version R4)

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Skapa kopior av mapparna Azure IoT Connector för FHIR (för hands version)
Kopieringen av Azure IoT Connector för FHIR-mappningar kan vara användbar för att redigera och arkivera utanför Azure Portal webbplats och för att ge teknisk support för Azure när du öppnar ett support ärende.

> [!NOTE]
> JSON är det enda format som stöds för enhets-och FHIR-mappning av filer för tillfället.

> [!TIP]
> Lär dig mer om Azure IoT Connector för FHIR- [enhet och FHIR-mappning av JSON-filer](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Välj **"IoT Connector (förhands granskning)"** på den nedre vänstra sidan av Azure-API: t för FHIR resurs instrument panel i avsnittet **"tillägg"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-koppling" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Välj **"Connector"** som du vill kopiera mappnings filerna från.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-koppling" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Den här processen kan också användas för att kopiera och spara innehållet i JSON-filen **"Konfigurera FHIR-mappning"** .

3. Välj **Konfigurera enhets mappning**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-koppling" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Välj innehållet i JSON och gör en kopierings åtgärd (till exempel: Välj CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-koppling" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Gör en Inklistrings åtgärd (till exempel: Välj Ctrl + v) i en ny fil i en redigerare (till exempel Visual Studio Code, Notepad) och spara filen med ett *. JSON-tillägg.

> [!TIP]
> Om du kommer att öppna ett [Azures tekniska support](https://azure.microsoft.com/support/create-ticket/) ärende för Azure IoT-ANSLUTNINGSPROGRAMMET för FHIR, måste du ta med kopior av dina mappnings filer för att hjälpa till med fel söknings processen.

## <a name="next-steps"></a>Nästa steg

Ta en titt på vanliga frågor om Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Vanliga frågor och svar om Azure IoT Connector för FHIR](fhir-faq.md#azure-iot-connector-for-fhir-preview)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version).

FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.