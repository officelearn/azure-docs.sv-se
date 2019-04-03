---
title: Vägledning om haveriberedskap för Azure Data Lake Storage Gen1 | Microsoft Docs
description: Vägledning om haveriberedskap för Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b3f1888a73baf2b7f9efa9f5e7cdb3305aa9f90d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878119"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Vägledning om haveriberedskap för data i Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 tillhandahåller lokalt redundant lagring (LRS). Exempelvis är data i ditt Data Lake Storage Gen1-konto motståndskraftiga mot tillfälliga maskinvarufel inom ett datacenter tack vare automatisk replikering. Detta säkerställer hållbarheten och hög tillgänglighet, vilket uppfyller SLA för Data Lake Storage Gen1. Den här artikeln innehåller råd om hur du skydda dina data ytterligare från ovanligt regionomfattande avbrott eller oavsiktliga borttagningar.

## <a name="disaster-recovery-guidance"></a>Vägledning om haveriberedskap
Det är viktigt för varje kund att förbereda en plan för haveriberedskap. Läs informationen i den här artikeln för att skapa din haveriberedskapsplan. Här finns resurser som hjälper dig att skapa ett eget schema.

* [Haveriberedskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Azure återhämtning, tekniska riktlinjer](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Bästa praxis
Vi rekommenderar att du kopierar dina viktigaste data till ett annat Data Lake Storage Gen1 konto i en annan region med en frekvens som är anpassad efter behoven i din haveriberedskapsplan. Det finns en mängd olika metoder för att kopiera data, inklusive [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) eller [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory är användbart för att skapa och distribuera pipelines för datarörlighet regelbundet.

Om ett regionalt strömavbrott kan du sedan komma åt dina data i den region där data har kopierats. Du kan övervaka den [Hälsoinstrumentpanelen för Azure](https://azure.microsoft.com/status/) att avgöra status för Azure-tjänsten över hela världen.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Skadade data eller vägledning för återställning av oavsiktligt borttagna data
Detta förhindrar inte att appen (eller utvecklare/användare) från skada data eller oavsiktligen raderar medan Data Lake Storage Gen1 skyddar data med hjälp av automatiska repliker.

### <a name="best-practices"></a>Bästa praxis
Om du vill förhindra oavsiktlig borttagning rekommenderar vi att du först ställer in rätt åtkomstprinciper för ditt Data Lake Storage Gen1-konto.  Det innebär att tillämpa [Azure resurslås](../azure-resource-manager/resource-group-lock-resources.md) att låsa viktiga resurser samt tillämpa konto- och nivå åtkomstkontroll med hjälp av de tillgängliga [säkerhetsfunktioner för Data Lake Storage Gen1](data-lake-store-security-overview.md). Vi rekommenderar också att du skapar regelbundna kopior av dina viktigaste data med [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) eller [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) i ett annat Data Lake Storage Gen1 konto, mapp eller Azure-prenumeration.  Detta kan användas för att återställa från skadade data eller en borttagning. Azure Data Factory är användbart för att skapa och distribuera pipelines för datarörlighet regelbundet.

Organisationer kan även aktivera [diagnostikloggning](data-lake-store-diagnostic-logs.md) för sitt konto för Data Lake Storage Gen1 att samla in granskningshistorik för dataåtkomst som ger information om vem som kan ha tagits bort eller uppdaterat en fil.

## <a name="next-steps"></a>Nästa steg
* [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)

