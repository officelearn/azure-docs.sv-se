---
title: Visa Azure reservationer | Microsoft Docs
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
ms.author: cwatson
ms.openlocfilehash: 2ae30ca55f3ca03a64438025960ddd807e288216
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48272652"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Visa Azure reservationer i Azure portal

Det finns ett par olika sätt att visa Azure reservationer beroende på din prenumerationstyp och behörigheter.

## <a name="view-reservations-as-owner-or-reader"></a>Visa reservationer som ägare eller läsare

Som standard när du köper en reservation kan du och kontoadministratören visa reservationen. Du och kontoadministratören får du automatiskt ägarrollen på reservationen. Om du vill att andra ska kunna se reservationen, måste du lägga till dem som en **ägare** eller **läsare** på reservationen. Mer information finns i [Lägg till eller ändra användare som kan hantera en reservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).
 
Visa en reservation ägare eller läsare,

1. Logga in på [Azure Portal]( http://portal.azure.com).
1. Sök på **reservationer**.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-view-reservation/portal-reservation-search.png)

1. Du se en lista över reservationerna där du har rollen ägare eller läsare.

Om du vill ändra omfattningen för en reservation kan dela en reservation eller ändra vem som kan hantera en reservation Se [hantera Azure-reservationer](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Visa Reservation transaktioner för Enterprise-registreringar

 Om du har en partner som leds Enterprise-registrering kan du visa reservationer genom att gå till **rapporter** i EA-portalen. Du kan visa reservationer i EA-portalen och i Azure-portalen för andra Enterprise-registreringar. Du måste vara ett EA-administratören ska kunna visa reservation transaktioner.

Visa transaktioner för reserverade Azure-portalen

1. Logga in på [Azure Portal]( http://portal.azure.com).
1. Sök på **Cost Management + fakturering**.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Välj **Reservation transaktioner**.
1. Om du vill filtrera resultatet väljer **Timespan**, **typ**, eller **beskrivning**.
1. Välj **Använd**.

    ![Skärmbild som visar resultat för Reservation transaktioner](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Om du vill hämta data med hjälp av ett API, se [hämta reserverad instans debiteras för företagskunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är Azure reservationer?](billing-save-compute-costs-reservations.md)
- [Betala i förskott för Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Hantera Azure Reservations](billing-manage-reserved-vm-instance.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Förstå användningen av reserverade för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du har fler frågor, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.
