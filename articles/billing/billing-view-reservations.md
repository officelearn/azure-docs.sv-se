---
title: Visa reservationer för Azure-resurser | Microsoft Docs
description: Lär dig hur du visar Azure reservationer i Azure-portalen.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 872837c774368820527b12778b1a7dd4ddc5c7af
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995531"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Visa Azure reservationer i Azure portal

Beroende på prenumerationstyp och behörigheter finns det ett par olika sätt att visa reservationer för Azure.

## <a name="view-purchased-reservations"></a>Visa köpta reservationer

Som standard när du köper en reservation kan du och kontoadministratören visa reservationen. Du och kontoadministratören får automatiskt rollen ägare reservationsbeställning och reservation. Om du vill att andra ska kunna se reservationen, måste du lägga till dem som en **ägare** eller **läsare** på reservationsbeställning eller reservation.

Mer information finns i [Lägg till eller ändra användare som kan hantera en reservation](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Visa en reservation ägare eller läsare,

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Sök på **reservationer**.
    ![Skärmbild som visar Azure portal-sökning](./media/billing-view-reservation/portal-reservation-search.png)  
3. I listan visas alla bokningar där du har rollen ägare eller läsare. Varje reservation visar det senaste kända nätverksutnyttjandet i procent.  
    ![Exemplet visar en lista med reservationer](./media/billing-view-reservation/view-reservations.png)
4. Välj en reservation och se användningstrenden sista fem dagar.  
    ![Exempel som visar reservation användningstrend](./media/billing-view-reservation/reservation-utilization.png)
5. Du kan också hämta den [reservation användning](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) med användningen av reserverade instanser API och med den [Microsoft Azure Consumption Insights Power BI-Innehållspaketet](/power-bi/service-connect-to-azure-consumption-insights).

Om du vill ändra omfattningen för en reservation kan dela en reservation eller ändra vem som kan hantera en reservation Se [hantera Azure-reservationer](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Visa reservation transaktioner för Enterprise-registreringar

 Om du har en partner som leds Enterprise-registrering kan du visa reservationer genom att gå till **rapporter** i EA-portalen. Du kan visa reservationer i EA-portalen och i Azure-portalen för andra Enterprise-registreringar. Du måste vara ett EA-administratören ska kunna visa reservation transaktioner.

Visa transaktioner för reserverade Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök efter **Kostnadshantering och fakturering**.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Välj **Reservation transaktioner**.
1. Om du vill filtrera resultatet väljer **Timespan**, **typ**, eller **beskrivning**.
1. Välj **Använd**.

    ![Skärmbild som visar reservation transaktioner resultat](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Om du vill hämta data med hjälp av ett API, se [hämta reserverad instans debiteras för företagskunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure reservationer, finns i följande artiklar:

- [Vad är reservationer för Azure?](billing-save-compute-costs-reservations.md)
- [Hantera reservationer för Azure](billing-manage-reserved-vm-instance.md)

Köp en serviceplan:

- [Betala i förskott för Cosmos DB reserverad kapacitet](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Köp en plan för programvara:

- [Betala i förskott för Red Hat programvaruplaner från Azure-reservationer](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Förskottsbetala för SUSE-programvaruplaner från Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)

Förstå användningen av:

- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Förstå användningen av reserverade för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
