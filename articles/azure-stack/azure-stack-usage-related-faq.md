---
title: Användnings-API relaterade vanliga frågor och svar | Microsoft Docs
description: Lista över Azure Stack mätare, jämförelse för användning av Azure API, tid för användning och rapporterade tid felkoder.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ac22ba34bff1d5321c05bc0a0a1b14ca742079a7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051516"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Vanliga frågor i Azure-stacken användnings-API

Den här artikeln besvarar några vanliga frågor om Azure-stacken användning API.

## <a name="what-meter-ids-can-i-see"></a>Vilka mätaren ID: N kan se?
Användning har rapporterats för följande resursproviders:

**Nätverk**  
  
**Läsa ID**: F271A8A388C44D93956A063E1D2FA80B  
**Namnet på mätaren**: användning av statisk IP-adress  
**Enhet**: IP-adresser  
**Anteckningar**: antalet IP-adresser som används. Om du anropar API för användning med en timme, returnerar mätaren IP-adress multiplicerat med antalet timmar.  
  
**Läsa ID**: 9E2739BA86744796B465F64674B822BA  
**Namnet på mätaren**: användning av dynamisk IP-adress  
**Enhet**: IP-adresser  
**Anteckningar**: antalet IP-adresser som används. Om du anropar API för användning med en timme, returnerar mätaren IP-adress multiplicerat med antalet timmar.  
  
**Storage**  
  
**Läsa ID**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Namnet på mätaren**: TableCapacity  
**Enhet**: GB\*timmar  
**Anteckningar**: Total kapacitet som används av tabeller.  
  
**Läsa ID**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Namnet på mätaren**: PageBlobCapacity  
**Enhet**: GB\*timmar  
**Anteckningar**: Total kapacitet som används av sidblobar.  
  
**Läsa ID**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Namnet på mätaren**: QueueCapacity  
**Enhet**: GB\*timmar  
**Anteckningar**: Total kapacitet som används av kön.  
  
**Läsa ID**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Namnet på mätaren**: BlockBlobCapacity  
**Enhet**: GB\*timmar  
**Anteckningar**: Total kapacitet som används av blockblobbar.  
  
**Läsa ID**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Namnet på mätaren**: TableTransactions  
**Enhet**: begär antal i 10 000's  
**Anteckningar**: tabell tjänstbegäranden (i 10 000-tal).  
  
**Läsa ID**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Namnet på mätaren**: TableDataTransIn  
**Enhet**: ingång data i GB  
**Anteckningar**: tabell tjänsten data ingång i GB.  
  
**Läsa ID**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Namnet på mätaren**: TableDataTransOut  
**Enhet**: utgång i GB  
**Anteckningar**: tabell service datatrafik i GB  
  
**Läsa ID**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Namnet på mätaren**: BlobTransactions  
**Enhet**: begäranden räknar på 10, 000's  
**Anteckningar**: Blob tjänstbegäranden (i 10 000-tal).  
  
**Läsa ID**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Namnet på mätaren**: BlobDataTransIn  
**Enhet**: ingång data i GB  
**Anteckningar**: Blob-tjänsten data ingång i GB.  
  
**Läsa ID**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Namnet på mätaren**: BlobDataTransOut  
**Enhet**: utgång i GB  
**Anteckningar**: Blob-tjänsten datatrafik i GB.  
  
**Läsa ID**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Namnet på mätaren**: QueueTransactions  
**Enhet**: begäranden räknar på 10, 000's  
**Anteckningar**: kö tjänstbegäranden (i 10 000-tal).  
  
**Läsa ID**: E518E809-E369-4A45-9274-2017B29FFF25  
**Namnet på mätaren**: QueueDataTransIn  
**Enhet**: ingång data i GB  
**Anteckningar**: kö tjänsten data ingång i GB.  
  
**Läsa ID**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Namnet på mätaren**: QueueDataTransOut  
**Enhet**: utgång i GB  
**Anteckningar**: kö service datatrafik i GB  
  
**SQL-RP**  
  
**Läsa ID**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Namnet på mätaren**: DatabaseSizeHourSqlMeter  
**Enhet**: MB\*timmar  
**Anteckningar**: Totalt antal DB-kapaciteten på Skapa. Om du anropar API för användning med en timme, returnerar mätaren MB multiplicerat med antalet timmar.  
  
**MySql RP**  
  
**Läsa ID**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Namnet på mätaren**: DatabaseSizeHourMySqlMeter  
**Enhet**: MB\*timmar  
**Anteckningar**: Totalt antal DB-kapaciteten på Skapa. Om du anropar API för användning med en timme, returnerar mätaren MB multiplicerat med antalet timmar.  
  
**Compute**  
  
**Läsa ID**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Namnet på mätaren**: Base VM storlek timmar  
**Enhet**: virtuella kärntimmar  
**Anteckningar**: antal virtuella kärnor multiplicerat med timmar som den virtuella datorn kördes.  
  
**Läsa ID**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Namnet på mätaren**: Windows VM storlek timmar  
**Enhet**: virtuella kärntimmar  
**Anteckningar**: antal virtuella kärnor multiplicerat med timmar för den virtuella datorn kördes.  
  
**Läsa ID**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Namnet på mätaren**: timmar för VM-storlek  
**Enhet**: VM timmar  
**Anteckningar**: samlar in bas- och Windows VM. Justeras inte för kärnor.  
  
**Key Vault**  
  
**Läsa ID**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Namnet på mätaren**: Key Vault transaktioner  
**Enhet**: begär antal i 10 000's  
**Anteckningar**: antal REST API-begäranden som tagits emot av Key Vault dataplan.  
  
**Läsa ID**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Namnet på mätaren**: avancerade nycklar transaktioner  
**Enhet**: 10 K transaktioner  
**Anteckningar**: RSA 3 K/4 K ECC-nyckel transaktioner. (förhandsversion).  
  
*Apptjänst**  
  
**Läsa ID**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Namnet på mätaren**: Apptjänst  
**Enhet**: virtuella kärntimmar  
**Anteckningar**: antal virtuella kärnor som används för att köra app service. Obs: Microsoft använder den här mätaren debiterar Apptjänst Azure stacken. Leverantörer av molntjänster kan använda App Service mätare (nedan) för att beräkna användning för sina klienter.  
  
**Läsa ID**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Namnet på mätaren**: funktioner begäranden  
**Enhet**: 10 begäranden  
**Anteckningar**: Totalt antal begärda körningar (per 10 körningar). Körningar räknas varje gång en funktion körs som svar på en händelse eller utlöses av en bindning.  
  
**Läsa ID**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Namnet på mätaren**: funktioner - Compute  
**Enhet**: GB-s  
**Anteckningar**: resursförbrukning mätt i sekunder för gigabyte (GB/s). **Observerade resursförbrukning** beräknas genom att multiplicera genomsnittlig minne i GB med tid i millisekunder det tar för att köra funktionen. Minne som används av en funktion som mäts med avrundningen upp till närmaste 128 MB upp till de största minnesstorleken 1,536 MB med körningstid beräknas genom att avrundas till närmaste 1 ms. Den minsta körningstid minne för en enskild funktion körningen är och 100 ms och 128 mb respektive.  
  
**Läsa ID**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Namnet på mätaren**: delade App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: Per timme användning av Fragmentera App Service-Plan. Planer förbrukade på grundval av per App.  
  
**Läsa ID**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Namnet på mätaren**: ledigt App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: Per timme användning av ledigt App Service-Plan. Planer förbrukade på grundval av per App.  
  
**Läsa ID**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Namnet på mätaren**: liten Standard App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: beräknas baserat på storleken och antalet instanser.  
  
**Läsa ID**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Namnet på mätaren**: medelstor Standard App servicetimmar  
**Enhet**: 1 timme  
**Anteckningar**: beräknas baserat på storleken och antalet instanser.  
  
**Läsa ID**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Namnet på mätaren**: stora Standard App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: beräknas baserat på storleken och antalet instanser.  
  
**Anpassade Worker nivåer**  
  
**Läsa ID**: *anpassade Worker nivåer*  
**Namnet på mätaren**: anpassad Worker nivåer  
**Enhet**: timmar  
**Anteckningar**: deterministiska mätaren ID skapas baserat på SKU: N och anpassade worker nivånamn. Den här mätaren-ID är unikt för varje anpassad worker-nivå.  
  
**Läsa ID**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Namnet på mätaren**: SNI SSL  
**Enhet**: Per SNI SSL-bindning  
**Anteckningar**: Apptjänst stöder två typer av SSL-anslutningar: Server Servernamnsindikation (SNI) SSL-anslutningar och IP-adress SSL-anslutningar. SNI-baserat SSL fungerar på moderna webbläsare och IP-baserat SSL fungerar på alla webbläsare.  
  
**Läsa ID**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Namnet på mätaren**: IP SSL  
**Enhet**: Per IP baserade SSL-bindning  
**Anteckningar**: Apptjänst stöder två typer av SSL-anslutningar: Server Servernamnsindikation (SNI) SSL-anslutningar och IP-adress SSL-anslutningar. SNI-baserat SSL fungerar på moderna webbläsare och IP-baserat SSL fungerar på alla webbläsare.  
  
**Läsa ID**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Namnet på mätaren**: webb-processen  
**Enhet**:  
**Anteckningar**: beräknade per aktiv plats per timme.  
  
**Läsa ID**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Namnet på mätaren**: extern utgång bandbredd  
**Enhet**: GB  
**Anteckningar**: Totalt antal inkommande begäran svar byte + total utgående begärans byte + totala inkommande FTP-begäran svar byte + totalt antal inkommande web distribuera begäran svar byte.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Hur gör Azure Stack-användning API: er som jämför med den [Azure användnings-API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (för närvarande i förhandsversion)?
* Användning-API-klient är konsekvent med Azure-API, med ett undantag: den *showDetails* flaggan för närvarande inte stöds i Azure-stacken.
* API: et för providern användning gäller bara för Azure-stacken.
* För närvarande den [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx) som är tillgängliga i Azure är inte tillgängligt i Azure-stacken.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Vad är skillnaden mellan användning och rapporterade tid?
Data användningsrapporter har två huvudsakliga tidsvärden:

* **Rapporterat gång**. Den tid när händelsen användning anges systemets användning
* **Användning tid**. Den tid när resursen Azure Stack förbrukades

Du kan se en avvikelse i värden för användning och rapporterade tiden för en specifik händelse. Fördröjningen kan vara upp till flera timmar i alla miljöer.

För närvarande kan du fråga endast av *rapporterade tid*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Vad betyder dessa användning API-felkoder?
| **HTTP-statuskod** | **Felkod** | **Beskrivning** |
| --- | --- | --- |
| 400/Felaktig begäran |*NoApiVersion* |Den *api-versionen* frågeparameter saknas. |
| 400/Felaktig begäran |*InvalidProperty* |En egenskap saknas eller har ett ogiltigt värde. Meddelandet i felkoden i svarstexten identifierar egenskapen saknas. |
| 400/Felaktig begäran |*RequestEndTimeIsInFuture* |Värdet för *ReportedEndTime* ligger i framtiden. Värden är i framtiden inte tillåtna för det här argumentet. |
| 400/Felaktig begäran |*SubscriberIdIsNotDirectTenant* |Tjänstproviderns API-anrop har använt ett prenumerations-ID inte är en giltig klient för anroparen. |
| 400/Felaktig begäran |*SubscriptionIdMissingInRequest* |Prenumerations-ID för anroparen saknas. |
| 400/Felaktig begäran |*InvalidAggregationGranularity* |En ogiltig aggregering granularitet begärdes. Giltiga värden är varje dag och varje timme. |
| 503 |*ServiceUnavailable* |Ett återförsökbart fel inträffade eftersom tjänsten är upptagen eller anropet har begränsats. |

## <a name="next-steps"></a>Nästa steg
[Kunden fakturering och återbetalning i Azure-stacken](azure-stack-billing-and-chargeback.md)

[Providern Resursanvändning API](azure-stack-provider-resource-api.md)

[Resursanvändning API-klient](azure-stack-tenant-resource-usage-api.md)
