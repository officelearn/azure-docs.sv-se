---
title: Mått med Azure Monitor som stöds på Azure Stack | Microsoft Docs
description: Läs mer om mått som stöds för Azure Monitor på Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: 0cd8d309cfbf72a05c83c2a536d754e9cbc6e008
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022667"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Mått som stöds med Azure Monitor på Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Du kan hämta dina mått från Azure monitor på Azure Stack i samma som global Azure. Du kan dina mått i portalen, kommer från REST API eller skicka frågor mot dem med PowerShell eller CLI.

I tabellerna nedan listas mått som är tillgängliga med Azure Monitor mått pipeline på Azure Stack. Om du vill fråga efter och komma åt de här måtten, behöver du den **2018-01-01** api-versionen version av API-profilen. Mer information om API-profiler och Azure Stack finns i [hantera API-versionsprofiler i Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Mått | Metrisk visningsnamn | Enhet | Sammansättningstyp: | Beskrivning | Dimensioner |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Procent CPU | Procent CPU | Procent | Medel | Procentandel tilldelade beräkningsenheter som används just nu av virtuella datorer | Inga dimensioner |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Mått | Metrisk visningsnamn | Enhet | Sammansättningstyp: | Beskrivning | Dimensioner |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Använd kapacitet | Byte | Medel | Kapacitet som används av konto | Inga dimensioner |
| Transaktioner | Transaktioner | Antal | Totalt | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar. | ResponseType, GeoType, ApiName |
| Ingångshändelser | Ingångshändelser | Byte | Totalt | Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. | GeoType ApiName |
| Egress | Egress | Byte | Totalt | Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. | GeoType ApiName |
| SuccessServerLatency | Lyckad serversvarstid | Millisekunder | Medel | Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency. | GeoType ApiName |
| SuccessE2ELatency | Lyckad E2E-svarstid | Millisekunder | Medel | Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. | GeoType ApiName |
| Tillgänglighet | Tillgänglighet | Procent | Medel | Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Mått | Metrisk visningsnamn | Enhet | Sammansättningstyp: | Beskrivning | Dimensioner |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Blobkapacitet | Byte | Totalt | Mängden lagring som används av lagringskontots Blob Service i byte. | BlobType |
| BlobCount | Antalet blobar | Antal | Totalt | Antalet blobar i lagringskontots Blob Service. | BlobType |
| ContainerCount | Antal blobcontainrar | Antal | Medel | Antalet containrar i lagringskontots Blob Service. | Inga dimensioner |
| Transaktioner | Transaktioner | Antal | Totalt | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar. | ResponseType, GeoType, ApiName |
| Ingångshändelser | Ingångshändelser | Byte | Totalt | Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. | GeoType ApiName |
| Egress | Egress | Byte | Totalt | Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. | GeoType ApiName |
| SuccessServerLatency | Lyckad serversvarstid | Millisekunder | Medel | Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency. | GeoType ApiName |
| SuccessE2ELatency | Lyckad E2E-svarstid | Millisekunder | Medel | Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. | GeoType ApiName |
| Tillgänglighet | Tillgänglighet | Procent | Medel | Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Mått | Metrisk visningsnamn | Enhet | Sammansättningstyp: | Beskrivning | Dimensioner |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Tabellkapacitet | Byte | Medel | Mängden lagring som används av lagringskontots tabelltjänst i byte. | Inga dimensioner |
| TableCount | Antal tabeller | Antal | Medel | Antalet tabeller i lagringskontots tabelltjänst. | Inga dimensioner |
| TableEntityCount | Antal tabellentiteter | Antal | Medel | Antalet tabellentiteter i lagringskontots tabelltjänst. | Inga dimensioner |
| Transaktioner | Transaktioner | Antal | Totalt | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar. | ResponseType, GeoType, ApiName |
| Ingångshändelser | Ingångshändelser | Byte | Totalt | Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. | GeoType ApiName |
| Egress | Egress | Byte | Totalt | Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. | GeoType ApiName |
| SuccessServerLatency | Lyckad serversvarstid | Millisekunder | Medel | Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency. | GeoType ApiName |
| SuccessE2ELatency | Lyckad E2E-svarstid | Millisekunder | Medel | Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. | GeoType ApiName |
| Tillgänglighet | Tillgänglighet | Procent | Medel | Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Mått | Metrisk visningsnamn | Enhet | Sammansättningstyp: | Beskrivning | Dimensioner |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Kökapacitet | Byte | Medel | Mängden lagring som används av lagringskontots kötjänst i byte. | Inga dimensioner |
| QueueCount | Antal köer | Antal | Medel | Antalet köer i lagringskontots kötjänst. | Inga dimensioner |
| QueueMessageCount | Antal kömeddelanden | Antal | Medel | Ungefärligt antal kömeddelanden i lagringskontots kötjänst. | Inga dimensioner |
| Transaktioner | Transaktioner | Antal | Totalt | Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. Det här värdet innefattar lyckade och misslyckade begäranden samt begäranden som genererat fel. Använd måttet responsetype för antalet annan typ av svar. | ResponseType, GeoType, ApiName |
| Ingångshändelser | Ingångshändelser | Byte | Totalt | Mängden ingående data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure. | GeoType ApiName |
| Egress | Egress | Byte | Totalt | Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data. | GeoType ApiName |
| SuccessServerLatency | Lyckad serversvarstid | Millisekunder | Medel | Genomsnittlig svarstid för Azure Storage för att bearbeta en lyckad förfrågan, i millisekunder. Det här värdet omfattar inte Nätverksfördröjningen som anges i AverageE2ELatency. | GeoType ApiName |
| SuccessE2ELatency | Lyckad E2E-svarstid | Millisekunder | Medel | Den genomsnittliga svarstiden slutpunkt till slutpunkt för lyckade förfrågningar som gjorts till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret. | GeoType ApiName |
| Tillgänglighet | Tillgänglighet | Procent | Medel | Procentandel tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att ta TotalBillableRequests-värdet och att dela den med antalet tillämpliga förfrågningar, inklusive sådana som genererar oväntade fel. Alla oväntade fel leda till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. | GeoType ApiName |

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure övervaka på Azure Stack](azure-stack-metrics-azure-data.md).