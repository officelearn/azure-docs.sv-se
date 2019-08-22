---
title: Felsöka inloggnings problem i Azure-prenumeration
description: Hjälper till att lösa de problem som du inte kan använda för att logga in Azure Portal eller Azure konto Center.
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657052"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Felsöka inloggnings problem i Azure-prenumeration

Den här guiden hjälper dig att lösa de problem som du inte kan använda för att logga in Azure Portal eller Azure konto Center. 

## <a name="issues"></a>Problem

### <a name="page-hangs-in-the-loading-status"></a>Sidan låser sig i inläsnings status

Om sidan på din webbläsare låser sig kan du prova med följande steg tills du får Azure Portal.

- Uppdatera sidan.
- Använd en annan webbläsare.
- Använd läget privat webbläsare för webbläsaren. För Internet Explorer: Klicka på **verktyg** > **säkerhet** > InPrivate-**surfning**och bläddra sedan och logga in i [Azure Portal](https://portal.azure.com/) eller [Azure konto Center](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Du är automatiskt inloggad som en annan användare

Det här problemet kan uppstå om du använder mer än ett användar konto i en webbläsare.

Försök att lösa problemet på något av följande sätt:

- Rensa cacheminnet och ta bort Internet-cookies. I Internet Explorer klickar du på **verktyg** > **Internet alternativ** > **ta bort**. Kontrol lera att kryss rutorna för temporära filer, cookies, lösen ord och webb läsar historik är markerade och klicka sedan på ta bort.
- Återställ Internet Explorer-inställningarna för att återställa alla personliga inställningar som du har gjort. Klicka på **verktyg** > **Internet alternativ** > **Avancerat** > Välj rutan **ta bort personliga inställningar** > **Återställ**.
- Använd läget privat webbläsare för webbläsaren. För Internet Explorer:  Klicka på **verktyg** > **säkerhet** > InPrivate-**surfning**och bläddra sedan och logga in i [Azure Portal](https://portal.azure.com/) eller [Azure konto Center](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Jag kan logga in, men jag ser *inga prenumerationer som hittas*

Det här problemet uppstår om du valde i fel katalog, eller om ditt konto inte har tillräcklig behörighet.

**Scenario 1:** Fel meddelandet tas emot i [Azure Portal](https://portal.azure.com/)

Att åtgärda problemet:

- Kontrollera att rätt Azure-katalogen har valts genom att klicka på ditt konto längst upp till höger.
- Om rätt Azure-katalog är markerad men du fortfarande får fel meddelandet, har ditt konto [lagts till som ägare](billing-add-change-azure-subscription-administrator.md).

**Scenario 2:** Fel meddelandet tas emot i [Azure-kontocenter](https://account.windowsazure.com/Subscriptions)

Kontrollera om det konto som du använde kontoadministratör. Följ dessa steg för att kontrollera vem som är kontoadministratör:

1. Logga in på den [prenumerationer visa i Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Välj den prenumeration som du vill kontrollera och tittar sedan under **inställningar**.

3. Välj **egenskaper**. Kontoadministratör för prenumerationen visas i den **kontoadministratören** box.

## <a name="additional-help-resources"></a>Ytterligare hjälp resurser

Andra fel söknings artiklar för Azure-fakturering och-prenumerationer

- [Nekat kort](billing-troubleshoot-declined-card.md)
- [Prenumerations registrerings problem](billing-troubleshoot-azure-sign-up.md)
- [Inga prenumerationer hittades](billing-no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](index.md)
