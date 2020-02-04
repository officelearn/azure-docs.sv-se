---
title: Registrera dig för Azure med ett Office 365-konto | Microsoft Docs
description: Lär dig hur du skapar en Azure-prenumeration med hjälp av ett Office 365-konto
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 612c9d3e66a54e248c011e8dbedec2e2291b6a8f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "75988128"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Registrera dig för en Azure-prenumeration med ditt Office 365-konto
Om du har en Office 365-prenumeration kan du använda ditt Office 365-konto för att skapa en Azure-prenumeration. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt användarnamn och lösenord för Office 365. Om du vill konfigurera virtuella datorer eller använda andra Azure-tjänster måste du registrera dig för en Azure-prenumeration. Du kan dela din Azure-prenumeration med andra och [använda rollbaserad åtkomstkontroll för att hantera åtkomst till din Azure-prenumeration och dina resurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Om du redan har både ett Office 365-konto och en Azure-prenumeration kan du läsa [Associera en Office 365-klientorganisation med en Azure-prenumeration](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Skaffa en Azure-prenumeration med hjälp av ditt Office 365-konto

Spara tid och undvik kontospridning genom att registrera dig för Azure med hjälp av ditt användarnamn och lösenord för Office 365.

1. Registrera dig på [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Logga in med hjälp av ditt användarnamn och lösenord för Office 365. Det konto som du använder behöver inte ha administratörsbehörighet. Om du har fler än ett Office 365-konto bör du använda autentiseringsuppgifterna för det Office 365-konto som du vill associera med din Azure-prenumeration.

   ![Skärmbild som visar inloggningssidan.](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Ange den information som krävs och slutför registreringsprocessen. Viss information krävs kanske inte om du redan har ett Office 365-konto.

    ![Skärmbild som visar registreringsformuläret.](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Om du behöver lägga till andra personer i din organisation till Azure-prenumerationen kan du läsa [Kom igång med åtkomsthantering i Azure-portalen](../../role-based-access-control/overview.md).

## <a id="more-about-subs">Mer information om Azure- och Office 365-prenumerationer</a>
Office 365 och Azure använder Azure AD-tjänsten för att hantera användare och prenumerationer. Azure-katalogen är som en container där du kan gruppera användare och prenumerationer. Om du vill använda samma användarkonton för dina Azure- och Office 365-prenumerationer behöver du se till att Azure-prenumerationerna skapas i samma katalog som Office 365-prenumerationerna. Tänk på följande punkter:

* En prenumeration skapas under en katalog
* Användare tillhör kataloger
* En prenumeration hamnar i katalogen för den användare som skapar prenumerationen. Din Office 365-prenumeration är alltså kopplad till samma konto som din Azure-prenumeration.
* Azure-prenumerationer ägs av enskilda användare i katalogen
* Office 365-prenumerationer ägs av själva katalogen. Användare med rätt behörigheter i katalogen kan hantera dessa prenumerationer.

![Skärmbild som visar relationen mellan katalogen, användare och prenumerationer.](./media/office-365-account-for-azure-subscription/19-background-information.png)

Mer information finns i [Så är Azure-prenumerationer associerade med Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
