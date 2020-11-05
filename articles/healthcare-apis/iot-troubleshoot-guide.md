---
title: Azure IoT Connector för FHIR (för hands version) – fel söknings guide och anvisningar
description: Så här felsöker du vanliga Azure IoT Connector för FHIR (för hands version) fel meddelanden och villkor och kopierar mappnings filer
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: jasteppe
ms.openlocfilehash: a843ee15d4e7c67bcf69609067d70f592b9b50d6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394228"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Fel söknings guide för Azure IoT Connector för FHIR (för hands version)

Den här artikeln innehåller steg för att felsöka vanliga Azure IoT-anslutningar för FHIR * fel meddelanden och villkor.  

Du lär dig också hur du skapar kopior av Azure IoT Connector för FHIR Conversion mappnings-JSON (till exempel: enhet och FHIR).  

Du kan använda konverterings mappnings-JSON-kopior för att redigera och arkivera utanför Azure Portal.  

> [!TIP]
> Om du kommer att öppna ett [Azures tekniska support](https://azure.microsoft.com/support/create-ticket/) ärende för Azure IoT-ANSLUTNINGSPROGRAMMET för FHIR, måste du ta med kopior av konverterings-JSON-JSON för att hjälpa till med fel söknings processen.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Mappning av JSON-mall för enhets-och FHIR konvertering för Azure IoT Connector för FHIR (för hands version)
I det här avsnittet får du lära dig om validerings processen som Azure IoT Connector för FHIR utför för att validera JSON-mallarna för enhets-och FHIR konverterings mappning innan de tillåts att sparas för användning.  Dessa element krävs i JSON för enhets-och FHIR-konverterings mappning.

**Enhets mappning**

|Element|Obligatorisk|
|:-------|:------|
|Typnamn|Sant|
|TypeMatchExpression|Sant|
|DeviceIdExpression|Sant|
|TimestampExpression|Sant|
|Värden []. Värdets namn|Sant|
|Värden []. ValueExpression|Sant|

> [!NOTE]
> Värden []. ValueName och värden []. ValueExpression
>
> De här elementen krävs endast om du har en värde post i matrisen. det är inte tillåtet att mappa några värden. Detta används när Telemetrin som skickas är en händelse. Exempel: när en Wearable IoMT-enhet placeras eller tas bort. Elementen (-erna) har inga värden förutom ett namn som Azure IoT Connector för FHIR matchar och genererar. I FHIR-konverteringen mappar Azure IoT Connector för FHIR till ett kod möjligt koncept baserat på den semantiska typen – inga faktiska värden fylls i.

**FHIR-mappning**

|Element|Obligatorisk|
|:------|:-------|
|Typnamn|Sant|

> [!NOTE]
> Detta är det enda obligatoriska mappnings elementet för FHIR verifieras just nu.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Fel meddelanden och korrigeringar för Azure IoT Connector för FHIR (för hands version)

|Meddelande|Visat|Condition (Väderförhållanden)|Åtgärda| 
|-------|---------|---------|---|
|Ogiltigt mappnings namn, mappnings namn ska vara enhets-eller FHIR.|API|Den angivna mappnings typen är inte enhets-eller FHIR.|Använd en av de två mappnings typer som stöds (till exempel: enhet eller FHIR).|
|Verifieringen misslyckades. Nödvändig information saknas eller är ogiltig.|API och Azure Portal|Försök att spara en konverterings mappning saknar nödvändig information eller element.|Lägg till information om konverterings mappning som saknas eller element och försök att spara konverterings mappningen igen.|
|Återskapande av nyckel parametrar har inte definierats.|API|Återskapa nyckel förfrågan.|Inkludera parametrarna i begäran om att skapa en nyckel.|
|Det maximala antalet IoT Connector-instanser som kan tillhandahållas i den här prenumerationen har uppnåtts.|API och Azure Portal|Prenumerations kvoten för Azure IoT Connector för FHIR har nåtts (standard är (2) per prenumeration).|Ta bort en av de befintliga instanserna av Azure IoT Connector för FHIR.  Använd en annan prenumeration som inte har nått prenumerations kvoten.  Begär en ökning av prenumerations kvot.|
|Flytt av resurs stöds inte för IoT Connector aktiverat Azure API för FHIR-resursen.|API och Azure Portal|Försöker utföra en flyttnings åtgärd på en Azure API för FHIR-resurs som har en eller flera instanser av Azure IoT-anslutaren för FHIR.|Ta bort befintliga instanser av Azure IoT Connector för FHIR för att utföra flytt åtgärden.|
|IoT-kopplingen har inte tillhandahållits.|API|Försök att använda underordnade tjänster (anslutningar & mappningar) när överordnad (Azure IoT Connector för FHIR) inte har etablerats.|Etablera en Azure IoT-anslutning för FHIR.|
|Begäran stöds inte.|API|En speciell API-begäran stöds inte.|Använd rätt API-begäran.|
|Kontot finns inte.|API|Försök att lägga till en Azure IoT-anslutning för FHIR och Azure API för FHIR-resursen finns inte.|Skapa Azure API för FHIR-resursen och försök sedan igen.|
|Azure API för FHIR Resource FHIR-versionen stöds inte för IoT Connector.|API|Försök att använda en Azure IoT-anslutning för FHIR med en inkompatibel version av Azure API för FHIR-resursen.|Skapa en ny Azure API för FHIR-resurs (version R4) eller Använd en befintlig Azure API för FHIR-resurs (version R4).

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Varför visas inte min Azure IoT Connector för FHIR-data (för hands version) i Azure API för FHIR?

|Potentiella problem|Korrigeringar|
|----------------|-----|
|Data bearbetas fortfarande.|Data skickas till Azure-API: et för FHIR i batchar (var 15: e minut).  Det är möjligt att data fortfarande bearbetas och att ytterligare tid krävs för att data ska sparas i Azure API för FHIR.|
|Mappnings-JSON för enhets konvertering har inte kon figurer ATS.|Konfigurera och spara omvandlings-JSON för enhets konvertering.|
|FHIR Conversion mappnings-JSON har inte kon figurer ATS.|Konfigurera och spara FHIR Conversion Mapping JSON.|
|Enhets meddelandet innehåller inte ett förväntat uttryck som definierats i enhets mappningen.|Verifiera JsonPath-uttryck som definierats i enhets mappningens matchnings-token som definierats i enhets meddelandet.|
|En enhets resurs har inte skapats i Azure API för FHIR (lösnings typ: endast sökning) *.|Skapa en giltig enhets resurs i Azure API för FHIR. Se till att enhets resursen innehåller en identifierare som matchar den enhets identifierare som anges i det inkommande meddelandet.|
|En patient resurs har inte skapats i Azure API för FHIR (lösnings typ: endast sökning) *.|Skapa en giltig patient resurs i Azure API för FHIR.|
|Referensen för enheten. patient har inte angetts eller så är referensen ogiltig (lösnings typ: endast sökning) *.|Kontrol lera att enhets resursen innehåller en giltig [referens](https://www.hl7.org/fhir/device-definitions.html#Device.patient) till en patient resurs.| 

* Referens [snabb start: Distribuera Azure IoT Connector (för hands version) med Azure Portal](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) för en funktionell beskrivning av Azure IoT Connector för FHIR lösnings typer (till exempel lookup eller Create).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Skapa kopior av Azure IoT Connector för FHIR (förhands granskning) konverterings mappnings-JSON
Kopieringen av Azure IoT Connector för FHIR-mappar kan vara användbar för att redigera och arkivera utanför Azure Portal webbplats.

Mappnings filens kopior bör tillhandahållas till teknisk support för Azure när du öppnar ett support ärende för att under lätta fel sökningen.

> [!NOTE]
> JSON är det enda format som stöds för enhets-och FHIR-mappning av filer för tillfället.

> [!TIP]
> Lär dig mer om Azure IoT Connector för FHIR- [enhet och FHIR Conversion Mapping JSON](./iot-mapping-templates.md)

1. Välj **"IoT Connector (förhands granskning)"** på den nedre vänstra sidan av Azure-API: t för FHIR resurs instrument panel i avsnittet **"tillägg"** .

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT-Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Välj **"Connector"** som du vill kopiera konverterings-JSON från.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT-Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Den här processen kan också användas för att kopiera och spara innehållet i JSON-filen **"Konfigurera FHIR-mappning"** .

3. Välj **Konfigurera enhets mappning**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT-Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Välj innehållet i JSON och gör en kopierings åtgärd (till exempel: Välj CTRL + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT-Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Gör en Inklistrings åtgärd (till exempel: Välj Ctrl + v) i en ny fil i en redigerare (till exempel Visual Studio Code, Notepad) och spara filen med ett *. JSON-tillägg.

> [!TIP]
> Om du kommer att öppna ett [Azures tekniska support](https://azure.microsoft.com/support/create-ticket/) ärende för Azure IoT-ANSLUTNINGSPROGRAMMET för FHIR, måste du ta med kopior av konverterings-JSON-JSON för att hjälpa till med fel söknings processen.

## <a name="next-steps"></a>Nästa steg

Ta en titt på vanliga frågor om Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Vanliga frågor och svar om Azure IoT Connector för FHIR](fhir-faq.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version).

FHIR är ett registrerat varumärke som tillhör HL7 och används med tillåtelse av HL7.