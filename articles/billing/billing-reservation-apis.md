---
title: 'API: er för Azure reservation Automation | Microsoft Docs'
description: 'Lär dig mer om de Azure-API: er som du kan använda för att hämta reservations information via programmering.'
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: 36bc403c4000e58541f22c2cb44f77a28e81cb72
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779893"
---
# <a name="apis-for-azure-reservation-automation"></a>API: er för Azure reservation Automation

Använd Azure API: er för att få information om din organisation att hämta information om Azure-tjänster eller program varu reservationer.

## <a name="find-reservation-plans-to-buy"></a>Hitta reservations planer att köpa

Använd reservations rekommendations-API: et för att få rekommendationer om vilka boknings planer som ska köpas utifrån organisationens användning. Mer information finns i [Hämta reservations rekommendationer](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Du kan också analysera resursanvändningen genom att använda informationen om användning av förbruknings-API. Mer information finns i [användnings information – lista för fakturerings perioden per fakturerings konto](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod). De Azure-resurser som du använder konsekvent är vanligt vis den bästa kandidaten för en reservation.

## <a name="buy-a-reservation"></a>Köpa en reservation

Du kan köpa Azure-reservationer och program varu planer program mässigt med hjälp av REST API: er. Mer information finns i [reservations order – inköps-API](/rest/api/reserved-vm-instances/reservationorder/purchase).

Här är en exempel förfrågan att köpa med hjälp av REST API:

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

Du kan också köpa en reservation i Azure Portal. Mer information finns i följande artiklar:

Service planer:
- [Virtuell dator](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Program varu planer:
- [SUSE Linux-programvara](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Hämta reservationer

Om du är en Azure-kund med en Enterprise-avtal (EA-kund) kan du hämta de reservationer som organisationen har köpt med hjälp av prenumerations- [API: t get reserverad instance Transaction](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). För andra prenumerationer hämtar du listan över reservationer som du har köpt och har behörighet att visa med hjälp av [listan API reservation order-list](/rest/api/reserved-vm-instances/reservationorder/list). Som standard har konto ägaren eller personen som köpte reservationen behörighet att Visa reservationen.

## <a name="see-reservation-usage"></a>Se reservations användning

Om du är EA-kund kan du se hur reservationer i din organisation används program mässigt. Mer information finns i [Hämta reserverad instans användning för företags kunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). För andra prenumerationer använder du sammanfattningar av API [-reservationer – lista efter reservations order och reservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Om du upptäcker att din organisations reservationer används:

- Se till att de virtuella datorerna som din organisation skapar matchar den virtuella dator storleken som finns på reservationen.
- Kontrol lera att storleks flexibiliteten för en instans är på. Mer information finns i [hantera reservationer-ändra optimerings inställning för reserverade VM-instanser](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Ändra reservations omfånget till delat så att det kan användas mer brett. Mer information finns i [hantera reservationer – ändra omfånget för en reservation](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Utbyt den outnyttjade kvantiteten. Mer information finns i [hantera reservationer](billing-manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>Ge åtkomst till reservationer

Hämta listan över alla reservationer som en användare har åtkomst till med hjälp av [API: t reservation-operation-List](/rest/api/reserved-vm-instances/reservationorder/list). Information om hur du ger åtkomst till en reservation program mässigt finns i någon av följande artiklar:

- [Hantera åtkomst med RBAC och REST API](../role-based-access-control/role-assignments-rest.md)
- [Hantera åtkomst med RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Hantera åtkomst med RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dela eller sammanfoga reservation

När du har köpt fler än en resurs instans i en reservation kanske du vill tilldela instanser i den reservationen till olika prenumerationer. Du kan ändra reservations omfånget så att det gäller alla prenumerationer inom samma fakturerings kontext. Men för kostnads hanterings-eller budgeterings syfte kanske du vill behålla omfånget som "enskild prenumeration" och tilldela reservations instanser till en viss prenumeration.

Om du vill dela upp en reservation använder du API [-reservationen-Split](/rest/api/reserved-vm-instances/reservation/split). Du kan också dela en reservation med hjälp av PowerShell. Mer information finns i [hantera reservationer – dela reservation i två reservationer](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Använd API [reservation-sammanslagning](/rest/api/reserved-vm-instances/reservation/merge)för att koppla två reservationer till en reservation.

## <a name="change-scope-for-a-reservation"></a>Ändra omfång för en reservation

Omfånget för en reservation kan vara en enskild prenumeration, en enda resurs grupp eller alla prenumerationer i din fakturerings kontext. Om du anger omfånget till en enskild prenumeration eller en enskild resurs grupp matchas reservationen för att köra resurser i den valda prenumerationen. Om du tar bort eller flyttar prenumerationen eller resurs gruppen används inte reservationen.  Om du ställer in scopet på delad, matchar Azure reservationen för resurser som körs i alla prenumerationer i fakturerings kontexten. Fakturerings kontexten är beroende av den prenumeration som du använde för att köpa reservationen. Du kan välja omfånget vid köp eller ändra det när du har köpt. Mer information finns i [hantera reservationer – ändra](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)omfånget.

Om du vill ändra omfånget program mässigt använder du API [reservation-Update](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Lär dig mer

- [Vad är reservationer för Azure?](billing-save-compute-costs-reservations.md)
- [Förstå hur reservations rabatten för den virtuella datorn tillämpas](billing-understand-vm-reservation-charges.md)
- [Ta reda på hur SUSE Linux Enterprise Software plan-rabatten tillämpas](billing-understand-suse-reservation-charges.md)
- [Förstå hur andra reservations rabatter tillämpas](billing-understand-reservation-charges.md)
- [Förstå reservations användningen för din prenumeration enligt principen betala per användning](billing-understand-reserved-instance-usage.md)
- [Förstå reservations användningen för företags registreringen](billing-understand-reserved-instance-usage-ea.md)
- [Windows-programkostnader som inte ingår i reservationer](billing-reserved-instance-windows-software-costs.md)
- [Azure Reservations i programmet Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
