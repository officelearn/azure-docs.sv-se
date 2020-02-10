---
title: Begär en kvot ökning
description: Den här sidan beskriver hur du skapar en supportbegäran för att öka kvoterna för Azure SQL Database enskilda databaser, servrar och hanterade instanser.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: fb576b81adeec99e4080c744749097390d1add1d
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111103"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Begär ande kvoten ökar för Azure SQL Database

Den här artikeln förklarar hur du begär en kvot ökning för Azure SQL Database för enskilda databaser, servrar och hanterade instanser. Det förklarar också hur du aktiverar prenumerations åtkomst till en region.

## <a id="newquota"></a>Skapa en ny supportbegäran

Använd följande steg för att skapa en ny supportbegäran från Azure Portal för SQL Database.

1. På [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**.

   ![Länken Hjälp + Support](./media/quota-increase-request/help-plus-support.png)

1. I **Hjälp + Support**väljer du **ny supportbegäran**.

    ![Skapa en ny supportbegäran](./media/quota-increase-request/new-support-request.png)

1. För **typ av problem**väljer du **begränsningar för tjänsten och prenumerationen (kvoter)** .

   ![Välj en typ av problem](./media/quota-increase-request/select-quota-issue-type.png)

1. För **prenumeration**väljer du den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration för ökad kvot](./media/quota-increase-request/select-subscription-support-request.png)

1. För **typ av kvot**väljer du någon av följande kvot typer:

   - **SQL Database** för kvoter för enkel databas och elastisk pool.
   - **SQL Database Hanterad instans** för hanterade instanser.

   Välj sedan **Nästa: lösningar > >** .

   ![Välj en kvot typ](./media/quota-increase-request/select-quota-type.png)

1. I **informations** fönstret väljer du **Ange information** för att ange ytterligare information.

   ![Länken "Tillhandahåll information"](./media/quota-increase-request/provide-details-link.png)

Om du klickar på **Ange information** visas fönstret **kvot Detaljer** där du kan lägga till ytterligare information. I följande avsnitt beskrivs de olika alternativen för **SQL Database** och **SQL Database hanterade instans** kvot typer.

## <a id="sqldbquota"></a>SQL Database kvot typer

I följande avsnitt beskrivs tre kvot öknings alternativ för **SQL Database** kvot typer:

- Databas transaktions enheter (DTU: er) per server
- Servrar per prenumeration
- Aktivera prenumerations åtkomst till en region

### <a name="database-transaction-units-dtus-per-server"></a>Databas transaktions enheter (DTU: er) per server

Använd följande steg för att begära en ökning av DTU: er per server.

1. Välj kvot typ för **databas transaktions enheter (DTU: er) per server** .

1. I listan **resurs** väljer du den resurs som ska riktas mot målet.

1. I fältet **ny kvot** anger du den nya DTU-gränsen som du begär.

   ![Information om DTU-kvot](./media/quota-increase-request/quota-details-dtus.png)

Mer information finns i [resurs gränser för enskilda databaser med hjälp av DTU-inköps modell](sql-database-dtu-resource-limits-single-databases.md) och [resursers gränser för elastiska pooler med hjälp av inköps modellen DTU](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servrar per prenumeration

Använd följande steg för att begära en ökning av antalet servrar per prenumeration.

1. Välj kvot typ för **servrar per prenumeration** .

1. I listan **plats** väljer du den Azure-region som du vill använda. Kvoten är per prenumeration i varje region.

1. I fältet **ny kvot** anger du din begäran om maximalt antal servrar i den regionen.

   ![Information om servrars kvot](./media/quota-increase-request/quota-details-servers.png)

Mer information finns i [SQL Database resurs gränser och resurs styrning](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a>Aktivera prenumerations åtkomst till en region

Vissa erbjudande typer är inte tillgängliga i varje region. Du kan se ett fel meddelande, till exempel följande:

`This location is not available for subscription`

Om din prenumeration behöver åtkomst i en viss region kan du använda alternativet **annan kvot förfrågan** för att begära åtkomst. I din begäran anger du det erbjudande och den SKU-information som du vill aktivera för regionen. Om du vill utforska erbjudande-och SKU-alternativen kan du läsa [Azure SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Annan kvot information](./media/quota-increase-request/quota-details-whitelisting.png)

## <a id="sqlmiquota"></a>Kvot typ för hanterad instans

Använd följande steg för kvot typen **SQL Server hanterad instans** :

1. I listan **region** väljer du den Azure-region som du vill använda som mål.

1. Ange de nya gränser som du begär för **undernät** och **vCore**.

   ![Kvot information för hanterad instans](./media/quota-increase-request/quota-details-managed-instance.png)

Mer information finns i [översikt Azure SQL Database hanterade instans resurs gränser](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Skicka in din begäran

Det sista steget är att fylla i återstående information om din SQL Database kvot förfrågan. Välj sedan **Nästa: granska + skapa > >** och klicka på **skapa** när du har granskat förfrågnings informationen och skicka begäran.

## <a name="next-steps"></a>Nästa steg

När du har skickat in din begäran kommer den att granskas. Du kommer att kontaktas med ett svar baserat på den information som du har angett i formuläret.

Mer information om andra Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
