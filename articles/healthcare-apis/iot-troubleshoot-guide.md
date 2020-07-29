---
title: Azure IoT Connector för FHIR (IoT Connector) – fel söknings guide och anvisningar
description: Så här felsöker du vanliga IoT Connector-fel meddelanden och-villkor och kopierar mappnings-filer
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: eff1272318413da7855134b0a8a44dd0a0711a6c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285585"
---
# <a name="iot-connector-preview-troubleshooting-guide"></a>IoT Connector (för hands version) fel söknings guide

Den här artikeln innehåller steg för att felsöka vanliga IoT Connector (för hands version) fel meddelanden och villkor.  

Du lär dig också hur du skapar kopior av IoT Connector (Preview) JSON-mappning av filer för redigering och arkivering utanför Azure Portal.

## <a name="error-messages-and-fixes"></a>Fel meddelanden och korrigeringar

|Meddelande   |Villkor  |Åtgärda         |
|----------|-----------|------------|
|Ogiltigt mappnings namn, mappnings namn ska vara enhets-eller FHIR|Den angivna mappnings typen är inte enhets-eller FHIR|Använd en av de två mappnings typer som stöds (till exempel: enhet eller FHIR)|
|Återskapa nyckel parametrar har inte definierats|Återskapa nyckel förfrågan|Inkludera parametrarna i begäran om att skapa en nyckel|
|Det maximala antalet IoT Connector-instanser som kan tillhandahållas i den här prenumerationen har uppnåtts|Prenumerations kvoten för IoT-kopplingen har nåtts (standard är 2 per prenumeration)|Ta bort en av de befintliga anslutningarna, Använd en annan prenumeration som inte har nått (2) anslutningar per prenumerations kvot eller begär en ökad prenumerations kvot|
|Flytt av resurs stöds inte för IoT Connector aktiverat Azure API för FHIR-resursen|Försöker utföra en flyttnings åtgärd på en Azure API för FHIR-resurs som har en eller flera IoT-kopplingar|Ta bort befintliga anslutningar för att utföra/slutföra flytt åtgärden (s)|
|En privat länk har Aktiver ATS för Azure API för FHIR-resursen.  Privat länk stöds inte med IoT Connector|Försök att lägga till en IoT-koppling till en Azure API för FHIR-resurs som har privat länk aktive rad|Välj eller skapa en Azure API för FHIR-resurs (version R4) som inte har någon privat länk aktive rad|
|IoT-kopplingen är inte etablerad|Försök att använda underordnade tjänster (anslutningar & mappningar) när överordnad (IoT Connector) inte har etablerats|Etablera en IoT-koppling|
|Begäran stöds inte|En speciell API-begäran stöds inte|Använd rätt API-begäran|
|Kontot finns inte|Försök att lägga till en IoT-koppling och Azure API för FHIR-resursen finns inte|Skapa Azure API för FHIR-resursen och försök sedan igen|
|Azure API för FHIR Resource FHIR-versionen stöds inte för IoT Connector|Försök att använda en IoT-koppling med en inkompatibel version av Azure API för FHIR-resursen|Skapa en ny Azure API för FHIR-resurs (version R4) eller Använd en befintlig Azure API för FHIR-resurs (version R4)

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>Skapa kopior av mappnings filen för IoT Connector (för hands version)
> [!NOTE]
> JSON är det enda format som stöds för enhets-och FHIR-mappning av filer för tillfället.

> [!TIP]
> Kopiering av IoT Connector-mappningar kan vara användbart för att redigera och arkivera utanför Azure Portal-webbplatsen och för att ge teknisk support för Azure möjlighet att öppna ett support ärende.
> 
> Lär dig mer om IoT Connector- [enheten och FHIR-mappning av JSON-filer](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Välj **"IoT Connector (förhands granskning)"** på den nedre vänstra sidan av Azure-API: t för FHIR resurs instrument panel i avsnittet **"tillägg"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-koppling" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Välj **"Connector"** som du vill kopiera mappnings filerna från.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-koppling" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. Välj **Konfigurera enhets mappning**.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-koppling" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> Den här processen kan också användas för att kopiera/spara innehållet i JSON för **"Konfigurera FHIR-mappning"** .

4. Välj innehållet i JSON och gör en kopierings åtgärd (till exempel: Välj CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-koppling" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Gör en Inklistrings åtgärd (till exempel: Välj Ctrl + v) i en ny fil i en redigerare (till exempel Visual Studio Code, Notepad) och spara filen med ett *. JSON-tillägg.

> [!TIP]
> Om du kommer att öppna ett [Azure-tekniskt support](https://azure.microsoft.com/support/create-ticket/) ärende för IoT-anslutningsprogrammet, måste du ta med kopior av dina mappnings filer för att kunna felsöka processen.

## <a name="next-steps"></a>Nästa steg

Ta en titt på vanliga frågor om IoT Connector

>[!div class="nextstepaction"]
>[Vanliga frågor och svar om IoT Connector](fhir-faq.md#iot-connector-preview)


FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.