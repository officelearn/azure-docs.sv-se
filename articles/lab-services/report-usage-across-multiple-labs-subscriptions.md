---
title: Azure DevTest Labs-användning i flera labb och prenumerationer
description: Lär dig hur du rapporterar Azure DevTest Labs-användning i flera labb och prenumerationer.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169183"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Rapportera Azure DevTest Labs-användning i flera labb och prenumerationer

De flesta stora organisationer vill spåra resursanvändning för att bli mer effektiva med dessa resurser genom att visualisera trender och extremvärden i användningen. Baserat på resursanvändning kan labbägare eller chefer anpassa labben för att [förbättra resursanvändning och resurskostnader](https://docs.microsoft.com/azure/billing/billing-getting-started). I Azure DevTest Labs kan du hämta resursanvändning per labb så att du kan titta djupare på användningsmönstren. Dessa användningsmönster kan hjälpa till att identifiera ändringar för att förbättra effektiviteten. De flesta företag vill ha både individuell labbanvändning och övergripande användning i [flera labb och prenumerationer.](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 

I den här artikeln beskrivs hur du hanterar information om resursanvändning i flera labb och prenumerationer.

![Rapportanvändning](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Individuell användning av labb

I det här avsnittet beskrivs hur du exporterar resursanvändning för ett enda labb.

Innan du kan exportera resursanvändning av DevTest Labs måste du konfigurera ett Azure Storage-konto så att de olika filerna som innehåller användningsdata lagras. Det finns två vanliga sätt att köra export av data:

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* PowerShell Az.Resource-modulen [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) `exportResourceUsage`med åtgärden , labbresurs-ID och nödvändiga parametrar. 

    Artikeln [exportera eller ta bort personuppgifter](personal-data-delete-export.md) innehåller ett exempel på PowerShell-skript med detaljerad information om de data som exporteras. 

    > [!NOTE]
    > Datumparametern innehåller inte en tidsstämpel så data innehåller allt från midnatt baserat på tidszonen där labbet finns.

När exporten är klar kommer det att finnas flera CSV-filer i blob-lagringen med den olika resursinformationen.
  
För närvarande finns det två CSV-filer:

* *virtualmachines.csv* - innehåller information om de virtuella datorerna i labbet
* *disks.csv* - innehåller information om de olika diskarna i labbet 

Dessa filer lagras i *labresourceusage* blob-behållaren under labbnamnet, Lab unikt ID, kört datum och antingen fullständigt eller startdatum som baserades på exportbegäran. Ett exempel blob struktur skulle vara:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Exportera användning för alla labb

Om du vill exportera användningsinformationen för flera laboratorier bör du använda 

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/), finns på många språk, inklusive PowerShell eller 
* [Azure Automation runbook](https://docs.microsoft.com/azure/automation/), använd PowerShell, Python eller en anpassad grafisk designer för att skriva exportkoden.

Med hjälp av dessa tekniker kan du köra den enskilda labbexporten på alla labb vid ett visst datum och en viss tidpunkt. 

Din Azure-funktion bör skicka data till långsiktig lagring. När du exporterar data för flera labb kan exporten ta lite tid. För att hjälpa till med prestanda och minska risken för dubblering av information rekommenderar vi att du kör varje labb parallellt. För att åstadkomma parallellitet, kör Azure Functions asynkront. Dra också nytta av timern som Azure Functions erbjuder.

## <a name="using-a-long-term-storage"></a>Använda en långsiktig lagring

En långsiktig lagring konsoliderar exportinformationen från olika labb till en enda datakälla. En annan fördel med att använda långsiktig lagring är att kunna ta bort filerna från lagringskontot för att minska dubblering och kostnader. 

Långtidslagringen kan användas för att göra all textmanipulering, till exempel: 

* lägga till egna namn
* skapa komplexa grupperingar
* samla in data.

Några vanliga lagringslösningar är: [SQL Server,](https://azure.microsoft.com/services/sql-database/) [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)och [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Att välja vilken långsiktig lagringslösning du väljer beror på inställningarna. Du kan överväga att välja verktyget beroende på vad det erbjuder när det gäller interaktionstillgänglighet när du visualiserar data.

## <a name="visualizing-data-and-gathering-insights"></a>Visualisera data och samla in insikter

Använd ett datavisualiseringsverktyg som du väljer för att ansluta till din långtidslagring för att visa användningsdata och samla in insikter för att verifiera användningseffektiviteten. [Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) kan till exempel användas för att ordna och visa användningsdata. 

Du kan använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att skapa, länka och hantera dina resurser inom ett enda platsgränssnitt. Om större kontroll behövs kan den enskilda resursen skapas inom en enda resursgrupp och hanteras oberoende av datafabrikstjänsten.  

## <a name="next-steps"></a>Efterföljande moment

När systemet har konfigurerats och data flyttas till långtidslagringen är nästa steg att komma med de frågor som data behöver svara på. Ett exempel: 

-   Vad är användning av vm-storlek?

    Väljer användare hög prestanda (dyrare) VM-storlekar?
-   Vilka Marketplace-bilder används?

    Är anpassade avbildningar den vanligaste VM-basen om ett gemensamt bildarkiv skulle byggas som [Delat bildgalleri](../virtual-machines/windows/shared-image-galleries.md) eller [Bildfabrik](image-factory-create.md).
-   Vilka anpassade bilder används eller används inte?
