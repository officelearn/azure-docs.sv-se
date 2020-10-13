---
title: Ord lista med termer – IoT Plug and Play | Microsoft Docs
description: Koncept – en ord lista med vanliga termer som rör IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d44866e2d04ab1bab5d2eca01374350a7d73a0ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577347"
---
# <a name="glossary-of-terms-for-iot-plug-and-play"></a>Ord lista med villkor för IoT Plug and Play

Definitioner av vanliga termer som används i IoT Plug and Play-artiklar.

## <a name="azure-iot-explorer-tool"></a>Verktyget Azure IoT Explorer

Azure IoT Explorer är ett grafiskt verktyg som du kan använda för att interagera med och testa dina [IoT plug and Play-enheter](#iot-plug-and-play-device). När du har installerat verktyget på den lokala datorn kan du använda det för att:

- Visa de enheter som är anslutna till din [IoT-hubb](#azure-iot-hub).
- Anslut till en IoT Plug and Play-enhet.
- Visa enhets [komponenterna](#component).
- Visa den [telemetri](#telemetry) som enheten skickar.
- Arbeta med enhets [Egenskaper](#properties).
- Anropa enhets [kommandon](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT-Hub är en hanterad tjänst som finns i molnet, och som fungerar som ett centralt meddelandenav för dubbelriktad kommunikation mellan din IoT-program och de enheter som den hanterar. [Iot plug and Play-enheter](#iot-plug-and-play-device) kan ansluta till en IoT-hubb. En IoT-lösning använder en IoT-hubb för att aktivera:

- Enheter för att skicka telemetri till en molnbaserad lösning.
- En molnbaserad lösning för att hantera anslutna enheter.

## <a name="azure-iot-device-sdk"></a>Azure IoT-enhetens SDK

Det finns enhets-SDK: er för flera språk som du kan använda för att bygga IoT Plug and Play enhets klient program. Använd **DeviceClient** för enheter och **ModuleClient** för moduler och IoT Edge moduler.

## <a name="commands"></a>Kommandon

Kommandon som definieras i ett [gränssnitt](#interface) representerar metoder som kan köras på den [digitala, dubbla](#digital-twin). Till exempel ett kommando för att starta om en enhet.

## <a name="component"></a>Komponent

Med komponenter kan du bygga ett modell [gränssnitt](#interface) som en sammansättning av andra gränssnitt. En [enhets modell](#device-model) kan kombinera flera gränssnitt som komponenter. En modell kan till exempel innehålla en växel komponent och en termostat-komponent. Flera komponenter i en modell kan också använda samma gränssnitts typ. En modell kan till exempel innehålla två termostat-komponenter.

## <a name="connection-string"></a>Anslutningssträng

En anslutnings sträng kapslar in den information som krävs för att ansluta till en slut punkt. En anslutnings sträng innehåller vanligt vis adressen till slut punkten och säkerhets informationen, men anslutnings sträng formaten varierar mellan olika tjänster. Det finns två typer av anslutnings strängar kopplade till den IoT Hub tjänsten:

- Anslutnings strängar för enheter gör att [IoT plug and Play-enheter](#iot-plug-and-play-device) kan ansluta till enhetens slut punkter på en IoT-hubb. Klient koden på en enhet använder anslutnings strängen för att upprätta en säker anslutning till en IoT-hubb.
- IoT Hub anslutnings strängar möjliggör backend-lösningar och-verktyg för att ansluta säkert till tjänsteorienterade slut punkter på en IoT-hubb. Dessa lösningar och verktyg hanterar IoT-hubben och de enheter som är anslutna till den.

## <a name="default-component"></a>Standard komponent

Alla [enhets modeller](#device-model) har en standard komponent. En enkel enhets modell har bara en standard komponent – sådan en modell kallas även ingen komponent enhet. En mer komplex modell har flera komponenter som är kapslade under standard komponenten.

## <a name="device-certification"></a>Enhetscertifiering

IoT Plug and Play enhets certifierings programmet verifierar att en enhet uppfyller IoT-Plug and Play certifierings krav. Du kan lägga till en certifierad enhet i [katalogen för offentlig certifierad för Azure IoT-enheter](https://aka.ms/devicecatalog).

## <a name="device-model"></a>Enhetsmodell

En enhets modell beskriver en [IoT plug and Play-enhet](#iot-plug-and-play-device) och definierar de [komponenter](#component) som utgör enheten. En enkel enhets modell har inga separata komponenter och innehåller en definition för ett enda gränssnitt. Verktyget Azure IoT Explorer visar en enkel modell som har en enda [standard komponent](#default-component).

En mer komplex enhets modell innehåller flera komponenter. En enhets modell motsvarar vanligt vis en fysisk enhet, produkt eller SKU. Du använder den digitala endefinierade [definitions språket version 2](#digital-twins-definition-language) för att definiera en enhets modell.

## <a name="device-builder"></a>Enhets verktyg

En Device Builder använder en [enhets modell](#device-model) och [gränssnitt](#interface) när du implementerar kod som ska köras på en [IoT plug and Play-enhet](#iot-plug-and-play-device). Enhets byggare använder vanligt vis en av [Azure IoT-enhetens SDK](#azure-iot-device-sdk) : er för att implementera enhets klienten, men detta krävs inte.

## <a name="device-modeling"></a>Enhets modellering

Ett [Device Builder](#device-builder) -eller [modul Builder](#module-builder)använder det [digitala dubbla definitions språket](#digital-twins-definition-language) för att modellera funktionerna i en [IoT plug and Play-enhet](#iot-plug-and-play-device). Ett [Solution Builder](#solution-builder) kan konfigurera en IoT-lösning från modellen.

## <a name="digital-twin"></a>Digital, dubbel

En digital enhet är en modell av en [IoT plug and Play-enhet](#iot-plug-and-play-device). En Digitals fläta modelleras med hjälp av det [digitala, dubbla definitions språket](#digital-twins-definition-language). Du kan använda SDK: er för [Azure IoT-enheter](#azure-iot-device-sdk) för att interagera med digitala dubbla i kör tid. Du kan till exempel ange ett egenskaps värde i en digital enhet på en enhet och SDK: n skickar den här ändringen till din IoT-lösning i molnet.

## <a name="digital-twin-change-events"></a>Ändringshändelser för digitala tvillingar

När en [iot plug and Play-enhet](#iot-plug-and-play-device) är ansluten till en [IoT-hubb](#azure-iot-hub)kan hubben använda sin routningsfunktioner för att skicka meddelanden om digitala dubbla ändringar. När ett [egenskaps](#properties) värde ändras på en enhet kan IoT Hub till exempel skicka ett meddelande till en slut punkt, till exempel en Event Hub.

## <a name="digital-twins-definition-language"></a>Digitalt flätat definitions språk

Ett språk för att beskriva modeller och gränssnitt för [IoT plug and Play-enheter](#iot-plug-and-play-device). Använd det [digitala flätade definitions språket version 2](https://github.com/Azure/opendigitaltwins-dtdl) för att beskriva [digitala dubbla](#digital-twin) funktioner och aktivera IoT-plattformen och IoT-lösningarna för att dra nytta av entitetens semantik.

## <a name="digital-twin-route"></a>Digital, dubbel väg

En väg som kon figurer ATS i en [IoT-hubb](#azure-iot-hub) för att leverera [digitala dubbla ändrings händelser](#digital-twin-change-events) till och slut punkt, till exempel en Event Hub.

## <a name="interface"></a>Gränssnitt

Ett gränssnitt beskriver relaterade funktioner som implementeras av en [IoT plug and Play-enhet](#iot-plug-and-play-device) eller [Digitals](#digital-twin). Du kan återanvända gränssnitt mellan olika [enhets modeller](#device-model). När ett gränssnitt används i en enhets modell definierar den en [komponent](#component) i enheten. En enkel enhet innehåller bara ett standard gränssnitt.

## <a name="iot-hub-query-language"></a>IoT Hub frågespråk

Det IoT Hub frågespråket används i flera olika syfte. Du kan till exempel använda språket för att söka efter enheter som är registrerade i IoT Hub eller förfina det [digitala dubbla routnings](#digital-twin-route) beteendet.

## <a name="iot-plug-and-play-bridge"></a>IoT Plug and Play-brygga

IoT Plug and Play Bridge är ett program med öppen källkod som gör att befintliga sensorer och kring utrustning som är anslutna till Windows-eller Linux-gatewayer kan anslutas som [IoT plug and Play-enheter](#iot-plug-and-play-device).

## <a name="iot-plug-and-play-device"></a>IoT Plug and Play-enhet

En IoT Plug and Play-enhet är vanligt vis en småskalig, fristående data behandlings enhet som samlar in data eller styr andra enheter och som kör program vara eller inbyggd program vara som implementerar en [enhets modell](#device-model).  En IoT Plug and Play-enhet kan till exempel vara en miljö övervaknings enhet eller en kontrollant för ett bevattnings system med Smart-jordbruk. En IoT Plug and Play-enhet kan implementeras direkt eller som en IoT Edge modul. Du kan skriva en molnbaserad IoT-lösning i molnet för att kommando, kontrol lera och ta emot data från IoT Plug and Play-enheter.

## <a name="iot-plug-and-play-conventions"></a>Konventioner för IoT Plug and Play

IoT Plug and Play- [enheter](#iot-plug-and-play-device) förväntas följa en uppsättning [konventioner](concepts-convention.md) när de utbyter data med en lösning.

## <a name="model-id"></a>Modell-ID

När en IoT Plug and Play-enhet ansluter till en IoT Hub skickas **modell-ID: t** för den [DTDL](#digital-twins-definition-language) -modell som den implementerar. Detta gör att lösningen kan hitta enhets modellen.

## <a name="model-repository"></a>Modelldatabas

En [modell databas](concepts-model-repository.md) lagrar [enhets modeller](#device-model) och [gränssnitt](#interface).

## <a name="model-repository-rest-api"></a>Modell databas REST API

Ett API för att hantera och interagera med modell databasen. Du kan till exempel använda API: et för att lägga till och söka efter [enhets modeller](#device-model).

## <a name="module-builder"></a>Modul Builder

En modul Builder använder en [enhets modell](#device-model) och [gränssnitt](#interface) när de implementerar kod som ska köras på en [IoT plug and Play-enhet](#iot-plug-and-play-device). Modul Builder implementerar koden som en modul eller en IoT Edge-modul som ska distribueras till IoT Edge runtime på en enhet.

## <a name="properties"></a>Egenskaper

Egenskaper är data fält som definierats i ett [gränssnitt](#interface) och som representerar status för en digital, dubbel. Du kan deklarera egenskaper som skrivskyddade eller skrivbara. Skrivskyddade egenskaper, till exempel serie nummer, anges med kod som körs på [IoT plug and Play själva enheten](#iot-plug-and-play-device) .  Skrivbara egenskaper, till exempel ett larm tröskelvärde, anges vanligt vis från den molnbaserade IoT-lösningen.

## <a name="shared-access-signature"></a>Signatur för delad åtkomst

Signaturer för delad åtkomst är en autentiseringsmekanism som baseras på SHA-256-säkra hash-värden eller URI: er. Autentiseringen av signaturen för delad åtkomst har två komponenter: en delad åtkomst princip och en signatur för delad åtkomst (kallas ofta för en token). En [iot plug and Play-enhet](#iot-plug-and-play-device) använder en signatur för delad åtkomst för att autentisera med en [IoT-hubb](#azure-iot-hub).

## <a name="solution-builder"></a>Solution Builder

En Solution Builder skapar lösningens Server del. En Solution Builder fungerar vanligt vis med Azure-resurser som [IoT Hub](#azure-iot-hub) och [modell databaser](#model-repository).

## <a name="telemetry"></a>Telemetri

Telemetri fält som definierats i ett [gränssnitt](#interface) representerar mått. Dessa mått är vanligt vis värden, till exempel sensor avläsningar som skickas av [IoT plug and Play-enheten](#iot-plug-and-play-device) som en data ström.
