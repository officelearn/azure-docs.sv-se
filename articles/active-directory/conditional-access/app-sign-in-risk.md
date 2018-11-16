---
title: Snabbstart – blockera åtkomst när en risk för session har identifierats med villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du kan konfigurera en princip för villkorlig åtkomst för Azure Active Directory (Azure AD) för att blockera inloggningar baserat på sessionen risker.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
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
ms.date: 07/17/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: a3c5c26f43fddf5d23df3bee0b0511e865b7e71b
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51705930"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Snabbstart: Blockera åtkomst när en risk för session har identifierats med villkorlig åtkomst i Azure Active Directory  

För att hålla din miljö skyddad, kanske du vill blockera misstänkta användare från att registrera insign i aktiviteten. [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyserar varje inloggning och beräknar sannolikheten att en inloggning försök inte har utförts av är tillförlitligt ägare för ett användarkonto. Sannolikheten (låg, medelhög och hög) anges i form av ett beräknat värde med namnet [risknivåer för inloggning](conditions.md#sign-in-risk). Du kan konfigurera principer för villkorlig åtkomst för att svara på specifika inloggningsrisk nivåer genom att ange villkoret inloggningsrisk. 

Den här snabbstarten visar hur du konfigurerar en [princip för villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) som blockerar en inloggning vid en konfigurerad inloggningsrisk nivå har identifierats. 

![Skapa princip](./media/app-sign-in-risk/1000.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.



## <a name="prerequisites"></a>Förutsättningar 

För att kunna slutföra scenariot i den här självstudien behöver du:

- **Åtkomst till en Azure AD Premium P2-versionen** -villkorlig åtkomst är en funktion i Azure AD Premium P1, du behöver en P2-versionen eftersom scenariot i den här snabbstarten kräver Identity Protection. 

- **Identitetsskydd** -scenariot i den här snabbstarten kräver Identity Protection är aktiverat. Om du inte vet hur du aktiverar du Identity Protection, se [aktiverar Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Tor Browser** – [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) är utformat för att bevara sekretessen online. Identity Protection identifierar en inloggning från en Tor Browser som **inloggningar från anonyma IP-adresser**, som har en Medelrisk-nivå. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](../reports-monitoring/concept-risk-events.md).  

- **Ett testkonto kallas Alain Charon** – om du inte vet hur du skapar ett testkonto finns [lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Testa din inloggning 

Målet med det här steget är att se till att ditt testkonto har åtkomst till din klient med Tor Browser.

**Så här testar din inloggning:**

1. Logga in på din [Azure-portalen](https://portal.azure.com) som **Alain Charon**.

2. Logga ut. 


## <a name="create-your-conditional-access-policy"></a>Skapa principer för villkorlig åtkomst 

Scenariot i den här snabbstarten använder en inloggning från en Tor Browser för att generera en identifierad **inloggningar från anonyma IP-adresser** riskhändelsen. Risknivån för den här riskhändelsen är medium. Om du vill svara på den här riskhändelsen kan ange du inloggningsrisk villkoret medium. I en produktionsmiljö bör du ange villkoret inloggningsrisk till hög eller medelhög och hög.     

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs. Ange i din princip:

|Inställning |Värde|
|---     | --- |
| Användare och grupper | Alain Charon  |
| Molnappar | Alla molnappar |
| Inloggningsrisk | Medel |
| Bevilja | Blockera åtkomst |
 

![Skapa princip](./media/app-sign-in-risk/130.png)

 


**Konfigurera principer för villkorlig åtkomst:**

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.

2. I Azure portal, på det vänstra navigeringsfältet, klickar du på **Azure Active Directory**. 

    ![Azure Active Directory](./media/app-sign-in-risk/02.png)

3. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/app-sign-in-risk/03.png)
 
4. På den **villkorlig åtkomst** , i verktygsfältet högst upp, klickar du på **Lägg till**.

    ![Namn](./media/app-sign-in-risk/108.png)

5. På den **New** sidan den **namn** textrutan typ **blockera åtkomst för medelstora risknivå**.

    ![Namn](./media/app-sign-in-risk/104.png)

6. I den **tilldelning** klickar du på **användare och grupper**.

    ![Användare och grupper](./media/app-sign-in-risk/06.png)

7. På den **användare och grupper** sidan:

    ![Villkorlig åtkomst](./media/app-sign-in-risk/107.png)

    a. Klicka på **Välj användare och grupper**, och välj sedan **användare och grupper**.

    b. Klicka på **Välj**.

    c. På den **Välj** väljer **Alain Charon**, och klicka sedan på **Välj**.

    d. På den **användare och grupper** klickar du på **klar**.

8. Klicka på **Molnappar**.

    ![Molnappar](./media/app-sign-in-risk/08.png)

9. På den **Molnappar** sidan:

    ![Villkorlig åtkomst](./media/app-sign-in-risk/109.png)

    a. Klicka på **alla molnappar**.

    b. Klicka på **Klar**.

10. Klicka på **villkor**. 

    ![Åtkomstkontroller](./media/app-sign-in-risk/19.png)

11. På den **villkor** sidan:

    ![Risknivå för inloggning](./media/app-sign-in-risk/21.png)

    a. Klicka på **inloggningsrisk**.
 
    b. Som **konfigurera**, klickar du på **Ja**.

    c. Logga in risknivå väljer **medel**.

    d. Klicka på **Välj**.

    e. På den **villkor** klickar du på **klar**.



10. I den **åtkomstkontroller** klickar du på **bevilja**.

    ![Åtkomstkontroller](./media/app-sign-in-risk/10.png)

11. På den **bevilja** sidan:

    ![Villkorlig åtkomst](./media/app-sign-in-risk/105.png)

    a. Välj **blockera åtkomst**.

    b. Klicka på **Välj**.

12. I den **aktiverar principen** klickar du på **på**.

    ![Aktivera princip](./media/app-sign-in-risk/18.png)

13. Klicka på **Skapa**.


## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat din princip för villkorsstyrd åtkomst vill du förmodligen veta om den fungerar som förväntat. Använda villkorlig åtkomst som ett första steg **vad händer om principen verktyget** att simulera en inloggning av din testanvändare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.  

När du kör den **vad händer om principen verktyget** i det här scenariot i **blockera åtkomst för medelstora risknivå** bör visas under **principer som gäller**. 

![Användare](./media/app-sign-in-risk/117.png)


**Utvärdera din princip för villkorlig åtkomst:**

1. På den [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) i menyn längst upp på sidan klickar du på **vad händer om**.  
 
    ![What If](./media/app-sign-in-risk/14.png)

2. Klicka på **användaren**väljer **Alan Charon** på den **användare** , och klicka sedan på **Välj**.

    ![Användare](./media/app-sign-in-risk/116.png)

3. Som **inloggningsrisk**väljer **medel**.

    ![Användare](./media/app-sign-in-risk/119.png)


3. Klicka på **vad händer om**.


## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorsstyrd åtkomst

I det föregående avsnittet har du lärt dig hur att utvärdera en simulerad inloggning. Förutom en simulering, bör du också testa principer för villkorlig åtkomst att se till att den fungerar som förväntat. 

Om du vill testa din princip, försöker logga in på din [Azure-portalen](https://portal.azure.com) som **Alan Charon** med Tor Browser. Dina inloggningsförsök ska blockeras av din princip för villkorlig åtkomst.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort testanvändaren, Tor Browser och principen för villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare kan se [ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Välj din princip för att ta bort principen, och klicka sedan på **ta bort** i verktygsfältet för snabb åtkomst.

    ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Anvisningar för att ta bort Tor Browser finns i [avinstallerar](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräv användningsvillkor godkännas](require-tou.md)
> [kräver MFA för specifika appar](app-based-mfa.md)

