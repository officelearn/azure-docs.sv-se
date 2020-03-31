---
title: Vägledning för katastrofåterställning för Azure Data Lake Analytics
description: Lär dig hur du planerar haveriberedskap för dina Azure Data Lake Analytics-konton.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889767"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Vägledning för katastrofåterställning för Azure Data Lake Analytics

Azure Data Lake Analytics är en tjänst på begäran som gör det enklare att analysera stordata. I stället för att distribuera, konfigurera och justera maskinvara, kan du skriva frågor genom vilka du kan omvandla dina data och extrahera värdefulla insikter. Med Analytics-tjänsten kan du hantera jobb av alla skalor direkt bara genom att ange hur mycket kraft du behöver. Du betalar endast för jobbet när tjänsten körs vilket gör den kostnadseffektiv. Den här artikeln innehåller vägledning om hur du skyddar dina jobb från sällsynta avbrott i hela regionen eller oavsiktliga borttagningar.

## <a name="disaster-recovery-guidance"></a>Vägledning om haveriberedskap

När du använder Azure Data Lake Analytics är det viktigt för dig att förbereda din egen katastrofåterställningsplan. Den här artikeln hjälper dig att skapa en katastrofåterställningsplan. Det finns ytterligare resurser som kan hjälpa dig att skapa en egen plan:
- [Fel- och haveriberedskap för Azure-program](/azure/architecture/reliability/disaster-recovery)
- [Azure-återhämtning, tekniska riktlinjer](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Riktlinjer för bästa praxis och scenarier

Du kan köra ett återkommande U-SQL-jobb i ett ADLA-konto i en region som läser och skriver U-SQL-tabeller samt ostrukturerade data.  Förbered dig för en katastrof genom att vidta följande åtgärder:

1. Skapa ADLA- och ADLS-konton i den sekundära regionen som ska användas under ett avbrott.

   > [!NOTE]
   > Eftersom kontonamn är globalt unika använder du ett konsekvent namngivningsschema som anger vilket konto som är sekundärt.

2. För ostrukturerade data refererar du till [vägledning för katastrofåterställning för data i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. För strukturerade data som lagras i ADLA-tabeller och databaser skapar du kopior av metadataartefakter som databaser, tabeller, tabellvärderade funktioner och sammansättningar. Du måste regelbundet synkronisera om dessa artefakter när ändringar sker i produktionen. Nyligen infogade data måste till exempel replikeras till det sekundära området genom att data kopieras och infogas i den sekundära tabellen.

   > [!NOTE]
   > Dessa objektnamn är begränsade till det sekundära kontot och är inte globalt unika, så de kan ha samma namn som i kontot för primär produktion.

Under ett avbrott måste du uppdatera skripten så att indatabanorna pekar på den sekundära slutpunkten. Sedan skickar användarna in sina jobb till ADLA-kontot i den sekundära regionen. Utdata för jobbet skrivs sedan till ADLA- och ADLS-kontot i den sekundära regionen.

## <a name="next-steps"></a>Nästa steg

[Vägledning för katastrofåterställning för data i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
