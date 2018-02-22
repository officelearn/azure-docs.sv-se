---
title: "Registrera dig för Office 365 med Azure-konto | Microsoft Docs"
description: "Lär dig hur du skapar en prenumeration på Office 365 med ett Azure-konto"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: 8279bc2ba2032ee40d24ddb1f7f23e2725e56e7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Registrera dig för Office 365-prenumeration med ditt Azure-konto
Om du använder Azure-prenumerant kan använda du ditt Azure-konto du registrerar dig för en prenumeration på Office 365. Om du är en del av en organisation som har en Azure-prenumeration kan skapa du Office 365-prenumerationer för användare i din befintliga Azure Active Directory (AD Azure). Logga på Office 365 med ett konto som har behörighet som Global administratör eller fakturering administratör i din Azure Active Directory-klient. Mer information finns i [Kontrollera min behörighet i Azure AD](#RoleInAzureAD) och [Tilldela administratörsroller i Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

Om du redan har både ett Office 365-konto och en Azure-prenumeration kan du [associera en Office 365-klient till en Azure-prenumeration](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Hämta en Office 365-prenumeration med ditt Azure-konto

1. Gå till den [produktsidan för Office 365](https://products.office.com/business), och välj en plan.
2. Klicka på **logga in** på det övre högra hörnet på sidan.

    ![Skärmbild av Office 365-utvärderingsversion sidan](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Logga in med dina Azure-konto. Om du skapar en prenumeration för din organisation kan du använda ett Azure-konto som är medlem i rollen Global administratör eller fakturering Admin katalog i Azure Active Directory-klient.

    ![Skärmbild av Office 365-inloggning](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Klicka på **försöker nu**.

    ![Skärmbild som bekräftar din order för Office 365.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. På sidan ordning inleverans **Fortsätt**.

    ![Skärmbild av Office 365 inleverans](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Nu är installeras allt klart. Om du har skapat Office 365-prenumeration för din organisation kan du använda följande steg för att kontrollera att Azure AD-användare är nu i Office 365.

1. Öppna administrationscentret för Office 365.
2. Expandera **användare**, och klicka sedan på **aktiva användare**.

    ![Skärmbild av Office 365 admin center-användare](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

När du registrerar dig läggs Office 365-prenumeration i samma Azure Active Directory-instans som tillhör din Azure-prenumeration. Mer information finns i [mer om Azure och Office 365-prenumerationer](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) och [hur Azure-prenumerationer är associerade med Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Kontrollera min behörighet i Azure AD
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **alla tjänster**, och sök sedan efter **Active Directory**.

    ![Skärmbild av Active Directory i Azure-portalen](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Klicka på **användare och grupper** > **alla användare**.
4. Välj användarnamnet. 

    ![Skärmbild som visar de Azure Active Directory-användarna](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Klicka på **Directory rollen**.
  
    ![Skärmbild som visar rollen Azure portal directory](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  Rollen **Global administratör** eller **begränsad administratör** > **faktureringsadministratör** krävs för att skapa en prenumeration på Office 365 för användare i en befintlig Azure Active Directory.

    ![Skärmbild som visar Azure portal directory rollen fakturering Admin](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet. 
