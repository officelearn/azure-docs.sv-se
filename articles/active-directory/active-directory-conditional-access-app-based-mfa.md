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
ms.component: protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 1501ca1c036a8db1d53b9b27170d9ae05d41f797
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724123"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Snabbstart: Kräv MFA för specifika appar med Azure Active Directory för villkorlig åtkomst 

För att förenkla inloggning av användare, kanske du vill att de kan logga in på dina molnappar med ett användarnamn och ett lösenord. Men har många miljöer minst några appar som är det lämpligt att kräva en säkrare form av verifiering för kontot, till exempel multifaktorautentisering (MFA). Det kan vara för exempel Sant för åtkomst till organisationens e-postsystemet eller HR-appar. Du kan göra det här målet med en princip för villkorlig åtkomst i Azure Active Directory (AD Azure).    

Den här snabbstarten visar hur du konfigurerar en [principen för villkorlig åtkomst i Azure AD](active-directory-conditional-access-azure-portal.md) som kräver multifaktorautentisering för ett valt moln-app i din miljö.

![Skapa princip](./media/active-directory-conditional-access-app-based-mfa/32.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.



## <a name="prerequisites"></a>Förutsättningar 

Om du vill slutföra scenariot i den här snabbstarten, behöver du:

- **Åtkomst till en Azure AD Premium edition** -Azure AD villkorlig åtkomst är en Azure AD Premium-funktion. 

- **Ett testkonto kallas Isabella Simonsen** - om du inte vet hur du skapar ett testkonto finns [lägga till molnbaserade användare](add-users-azure-active-directory.md#add-cloud-based-users).



## <a name="create-your-conditional-access-policy"></a>Skapa princip för villkorlig åtkomst 

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs. Ett scenario för denna Snabbstart använder:

- Azure-portalen som platshållare för en molnapp som kräver MFA. 
- Din exempelanvändare att testa principen för villkorlig åtkomst.  

Ange din princip:

|Inställning |Värde|
|---     | --- |
|Användare och grupper | Isabella Simonsen |
|Molnappar | Hantering av Microsoft Azure |
|Bevilja åtkomst | Kräv multifaktorautentisering |
 

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


## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat principen för villkorlig åtkomst kan vill du förmodligen veta om den fungerar som förväntat. Använd villkorlig åtkomst som ett första steg vad händer om principen för att simulera en inloggning för dina användare. Simuleringen beräknar effekt detta inloggning har på dina principer och genererar en simuleringsrapport.  

Initiera nyheter om utvärderingsverktyget för principen, ange:

- **Isabella Simonsen** som användare 
- **Microsoft Azure Management** som cloud app

 Klicka på **vad händer om** skapar en simuleringsrapport som visar:

- **Kräva MFA för Azure portalåtkomst** under **principer som gäller** 
- **Kräva multifaktorautentisering** som **bevilja kontroller**.

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

I det föregående avsnittet har du lärt dig hur du utvärdera en simulerad inloggning. Förutom en simulering bör du också testa din princip för villkorlig åtkomst för att säkerställa att den fungerar som förväntat. 

Om du vill testa din princip försöker logga in på ditt [Azure-portalen](https://portal.azure.com) med din **Isabella Simonsen** testa konto. Du bör se en dialogruta som du måste ange ditt konto för ytterligare säkerhetsverifiering.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort testanvändaren och principen för villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare kan se [ta bort användare från Azure AD](add-users-azure-active-directory.md#delete-users-from-azure-ad).

- Välj din princip för att ta bort principen, och klicka sedan på **ta bort** i verktygsfältet Snabbåtkomst.

    ![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/33.png)


## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om villkorlig åtkomst kan se [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

