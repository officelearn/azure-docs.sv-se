---
title: Felsöka Inga berättigade prenumerationer i Azure-portalen
description: Den här artikeln hjälper dig att felsöka felmeddelandet Inga berättigade prenumerationer i Azure-portalen när du försöker köpa en reservation.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/14/2020
ms.openlocfilehash: fd7a2bde47f34a61390082a223409070275b64ce
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92115234"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Felsöka Inga berättigade prenumerationer

Den här artikeln hjälper dig att felsöka felmeddelandet *Inga berättigade prenumerationer* i Azure-portalen när du försöker köpa en reservation.

## <a name="symptoms"></a>Symtom

1. Logga in på [Azure-portalen](https://portal.azure.com) och gå till **Reservationer** .
1. Välj **Lägg till** och välj sedan en tjänst.
1. Följande felmeddelande visas:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should be an owner on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. I området **Välj den produkt du vill köpa** expanderar du listan **Faktureringsprenumeration** för att se orsaken till att en specifik prenumeration inte är berättigad att köpa en reserverad instans. Följande bild visar exempel på varför en reservation inte kan köpas.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Exempel som visar varför en reservation inte kan köpas" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Orsak

För att köpa en reserverad Azure-instans måste du ha minst en prenumeration som uppfyller följande krav:

- Prenumerationen måste vara en erbjudandetyp som stöds. Erbjudandetyper som stöds är: Betala per användning, Cloud Solution Provider (CSP), Microsoft Azure Enterprise eller Microsoft-kundavtal.
- Du måste vara ägare till prenumerationen.

När du inte har en prenumeration som uppfyller kraven får du felet `No eligible subscriptions`.

### <a name="cause-1"></a>Orsak 1

Prenumerationen måste vara en erbjudandetyp som stöds. Erbjudandetyper som stöds är: Betala per användning, Microsoft Azure Enterprise eller Microsoft-kundavtal. Din prenumerationstyp stöds inte. När du väljer en prenumeration som har en erbjudandetyp som inte stöder reservationer visas följande fel.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Exempel som visar varför en reservation inte kan köpas" :::

### <a name="cause-2"></a>Orsak 2

Du måste vara ägare till prenumerationen. Du är inte ägare till prenumerationen. När du väljer en prenumeration som du inte är ägare av visas följande fel.

```
You do not have owner access on the subscription

You can only purchase reservations using subscriptions on which you have owner access.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/no-owner-access.png" alt-text="Exempel som visar varför en reservation inte kan köpas" :::

## <a name="solution"></a>Lösning

- Om ditt aktuella erbjudande inte stöder reservationer behöver du skapa en ny Azure-prenumeration.
- Om du inte har åtkomst till en befintlig reservation kan du få åtkomst till den från den aktuella ägaren.

### <a name="solution-1"></a>Lösning 1

För att köpa en reservation behöver du skapa en ny Azure-prenumeration som stöder reservationer.

- Om du har en kostnadsfri utvärderingsversion av Azure kan du [uppgradera dina prenumerationer](../manage/upgrade-azure-subscription.md).
- Du kan skapa en ny Azure-prenumeration med [Betala per användning-pris](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Registrera dig för ett [Microsoft-kundavtal](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) och skapa en ny prenumeration.
- Köp en ny prenumeration med en [CSP](https://www.microsoft.com/solution-providers/home) och skapa en ny prenumeration.

### <a name="solution-2"></a>Lösning 2

För att få ägaråtkomst till en reservation måste du få åtkomst till antingen:

- Den reservationsorder som reservationen köptes med
- Själva reservationen

Den aktuella ägaren till reservationsordern eller reservationsägaren kan delegera åtkomst till dig via följande steg.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** > **Reservationer** så visas de reservationer du har åtkomst till.
1. Markera den reservation som du vill ge andra användare åtkomst till.
1. Välj **Åtkomstkontroll (IAM)** .
1. Välj **Lägg till rolltilldelning** > **Roll** > **Ägare** . Du kan också välja en annan roll om du vill ge begränsad åtkomst.
1. Ange e-postadressen för den användare som du vill lägga till som ägare.
1. Välj användaren och sedan **Spara** .

Du kan läsa mer i [Lägga till eller ändra användare som kan hantera en reservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="next-steps"></a>Nästa steg

- Läs [Lägga till eller ändra användare som kan hantera en reservation](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation) om du behöver beviljas åtkomst av en reservationsägare.