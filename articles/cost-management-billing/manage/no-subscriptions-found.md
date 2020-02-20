---
title: Felet Inga prenumerationer hittades – inloggningen i Azure-portalen | Microsoft Docs
description: Den här artikeln innehåller en lösning på problemet där inga prenumerationer hittas när du loggar in i Azure-portalen eller Azure-kontocentret.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 5011f0a09e33221650cf050578db5ab1d6b0a033
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200630"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Felet Inga prenumerationer hittades för Azure-portalen och Azure-kontocentret

Du kan se felet ”Inga prenumerationer hittades” när du försöker logga in på [Azure-portalen](https://portal.azure.com/) eller [Azure-kontocentret](https://account.windowsazure.com/Subscriptions). Den här artikeln innehåller en lösning på det här problemet.

## <a name="symptom"></a>Symptom

När du försöker logga in i [Azure-portalen](https://portal.azure.com/) eller [Azure-kontocentret](https://account.windowsazure.com/Subscriptions) visas följande felmeddelande: ”Inga prenumerationer hittades”.

## <a name="cause"></a>Orsak

Det här problemet inträffar om du har valt fel katalog eller om kontot inte har tillräcklig behörighet.

## <a name="solution"></a>Lösning

### <a name="scenario-1-error-message-is-received-in-the-azure-portal"></a>Scenario 1: Felmeddelandet visas i [Azure-portalen](https://portal.azure.com)

Så här åtgärdar du problemet:

* Se till att du har valt rätt Azure-katalog genom att klicka på ditt konto längst upp till höger.

  ![Välj katalogen längst upp till höger i Azure-portalen](./media/no-subscriptions-found/directory-switch.png)
* Om du har valt rätt Azure-katalog men du fortfarande ser felmeddelandet ska du [lägga till rollen som ägare för kontot](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-center"></a>Scenario 2: Felmeddelandet visas i [Azure-kontocentret](https://account.windowsazure.com/Subscriptions)

Kontrollera om kontot du använder är en kontoadministratör. Så här kontrollerar du vem som är kontoadministratör:

1. Logga in i [vyn Prenumerationer i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Välj den prenumeration du vill kontrollera och gå till **Inställningar**.
1. Välj **Egenskaper**. Prenumerationens kontoadministratör visas i rutan **Kontoadministratör**.  

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
