---
title: Felsöka problem med inloggning för Azure-prenumeration
description: Hjälper till att lösa de problem som gör att du inte kan logga in på Azure-portalen eller Azure-kontocentret.
services: azure
author: v-miegge
manager: dcscontentpm
editor: na
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 74cf3f197895823bcbd94db0bf3c7bd8065df767
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988141"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Felsöka problem med inloggning för Azure-prenumeration

Den här guiden hjälper till att lösa de problem som gör att du inte kan logga in på Azure-portalen eller Azure-kontocentret.

## <a name="issues"></a>Problem

### <a name="page-hangs-in-the-loading-status"></a>Sidan låser sig under inläsningen

Om sidan i webbläsaren låser sig provar du vart och ett av följande steg tills du kommer till Azure-portalen.

- Uppdatera sidan.
- Använd en annan webbläsare.
- Använd privat surfläge för webbläsaren. För Internet Explorer: Klicka på **verktyg** > **säkerhet** > **InPrivate-surfning**och bläddra sedan och logga in i [Azure Portal](https://portal.azure.com/) eller [Azure konto Center](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Du loggas automatiskt in som en annan användare

Det här problemet kan uppstå om du använder mer än ett användarkonto i en webbläsare.

Försök att lösa problemet på något av följande sätt:

- Rensa cacheminnet och ta bort Internet-cookies. I Internet Explorer klickar du på **Verktyg** > **Internetalternativ** > **Ta bort**. Kontrollera att kryssrutorna för temporära filer, cookies, lösenord och webbläsarhistorik är markerade och klicka sedan på ta bort.
- Återställ Internet Explorer-inställningarna för att återställa alla personliga inställningar som du har gjort. Klicka på **Verktyg** > **Internetalternativ** > **Avancerat** > välj rutan **Ta bort personliga inställningar** > **Återställ**.
- Använd privat surfläge för webbläsaren. För Internet Explorer: Klicka på **verktyg** > **säkerhet** > **InPrivate-surfning**och bläddra sedan och logga in i [Azure Portal](https://portal.azure.com/) eller [Azure konto Center](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Jag kan logga in men det står *Inga prenumerationer hittades*

Det här problemet inträffar om du har valt fel katalog eller om kontot inte har tillräcklig behörighet.

**Scenario 1:** Fel meddelandet tas emot i [Azure Portal](https://portal.azure.com/)

Så här åtgärdar du problemet:

- Se till att du har valt rätt Azure-katalog genom att klicka på ditt konto längst upp till höger.
- Om rätt Azure-katalog har valts men du fortfarande får felmeddelandet ska du [lägga till ditt konto som ägare](add-change-subscription-administrator.md).

**Scenario 2:** Fel meddelandet tas emot i [Azure-kontocenter](https://account.windowsazure.com/Subscriptions)

Kontrollera om kontot du använder är en kontoadministratör. Så här kontrollerar du vem som är kontoadministratör:

1. Logga in i [vyn Prenumerationer i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Välj den prenumeration du vill kontrollera och gå till **Inställningar**.

3. Välj **Egenskaper**. Prenumerationens kontoadministratör visas i rutan **Kontoadministratör**.

## <a name="additional-help-resources"></a>Fler hjälpresurser

Här är några fler felsökningsartiklar som gäller fakturering och prenumerationer i Azure

- [Nekat kort](troubleshoot-declined-card.md)
- [Problem med prenumerationsregistrering](troubleshoot-azure-sign-up.md)
- [Inga prenumerationer hittades](no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](../../billing/index.md)
