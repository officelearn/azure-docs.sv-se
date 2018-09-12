---
title: Användnings-API relaterade vanliga frågor och svar | Microsoft Docs
description: Lista över Azure Stack-mätare, jämförelse med API för Azure-användning, skapandet och rapporterade tid, felkoder.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ffe3dbf975984eb8df341728075e628d14080ada
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380241"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Vanliga frågor i Azure Stack användnings-API

Den här artikeln får du svar på några vanliga frågor om API för användning i Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Vilka mätnings-ID: N kan jag se?
Användning har rapporterats för följande resource providers:

### <a name="network"></a>Nätverk
  
**Mäter ID**: F271A8A388C44D93956A063E1D2FA80B  
**Mätarnamn**: användning av statisk IP-adress  
**Enhet**: IP-adresser  
**Anteckningar**: antal IP-adresser som används. Om du anropar API: et för användning med en daglig kornighet som returnerar mätaren IP-adress som multiplicerat med antalet timmar.  
  
**Mäter ID**: 9E2739BA86744796B465F64674B822BA  
**Mätarnamn**: användning av dynamisk IP-adress  
**Enhet**: IP-adresser  
**Anteckningar**: antal IP-adresser som används. Om du anropar API: et för användning med en daglig kornighet som returnerar mätaren IP-adress som multiplicerat med antalet timmar.  
  
### <a name="storage"></a>Lagringsutrymme
  
**Mäter ID**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Mätarnamn**: TableCapacity  
**Enhet**: GB\*timmar  
**Anteckningar**: Total kapacitet som används av tabeller.  
  
**Mäter ID**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Mätarnamn**: PageBlobCapacity  
**Enhet**: GB\*timmar  
**Anteckningar**: Total kapacitet som används av sidblobar.  
  
**Mäter ID**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Mätarnamn**: QueueCapacity  
**Enhet**: GB\*timmar  
**Anteckningar**: Total kapacitet som används av kön.  
  
**Mäter ID**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Mätarnamn**: BlockBlobCapacity  
**Enhet**: GB\*timmar  
**Anteckningar**: Total kapacitet som används av blockblob-objekt.  
  
**Mäter ID**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Mätarnamn**: TableTransactions  
**Enhet**: antal i 10, förfrågningar 000's  
**Anteckningar**: tabellen tjänstbegäranden (i 10 000-tal).  
  
**Mäter ID**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Mätarnamn**: TableDataTransIn  
**Enhet**: inkommande data i GB  
**Anteckningar**: Table service inkommande i GB.  
  
**Mäter ID**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Mätarnamn**: TableDataTransOut  
**Enhet**: utgående data i GB  
**Anteckningar**: Table service datatrafik i GB  
  
**Mäter ID**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Mätarnamn**: BlobTransactions  
**Enhet**: begäranden som räknar på 10, 000's  
**Anteckningar**: Blob servicebegäranden (i 10 000-tal).  
  
**Mäter ID**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Mätarnamn**: BlobDataTransIn  
**Enhet**: inkommande data i GB  
**Anteckningar**: Blob service inkommande i GB.  
  
**Mäter ID**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Mätarnamn**: BlobDataTransOut  
**Enhet**: utgående data i GB  
**Anteckningar**: Blob service datatrafik i GB.  
  
**Mäter ID**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Mätarnamn**: QueueTransactions  
**Enhet**: begäranden som räknar på 10, 000's  
**Anteckningar**: tjänstbegäranden i kö (i 10 000-tal).  
  
**Mäter ID**: E518E809-E369-4A45-9274-2017B29FFF25  
**Mätarnamn**: QueueDataTransIn  
**Enhet**: inkommande data i GB  
**Anteckningar**: Queue service inkommande i GB.  
  
**Mäter ID**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Mätarnamn**: QueueDataTransOut  
**Enhet**: utgående data i GB  
**Anteckningar**: Queue service datatrafik i GB  

### <a name="compute"></a>Compute 
  
**Mäter ID**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Mätarnamn**: Base VM-storlek timmar  
**Enhet**: virtuella kärntimmar  
**Anteckningar**: antalet virtuella kärnor multiplicerat med timmar som den virtuella datorn kördes.  
  
**Mäter ID**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Mätarnamn**: Windows VM-storlek timmar  
**Enhet**: virtuella kärntimmar  
**Anteckningar**: antalet virtuella kärnor multiplicerat med timmar som den virtuella datorn kördes.  
  
**Mäter ID**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Mätarnamn**: timmar för VM-storlek  
**Enhet**: VM-timmar  
**Anteckningar**: samlar in både Base och Windows VM. Justeras inte för kärnor.  
  
### <a name="managed-disks"></a>Managed Disks

**Mäter ID**: 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Mätarnamn**: S4   
**Enhet**: antal diskar   
**Anteckningar**: Standard Managed Disk – 32 GB 

**Mäter ID**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Mätarnamn**: S6   
**Enhet**: antal diskar   
**Anteckningar**: Standard Managed Disk – 64 GB 

**Mäter ID**: e5572fce-9f58-49d7-840c-b168c0f01fff   
**Mätarnamn**: S10   
**Enhet**: antal diskar   
**Anteckningar**: Standard Managed Disk – 128 GB 

**Mäter ID**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Mätarnamn**: S15   
**Enhet**: antal diskar   
**Anteckningar**: Standard Managed Disk – 256 GB 

**Mäter ID**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Mätarnamn**: S20   
**Enhet**: antal diskar      
**Anteckningar**: Standard Managed Disk – 512 GB 

**Mäter ID**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Mätarnamn**: S30   
**Enhet**: antal diskar   
**Anteckningar**: Standard Managed Disk – 1 024 GB 

**Mäter ID**: d9aac1eb-a5d1-42f2-b617-9e3ea94fed88   
**Mätarnamn**: S40   
**Enhet**: antal diskar   
**Anteckningar**: Standard Managed Disk – 2 048 GB 

**Mäter ID**: a54899dd-458e-4a40-9abd-f57cafd936a7   
**Mätarnamn**: S50   
**Enhet**: antal diskar   
**Anteckningar**: Standard Managed Disk – 4096 GB 

**Mäter ID**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Mätarnamn**: P4   
**Enhet**: antal diskar   
**Anteckningar**: Premium Managed Disk – 32 GB 

**Mäter ID**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Mätarnamn**: P6   
**Enhet**: antal diskar   
**Anteckningar**: Premium Managed Disk – 64 GB 

**Mäter ID**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Mätarnamn**: P10   
**Enhet**: antal diskar   
**Anteckningar**: Premium Managed Disk – 128 GB  

**Mäter ID**: 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Mätarnamn**: P15  
**Enhet**: antal diskar   
**Anteckningar**: Premium Managed Disk – 256 GB 

**Mäter ID**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Mätarnamn**: P20   
**Enhet**: antal diskar   
**Anteckningar**: Premium Managed Disk – 512 GB 

**Mäter ID**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Mätarnamn**: P30   
**Enhet**: antal diskar   
**Anteckningar**: Premium Managed Disk – 1 024 GB 

**Mäter ID**: 043757fc-049f-4e8b-8379-45bb203c36b1   
**Mätarnamn**: P40   
**Enhet**: antal diskar    
**Anteckningar**: Premium Managed Disk – 2 048 GB 

**Mäter ID**: c0342c6f-810b-4942-85d3-6eaa561b6570   
**Mätarnamn**: P50   
**Enhet**: antal diskar   
**Anteckningar**: Premium Managed Disk – 4096 GB 

**Mäter ID**: 8a409390-1913-40ae-917b-08d0f16f3c38   
**Mätarnamn**: ActualStandardDiskSize   
**Enhet**: Byte      
**Anteckningar**: den verkliga storleken på disken för hanterade standarddiskar  

**Mäter ID**: 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Mätarnamn**: ActualPremiumDiskSize   
**Enhet**: Byte      
**Anteckningar**: den verkliga storleken på disken för premium managed disk 

**Mäter ID**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Mätarnamn**: ActualStandardSnapshotSize   
**Enhet**: Byte   
**Anteckningar**: den verkliga storleken på disken för hanterade standardögonblicksbild.  

**Mäter ID**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Mätarnamn**: ActualPremiumSnapshotSize   
**Enhet**: Byte   
**Anteckningar**: den verkliga storleken på disken för hanterade premium.   

### <a name="sql-rp"></a>SQL-RP
  
**Mäter ID**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Mätarnamn**: DatabaseSizeHourSqlMeter  
**Enhet**: MB\*timmar  
**Anteckningar**: Totalt antal DB kapacitet när du skapar. Om du anropar API: et för användning med en daglig kornighet som returnerar mätaren MB multiplicerat med antalet timmar.  
  
### <a name="mysql-rp"></a>MySql-RP   
  
**Mäter ID**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Mätarnamn**: DatabaseSizeHourMySqlMeter  
**Enhet**: MB\*timmar  
**Anteckningar**: Totalt antal DB kapacitet när du skapar. Om du anropar API: et för användning med en daglig kornighet som returnerar mätaren MB multiplicerat med antalet timmar.    
### <a name="key-vault"></a>Nyckelvalv   
  
**Mäter ID**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Mätarnamn**: Key Vault-transaktioner  
**Enhet**: antal i 10, förfrågningar 000's  
**Anteckningar**: antal av REST API-begäranden som tas emot av Key Vault dataplanet.  
  
**Mäter ID**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Mätarnamn**: avancerade nycklar transaktioner  
**Enhet**: 10 K transaktioner  
**Anteckningar**: RSA 3 K/4 K, ECC viktiga transaktioner. (förhandsversion).  
  
### <a name="app-service"></a>Apptjänst   
  
**Mäter ID**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Mätarnamn**: App Service  
**Enhet**: virtuella kärntimmar  
**Anteckningar**: antal virtuella kärnor som används för att köra app service. Obs: Microsoft använder den här mätaren debiterar App Service på Azure Stack. Leverantörer av molntjänster kan använda andra App Service-mätare (nedan) för att beräkna förbrukning för sina klienter.  
  
**Mäter ID**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Mätarnamn**: Functions-begäranden  
**Enhet**: 10 förfrågningar  
**Anteckningar**: sammanlagda antalet begärda körningar (per 10 körningar). Körningar räknas varje gång en funktion körs som svar på en händelse eller utlöses av en bindning.  
  
**Mäter ID**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Mätarnamn**: Functions – Compute  
**Enhet**: GB-s  
**Anteckningar**: resursförbrukningen mätt i gigabyte-sekunder (GB/s). **Observerade resursförbrukningen** beräknas genom att den genomsnittliga minnesstorleken i GB med tiden i millisekunder som det tar för att köra funktionen. Minne som används av en funktion mäts genom avrundning till närmaste 128 MB, upp till den högsta minnesstorleken på 1,536 MB, körningstiden beräknas genom avrundning till närmaste 1 ms. Minsta körningstid och minne för körning av en enda funktion avseende att vara 100 ms och 128 mb.  
  
**Mäter ID**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Mätarnamn**: delade App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: Per timme användningen av fragment App Service-Plan. Planer mäts på basis av per App.  
  
**Mäter ID**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Mätarnamn**: kostnadsfri App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: Per timme användningen av kostnadsfria App Service-Plan. Planer mäts på basis av per App.  
  
**Mäter ID**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Mätarnamn**: liten Standard App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: beräknas baserat på diskstorleken, antalet instanser.  
  
**Mäter ID**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Mätarnamn**: Medium Standard App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: beräknas baserat på diskstorleken, antalet instanser.  
  
**Mäter ID**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Mätarnamn**: stora Standard App Service-timmar  
**Enhet**: 1 timme  
**Anteckningar**: beräknas baserat på diskstorleken, antalet instanser.  
  
### <a name="custom-worker-tiers"></a>Anpassade arbetsnivåer   
  
**Mäter ID**: *anpassade arbetsnivåer*  
**Mätarnamn**: anpassad Arbetarnivåer  
**Enhet**: timmar  
**Anteckningar**: Deterministisk mätnings-ID skapas baserat på SKU och namn på anpassade arbetarnivå. Den här mätnings-ID är unikt för varje anpassad worker-nivå.  
  
**Mäter ID**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Mätarnamn**: SNI SSL  
**Enhet**: Per SNI SSL-bindning  
**Anteckningar**: App Service stöder två typer av SSL-anslutningar: (Servernamnindikator) SSL-anslutningar och SSL-anslutningar för IP-adress. SNI-baserat SSL fungerar på moderna webbläsare och IP-baserat SSL fungerar på alla webbläsare.  
  
**Mäter ID**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Mätarnamn**: IP SSL  
**Enhet**: Per IP baserat SSL-bindning  
**Anteckningar**: App Service stöder två typer av SSL-anslutningar: (Servernamnindikator) SSL-anslutningar och SSL-anslutningar för IP-adress. SNI-baserat SSL fungerar på moderna webbläsare och IP-baserat SSL fungerar på alla webbläsare.  
  
**Mäter ID**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Mätarnamn**: webbprocess  
**Enhet**:  
**Anteckningar**: beräknas per aktiv webbplats per timme.  
  
**Mäter ID**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Mätarnamn**: externa utgående bandbredd  
**Enhet**: GB  
**Anteckningar**: Totalt antal inkommande begäran och svaret byte + total utgående begäran byte + totalt antal inkommande FTP Begär svar byte + totalt antal inkommande web distribuera byte svar på begäran.  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Hur gör Azure Stack-användning jämfört med API: er i [användningen av Azure API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (för närvarande i allmänt tillgänglig förhandsversion)?
* API för klient-användning är konsekvent med Azure-API, med ett undantag: den *showDetails* flaggan stöds inte för närvarande i Azure Stack.
* API: T för Provider användning gäller enbart för Azure Stack.
* För närvarande den [: T för RateCard](https://msdn.microsoft.com/library/azure/mt219004.aspx) som är tillgänglig i Azure är inte tillgänglig i Azure Stack.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Vad är skillnaden mellan användning och rapporterade tiden?
Data användningsrapporter har två huvudsakliga tidsvärden:

* **Rapporterad tid**. Den tid när händelsen användning har angett användning-system
* **Användningstiden**. Tiden när Azure Stack-resursen förbrukades

Du kan se en avvikelse i värden för användning och rapporterade tiden för en specifik händelse. Fördröjningen kan vara upp till flera timmar i alla miljöer.

För närvarande kan du fråga endast av *rapporterade tid*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Vad betyder de här användning API-felkoder?
| **HTTP-statuskod** | **Felkod** | **Beskrivning** |
| --- | --- | --- |
| 400/Felaktig begäran |*NoApiVersion* |Den *api-versionen* Frågeparametern saknas. |
| 400/Felaktig begäran |*InvalidProperty* |En egenskap saknas eller har ett ogiltigt värde. Meddelandet i felkoden i svarstexten identifierar egenskapen som saknas. |
| 400/Felaktig begäran |*RequestEndTimeIsInFuture* |Värdet för *ReportedEndTime* ligger i framtiden. Värden är i framtiden inte tillåtna för det här argumentet. |
| 400/Felaktig begäran |*SubscriberIdIsNotDirectTenant* |En tjänstproviderns API-anrop har använt ett prenumerations-ID som inte är en giltig klient av anroparen. |
| 400/Felaktig begäran |*SubscriptionIdMissingInRequest* |Prenumerations-ID för anroparen saknas. |
| 400/Felaktig begäran |*InvalidAggregationGranularity* |En ogiltig aggregering kornighet begärdes. Giltiga värden är daglig basis och per timme. |
| 503 |*ServiceUnavailable* |Ett återförsökbart fel inträffade eftersom tjänsten är upptagen eller anropet har begränsats. |

## <a name="next-steps"></a>Nästa steg
[Kundfaktureringen och chargeback i Azure Stack](azure-stack-billing-and-chargeback.md)

[Providerresursanvändning](azure-stack-provider-resource-api.md)

[Klientresursanvändning](azure-stack-tenant-resource-usage-api.md)
