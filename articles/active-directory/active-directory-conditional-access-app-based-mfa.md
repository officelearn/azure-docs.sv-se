---
title: Snabbstart - konfigurera respektive molnapp MFA med Azure Active Directory för villkorlig åtkomst | Microsoft Docs
description: Lär dig hur du kan koppla ditt autentiseringskraven för typ av åtkomst molnappen med villkorlig åtkomst i Azure Active Directory (AD Azure).
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: ac43817fb3f253c35cd69a8ecd8931afca50892b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="quickstart-configure-per-cloud-app-mfa-with-azure-active-directory-conditional-access"></a>Snabbstart: Konfigurera respektive molnapp MFA med Azure Active Directory för villkorlig åtkomst 


För att förenkla inloggning av användare, kanske du vill att de kan logga in på dina molnappar med ett användarnamn och ett lösenord. Men har många miljöer minst några appar som är det lämpligt att kräva en säkrare form av verifiering för kontot, till exempel multifaktorautentisering. Det kan vara för exempel Sant för åtkomst till organisationens e-postsystemet eller HR-appar.  

Den här snabbstarten visar hur du kan kräva multifaktorautentisering endast för en uppsättning valda molnappar i din miljö genom att använda en [principen för villkorlig åtkomst i Azure AD](active-directory-conditional-access-azure-portal.md).

Att slutföra scenariot i den här snabbstarten du:


> [!div class="checklist"]
> * Skapa princip för villkorlig åtkomst
> * Utvärdera princip för villkorlig åtkomst
> * Testa din princip för villkorlig åtkomst  


## <a name="scenario-description"></a>Scenariobeskrivning

Scenario i den här artikeln används Azure portal som platshållare för ett molnapp som kräver multifaktorautentisering för en viss användare. Britta Simon är en användare i din organisation. När hon loggar in på Azure-portalen, vill henne att ytterligare Kontrollera sitt konto med multifaktorautentisering.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/01.png)



## <a name="before-you-begin"></a>Innan du börjar 

Om du vill slutföra scenariot i den här snabbstarten, behöver du:

- **Åtkomst till en Azure AD Premium edition** -Azure AD villkorlig åtkomst är en Azure AD Premium-funktion. Om du inte har åtkomst till en Azure AD Premium edition kan du [registrerar dig för en utvärderingsversion](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Ett testkonto kallas Britta Simon** - om du inte vet hur du skapar ett testkonto läsa [instruktionerna](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).



## <a name="create-your-conditional-access-policy"></a>Skapa princip för villkorlig åtkomst 

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs.  
I din princip anger du:

|Inställning |Värde|
|---     | --- |
|Användare och grupper | Britta Simon |
|Molnappar | Hantering av Microsoft Azure |
|Bevilja | Kräv multifaktorautentisering |
 

![Skapa princip](./media/active-directory-conditional-access-app-based-mfa/12.png)




**Konfigurera princip för villkorlig åtkomst:**

1. Logga in på ditt [Azure-portalen](https://portal.azure.com) som global administratör.

2. I Azure-portalen på den vänstra navigeringsfält för, klickar du på **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. På den **villkorlig åtkomst** i verktygsfältet högst upp på sidan klickar du på **Lägg till**.

    ![Lägg till](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. På den **ny** sidan den **namn** textruta typen **kräver MFA för Britta**.

    ![Namn](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. I den **tilldelning** klickar du på **användare och grupper**.

    ![Användare och grupper](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. På den **användare och grupper** utför följande steg:

    ![Användare och grupper](./media/active-directory-conditional-access-app-based-mfa/07.png)

    a. Klicka på **Välj användare och grupper**.

    b. Klicka på **Välj**.

    c. På den **Välj** sidan, Välj din testanvändare och klicka sedan på **Välj**.

    d. På den **användare och grupper** klickar du på **klar**.

8. Klicka på **Molnappar**.

    ![Molnappar](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. På den **Molnappar** utför följande steg:

    ![Välj molnappar](./media/active-directory-conditional-access-app-based-mfa/09.png)

    a. Klicka på **Välj appar**.

    b. Klicka på **Välj**.

    c. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

    d. På den **Molnappar** klickar du på **klar**.


10. I den **åtkomstkontroller** klickar du på **bevilja**.

    ![Åtkomstkontroller](./media/active-directory-conditional-access-app-based-mfa/10.png)

11. På den **bevilja** utför följande steg:

    ![Bevilja](./media/active-directory-conditional-access-app-based-mfa/11.png)

    a. Välj **bevilja åtkomst**.

    a. Välj **kräver Multi-Factor authentication**.

    b. Klicka på **Välj**.

12. I den **aktiverar principen** klickar du på **på**.

    ![Aktivera princip](./media/active-directory-conditional-access-app-based-mfa/18.png)

13. Klicka på **Skapa**.


## <a name="evaluate-your-conditional-access-policy"></a>Utvärdera princip för villkorlig åtkomst

För att förstå effekten av dina principer för villkorlig åtkomst på din miljö kan du använda den [villkorlig åtkomst till vilka om principen verktyget](active-directory-conditional-access-whatif.md). Med det här verktyget kan utvärdera du en simulerad inloggning för en användare.

När du konfigurerar verktyget med **Britta Simon** som användare och **Microsoft Azure Management** som molnappen, verktyget visar **kräver MFA för Britta** under  **Principer som gäller**.

![Vad händer om principen verktyget](./media/active-directory-conditional-access-app-based-mfa/17.png)



**Utvärdera princip för villkorlig åtkomst:**

1. På den [villkorlig åtkomst - principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) i menyn överst, klickar du på **vad händer om**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Klicka på **användare**väljer **Britta Simon**, och klicka sedan på **Välj**.

    ![Användare](./media/active-directory-conditional-access-app-based-mfa/15.png)

2. Utför följande steg för att välja en molnapp:

    ![Molnappar](./media/active-directory-conditional-access-app-based-mfa/16.png)

    a. Klicka på **Molnappar**.

    b. På den **moln appar sidan**, klickar du på **Välj appar**.

    c. Klicka på **Välj**.

    d. På den **Välj** sidan Välj Microsoft Azure Management ** och klicka sedan på **Välj**.

    e. På sidan moln appar **klar**.

3. Klicka på **vad händer om**.


## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorlig åtkomst

Om du vill testa din princip försöker logga in på ditt [Azure-portalen](https://portal.azure.com) med din **Britta Simon** testa konto. Du bör se en dialogruta som du måste ange ditt konto för ytterligare säkerhetsverifiering.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/01.png)


## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om villkorlig åtkomst kan se [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

