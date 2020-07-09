---
title: Felsöka problem med att visa faktureringskontot på Azure-portalen
description: Den här artikeln beskriver hur du felsöker problem med att visa ditt faktureringskonto på Azure-portalen.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/24/2020
ms.author: banders
ms.openlocfilehash: 671189a6a328a77653b61542e3e30c848a75cb65
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85364286"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Felsöka problem med att visa faktureringskontot på Azure-portalen

När du registrerar dig för att använda Azure skapas ett faktureringskonto. Du använder faktureringskontot för att hantera fakturor och betalningar samt spåra kostnader. Du kan ha åtkomst till flera faktureringskonton. Du kanske till exempel ha registrerat dig för Azure för personligt bruk. Du kan även ha åtkomst till Azure via organisationens Enterprise-avtal eller Microsoft-kundavtal. I den här situationen skulle du ha två separata faktureringskonton. Den här artikeln beskriver hur du felsöker problem med att visa ditt faktureringskonto på Azure-portalen.

Du kan visa dina faktureringskonton på sidan [Kostnadshantering + fakturering](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade).

Du kan läsa mer om faktureringskonton och se vilken typ av faktureringskonto du har i [Visa faktureringskonton i Azure-portalen](view-all-accounts.md).

Om du inte ser ditt faktureringskonto på Azure-portalen provar du följande:

## <a name="sign-in-to-a-different-tenant"></a>Logga in i en annan klientorganisation

Ditt faktureringskonto är associerat med en enda Azure Active Directory-klientorganisation. Du ser inte ditt faktureringskonto på sidan Kostnadshantering + fakturering om du är inloggad i fel klientorganisation. Följ stegen nedan för att byta till en annan klientorganisation på Azure-portalen och visa dina faktureringskonton i den klientorganisationen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj din profil (e-postadress) längst upp till höger på sidan.
1. Välj **Växla katalog**.  
    ![Skärmbild som visar hur du byter katalog på portalen](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. Välj en katalog i avsnittet **Alla kataloger**.  
    ![Skärmbild som visar hur du väljer en katalog på portalen](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>Logga in med en annan e-postadress

Vissa användare har flera e-postadresser som de kan använda för att logga in på [Azure-portalen](https://portal.azure.com). Alla e-postadresser har åtkomst till ett faktureringskonto. Om du loggar in med en e-postadress som har behörighet att hantera resurser, men som inte har behörighet att visa ett faktureringskonto, så ser du inte faktureringskontot på sidan [Kostnadshantering + fakturering](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) på Azure-portalen.

Logga in på Azure-portalen med en e-postadress som har behörighet till faktureringskontot för att komma åt ditt faktureringskonto.

## <a name="sign-in-with-a-different-identity"></a>Logga in med en annan identitet

Vissa användare har två identiteter med samma e-postadress, ett arbetskonto eller skolkonto och ett personligt konto. Normalt har bara en av identiteterna behörighet att visa ett faktureringskonto. Du kan ha två identiteter med en enda e-postadress. När du loggar in med en identitet som inte har behörighet att visa ett faktureringskonto visas inte faktureringskontot på sidan [Kostnadshantering + fakturering](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade). Växla identitet genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com) i ett InPrivate-/Incognito-fönster.
1. Om din e-postadress har två identiteter visas ett alternativ som låter dig välja ett personligt konto eller ett arbets- eller skolkonto. Välj ett av kontona.
1. Om du inte ser faktureringskontot på sidan Kostnadshantering + fakturering på Azure-portalen upprepar du steg 1 och 2 och väljer den andra identiteten.

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om fakturering och prenumerationer om du behöver felsöka problem.

- [Nekat kort](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [Problem med inloggning till prenumeration](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [Inga prenumerationer hittades](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Företagskostnadsvy inaktiverad](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
