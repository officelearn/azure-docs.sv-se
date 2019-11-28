---
title: Registrera dig för Office 365 med ett Azure-konto | Microsoft Docs
description: Lär dig hur du skapar en Office 365-prenumeration med hjälp av ett Azure-konto
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 998aedc8cb799d0064a2704dc819d483ced9e99e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225652"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Registrera dig för en Office 365-prenumeration med ditt Azure-konto
Om du är Azure-prenumerant kan du använda ditt Azure-konto för att registrera dig för en Office 365-prenumeration. Om du ingår i en organisation som har en Azure-prenumeration kan du skapa Office 365-prenumerationer för användare i din befintliga Azure Active Directory (Azure AD). Registrera dig för Office 365 med hjälp av ett konto som har behörigheter för global administratör eller faktureringsadministratör i din Azure Active Directory-klientorganisation. Mer information finns i [Kontrollera mina kontobehörigheter i Azure AD](#RoleInAzureAD) och [Tilldela administratörsroller i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Om du redan har både ett Office 365-konto och en Azure-prenumeration kan du [associera en Office 365-klientorganisation med en Azure-prenumeration](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Skaffa en Office 365-prenumeration med hjälp av ditt Azure-konto

1. Gå till [produktsidan för Office 365](https://products.office.com/business) och välj en plan.
2. Klicka på **Logga in** i det övre högra hörnet på sidan.

    ![skärmbild av sidan för Office 365-utvärdering](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Logga in med autentiseringsuppgifterna för ditt Azure-konto. Om du skapar en prenumeration för din organisation använder du ett Azure-konto som är medlem i katalogrollen Global administratör eller Faktureringsadministratör i din Azure Active Directory-klientorganisation.

    ![Skärmbild av inloggningssidan för Office 365](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Klicka på **Testa nu**.

    ![Skärmbild som bekräftar din beställning av Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. På sidan för orderkvitto klickar du på **Fortsätt**.

    ![Skärmbild av orderkvittot för Office 365](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Nu är allt klart.
Om du skapade Office 365-prenumerationen för din organisation använder du följande steg för att kontrollera att dina Azure AD-användare nu finns i Office 365.

1. Öppna Microsoft 365-administrationscentret.
2. Expandera **ANVÄNDARE** och klicka sedan på **Aktiva användare**.

    ![Skärmbild av användare i Microsoft 365-administrationscentret](./media/billing-use-existing-azure-account-office-365-subscription/16-microsoft-365-admin-center-users.png)

När du har registrerat dig läggs Office 365-prenumerationen till i samma Azure Active Directory-instans som din Azure-prenumeration tillhör. Mer information finns i [Mer information om Azure- och Office 365-prenumerationer](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) och [Så är Azure-prenumerationer associerade med Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Kontrollera mina kontobehörigheter i Azure AD
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Alla tjänster** och sök sedan efter **Active Directory**.

    ![Skärmbild av Active Directory i Azure-portalen](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Klicka på **Användare och grupper** > **Alla användare**.
4. Välj användarnamn.

    ![Skärmbild som visar Azure Active Directory-användare](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Klicka på **Katalogroll**.

    ![Skärmbild som visar katalogrollen i Azure-portalen](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  Rollen **Global administratör** eller **Begränsad administratör** > **Faktureringsadministratör** krävs för att du ska kunna skapa en Office 365-prenumeration för användare i din befintliga Azure Active Directory.

    ![Skärmbild som visar katalogrollen Faktureringsadministratör i Azure-portalen](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
