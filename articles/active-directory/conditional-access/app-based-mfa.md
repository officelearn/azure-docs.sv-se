---
title: Snabbstart – kräva multifaktorautentisering (MFA) för specifika appar med villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du kan koppla din autentiseringskrav för typ av används molnapp med villkorlig åtkomst i Azure Active Directory (AD Azure).
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/14/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: b474c0fdabbe5c2a2bb9731fbeb24c1715f7c859
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080744"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Snabbstart: Kräva MFA för specifika appar med villkorlig åtkomst i Azure Active Directory 

För att förenkla inloggning för dina användare kan vilja du att de kan logga in på dina appar i molnet med ett användarnamn och ett lösenord. Men har många miljöer minst ett fåtal program som det är lämpligt att kräva en starkare form av verifiering för kontot, till exempel multifaktorautentisering (MFA). Det kan vara, för exempel Sant för åtkomst till organisationens e-postsystem eller HR-appar. I Azure Active Directory (AD Azure), kan du göra det här målet med en princip för villkorlig åtkomst.    

Den här snabbstarten visar hur du konfigurerar en [princip för villkorlig åtkomst i Azure AD](../active-directory-conditional-access-azure-portal.md) som kräver multifaktorautentisering för ett valt moln-app i din miljö.

![Skapa princip](./media/app-based-mfa/32.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.



## <a name="prerequisites"></a>Förutsättningar 

För att slutföra scenariot i den här snabbstarten behöver du:

- **Åtkomst till en Azure AD Premium-versionen** -villkorlig åtkomst i Azure AD är en Azure AD Premium-funktion. 

- **Ett testkonto kallas Isabella Simonsen** – om du inte vet hur du skapar ett testkonto finns [lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Testa din inloggning

Målet med det här steget är att få en bild av inloggning utan en princip för villkorlig åtkomst.

**Initiera miljön:**

1. Logga in på Azure portal som Isabella Simonsen.

2. Logga ut.


## <a name="create-your-conditional-access-policy"></a>Skapa principer för villkorlig åtkomst 

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs. Scenariot i den här snabbstarten använder:

- Azure-portalen som platshållare för en molnapp som kräver MFA. 
- Exempelanvändaren att testa principen för villkorlig åtkomst.  

Ange i din princip:

|Inställning |Värde|
|---     | --- |
|Användare och grupper | Isabella Simonsen |
|Molnappar | Microsoft Azure Management |
|Bevilja åtkomst | Kräv multifaktorautentisering |
 

![Skapa princip](./media/app-based-mfa/31.png)

 


**Konfigurera principer för villkorlig åtkomst:**

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.

2. I Azure portal, på det vänstra navigeringsfältet, klickar du på **Azure Active Directory**. 

    ![Azure Active Directory](./media/app-based-mfa/02.png)

3. På den **Azure Active Directory** sidan den **Security** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/app-based-mfa/03.png)
 
4. På den **villkorlig åtkomst** , i verktygsfältet högst upp, klickar du på **ny princip**.

    ![Lägg till](./media/app-based-mfa/04.png)

5. På den **New** sidan den **namn** textrutan typ **kräver MFA för Azure portalåtkomst**.

    ![Namn](./media/app-based-mfa/05.png)

6. I den **tilldelning** klickar du på **användare och grupper**.

    ![Användare och grupper](./media/app-based-mfa/06.png)

7. På den **användare och grupper** utför följande steg:

    ![Användare och grupper](./media/app-based-mfa/24.png)

    a. Klicka på **Välj användare och grupper**, och välj sedan **användare och grupper**.

    b. Klicka på **Välj**.

    c. På den **Välj** väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

    d. På den **användare och grupper** klickar du på **klar**.

8. Klicka på **Molnappar**.

    ![Molnappar](./media/app-based-mfa/08.png)

9. På den **Molnappar** utför följande steg:

    ![Välj molnappar](./media/app-based-mfa/26.png)

    a. Klicka på **Välj appar**.

    b. Klicka på **Välj**.

    c. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

    d. På den **Molnappar** klickar du på **klar**.


10. I den **åtkomstkontroller** klickar du på **bevilja**.

    ![Åtkomstkontroller](./media/app-based-mfa/10.png)

11. På den **bevilja** utför följande steg:

    ![Bevilja](./media/app-based-mfa/11.png)

    a. Välj **bevilja åtkomst**.

    a. Välj **kräva multifaktorautentisering**.

    b. Klicka på **Välj**.

12. I den **aktiverar principen** klickar du på **på**.

    ![Aktivera princip](./media/app-based-mfa/18.png)

13. Klicka på **Skapa**.


## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat din princip för villkorsstyrd åtkomst vill du förmodligen veta om den fungerar som förväntat. Använd villkorlig åtkomst som ett första steg, vad händer om principen för att simulera en inloggning av din testanvändare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.  

Initiera vad om utvärderingsverktyg för principen, Ställ in:

- **Isabella Simonsen** som användare 
- **Microsoft Azure Management** som appar i molnet

 Klicka på **vad händer om** skapar en simuleringsrapport som visar:

- **Kräva MFA för Azure portalåtkomst** under **principer som gäller** 
- **Kräv multifaktorautentisering** som **bevilja kontroller**.

![Vad händer om principen verktyget](./media/app-based-mfa/23.png)



**Utvärdera din princip för villkorlig åtkomst:**

1. På den [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) i menyn längst upp på sidan klickar du på **vad händer om**.  
 
    ![What If](./media/app-based-mfa/14.png)

2. Klicka på **användare**väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

    ![Användare](./media/app-based-mfa/15.png)

2. Utför följande steg för att välja en molnapp:

    ![Molnappar](./media/app-based-mfa/16.png)

    a. Klicka på **Molnappar**.

    b. På den **molnet appsida**, klickar du på **Välj appar**.

    c. Klicka på **Välj**.

    d. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

    e. På sidan moln appar **klar**.

3. Klicka på **vad händer om**.


## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorsstyrd åtkomst

I det föregående avsnittet har du lärt dig hur att utvärdera en simulerad inloggning. Förutom en simulering, bör du också testa principer för villkorlig åtkomst för att säkerställa att den fungerar som förväntat. 

Om du vill testa din princip, försöker logga in på din [Azure-portalen](https://portal.azure.com) med hjälp av din **Isabella Simonsen** testa konto. Du bör se en dialogruta som du måste ange ditt konto för ytterligare säkerhetsverifiering.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort testanvändaren och principen för villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare kan se [ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Välj din princip för att ta bort principen, och klicka sedan på **ta bort** i verktygsfältet för snabb åtkomst.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräv användningsvillkor godkännas](require-tou.md)
> [blockera åtkomst när en risk för session har identifierats](app-sign-in-risk.md)
