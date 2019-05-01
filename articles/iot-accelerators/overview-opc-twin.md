---
title: What is OPC Twin - Azure | Microsoft Docs
description: Översikt över OPC-Twin
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 15deadad3b53b64c619933db76d28f012c85d6d1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730265"
---
# <a name="what-is-opc-twin"></a>Vad är OPC-Twin?

OPC-Twin består av mikrotjänster som använder Azure IoT Edge och IoT Hub för att ansluta molnet och factory nätverket. OPC-Twin innehåller identifiering, registrering och fjärrstyrning av industriella enheter via REST API: er. OPC-Twin kräver inte en OPC Unified arkitektur (OPC UA) SDK, programmering språkoberoende och kan tas med i ett arbetsflöde för utan server. Den här artikeln beskriver flera OPC-Twin-användningsfall.

## <a name="discovery-and-control"></a>Identifiering och kontroll
Du kan använda OPC-Twin för enkel för identifiering och registrering.

### <a name="simple-discovery-and-registration"></a>Enkel identifiering och registrering
OPC-Twin kan factory operatörer att skanna factory nätverket, så att OPC UA-servrar kan identifieras och registreras. Alternativt kan kan factory operatörer också manuellt registrera OPC UA-enheter med hjälp av en känd identifierings-URL. Till exempel för att ansluta till alla OPC UA-enheter när du har installerat IoT Edge-gateway med en OPC-Twin-modulen på fabriksgolvet, kan operatorn factory utlösa en avsökning av nätverket via en fjärranslutning och visuellt se alla OPC UA-servrar. 

### <a name="simple-control"></a>Enkel åtkomstkontroll
OPC-Twin kan factory operatörer att reagera på händelser och konfigurera om sina factory våning datorer från molnet automatiskt eller manuellt i farten. OPC-Twin ger REST API: er att anropa tjänster på OPC UA-servern, bläddra sitt adressutrymme avseende skrivbara variabler och execute-metoder. En behållare använder till exempel temperatur KPI för att styra produktionsraden. Temperatursensor publicerar ändringen i data med hjälp av OPC Publisher. Operatorn factory tar emot aviseringen att temperaturen har nått tröskelvärdet. Produktionsraden kylningspaket automatiskt via OPC-Twin. Operatorn factory meddelas om coolbar ned.

## <a name="authentication"></a>Autentisering
Du kan använda OPC-Twin för enkelt för autentisering och för en enkel utvecklarupplevelse.

### <a name="simple-authentication"></a>Enkel autentisering 
OPC-Twin använder Azure Active Directory AAD-baserad autentisering och granskning från slutpunkt till slutpunkt. Till exempel gör OPC-Twin att programmet kan byggas ovanpå OPC-Twin att fastställa vad operatören har utförts på en dator. På dator-sida är det via OPC UA-granskning. På sidan moln är det genom att lagra en oföränderlig klienten granskningsloggen och AAD-autentisering på REST API.

### <a name="simple-developer-experience"></a>Enkel utvecklarupplevelse 
OPC-Twin kan användas med appar som skrivits i alla programmeringsspråk via REST API: er. Som utvecklare integrera en OPC UA-klient i en lösning, behövs inte kunskap om OPC UA-SDK. OPC-Twin kan smidigt integrera tillståndslösa, serverlös arkitektur. Till exempel fullständig stack webbutvecklare som utvecklar ett program för ett larm och händelse-instrumentpanelen kan skriva logik för att svara på händelser i JavaScript- eller TypeScript med hjälp av OPC-Twin utan kunskaper om C, C#, eller fullständig OPC UA-stacken implementeringen. 

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om OPC-Twin och dess användning, är här nästa föreslagna steg:

> [!div class="nextstepaction"]
> [Vad är OPC-valv](overview-opc-twin-architecture.md)