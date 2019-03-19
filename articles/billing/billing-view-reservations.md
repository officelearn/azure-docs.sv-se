---
title: Visa reservationer för Azure-resurser | Microsoft Docs
description: Lär dig hur du visar Azure reservationer i Azure-portalen.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2018
ms.author: banders
ms.openlocfilehash: a66547ed4bf089bb618dc8b98da7f260b6b5536a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904311"
---
# <a name="view-reservations-for-azure-in-the-azure-portal"></a>Visa reservationer för Azure i Azure portal

Beroende på prenumerationstyp och behörigheter finns det ett par olika sätt att visa reservationer för Azure-resurser.

## <a name="view-reservations-as-owner-or-reader"></a>Visa reservationer som ägare eller läsare

Som standard när du köper en reservation kan du och kontoadministratören visa reservationen. Du och kontoadministratören får du automatiskt ägarrollen på reservationen. Om du vill att andra ska kunna se reservationen, måste du lägga till dem som en **ägare** eller **läsare** på reservationen. Mer information finns i [Lägg till eller ändra användare som kan hantera en reservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
 
Visa en reservation ägare eller läsare,

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök på **reservationer**.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-view-reservation/portal-reservation-search.png)

1. Du se en lista över reservationerna där du har rollen ägare eller läsare.

Om du vill ändra omfattningen för en reservation kan dela en reservation eller ändra vem som kan hantera en reservation Se [hantera Azure-reservationer](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Visa reservation transaktioner för Enterprise-registreringar

 Om du har en partner som leds Enterprise-registrering kan du visa reservationer genom att gå till **rapporter** i EA-portalen. Du kan visa reservationer i EA-portalen och i Azure-portalen för andra Enterprise-registreringar. Du måste vara ett EA-administratören ska kunna visa reservation transaktioner.

Visa transaktioner för reserverade Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök på **Cost Management + fakturering**.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Välj **Reservation transaktioner**.
1. Om du vill filtrera resultatet väljer **Timespan**, **typ**, eller **beskrivning**.
1. Välj **Använd**.

    ![Skärmbild som visar reservation transaktioner resultat](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Om du vill hämta data med hjälp av ett API, se [hämta reserverad instans debiteras för företagskunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Betala i förskott för Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera Azure-reservationer](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Förstå användningen av reserverade för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
