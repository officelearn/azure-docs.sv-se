---
title: Registrera dig för Azure med Office 365-konto | Microsoft Docs
description: Lär dig hur du skapar en Azure-prenumeration med en Office 365-konto
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: cjiang
ms.openlocfilehash: dd7b9609495cacd324ef5509f57db0384775e0a7
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35966484"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Registrera dig för en Azure-prenumeration med ditt Office 365-konto
Om du har en Office 365-prenumeration kan använda du ditt Office 365-konto för att skapa en Azure-prenumeration. Logga in på den [Azure-portalen](https://portal.azure.com/) med ditt Office 365-användarnamn och lösenord. Om du vill konfigurera virtuella datorer eller använda andra Azure-tjänster måste du registrera dig för en Azure-prenumeration. Du kan dela din Azure-prenumeration med andra och [använda rollbaserad åtkomstkontroll för att hantera åtkomst till din Azure-prenumeration och resurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Om du redan har både ett Office 365-konto och en Azure-prenumeration kan du läsa [associera en Office 365-klient till en Azure-prenumeration](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Skaffa en Azure-prenumeration med ditt Office 365-konto

Spara tid och undvika konto utbredningen genom att registrera dig för Azure med ditt Office 365-användarnamn och lösenord. 

1. Registrera dig på [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Logga in med ditt Office 365-användarnamn och lösenord. Det konto som används behöver inte ha behörighet som administratör. Om du har mer än en Office 365-konto, kontrollera att du använder autentiseringsuppgifterna för Office 365-konto som du vill associera med din Azure-prenumeration. 

   ![Skärmbild som visar sidan logga in.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Ange informationen som krävs och slutföra registreringen. Viss information kanske inte krävs om du redan har en Office 365-konto.

    ![Skärmbild som visar fyllt i registreringsformuläret.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Om du behöver lägga till andra personer i din organisation till Azure-prenumeration, se [Kom igång med åtkomsthantering i Azure portal](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Mer om Azure och Office 365-prenumerationer</a>
Office 365 och Azure kan du använda Azure AD-tjänsten för att hantera användare och prenumerationer. Azure-katalogen är som en behållare där du kan gruppera användare och prenumerationer. Om du vill använda samma användarkonton för dina Azure och Office 365-prenumerationer, måste du se till att de Azure-prenumerationerna skapas i samma katalog som Office 365-prenumerationer. Tänk på följande saker övervägas:

* En prenumeration skapas under en katalog
* Användare som hör till kataloger
* En prenumeration finns i katalogen för den användare som skapar prenumerationen. Så att din Office 365-prenumeration är kopplad till samma konto som din Azure-prenumeration.
* Azure-prenumerationer som ägs av enskilda användare i katalogen
* Office 365-prenumerationer ägs av den aktuella katalogen. Användare med rätt behörigheter i katalogen kan hantera dessa prenumerationer.

![Skärmbild som visar relationen mellan directory, användare och prenumerationer.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Mer information finns i [hur Azure-prenumerationer är associerade med Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt. 
