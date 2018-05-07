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
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: dfb39b809f9a1082682d269d8bd4c180c8a264ce
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Registrera dig för en Azure-prenumeration med ditt Office 365-konto
Om du har en Office 365-prenumeration kan använda du Office 365-konto för att skapa en Azure-prenumeration. Logga in på den [Azure-portalen](https://portal.azure.com/) med ditt Office 365-användarnamn och lösenord. Om du vill konfigurera virtuella datorer eller andra Azure-tjänster måste du registrera dig för en Azure-prenumeration. Du kan dela din Azure-prenumeration med andra och [använda rollbaserad åtkomstkontroll för att hantera åtkomst till din Azure-prenumeration och resurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Om du redan har både ett Office 365-konto och en Azure-prenumeration finns [associera en Office 365-klient till en Azure-prenumeration](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Hämta en Azure-prenumeration med ditt Office 365-konto

Spara tid och undvika konto spridning genom att registrera dig för Azure med ditt Office 365-användarnamn och lösenord. 

1. Registrera dig på [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Logga in med ditt Office 365-användarnamn och lösenord. Kontot du använder behöver inte ha administratörsbehörighet. Om du har mer än en Office 365-konto kan du kontrollera att du använder autentiseringsuppgifterna för Office 365-konto som du vill associera med din Azure-prenumeration. 

   ![Skärmbild som visar sidan logga in.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Ange informationen som krävs och slutföra registreringen. Viss information kan inte krävas om du redan har en Office 365-konto.

    ![Skärmbild som visar i registreringsformuläret.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Om du behöver lägga till andra personer i din organisation till Azure-prenumerationen finns [Kom igång med åtkomsthantering i Azure portal](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Mer information om Azure och Office 365-prenumerationer</a>
Office 365 och Azure kan du använda Azure AD-tjänsten för att hantera användare och -prenumerationer. Azure-katalogen är som en behållare där du kan gruppera användare och -prenumerationer. Om du vill använda samma användarkonton för din Azure och Office 365-prenumerationer måste du kontrollera att Azure-prenumerationer skapas i samma katalog som Office 365-prenumerationer. Tänk på följande saker övervägas:

* En prenumeration skapas under en katalog
* Användare som hör till kataloger
* En prenumeration hamnar i katalogen för den användare som skapar prenumerationen. Så att Office 365-prenumeration är kopplad till samma konto som din Azure-prenumeration.
* Azure-prenumerationer som ägs av enskilda användare i katalogen
* Office 365-prenumerationer ägs av den aktuella katalogen. Användare med rätt behörigheter i katalogen kan hantera dessa prenumerationer.

![Skärmbild som visar relationen mellan directory, användare och prenumerationer.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Mer information finns i [hur Azure-prenumerationer är associerade med Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet. 
