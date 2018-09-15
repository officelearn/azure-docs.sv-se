---
title: Snabbstart – kräver användningsvillkor godkännas före åtkomst till molnappar som skyddas av Azure Active Directory villkorlig åtkomst | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du kan kräva att dina användningsvillkor accepteras innan åtkomst till valda molnappar som ska beviljas av villkorlig åtkomst i Azure Active Directory.
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst, användningsvillkor
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: conditional-access
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2b06dcfd4bdeb68d114c0c6dd57134fadfef57b5
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605852"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Snabbstart: Kräv användningsvillkor godkännas före åtkomst till molnappar 

Innan du använder vissa molnappar i din miljö, kanske du vill få medgivande från användare i form av accepterar dina användningsvillkor (ToU). Villkorlig åtkomst i Azure Active Directory (Azure AD) ger dig: 

- En enkel metod för att konfigurera villkor för användning
- Alternativet för att kräva accepterar dina användningsvillkor via principer för villkorlig åtkomst  

Den här snabbstarten visar hur du konfigurerar en [princip för villkorlig åtkomst i Azure AD](../active-directory-conditional-access-azure-portal.md) som kräver en ToU ska godkännas för ett valt moln-app i din miljö.

![Skapa princip](./media/require-tou/5555.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.



## <a name="prerequisites"></a>Förutsättningar 

För att slutföra scenariot i den här snabbstarten behöver du:

- **Åtkomst till en Azure AD Premium-versionen** -villkorlig åtkomst i Azure AD är en Azure AD Premium-funktion. 

- **Ett testkonto kallas Isabella Simonsen** – om du inte vet hur du skapar ett testkonto finns [lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Testa din inloggning

Målet med det här steget är att få en bild av inloggning utan en princip för villkorlig åtkomst.

**Så här testar din inloggning:**

1. Logga in på din [Azure-portalen](https://portal.azure.com/) som Isabella Simonsen.

2. Logga ut.




## <a name="create-your-terms-of-use"></a>Skapa dina användningsvillkor

Det här avsnittet ger dig stegen för att skapa ett exempel på villkor för användning. När du skapar en ToU kan du välja ett värde för **tvinga med villkorlig åtkomst principmallar**. Att välja **kundpolicy** öppnas dialogrutan för att skapa en ny princip för villkorlig åtkomst så snart din ToU har skapats.


**Skapa dina användningsvillkor:**

1. Skapa ett nytt dokument i Microsoft Word.

2. Typ **min användningsvillkor**, och spara dokumentet på datorn som **mytou.pdf**.

3. Logga in på din [Azure-portalen](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.

4. I Azure portal, på det vänstra navigeringsfältet, klickar du på **Azure Active Directory**. 

    ![Azure Active Directory](./media/require-tou/02.png)

5. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/require-tou/03.png) 

6. I den **hantera** klickar du på **användningsvillkoren**.

    ![Användningsvillkor](./media/require-tou/04.png) 

7. Klicka på menyn längst upp **nya allmänna**.

    ![Användningsvillkor](./media/require-tou/05.png) 

8. På den **nya användningsvillkor** sidan:

    ![Användningsvillkor](./media/require-tou/112.png) 

    a. I den **namn** textrutan typ **Mina TOU**.

    b. I den **visningsnamn** textrutan typ **Mina TOU**.

    c. Ladda upp dina villkor för användning PDF-fil.

    d. Som **språk**väljer **engelska**.

    e. Som **kräva att användarna expanderar användningsvillkoren**väljer **på**.

    f. Som **tvinga med villkorlig åtkomst principmallar**väljer **kundpolicy**.

    g. Klicka på **Skapa**.
 


## <a name="create-your-conditional-access-policy"></a>Skapa principer för villkorlig åtkomst 

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs. Scenariot i den här snabbstarten använder:

- Azure-portalen som platshållare för en molnapp som kräver att dina användningsvillkor godkännas. 
- Exempelanvändaren att testa principen för villkorlig åtkomst.  

Ange i din princip:

|Inställning |Värde|
|---     | --- |
|Användare och grupper | Isabella Simonsen |
|Molnappar | Hantering av Microsoft Azure |
|Bevilja åtkomst | Min TOU |
 

![Skapa princip](./media/require-tou/1234.png)

 


**Konfigurera principer för villkorlig åtkomst:**

1. På den **New** sidan den **namn** textrutan typ **kräver TOU för Isabella**.

    ![Namn](./media/require-tou/71.png)

2. I den **tilldelning** klickar du på **användare och grupper**.

    ![Användare och grupper](./media/require-tou/06.png)

3. På den **användare och grupper** sidan:

    ![Användare och grupper](./media/require-tou/24.png)

    a. Klicka på **Välj användare och grupper**, och välj sedan **användare och grupper**.

    b. Klicka på **Välj**.

    c. På den **Välj** väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

    d. På den **användare och grupper** klickar du på **klar**.

4. Klicka på **Molnappar**.

    ![Molnappar](./media/require-tou/08.png)

5. På den **Molnappar** sidan:

    ![Välj molnappar](./media/require-tou/26.png)

    a. Klicka på **Välj appar**.

    b. Klicka på **Välj**.

    c. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

    d. På den **Molnappar** klickar du på **klar**.


6. I den **åtkomstkontroller** klickar du på **bevilja**.

    ![Åtkomstkontroller](./media/require-tou/10.png)

7. På den **bevilja** sidan:

    ![Bevilja](./media/require-tou/111.png)

    a. Välj **bevilja åtkomst**.

    a. Välj **min TOU**.

    b. Klicka på **Välj**.

8. I den **aktiverar principen** klickar du på **på**.

    ![Aktivera princip](./media/require-tou/18.png)

9. Klicka på **Skapa**.


## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat principer för villkorlig åtkomst kan vill du förmodligen veta om den fungerar som förväntat. Använd villkorlig åtkomst som ett första steg, vad händer om principen för att simulera en inloggning av din testanvändare. Simuleringen beräknar inverkan detta inloggning har på dina principer och genererar en simuleringsrapport.  

Initiera vad om utvärderingsverktyg för principen, Ställ in:

- **Isabella Simonsen** som användare 
- **Microsoft Azure Management** som appar i molnet


Klicka på **vad händer om** skapar en simuleringsrapport som visar:

- **Kräv TOU för Isabella** under **principer som gäller** 
- **Min TOU** som **bevilja kontroller**.

![Vad händer om principen verktyget](./media/require-tou/79.png)



**Utvärdera din princip för villkorlig åtkomst:**

1. På den [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) i menyn längst upp på sidan klickar du på **vad händer om**.  
 
    ![What If](./media/require-tou/14.png)

2. Klicka på **användare**väljer **Isabella Simonsen**, och klicka sedan på **Välj**.

    ![Användare](./media/require-tou/15.png)

2. Att välja en molnapp:

    ![Molnappar](./media/require-tou/16.png)

    a. Klicka på **Molnappar**.

    b. På den **molnet appsida**, klickar du på **Välj appar**.

    c. Klicka på **Välj**.

    d. På den **Välj** väljer **Microsoft Azure Management**, och klicka sedan på **Välj**.

    e. På sidan moln appar **klar**.

3. Klicka på **vad händer om**.


## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorlig åtkomst

I det föregående avsnittet har du lärt dig hur att utvärdera en simulerad inloggning. Förutom en simulering, bör du också testa principer för villkorlig åtkomst för att säkerställa att den fungerar som förväntat. 

Om du vill testa din princip, försöker logga in på din [Azure-portalen](https://portal.azure.com) med hjälp av din **Isabella Simonsen** testa konto. Du bör se en dialogruta som kräver att du accepterar dina användningsvillkor.

![Användningsvillkor](./media/require-tou/57.png)


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort testanvändaren och principen för villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare kan se [ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Välj din princip för att ta bort principen, och klicka sedan på **ta bort** i verktygsfältet för snabb åtkomst.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- Om du vill ta bort dina användningsvillkor, markera den och klicka sedan på **ta bort termer** i verktygsfältet högst upp. 

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräva MFA för specifika appar](app-based-mfa.md)
> [blockera åtkomst när en risk för session har identifierats](app-sign-in-risk.md)

