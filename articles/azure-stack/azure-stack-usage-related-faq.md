---
title: "Användnings-API relaterade vanliga frågor och svar | Microsoft Docs"
description: "Lista över Azure Stack mätare, jämförelse för användning av Azure API, tid för användning och rapporterade tid felkoder."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: alfredop
ms.openlocfilehash: 166147c8cb4949be1b23e0a06868e66c8a5844f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Vanliga frågor i Azure-stacken användnings-API 
Den här artikeln besvarar några vanliga frågor om Azure-stacken användning API.

## <a name="what-meter-ids-can-i-see"></a>Vilka mätaren ID: N kan se?
För närvarande rapporteras användning för nätverk, lagring och beräkning resursleverantörer.

| **Resursprovider** | **Mätaren ID** | **Mätaren namn** | **Enhet** | **Ytterligare information** |
| --- | --- | --- | --- | --- | 
| **Nätverk** |F271A8A388C44D93956A063E1D2FA80B |Användning av statisk IP-adress |IP-adresser|Räkna IP-adresser som används | 
| |9E2739BA86744796B465F64674B822BA |Dynamisk IP-Adressanvändning |IP-adresser|Räkna IP-adresser som används | 
| **Storage** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*timmar |Total kapacitet som används av tabeller |
| | B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*timmar |Total kapacitet som används av sidblobar |
| | B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*timmar |Total kapacitet som används av kön |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*timmar |Total kapacitet som används av blockblobbar |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Antalet begäranden i 10 000-tal |Tjänstbegäranden i tabell (i 10 000-tal) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Ingång data i GB |Tabellen service data ingång i GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Outgress i GB |Tabellen service datatrafik i GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Begäranden räkna i 10 000-tal |Begäranden för BLOB-tjänst (i 10 000-tal) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Ingång data i GB |BLOB-tjänsten data ingång i GB |
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Outgress i GB |BLOB-tjänsten datatrafik i GB |
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Begäranden räkna i 10 000-tal |Kön tjänstbegäranden (i 10 000-tal) |
| | E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Ingång data i GB |Kön tjänsten data ingång i GB | 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Outgress i GB |Kön service datatrafik i GB |
| **Compute** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Timmar för grundläggande VM-storlek |Virtuella core minuter | Vcores gånger minuter som den virtuella datorn kördes |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM storlek timmar |Virtuella core minuter | Vcores gånger minuter som den virtuella datorn kördes |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Timmar för VM-storlek |VM-timmar |Samlar in bas- och Windows-VM. Justeras inte för vcores |
| **Key Vault** | EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Key Vault transaktioner | Antalet begäranden i 10000s| Antal REST API-begäranden tas emot av Key Vault dataplan |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Hur gör Azure Stack-användning API: er som jämför med den [Azure användnings-API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (för närvarande i förhandsversion)?
* Användning-API-klient är konsekvent med Azure-API, med ett undantag: den *showDetails* flaggan för närvarande inte stöds i Azure-stacken.
* API: et för providern användning gäller bara för Azure-stacken.
* För närvarande den [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) som är tillgängliga i Azure är inte tillgängligt i Azure-stacken.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Vad är skillnaden mellan användning och rapporterade tid?
Data användningsrapporter har två huvudsakliga tidsvärden:

* **Rapporterat gång**. Den tid när händelsen användning anges systemets användning
* **Användning tid**. Den tid när resursen Azure Stack förbrukades

Du kan se en avvikelse i värden för användning och rapporterade tiden för en specifik händelse. Fördröjningen kan vara upp till flera timmar i alla miljöer.

För närvarande kan du fråga *endast av rapporterade tid*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Vad betyder dessa användning API-felkoder?
| **HTTP-statuskod** | **Felkod** | **Beskrivning** |
| --- | --- | --- |
| 400/Felaktig begäran |*NoApiVersion* |Den *api-versionen* frågeparameter saknas. |
| 400/Felaktig begäran |*InvalidProperty* |En egenskap saknas eller har ett ogiltigt värde. Meddelandet i felkoden i svarstexten identifierar egenskapen saknas. |
| 400/Felaktig begäran |*RequestEndTimeIsInFuture* |Värdet för *ReportedEndTime* ligger i framtiden. Värden är i framtiden inte tillåtna för det här argumentet. |
| 400/Felaktig begäran |*SubscriberIdIsNotDirectTenant* |Tjänstproviderns API-anrop används ett prenumerations-ID inte är en giltig klient för anroparen. |
| 400/Felaktig begäran |*SubscriptionIdMissingInRequest* |Prenumerations-ID för anroparen saknas. |
| 400/Felaktig begäran |*InvalidAggregationGranularity* |En ogiltig aggregering granularitet begärdes. Giltiga värden är varje dag och varje timme. |
| 503 |*ServiceUnavailable* |Ett återförsökbart fel inträffade eftersom tjänsten är upptagen eller anropet har begränsats. |

## <a name="next-steps"></a>Nästa steg
[Kunden fakturering och återbetalning i Azure-stacken](azure-stack-billing-and-chargeback.md)

[Providern Resursanvändning API](azure-stack-provider-resource-api.md)

[Resursanvändning API-klient](azure-stack-tenant-resource-usage-api.md)

