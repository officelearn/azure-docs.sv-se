---
title: Snabbstart - kräver Multi-Factor authentication (MFA) för specifika appar med Azure Active Directory för villkorlig åtkomst | Microsoft Docs
description: I den här snabbstarten du lära dig hur du kan koppla ditt autentiseringskraven för typ av åtkomst molnappen med villkorlig åtkomst i Azure Active Directory (AD Azure).
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: d675236f36840858f0f011484392186d355ac6df
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Snabbstart: Kräv MFA för specifika appar med Azure Active Directory för villkorlig åtkomst 

För att förenkla inloggning av användare, kanske du vill att de kan logga in på dina molnappar med ett användarnamn och ett lösenord. Men har många miljöer minst några appar som är det lämpligt att kräva en säkrare form av verifiering för kontot, till exempel multifaktorautentisering (MFA). Det kan vara för exempel Sant för åtkomst till organisationens e-postsystemet eller HR-appar. Du kan göra det här målet med en princip för villkorlig åtkomst i Azure Active Directory (AD Azure).    

Den här snabbstarten visar hur du konfigurerar en [principen för villkorlig åtkomst i Azure AD](active-directory-conditional-access-azure-portal.md) som kräver multifaktorautentisering för en uppsättning valda molnappar i din miljö.


## <a name="scenario-description"></a>Scenariobeskrivning

Scenario i den här artikeln används Azure portal som platshållare för ett molnapp som kräver MFA för en viss användare. Isabella Simonsen är en användare i din organisation. När hon loggar in på Azure-portalen, vill henne att ytterligare Kontrollera sitt konto med MFA.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)



## <a name="prerequisites"></a>Förutsättningar 

Om du vill slutföra scenariot i den här snabbstarten, behöver du:

- **Åtkomst till en Azure AD Premium edition** -Azure AD villkorlig åtkomst är en Azure AD Premium-funktion. Om du inte har åtkomst till en Azure AD Premium edition kan du [registrerar dig för en utvärderingsversion](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Ett testkonto kallas Isabella Simonsen** - om du inte vet hur du skapar ett testkonto läsa [instruktionerna](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).



## <a name="create-your-conditional-access-policy"></a>Skapa princip för villkorlig åtkomst 

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs.  
I din princip anger du:

|Inställning |Värde|
|---     | --- |
|Användare och grupper | Isabella Simonsen |
|Molnappar | Hantering av Microsoft Azure |
|Bevilja | Kräv multifaktorautentisering |
 

![Skapa princip](./media/active-directory-conditional-access-app-based-mfa/31.png)




**Konfigurera princip för villkorlig åtkomst:**

1. Logga in på ditt [Azure-portalen](https://portal.azure.com) som global administratör.

2. I Azure-portalen på den vänstra navigeringsfält för, klickar du på **Azure Active Directory**. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. På den **villkorlig åtkomst** i verktygsfältet högst upp på sidan klickar du på **Lägg till**.

    ![Lägg till](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. På den **ny** sidan den **namn** textruta typen **kräver MFA för Azure portalåtkomst**.

    ![Namn](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. I den **tilldelning** klickar du på **användare och grupper**.

    ![Användare och grupper](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. På den **användare och grupper** utför följande steg:

    ![Användare och grupper](./media/active-directory-conditional-access-app-based-mfa/24.png)

    a. Klicka på **Välj användare och grupper**, och välj sedan **användare och grupper**.

    b. Klicka på **Välj**.

    c. På den **Välj** väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

    d. På den **användare och grupper** klickar du på **klar**.

8. Klicka på **Molnappar**.

    ![Molnappar](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. På den **Molnappar** utför följande steg:

    ![Välj molnappar](./media/active-directory-conditional-access-app-based-mfa/26.png)

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

Nu när du har konfigurerat principen för villkorlig åtkomst kan vill du förmodligen veta om den fungerar som förväntat. Som ett första steg bör du använda den [villkorlig åtkomst till vilka om principen verktyget](active-directory-conditional-access-whatif.md) att simulera en inloggning för en användare. När du konfigurerar verktyget med **Isabella Simonsen** som användare och **Microsoft Azure Management** som molnappen, verktyget visar **kräver MFA för Azure portalåtkomst** under **Principer som gäller** och **kräva multifaktorautentisering** som **bevilja kontroller**.

![Vad händer om principen verktyget](./media/active-directory-conditional-access-app-based-mfa/23.png)



**Utvärdera princip för villkorlig åtkomst:**

1. På den [villkorlig åtkomst - principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) i menyn överst, klickar du på **vad händer om**.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. Klicka på **användare**väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

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

Föregående avsnitt i det föregående avsnittet du 

Om du vill testa din princip försöker logga in på ditt [Azure-portalen](https://portal.azure.com) med din **Isabella Simonsen** testa konto. Du bör se en dialogruta som du måste ange ditt konto för ytterligare säkerhetsverifiering.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om villkorlig åtkomst kan se [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

