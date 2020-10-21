---
title: Registrera dig för Azure med ett Microsoft 365-konto
description: Lär dig hur du skapar en Azure-prenumeration med hjälp av ett Microsoft 365-konto. Du kan också associera befintliga Azure- och Microsoft 365-konton med varandra.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 255fbe4036c83618dd0131b262d13709ab7f9c2f
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131895"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>Registrera dig för en Azure-prenumeration med ditt Microsoft 365-konto

Om du har en Microsoft 365-prenumeration kan du använda ditt Microsoft 365-konto för att skapa en Azure-prenumeration. Logga in på [Azure-portalen](https://portal.azure.com/) med ditt användarnamn och lösenord för Microsoft 365. Om du vill konfigurera virtuella datorer eller använda andra Azure-tjänster måste du registrera dig för en Azure-prenumeration. Du kan dela din Azure-prenumeration med andra och [använda rollbaserad åtkomst i Azure (Azure RBAC) för att hantera åtkomst till din Azure-prenumeration och dina resurser](../../role-based-access-control/role-assignments-portal.md).

Om du redan har både ett Microsoft 365-konto och en Azure-prenumeration läser du artikeln om att [associera en Microsoft 365-klientorganisation med en Azure-prenumeration](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>Skaffa en Azure-prenumeration med hjälp av ditt Microsoft 365-konto

Spara tid och undvik kontospridning genom att registrera dig för Azure med ditt användarnamn och lösenord för Microsoft 365.

1. Registrera dig på [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Logga in med ditt användarnamn och lösenord för Microsoft 365. Det konto som du använder behöver inte ha administratörsbehörighet. Om du har fler än ett Microsoft 365-konto använder du autentiseringsuppgifterna för det Microsoft 365-konto som du vill associera med din Azure-prenumeration.

   ![Skärmbild som visar inloggningssidan.](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Ange den information som krävs och slutför registreringsprocessen. Viss information krävs kanske inte om du redan har ett Microsoft 365-konto.

    ![Skärmbild som visar registreringsformuläret.](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Om du behöver lägga till andra personer i din organisation till Azure-prenumerationen kan du läsa [Kom igång med åtkomsthantering i Azure-portalen](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Mer information om Azure- och Microsoft 365-prenumerationer</a>

Microsoft 365 och Azure använder Azure AD-tjänsten för att hantera användare och prenumerationer. Azure-katalogen är som en container där du kan gruppera användare och prenumerationer. Om du vill använda samma användarkonton för dina Azure- och Microsoft 365-prenumerationer behöver du se till att Azure-prenumerationerna skapas i samma katalog som Microsoft 365-prenumerationerna. Tänk på följande punkter:

* En prenumeration skapas under en katalog
* Användare tillhör kataloger
* En prenumeration hamnar i katalogen för den användare som skapar prenumerationen. Din Microsoft 365-prenumeration är alltså kopplad till samma konto som din Azure-prenumeration.
* Azure-prenumerationer ägs av enskilda användare i katalogen
* Microsoft 365-prenumerationer ägs av själva katalogen. Användare med rätt behörigheter i katalogen kan hantera dessa prenumerationer.

![Skärmbild som visar relationen mellan katalogen, användare och prenumerationer.](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

Mer information finns i [Så är Azure-prenumerationer associerade med Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Dela din Azure-prenumeration med andra och [använd rollbaserad åtkomst i Azure (Azure RBAC) för att hantera åtkomst till din Azure-prenumeration och dina resurser](../../role-based-access-control/role-assignments-portal.md).