---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880467"
---
## <a name="iot-plug-and-play-device"></a>IoT Plug and Play-enhet

En IoT Plug and Play-enhet är vanligt vis en småskalig, fristående data behandlings enhet som kan samla in data eller styra andra enheter och som kör program vara eller inbyggd program vara som implementerar de funktioner som deklareras i en [enhets kapacitets modell](#device-capability-model).  En IoT Plug and Play-enhet kan till exempel vara en miljö övervaknings enhet eller en kontrollant för ett smart-agrigulture bevattnings system. IoT-lösningar som är värd för molnet kan skrivas för att kommandoa, kontrol lera och ta emot data från IoT Plug and Play-enheter. IoT Plug and Play-enheter kan hittas via [Azure-certifierad för IoT-katalogen](https://catalog.azureiotsolutions.com/). Varje IoT Plug and Play-enhet i katalogen har verifierats och har en enhets [kapacitets modell](#device-capability-model).

## <a name="digital-twin"></a>Digital, dubbel

Digitala dubbla är modeller av IoT Plug and Play-enheter.  Digitala garn modelleras med hjälp av det [digitala, dubbla definitions språket](https://aka.ms/DTDL).  Du kan använda Azure IoT API för att interagera med digitala dubbla. 

## <a name="digital-twin-definition-language"></a>Digitalt elektroniskt definitions språk

Ett språk för att beskriva modeller och gränssnitt för [IoT plug and Play-enheter](#iot-plug-and-play-device).  Använd det [digitala, dubbla definitions språket](https://aka.ms/DTDL) för att beskriva [digitala dubbla](#digital-twin) funktioner och aktivera IoT-plattformen och IoT-lösningarna för att dra nytta av entitetens semantik.

## <a name="device-capability-model"></a>Enhets kapacitets modell

En enhets kapacitets modell beskriver en enhet och definierar de gränssnitt som implementeras av enheten. Skapa en modell för enhets kapacitet som motsvarar en fysisk enhet, produkt eller SKU.

## <a name="interface"></a>Gränssnitt

Ett gränssnitt beskriver relaterade funktioner som implementeras av en enhet eller Digitals. Gränssnitt kan återanvändas över olika kapacitets modeller.

## <a name="property"></a>Egenskap

Egenskaper är data fält som definierats i ett [gränssnitt](#interface) och som representerar status för en digital, dubbel. Du kan deklarera egenskaper som skrivskyddade eller skrivbara. Skrivskyddade egenskaper anges med kod som körs i själva IoT-Plug and Play själva enheten, till exempel serie nummer.  Skrivbara egenskaper anges av externa entiteter, till exempel larm tröskelvärden.

## <a name="telemetry"></a>Telemetri

Telemetri fält som definierats i ett [gränssnitt](#interface) representerar mått. Dessa mått är vanligt vis värden som sensor avläsningar.

## <a name="command"></a>Kommando

Kommandon som definieras i ett [gränssnitt](#interface) representerar metoder som kan köras på den digitala, dubbla. Till exempel ett reset-kommando för att återställa en enhet.
