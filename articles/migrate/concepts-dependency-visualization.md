---
title: Visualisering av beroenden i Azure Migrate | Microsoft Docs
description: Översikt över utvärderingsberäkningar i Azure Migrate-tjänsten.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: raynew
ms.openlocfilehash: 5880c98b0f77b75dfb10969311408307b0c4afbd
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603370"
---
# <a name="dependency-visualization"></a>Visualisering av beroenden

Den [Azure Migrate](migrate-overview.md) tjänsterna utvärderar grupper av lokala datorer för migrering till Azure. Du kan använda visualisering av beroenden för att gruppera datorer. Den här artikeln innehåller information om den här funktionen.


## <a name="overview"></a>Översikt

Visualisering av beroenden i Azure Migrate kan du skapa grupper för migrering utvärdera ökad tryggt. Du kan visa nätverksberoenden för specifika datorer eller i en grupp med datorer med hjälp av beroendevisualisering. Detta är användbart för att se till att inga funktioner eller borttappade (eller datorer som har glömt) i migreringsprocessen, när appar och arbetsbelastningar som körs över flera datorer.  

## <a name="how-does-it-work"></a>Hur fungerar det?

Azure Migrate använder den [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) lösning i [Log Analytics](../log-analytics/log-analytics-overview.md) för visualisering av beroenden.
- När du skapar ett Azure Migration-projekt skapas en Log Analytics-arbetsytan i din prenumeration.
- Arbetsytans namn är namnet som du anger för migration-projekt med prefixet **migrera-**, och du kan också suffix med en siffra.
- Gå till Log Analytics-arbetsyta från den **Essentials** avsnittet projektets **översikt** sidan.
- Arbetsytan skapade märks med nyckeln **migreringsprojektet**, och värdet **projektnamn**. Du kan använda dessa för att söka i Azure-portalen.  

    ![Log Analytics-arbetsyta](./media/concepts-dependency-visualization/oms-workspace.png)

Om du vill använda visualisering av beroenden, måste du ladda ned och installera agenter på varje lokal dator som du vill analysera.  

## <a name="do-i-need-to-pay-for-it"></a>Behöver jag betala för det?

Azure Migrate är tillgänglig utan extra kostnad. Användning av beroendevisualiseringsfunktioner i Azure Migrate kräver Tjänstkarta. Vid skapandet av ett Azure Migrate-projekt skapar Azure Migrate automatiskt en ny Log Analytics-arbetsyta för din räkning.

> [!NOTE]
> Funktionen beroendevisualisering använder Tjänstkarta via en Log Analytics-arbetsyta. Eftersom den 28 februari 2018 är med tillkännagivandet av Azure Migrate allmänt tillgängliga, funktionen nu tillgänglig utan extra kostnad. Du måste skapa ett nytt projekt att använda arbetsytan kostnadsfri användning. Befintliga arbetsytor innan Allmänt availaibility är fortfarande chargable kan därför rekommenderar vi att du flyttar till ett nytt projekt.

1. Användning av några lösningar än Tjänstkartan i den här Log Analytics-arbetsytan kommer att debiteras standard Log Analytics.
2. För att stödja Migreringsscenarier utan extra kostnad, behöver lösningen Tjänstkarta inte betala något under de första 180 dagarna från skapandet av Azure Migrate-projektet, varefter tillkommer standardkostnader.
3. Endast den arbetsyta som skapats som en del av projektet har skapats, kommer för användning.

När du registrerar agenter till arbetsytan använder du ID: T och nyckeln som anges av projektet på sidan Installera agenten steg. Du kan inte använda en befintlig arbetsyta och koppla den till Azure Migrate-projektet.

När Azure Migrate-projekt tas bort, raderas inte arbetsytan tillsammans med den. Publicera projektet borttagningen, Service Map användning är inte kostnadsfria och varje nod debiteras enligt den kostnadsfria nivån av Log Analytics-arbetsyta.

Mer information om priser för Azure Migrate finns [här](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Hur hanterar jag arbetsytan?

Du kan använda Log Analytics-arbetsytan utanför Azure Migrate. Den tas inte bort om du tar bort migration-projekt där den skapades. Om du inte längre behöver arbetsytan [ta bort den](../log-analytics/log-analytics-manage-access.md) manuellt.

Ta inte bort den arbetsyta som skapats av Azure Migrate, såvida inte du ta bort migreringsprojektet. Om du gör fungerar beroenden inte som förväntat.

## <a name="next-steps"></a>Nästa steg

[Gruppera datorer med datorberoenden](how-to-create-group-machine-dependencies.md)
