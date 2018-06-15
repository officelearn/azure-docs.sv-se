---
title: Beroende visualisering i Azure migrera | Microsoft Docs
description: En översikt över assessment beräkningar i tjänsten Azure migrera.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 2b6b3e78436a215e64894b1102c4e109f72c896d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203075"
---
# <a name="dependency-visualization"></a>Visualisering av beroenden

Den [Azure migrera](migrate-overview.md) services utvärderar grupper av lokala datorer för migrering till Azure. Om du vill gruppera datorer kan du använda beroende visualiseringen. Den här artikeln innehåller information om den här funktionen.


## <a name="overview"></a>Översikt

Beroende visualisering i Azure migrera kan du skapa grupper för migrering utvärdera med ökat förtroende. Du kan visa nätverk beroenden för specifika datorer eller i en grupp datorer med beroende visualiseringen. Detta är användbart för att se till att inga funktioner eller förlorade (eller datorer som har glömt) i migreringsprocessen när appar och arbetsbelastningar som körs på flera datorer.  

## <a name="how-does-it-work"></a>Hur fungerar det?

Azure migrera använder den [Tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) lösning i [logganalys](../log-analytics/log-analytics-overview.md) för beroende visualiseringen.
- När du skapar ett projekt för migrering av Azure har logganalys-arbetsytan skapats i din prenumeration.
- Arbetsytans namn är namnet du anger för projektet migrering med prefixet **migrera-**, och eventuellt suffixet med en siffra. 
- Navigera till logganalys-arbetsytan från den **Essentials** avsnitt i projektet **översikt** sidan.
- Arbetsytan skapade är märkta med nyckeln **MigrateProject**, och värdet **projektnamn**. Du kan använda dessa för att söka i Azure-portalen.  

    ![Log Analytics-arbetsyta](./media/concepts-dependency-visualization/oms-workspace.png)

Om du vill använda beroende visualiseringen måste hämta och installera agenter på varje lokal dator som du vill analysera.  

## <a name="do-i-need-to-pay-for-it"></a>Behöver jag betala för den?

Azure Migrate är tillgänglig utan extra kostnad. Användning av beroende visualiseringen funktionerna i Azure migrera kräver Tjänstkartan. Vid skapandet av ett projekt för Azure migrera skapas Azure migrera automatiskt en ny logganalys-arbetsyta för din räkning.

> [!NOTE]
> Beroende visualiseringen funktionen använder Tjänstkarta via en logganalys-arbetsytan. Eftersom 28 februari 2018 är med meddelande om Azure migrera allmän tillgänglighet funktionen nu tillgänglig utan extra kostnad. Du behöver skapa ett nytt projekt att använda arbetsytan ledigt användning. Befintliga arbetsytor innan allmänna availaibility är fortfarande chargable därför rekommenderar vi att du flyttar till ett nytt projekt.

1. Användning av några lösningar än Tjänstkarta i logganalys-arbetsytan kommer avgifter standard logganalys. 
2. För att stödja Migreringsscenarier utan extra kostnad kommer Tjänstkarta lösningen inga avgifter för de första 180 dagarna från skapandet av Azure migrera-projektet som standard gäller.
3. På arbetsytan som skapats som en del av projektet har skapats, kommer vara fria för användning.

Använd ID och nyckeln som anges av-projektet på sidan Installera agenten steg när du registrerar agenter till arbetsytan. Du kan inte använda en befintlig arbetsyta och koppla den till Azure migrera projektet.

När projektet Azure migrera tas bort raderas inte arbetsytan tillsammans med den. Efter att projektet, Tjänstkarta användning inte ledigt och varje nod debiteras enligt betald nivå i logganalys-arbetsytan.

Mer information om priser för Azure Migrate finns [här](https://azure.microsoft.com/pricing/details/azure-migrate/). 

## <a name="how-do-i-manage-the-workspace"></a>Hur hanterar arbetsytan?

Du kan använda logganalys-arbetsytan utanför Azure migrera. Den tas inte bort om du tar bort projektet migrering där den skapades. Om du inte längre behöver arbetsytan [ta bort den](../log-analytics/log-analytics-manage-access.md) manuellt.

Ta inte bort arbetsytan som skapats av Azure migrera om du tar bort migreringsprojekt. Om du gör fungerar beroenden inte som förväntat.

## <a name="next-steps"></a>Nästa steg

[Grupp datorer som använder datorn beroenden](how-to-create-group-machine-dependencies.md)