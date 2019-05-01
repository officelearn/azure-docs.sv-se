---
title: 'API: er för Azure reservation automation | Microsoft Docs'
description: 'Läs mer om Azure-API: er som du kan använda för att hämta reservationsinformation om programmässigt.'
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2019
ms.author: banders
ms.openlocfilehash: 6d63f9a393dbb40c3b0952eba9ab9449fd7b558d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702611"
---
# <a name="apis-for-azure-reservation-automation"></a>API: er för Azure reservation automation

Använd Azure API: er för att programmässigt få information för din organisation om Azure-tjänsten eller programvaran reservationer.

## <a name="find-reservation-plans-to-buy"></a>Hitta reservation planer på att köpa

Använda reservationen Rekommendationen API för att få rekommendationer som reservationer som tänker köpa utifrån din organisations användning. Mer information finns i [få rekommendationer för reservation](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Du kan också analysera resursanvändningen genom att använda kostnadsinformation för förbrukning API. Mer information finns i [användningsinformation - lista för Billing Period av faktureringskonto](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod). Azure-resurser som du använder konsekvent är vanligtvis det bästa alternativet för en reservation.

## <a name="buy-a-reservation"></a>Köpa en reservation

Du kan köpa Azure-reservationer och programvaruplaner programmässigt med hjälp av REST API: er. Mer information finns i [Reservationsbeställnings - köp API](/rest/api/reserved-vm-instances/reservationorder/purchase).

Här är en exempelförfrågan att köpa med hjälp av REST-API:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Begärandetext:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Du kan också köpa en reservation i Azure-portalen. Mer information finns i följande artiklar:

Service-planer:
- [Virtuell dator](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Programvaruplaner:
- [SUSE Linux-programvara](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Hämta reservationer

Om du är Azure-kund med ett Enterprise-avtal (EA-kund) kan du få reservationerna organisationen har köpt med hjälp av den [hämta reserverad instans transaktion debiterar API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). För andra prenumerationer, hämta en lista över reservationer som du har köpt och har behörighet att visa med hjälp av API: et [Reservationsbeställnings - listan](/rest/api/reserved-vm-instances/reservationorder/list). Det ägare eller den person som har köpt reservationen har behörighet att visa reservationen som standard.

## <a name="see-reservation-usage"></a>Se reservation användning

Om du är en EA-kund kan visa du programmässigt hur reservationer i din organisation används. Mer information finns i [användningen av hämta reserverade instanser för företagskunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). Använd API: et för andra prenumerationer, [reservationer sammanfattningar - lista med Reservation ordning och Reservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Om du märker att din organisations reservationer som underanvänd:

- Kontrollera att de virtuella datorerna som skapas i din organisation matchar den Virtuella datorstorlek som finns på reservationen.
- Kontrollera att instansen storlek flexibilitet är på. Mer information finns i [hantera reservationer - ändringen Optimera för reserverade VM-instanser](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Ändra omfång för reservation som delas så att den gäller bredare. Mer information finns i [hantera reservationer – ändra omfattningen för en reservation](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Utbyta kvantitet som inte används. Mer information finns i [hantera reservationer - avbokningar och utbyten](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Ge åtkomst till reservationer

Hämta en lista över alla reservationer att en användare har åtkomst till med hjälp av den [Reservation - åtgärden - listan API](/rest/api/reserved-vm-instances/reservationorder/list). Om du vill ge åtkomst till en reservation via programmering finns i följande artiklar:

- [Hantera åtkomst med RBAC och REST API](../role-based-access-control/role-assignments-rest.md)
- [Hantera åtkomst med RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Hantera åtkomst med RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Delad tunnel eller merge-reservation

När du köper fler än en resursinstans inom en reservation kan du tilldela instanser inom det reserverade till olika prenumerationer. Du kan ändra omfång för reservation så att den gäller för alla prenumerationer i samma kontext för fakturering. Men för cost management eller budgetering, du kanske vill hålla som ”enstaka prenumeration” och tilldela reserverade instanser till en viss prenumeration.

Använd API för att dela upp en reservation [Reservation - dela](/rest/api/reserved-vm-instances/reservation/split). Du kan också dela upp en reservation med hjälp av PowerShell. Mer information finns i [hantera reservationer - Split-reservation i två reservationer](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Använd API för att slå samman två reservationer till en reservation [Reservation - Merge](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Ändra omfång för en reservation

En reservationens omfång kan vara enskild prenumeration eller i alla prenumerationer i din faktureringskontexten. Om du ställer in omfånget för enskild prenumeration matchade reservationen att köra resurser i den valda prenumerationen. Om du ställer in omfånget som delas matchar reservationen till resurser som körs i alla prenumerationer i kontexten fakturering i Azure. Fakturering kontexten är beroende av den prenumeration som du använde för att köpa reservationer. Mer information finns i [hantera reservationer – ändra omfattningen](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

Du kan ändra omfånget programmässigt med API: et [Reservation - uppdatering](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Läs mer

- [Vad är reservationer för Azure](billing-save-compute-costs-reservations.md)
- [Förstå hur VM-reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå hur SUSE Linux Enterprise software plan rabatten](billing-understand-suse-reservation-charges.md)
- [Förstå hur andra reservationen rabatter](billing-understand-reservation-charges.md)
- [Förstå användningen av reservation för prenumerationen med användningsbaserad betalning](billing-understand-reserved-instance-usage.md)
- [Förstå användningen av reserverade för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)
- [Azure reservationer i programmet för Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
