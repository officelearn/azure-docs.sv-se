---
title: Begär en kvotökning
description: På den här sidan beskrivs hur du skapar en supportbegäran för att öka kvoterna för enskilda Azure SQL Database-databaser, servrar och hanterade instanser.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: ff2be6972bb4e8af266d0aa8a56d1879bc1b8b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586163"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Begär kvot ökar för Azure SQL Database

I den här artikeln beskrivs hur du begär en kvotökning för Azure SQL Database för enskilda databaser, servrar och hanterade instanser. Det förklarar också hur du aktiverar prenumerationsåtkomst till en region.

## <a name="create-a-new-support-request"></a><a id="newquota"></a>Skapa en ny supportbegäran

Följ följande steg för att skapa en ny supportbegäran från Azure-portalen för SQL Database.

1. På [Portalmenyn i Azure](https://portal.azure.com) väljer du **Stöd för Hjälp +**.

   ![Supportlänken hjälp +](./media/quota-increase-request/help-plus-support.png)

1. Välj **Ny supportbegäran i** **Hjälp + support**.

    ![Skapa en ny supportbegäran](./media/quota-increase-request/new-support-request.png)

1. För **Ärendetyp**väljer du **Tjänst- och prenumerationsgränser (kvoter)**.

   ![Välj en ärendetyp](./media/quota-increase-request/select-quota-issue-type.png)

1. För **Prenumeration**väljer du den prenumeration vars kvot du vill öka.

   ![Välj en prenumeration för en utökad kvot](./media/quota-increase-request/select-subscription-support-request.png)

1. För **Kvottyp**väljer du en av följande kvottyper:

   - **SQL-databas** för kvoter för en databas och elastisk pool.
   - **SQL Database Managed Instance** för hanterade instanser.

   Välj sedan **Nästa: Lösningar >>**.

   ![Välj en kvottyp](./media/quota-increase-request/select-quota-type.png)

1. I fönstret **Information** väljer du **Ange information** om du vill ange ytterligare information.

   ![Länken "Ge information"](./media/quota-increase-request/provide-details-link.png)

Om du klickar på **Ange information** visas fönstret **Kvotinformation** som gör att du kan lägga till ytterligare information. I följande avsnitt beskrivs de olika alternativen för **kvottyperna SQL Database** och SQL Database Managed **Instance.**

## <a name="sql-database-quota-types"></a><a id="sqldbquota"></a>Kvottyper för SQL-databas

I följande avsnitt beskrivs tre alternativ för kvotökning för **SQL Database-kvottyperna:**

- Databastransaktionsenheter (DU: er) per server
- Servrar per prenumeration
- Aktivera prenumerationsåtkomst till en region

### <a name="database-transaction-units-dtus-per-server"></a>Databastransaktionsenheter (DU: er) per server

Följ följande steg för att begära en ökning av DU:erna per server.

1. Välj **databastransaktionsenheter (DU:er) per** serverkvottyp.

1. Välj den resurs som ska riktas i listan **Resurs.**

1. I fältet **Ny kvot** anger du den nya DTU-gräns som du begär.

   ![Information om DTU-kvot](./media/quota-increase-request/quota-details-dtus.png)

Mer information finns i [Resursgränser för enskilda databaser med DTU-inköpsmodellen](sql-database-dtu-resource-limits-single-databases.md) och [resursgränserna för elastiska pooler med hjälp av DTU-inköpsmodellen](sql-database-dtu-resource-limits-elastic-pools.md).

### <a name="servers-per-subscription"></a>Servrar per prenumeration

Följ följande steg för att begära en ökning av antalet servrar per prenumeration.

1. Välj **kvottypen Servrar per prenumeration.**

1. Välj den Azure-region som ska användas i listan **Plats.** Kvoten är per prenumeration i varje region.

1. I fältet **Ny kvot** anger du din begäran om det maximala antalet servrar i den regionen.

   ![Information om kvot för servrar](./media/quota-increase-request/quota-details-servers.png)

Mer information finns i [SQL Database-resursgränser och resursstyrning](sql-database-resource-limits-database-server.md).

### <a name="enable-subscription-access-to-a-region"></a><a id="other"></a>Aktivera prenumerationsåtkomst till en region

Vissa erbjudandetyper är inte tillgängliga i alla regioner. Du kan se ett fel som följande:

`This location is not available for subscription`

Om din prenumeration behöver åtkomst i en viss region använder du alternativet **Annan kvotbegäran för** att begära åtkomst. I din begäran anger du den erbjudande- och SKU-information som du vill aktivera för regionen. Information om hur du utforskar alternativen för erbjudandet och SKU finns i [Azure SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/single/).

![Övriga kvotuppgifter](./media/quota-increase-request/quota-details-whitelisting.png)

## <a name="managed-instance-quota-type"></a><a id="sqlmiquota"></a>Kvottyp för hanterad instans

FÃ¶r kvottypen **SQL Server Managed Instance** anpÃ¥¥

1. Välj **Azure-regionen** som du vill rikta in dig på i listan Region.

1. Ange de nya gränser som du begär för **Undernät** och **vCore**.

   ![Information om hanterad instanskvot](./media/quota-increase-request/quota-details-managed-instance.png)

Mer information finns i [Översikt Azure SQL Database hanterade instansresursgränser](sql-database-managed-instance-resource-limits.md).

## <a name="submit-your-request"></a>Skicka din förfrågan

Det sista steget är att fylla i återstående information om din SQL Database kvotbegäran. Välj sedan **Nästa: Granska + skapa>>** och när du har granskat information om begäran klickar du på **Skapa** för att skicka begäran.

## <a name="next-steps"></a>Nästa steg

När du har skickat din begäran granskas den. Du kommer att kontaktas med ett svar baserat på den information du lämnat i formuläret.

Mer information om andra Azure-begränsningar finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
