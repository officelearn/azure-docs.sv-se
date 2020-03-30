---
title: Planera och hantera kostnader för Azure Storage
description: Lär dig hur du planerar och hanterar kostnader för Azure Storage med hjälp av kostnadsanalys i Azure-portalen.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304529"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Planera och hantera kostnader för Azure Storage

I den här artikeln beskrivs hur du planerar och hanterar kostnader för Azure Storage. Först använder du Azure-priskalkylatorn för att planera för lagringskostnader innan du lägger till några resurser. När du har börjat använda Azure Storage-resurser använder du kostnadshanteringsfunktioner för att ange budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och övervaka utgiftstrender för att identifiera områden där du kanske vill agera.

Tänk på att kostnaderna för Azure Storage bara är en del av de månatliga kostnaderna i din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för Azure Storage debiteras du för alla Azure-tjänster och resurser som används för din Azure-prenumeration, inklusive tjänster från tredje part. När du är bekant med att hantera kostnader för Azure Storage kan du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

## <a name="prerequisites"></a>Krav

Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill visa en fullständig lista över typer av stöds kan du läsa [Förstå Cost Management-data](../../cost-management-billing/costs/understand-cost-mgt-data.md). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Uppskatta kostnader innan du skapar ett Azure Storage-konto

Använd [Azure-priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta kostnaderna innan du skapar och börjar överföra data till ett Azure Storage-konto.

1. På sidan [Azure-priskalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du panelen **Lagringskonton.**

2. Bläddra nedåt på sidan och leta reda på avsnittet **Lagringskonton** i uppskattningen.

3. Välj alternativ i listrutorna. 

   När du ändrar värdet för dessa listrutor ändras kostnadsuppskattningen. Denna uppskattning visas i det övre hörnet samt i botten av uppskattningen. 
    
   ![Övervaka kostnader med fönstret Kostnadsanalys](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   När du ändrar värdet för listrutan **Typ** ändras även andra alternativ som visas i det här kalkylbladet. Använd länkarna i avsnittet **Mer information** om du vill veta mer om vad varje alternativ innebär och hur dessa alternativ påverkar priset på lagringsrelaterade åtgärder. 

4. Ändra de återstående alternativen för att se deras inverkan på din uppskattning.

## <a name="use-budgets-and-cost-alerts"></a>Använda budgetar och kostnadsaviseringar

Du kan skapa [budgetar](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa aviseringar som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resursgrupper, så de är användbara som en del av en övergripande kostnadsövervakningsstrategi. De kan dock ha begränsad funktionalitet för att hantera enskilda Azure-tjänstkostnader som kostnaden för Azure Storage eftersom de är utformade för att spåra kostnader på en högre nivå.

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder Azure-resurser med Azure Storage ådrar du dig kostnader. Kostnaderna för resursanvändningsenhet varierar beroende på tidsintervall (sekunder, minuter, timmar och dagar) eller efter enhetsanvändning (byte, megabyte och så vidare.) Kostnader uppstår så snart användningen av Azure Storage startar. Du kan se kostnaderna i [kostnadsanalysfönstret](../../cost-management-billing/costs/quick-acm-cost-analysis.md) i Azure-portalen.

När du använder kostnadsanalys kan du visa Azure Storage-kostnader i diagram och tabeller för olika tidsintervall. Några exempel är per dag, aktuell och föregående månad och år. Du kan också visa kostnader mot budgetar och prognostiserade kostnader. Om du växlar till längre vyer över tid kan du identifiera utgiftstrender och se var överutnyttjande kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de har överskridits.

Så här visar du Azure Storage-kostnader i kostnadsanalys:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Öppna fönstret **Kostnadshantering + Fakturering,** välj **Kostnadshantering** på menyn och välj sedan **Kostnadsanalys**. Du kan sedan ändra omfånget för en viss prenumeration från listrutan **Scope.**

   ![Övervaka kostnader med fönstret Kostnadsanalys](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Om du bara vill visa kostnader för Azure Storage väljer du **Lägg till filter** och väljer sedan **Tjänstnamn**. Välj sedan **lagring** från listan. 

   Här är ett exempel som visar kostnader för just Azure Storage:

   ![Övervaka lagringskostnader med fönstret Kostnadsanalys](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) och per resursgrupp visas också.  

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hanterar kostnader med [kostnadsanalys](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

Se följande artiklar om du vill veta mer om hur priser fungerar med Azure Storage:

- [Priser för Azure Storage Overview](https://azure.microsoft.com/pricing/details/storage/)
- [Optimera kostnader för blobblagring med reserverad kapacitet](../blobs/storage-blob-reserved-capacity.md)
