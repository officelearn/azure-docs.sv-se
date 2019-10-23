---
title: API:er för Azure-reservationsautomation | Microsoft Docs
description: Lär dig mer om de Azure-API:er som du kan använda för att programmatiskt hämta reservationsinformation.
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 57e38888249de448f5286baa1b5ebb0e2c01bda9
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757406"
---
# <a name="apis-for-azure-reservation-automation"></a>API:er för Azure-reservationsautomation

Använd Azure-API:er för att programmatiskt hämta information för din organisation angående reservationer för Azure-tjänster eller programvara.

## <a name="find-reservation-plans-to-buy"></a>Hitta reservationsplaner att köpa

Använd API:et för reservationsrekommendationer för att få rekommendationer om vilka reservationsplaner som du bör köpa utifrån organisationens användning. Mer information finns i [Få reservationsrekommendationer](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Du kan även analysera din resursanvändning med hjälp av användningsinformationen från förbruknings-API:et. Mer information finns i [Användningsinformation – Lista över faktureringsperiod per faktureringskonto](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy). De Azure-resurser som du använder konsekvent är vanligtvis den bästa kandidaten för en reservation.

## <a name="buy-a-reservation"></a>Köpa en reservation

Du kan köpa Azure-reservationer och programvaruplaner programmatiskt med hjälp av REST-API:er. Mer information finns i [Reservationsbeställning – API för inköp](/rest/api/reserved-vm-instances/reservationorder/purchase).

Här är ett exempel på en köpbegäran med hjälp av REST API:

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

Du kan även köpa en reservation i Azure-portalen. Mer information finns i följande artiklar:

Tjänstplaner:
- [Virtuell dator](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Programvaruplaner:
- [SUSE Linux-programvara](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Hämta reservationer

Om du är Azure-kund med ett Enterprise-avtal (EA-kund) kan du hämta de reservationer som din organisation har köpt med hjälp av [API:et för hämtning av transaktionsavgifter för reserverad instans](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). För andra prenumerationer hämtar du listan över reservationer som du har köpt och har behörighet att visa med hjälp av API:et [Reservationsbeställning – lista](/rest/api/reserved-vm-instances/reservationorder/list). Som standard har kontoägaren eller den person som köpte reservationen behörighet att visa reservationen.

## <a name="see-reservation-usage"></a>Se reservationsanvändning

Om du är EA-kund kan du programmatiskt visa hur organisationens reservationer används. Mer information finns i [Hämta användning av reserverad instans för företagskunder](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). För andra prenumerationer använder du API:et [Reservationssammanfattningar – lista per reservationsbeställning och reservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Gör så här om du märker att organisationens reservationer underutnyttjas:

- Se till att de virtuella datorer som organisationen skapar matchar reservationens VM-storlek.
- Se till att flexibel instansstorlek är aktiverat. Mer information finns i [Hantera reservationer – ändra optimeringsinställningen för reserverade VM-instanser](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Ändra reservationsomfånget till delat så att det gäller mer allmänt. Mer information finns i [Hantera reservationer – ändra omfånget för en reservation](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Byt ut den outnyttjade kvantiteten. Mer information finns i [Hantera reservationer](billing-manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>Ge åtkomst till reservationer

Hämta listan över alla reservationer som en användare har åtkomst till med hjälp av [API:et för reservation – åtgärd – lista](/rest/api/reserved-vm-instances/reservationorder/list). Information om hur du ger åtkomst till en reservation programmatiskt finns i följande artiklar:

- [Hantera åtkomst med hjälp av RBAC och REST-API:et](../role-based-access-control/role-assignments-rest.md)
- [Hantera åtkomst med hjälp av RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Hantera åtkomst med hjälp av RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dela eller sammanslå reservation

När du har köpt fler än en resursinstans i en reservation vill du kanske tilldela instanserna i den reservationen till olika prenumerationer. Du kan ändra reservationsomfånget så att det gäller för alla prenumerationer inom samma faktureringskontext. Men för kostnadshantering eller budgetering vill du kanske behålla omfånget som ”enstaka prenumeration” och tilldela reservationsinstanser till en specifik prenumeration.

Du delar en reservation med hjälp av API:et [Reservation – dela](/rest/api/reserved-vm-instances/reservation/split). Du kan även dela en reservation med hjälp av PowerShell. Mer information finns i [Hantera reservationer – dela upp en reservation i två reservationer](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Om du vill sammanslå två reservationer till en reservation använder du API:et [Reservation – sammanslå](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Ändra omfång för en reservation

Omfånget för en reservation kan vara en enskild prenumeration, en enskild resursgrupp eller alla prenumerationer i din faktureringskontext. Om du anger omfånget till en enskild prenumeration eller en enskild resursgrupp matchas reservationen till resurser som körs i den valda prenumerationen. Om du tar bort eller flyttar prenumerationen eller resursgruppen används inte reservationen.  Om du anger omfånget till delat matchar Azure reservationen till resurser som körs i alla prenumerationer i faktureringskontexten. Faktureringskontexten är beroende av den prenumeration som du använde för att köpa reservationen. Du kan välja omfånget vid inköpstillfället eller ändra det när som helst efter köpet. Mer information finns i [Hantera reservationer – ändra omfånget](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

Om du vill ändra omfånget programmatiskt använder du API:et [Reservation – uppdatera](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Läs mer

- [Vad är reservationer för Azure](billing-save-compute-costs-reservations.md)
- [Förstå hur VM-reservationsrabatten tillämpas](billing-understand-vm-reservation-charges.md)
- [Förstå hur rabatten i programvaruplanen för SUSE Linux Enterprise tillämpas](billing-understand-suse-reservation-charges.md)
- [Förstå hur andra reservationsrabatter tillämpas](billing-understand-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](billing-understand-reserved-instance-usage.md)
- [Förstå reservationsanvändning för din Enterprise-registrering](billing-understand-reserved-instance-usage-ea.md)
- [Kostnader för Windows-programvara ingår inte i reservationer](billing-reserved-instance-windows-software-costs.md)
- [Azure-reservationer i ett CSP-program (Cloud Solution Provider) i Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)
