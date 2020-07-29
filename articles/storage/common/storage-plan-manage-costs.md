---
title: Planera och hantera kostnader för Azure Storage
description: Lär dig att planera för och hantera kostnader för Azure Storage genom att använda kostnads analys i Azure Portal.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304529"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Planera och hantera kostnader för Azure Storage

I den här artikeln beskrivs hur du planerar och hanterar kostnader för Azure Storage. Först använder du pris Kalkylatorn för Azure för att planera för lagrings kostnader innan du lägger till några resurser. När du har börjat använda Azure Storage-resurser kan du använda funktionerna för kostnads hantering för att ställa in budgetar och övervaka kostnader. Du kan också granska prognostiserade kostnader och övervaka utgifts trender för att identifiera områden där du kanske vill handla.

Tänk på att kostnader för Azure Storage bara är en del av månads kostnaderna på din Azure-faktura. Även om den här artikeln förklarar hur du planerar för och hanterar kostnader för Azure Storage debiteras du för alla Azure-tjänster och-resurser som används för din Azure-prenumeration, inklusive tjänster från tredje part. När du är bekant med att hantera kostnader för Azure Storage kan du använda liknande metoder för att hantera kostnader för alla Azure-tjänster som används i din prenumeration.

## <a name="prerequisites"></a>Krav

Kostnadsanalys stöder en mängd olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](../../cost-management-billing/costs/understand-cost-mgt-data.md). Om du vill visa kostnadsdata behöver du minst läsbehörighet för ditt Azure-konto. Mer information om hur du får åtkomst till Azure Cost Management finns i [Tilldela åtkomst till data](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Beräkna kostnader innan du skapar ett Azure Storage konto

Använd [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnader innan du skapar och börjar överföra data till ett Azure Storage-konto.

1. På sidan [pris kalkylator för Azure](https://azure.microsoft.com/pricing/calculator/) väljer du panelen **lagrings konton** .

2. Rulla ned sidan och leta upp avsnittet **lagrings konton** i beräkningen.

3. Välj alternativ i list rutorna. 

   När du ändrar värdet för dessa List rutor ändras kostnads beräkningen. Denna uppskattning visas i det övre hörnet och längst ned i beräkningen. 
    
   ![Övervaka kostnader med fönstret kostnads analys](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   När du ändrar värdet i list rutan **typ** ändras även andra alternativ som visas i det här kalkyl bladet. Använd länkarna i avsnittet **Mer information** för att lära dig mer om vad varje alternativ innebär och hur dessa alternativ påverkar priset för lagrings-relaterade åtgärder. 

4. Ändra de återstående alternativen för att se hur de påverkar beräkningen.

## <a name="use-budgets-and-cost-alerts"></a>Använda budgetar och kostnadsaviseringar

Du kan skapa [budgetar](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) för att hantera kostnader och skapa aviseringar som automatiskt meddelar mottagarna om kostnadsavvikelser och risker för överförbrukning. Aviseringar baseras på utgifter jämfört med budget- och kostnadströsklar. Budgetar och aviseringar skapas för Azure-prenumerationer och resurs grupper, så de är användbara som en del av en övergripande kostnads övervaknings strategi. De kan dock ha begränsade funktioner för att hantera enskilda kostnader för Azure-tjänster, till exempel kostnaden för Azure Storage eftersom de är utformade för att spåra kostnader på en högre nivå.

## <a name="monitor-costs"></a>Övervaka kostnader

När du använder Azure-resurser med Azure Storage debiteras du kostnader. Kostnaderna för resursanvändningen varierar med tidsintervall (sekunder, minuter, timmar och dagar) eller per enhets användning (byte, megabyte osv.) Kostnaderna påförs så snart användningen av Azure Storage startar. Du kan se kostnaderna i fönstret [kostnads analys](../../cost-management-billing/costs/quick-acm-cost-analysis.md) i Azure Portal.

När du använder kostnads analys kan du Visa Azure Storage kostnader i grafer och tabeller i olika tidsintervall. Några exempel är per dag, innevarande och föregående månad och år. Du kan också Visa kostnader för budgetar och prognostiserade kostnader. Om du växlar till längre vyer över tid kan du identifiera utgifts trender och se var överförbrukningen kan ha inträffat. Om du har skapat budgetar kan du också enkelt se var de överskreds.

Så här visar du Azure Storage kostnader i kostnads analys:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Öppna fönstret **Cost Management + fakturering** , Välj **kostnads hantering** på menyn och välj sedan **kostnads analys**. Du kan sedan ändra omfånget för en speciell prenumeration från List rutan **omfattning** .

   ![Övervaka kostnader med fönstret kostnads analys](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Om du bara vill visa kostnader för Azure Storage väljer du **Lägg till filter** och väljer sedan **tjänst namn**. Välj sedan **Storage (lagring** ) i listan. 

   Här är ett exempel som visar kostnader för just Azure Storage:

   ![Fönstret övervaka lagrings kostnader med kostnads analys](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

I föregående exempel visas den aktuella kostnaden för tjänsten. Kostnader per Azure-regioner (platser) och per resurs grupp visas också.  

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att hantera kostnader med [kostnads analys](../../cost-management-billing/costs/quick-acm-cost-analysis.md).

I följande artiklar finns mer information om hur prissättningen fungerar med Azure Storage:

- [Priser för Azure Storage översikt](https://azure.microsoft.com/pricing/details/storage/)
- [Optimera kostnader för blobblagring med reserverad kapacitet](../blobs/storage-blob-reserved-capacity.md)
