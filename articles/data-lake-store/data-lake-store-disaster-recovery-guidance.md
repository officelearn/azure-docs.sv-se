---
title: Vägledning för katastrofåterställning för Azure Data Lake Storage Gen1 | Microsoft-dokument
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
ms.openlocfilehash: b33977ca5184ea07b5651be18e3a132d30ce4b39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966058"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Vägledning för katastrofåterställning för data i Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 ger lokalt redundant lagring (LRS). Därför är data i ditt Data Lake Storage Gen1-konto motståndskraftigt mot tillfälliga maskinvarufel i ett datacenter via automatiserade repliker. Detta säkerställer hållbarhet och hög tillgänglighet, uppfyller Data Lake Storage Gen1 SLA. Den här artikeln innehåller vägledning om hur du ytterligare skyddar dina data från sällsynta avbrott i hela regionen eller oavsiktliga borttagningar.

## <a name="disaster-recovery-guidance"></a>Vägledning om haveriberedskap
Det är viktigt för varje kund att förbereda en plan för haveriberedskap. Läs informationen i den här artikeln för att skapa din katastrofåterställningsplan. Här finns resurser som hjälper dig att skapa ett eget schema.

* [Haveriberedskap och hög tillgänglighet för Azure-program](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Azure-återhämtning, tekniska riktlinjer](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Bästa praxis
Vi rekommenderar att du kopierar dina kritiska data till ett annat Data Lake Storage Gen1-konto i en annan region med en frekvens som är anpassad till behoven i din haveriberedskapsplan. Det finns en mängd olika metoder för att kopiera data, inklusive [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) eller [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory är användbart för att skapa och distribuera pipelines för datarörlighet regelbundet.

Om ett regionalt avbrott inträffar kan du sedan komma åt dina data i den region där data kopierades. Du kan övervaka [Azure Service Health Dashboard](https://azure.microsoft.com/status/) för att fastställa Azure-tjänststatus över hela världen.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Skadade data eller vägledning för återställning av oavsiktligt borttagna data
DataSjölagring Gen1 ger dataåtersåtergivande genom automatiska repliker, men detta hindrar inte ditt program (eller utvecklare/användare) från att skada data eller oavsiktligt ta bort dem.

### <a name="best-practices"></a>Bästa praxis
För att förhindra oavsiktlig borttagning rekommenderar vi att du först anger rätt åtkomstprinciper för ditt Data Lake Storage Gen1-konto.  Detta inkluderar att använda [Azure-resurslås](../azure-resource-manager/management/lock-resources.md) för att låsa viktiga resurser samt tillämpa åtkomstkontroll för konto- och filnivå med hjälp av de tillgängliga [säkerhetsfunktionerna Data Lake Storage Gen1](data-lake-store-security-overview.md). Vi rekommenderar också att du rutinmässigt skapar kopior av dina kritiska data med [ADLCopy,](data-lake-store-copy-data-azure-storage-blob.md) [Azure PowerShell](data-lake-store-get-started-powershell.md) eller [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) i ett annat Data Lake Storage Gen1-konto, mapp eller Azure-prenumeration.  Detta kan användas för att återställa från skadade data eller en borttagning. Azure Data Factory är användbart för att skapa och distribuera pipelines för datarörlighet regelbundet.

Organisationer kan också aktivera [diagnostikloggning](data-lake-store-diagnostic-logs.md) för sitt Data Lake Storage Gen1-konto för att samla in granskningsspår för dataåtkomst som ger information om vem som kan ha tagit bort eller uppdaterat en fil.

## <a name="next-steps"></a>Nästa steg
* [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)

