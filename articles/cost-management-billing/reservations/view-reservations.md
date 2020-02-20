---
title: Visa reservationer för Azure-resurser | Microsoft Docs
description: Lär dig hur du visar Azure-reservationer i Azure-portalen.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5c9d9074e4b8d0d9e36417daee4d58c1d9b28b64
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199253"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Visa Azure-reservationer i Azure-portalen

Beroende på typen av prenumerations och din behörighet finns det ett par olika sätt att visa reservationer för Azure.

## <a name="view-purchased-reservations"></a>Visa köpta reservationer

När du köper en reservation kan du och kontoadministratören som standard visa reservationen. Du och kontoadministratören får automatiskt rollen som ägare för reservationsbeställningen och reservationen. Om du vill att andra personer ska kunna visa reservationen måste du lägga till dem som **ägare** eller **läsare** för reservationsbeställningen eller reservationen.

Du kan läsa mer i [Lägga till eller ändra användare som kan hantera en reservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Så här visar du en reservation som ägare eller läsare:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Sök efter **Reservationer**.
    ![Skärmbild som visar en sökning i Azure-portalen](./media/view-reservations/portal-reservation-search.png)  
3. I listan visas alla reservationer där du har rollen ägare eller läsare. För varje reservation visas den senast kända utnyttjandegraden.  
    ![Exempel som visar en lista med reservationer](./media/view-reservations/view-reservations.png)
4. Om du väljer en reservation ser du utnyttjandetrenden för de senaste fem dagarna.  
    ![Exempel som visar trenden för reservationens utnyttjande](./media/view-reservations/reservation-utilization.png)
5. Du kan också hämta [reservationens utnyttjande](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) med hjälp av API:et för utnyttjande av reserverade instanser och [Power BI-innehållspaketet Microsoft Azure Consumption Insights](/power-bi/service-connect-to-azure-consumption-insights).

Om du behöver ändra omfattningen för en reservation, dela upp reservationen eller ändra vem som kan hantera den kan du läsa i [Hantera Azure-reservationer](manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Visa reservationstransaktioner för Enterprise-registreringar

 Om du har en partnerstyrd företagsregistrering kan du visa reservationer genom att gå till **Rapporter** i EA-portalen. För andra Enterprise-registreringar kan du visa reservationer i EA-portalen och i Azure-portalen. Du måste vara EA-administratör för att visa reservationstransaktioner.

Så här visar du reservationstransaktioner i Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar en sökning i Azure-portalen](./media/view-reservations/portal-cm-billing-search.png)

1. Välj **Reservationstransaktioner**.
1. Du kan filtrera resultaten genom att välja **Tidsintervall**, **Typ** eller **Beskrivning**.
1. Välj **Använd**.

    ![Skärmbild som visar resultat med reservationstransaktioner](./media/view-reservations/portal-billing-reservation-transaction-results.png)

Du kan läsa om att hämta data via ett API i [Hämta transaktionsavgifter för reserverade instanser för Enterprise-kunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
- [Hantera reservationer för Azure](manage-reserved-vm-instance.md)

Köpa en tjänstplan:

- [Förskottsbetala för en reserverad Cosmos DB-kapacitet](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../sql-database/sql-database-reserved-capacity.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

Köpa en programvaruplan:

- [Förskottsbetala för Red Hat-programvaruplaner från Azure Reservations](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Förskottsbetala för SUSE-programvaruplaner från Azure Reservations](../../virtual-machines/linux/prepay-suse-software-charges.md)

Förstå användningen:

- [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
- [Förstå reservationsanvändning för CSP-prenumerationer](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
