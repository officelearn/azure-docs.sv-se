---
title: Vad är OPC Twin - Azure | Microsoft-dokument
description: Den här artikeln innehåller en översikt över OPC Twin. OPC Twin tillhandahåller identifiering, registrering och fjärrstyrning av industriella enheter via REST API:er.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 91448f55f0ebb88ba6c685b960ece9d91cb98e25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826231"
---
# <a name="what-is-opc-twin"></a>Vad är OPC Twin?

OPC Twin består av mikrotjänster som använder Azure IoT Edge och IoT Hub för att ansluta molnet och fabriksnätverket. OPC Twin tillhandahåller identifiering, registrering och fjärrstyrning av industriella enheter via REST API:er. OPC Twin kräver inte en OPC Unified Architecture (OPC UA) SDK, är programmeringsspråk agnostiker, och kan ingå i ett serverlöst arbetsflöde. I den här artikeln beskrivs flera OPC Twin-användningsfall.

## <a name="discovery-and-control"></a>Upptäckt och kontroll
Du kan använda OPC Twin för enkel för upptäckt och registrering.

### <a name="simple-discovery-and-registration"></a>Enkel upptäckt och registrering
OPC Twin gör det möjligt för fabriksoperatörer att skanna fabriksnätverket, så att OPC UA-servrar kan upptäckas och registreras. Som ett alternativ kan fabriksoperatörer också manuellt registrera OPC UA-enheter med hjälp av en känd identifierings-URL. Till exempel, för att ansluta till alla OPC UA-enheter efter IoT Edge gateway med en OPC Twin modul har installerats på fabriksgolvet, kan fabriksoperatören fjärr utlösa en genomsökning av nätverket och visuellt se alla OPC UA-servrar. 

### <a name="simple-control"></a>Enkel kontroll
OPC Twin gör det möjligt för fabriksoperatörer att reagera på händelser och konfigurera om sina maskiner på fabriksgolvet från molnet antingen automatiskt eller manuellt i farten. OPC Twin tillhandahåller REST-API:er för att anropa tjänster på OPC UA-servern, bläddra i dess adressutrymme samt läsa/skriva variabler och köra metoder. En panna använder till exempel temperatur-KPI för att styra produktionslinjen. Temperaturgivaren publicerar ändringen av data med OPC Publisher. Fabriksoperatören får en varning om att temperaturen har nått tröskelvärdet. Produktionslinjen kyls ner automatiskt via OPC Twin. Fabriksoperatören underrättas om avkylningen.

## <a name="authentication"></a>Autentisering
Du kan använda OPC Twin för enkel autentisering och för en enkel utvecklarupplevelse.

### <a name="simple-authentication"></a>Enkel autentisering 
OPC Twin använder Azure Active Directory (AAD)-baserad autentisering och granskning från till. OPC Twin gör det till exempel möjligt att bygga programmet ovanpå OPC Twin för att avgöra vad en operatör har utfört på en maskin. På maskinsidan är det genom OPC UA-granskning. På molnsidan är det genom att lagra en oföränderlig klientgranskningslogg och AAD-autentisering på REST API.On the cloud side, it's through lagringsbar klientgranskningslogg och AAD-autentisering på REST API.

### <a name="simple-developer-experience"></a>Enkel utvecklarupplevelse 
OPC Twin kan användas med program skrivna i alla programmeringsspråk genom REST API: er. Eftersom utvecklare integrerar en OPC UA-klient i en lösning är kunskap om OPC UA SDK inte nödvändig. OPC Twin kan sömlöst integreras i tillståndslösa, serverlösa arkitekturer. En helstackwebbutvecklare som utvecklar ett program för en alarm- och händelseinstrumentpanel kan till exempel skriva logiken för att svara på händelser i JavaScript eller TypeScript med OPC Twin utan kunskap om C, C#, eller fullständig OPC UA-stackimplementering. 

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om OPC Twin och dess användningsområden, här är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Vad är OPC-valv](overview-opc-vault.md)
