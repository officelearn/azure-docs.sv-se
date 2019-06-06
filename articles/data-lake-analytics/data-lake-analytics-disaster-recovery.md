---
title: Vägledning om haveriberedskap för Azure Data Lake Analytics
description: Lär dig hur du planerar katastrofåterställning för dina Azure Data Lake Analytics-konton.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ea1d4020aa9be23b4839690ae0b386d35bce8a23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66498896"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Vägledning om haveriberedskap för Azure Data Lake Analytics

Azure Data Lake Analytics är en tjänst på begäran som gör det enklare att analysera stordata. I stället för att distribuera, konfigurera och justera maskinvara, kan du skriva frågor genom vilka du kan omvandla dina data och extrahera värdefulla insikter. Med Analytics-tjänsten kan du hantera jobb av alla skalor direkt bara genom att ange hur mycket kraft du behöver. Du betalar endast för jobbet när tjänsten körs vilket gör den kostnadseffektiv. Den här artikeln innehåller råd om hur du skyddar dina jobb från ovanligt regionomfattande avbrott eller oavsiktliga borttagningar.

## <a name="disaster-recovery-guidance"></a>Vägledning om haveriberedskap

När du använder Azure Data Lake Analytics, är det viktigt för dig att förbereda en egen plan för katastrofåterställning. Den här artikeln hjälper dig för att skapa en plan för haveriberedskap. Det finns ytterligare resurser som hjälper dig att skapa ett eget schema:
- [Fel- och haveriberedskap för Azure-program](/azure/architecture/reliability/disaster-recovery)
- [Azure-återhämtning, tekniska riktlinjer](/azure/architecture/reliability)

## <a name="best-practices-and-scenario-guidance"></a>Metodtips och riktlinjer för scenario

Du kan köra ett återkommande U-SQL-jobb i ett ADLA-konto i en region som läser och skriver U-SQL-tabeller samt Ostrukturerade data.  Förbereda för en katastrof genom att göra följande:

1. Skapa ADLA och ADLS-konton i den sekundära regionen som ska användas under ett avbrott.

   > [!NOTE]
   > Använda en konsekvent namngivningsschema som anger vilket konto som är sekundär eftersom kontonamn är globalt unikt.

2. För Ostrukturerade data, referera till [vägledning om Haveriberedskap för data i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. Skapa kopior av för metadataartefakter som databaser, tabeller, tabellvärdesfunktioner och sammansättningar för strukturerade data som lagras i ADLA-tabeller och databaser. Du måste synkronisera med jämna mellanrum om dessa artefakter när ändringar sker i produktion. Till exempel har nyligen infogad data replikeras till den sekundära regionen genom att kopiera data och infoga i tabellen sekundär.

   > [!NOTE]
   > Dessa objektnamn är begränsade till det sekundära kontot och är inte globalt unikt, så att de kan ha samma namn som det primära produktionskontot.

Under ett avbrott måste du uppdatera skripten så indatasökvägarna pekar på den sekundära slutpunkten. Användarna skicka sina jobb till ADLA-konto i den sekundära regionen. Utdata från jobbet skrivs sedan till ADLA och ADLS-konto i den sekundära regionen.

## <a name="next-steps"></a>Nästa steg

[Vägledning om haveriberedskap för data i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
