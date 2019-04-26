---
title: 'Självstudier: Multifaktorautentisering för B2B – Azure Active Directory | Microsoft Docs'
description: Lär dig mer om att kräva multifaktorautentisering (MFA) när du använder Azure AD B2B för att samarbeta med externa användare och partnerorganisationer.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celested
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d83cad9c6681a9d1c852c3d874028ceb6913344f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60357907"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Självstudier: Framtvinga multifaktorautentisering för B2B-gästanvändare

När du samarbetar med externa B2B-gästanvändare är det en bra idé om du skyddar dina appar med principer för multifaktorautentisering (MFA). De externa användarna kommer då att behöva mer än bara ett användarnamn och ett lösenord för att få åtkomst till dina resurser. I Azure Active Directory (AD Azure), kan du uppnå detta med en princip för villkorsstyrd åtkomst som kräver MFA för åtkomst. MFA-principerna kan tillämpas i klientorganisations- eller appnivå eller på enskild gästanvändarnivå, på samma sätt som de aktiveras för medlemmar i din organisation.

Exempel:

![Diagram som visar en gästanvändare loggar in på ett företags-appar](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  En administratör eller anställd på företag A bjuder in en gästanvändare att använda ett molnprogram eller lokalt program som är konfigurerat för att kräva MFA för åtkomst.
2.  Gästanvändaren loggar med sina egna arbets- eller skolidentiteter eller sociala identiteter. 
3.  Användaren uppmanas att slutföra en MFA-kontroll. 
4.  Användaren ställer in MFA med företag A och väljer sina MFA-alternativ. Användaren får åtkomst till programmet.

I den här kursen ska du:

> [!div class="checklist"]
> * Testa inloggningen innan du påbörjar MFA-installationen.
> * Skapa en princip för villkorsstyrd åtkomst som kräver MFA för åtkomst till en molnapp i din miljö. I den här självstudien använder vi Microsoft Azure Management-appen för att illustrera processen.
> * Använd What If-verktyget för att simulera MFA-inloggningen.
> * Testa din princip för villkorsstyrd åtkomst.
> * Rensa testanvändare och princip.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra scenariot i den här självstudien behöver du:

 - **Åtkomst till Azure AD Premium-versionen**, som innehåller funktioner för princip för villkorsstyrd åtkomst. Om du vill införa MFA måste du skapa en princip för villkorsstyrd åtkomst i Azure AD. Observera att MFA-principer alltid tillämpas i din organisation, oavsett om partnern har MFA-funktioner eller inte. Om du konfigurerar MFA i organisationen måste du se till att du har tillräckligt med Azure AD Premium-licenser för din gästanvändare. 
 - **Ett giltigt externt e-postkonto** som du kan lägga till i din klientkatalog som gästanvändare och använda för att logga in. Om du inte vet hur du skapar ett gästkonto, så gå till [Lägga till en B2B-gästanvändare i Azure Portal](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Skapa en testgästanvändare i Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com/) som Azure AD-administratör.
2. Välj **Azure Active Directory** i den vänstra rutan.
3.  Under **Hantera** väljer du **Användare**.
4.  Välj **Ny gästanvändare**.

    ![Skärmbild som visar var du väljer alternativet för användare av nya gäst](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  Under **Användarnamn** anger du den externa användarens e-postadress. Du kan också lägga till ett välkomstmeddelande. 

    ![Skärmbild som visar var du anger gäst inbjudningsmeddelandet](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Välj **Bjud in** för att skicka inbjudan till gästanvändaren automatiskt. Meddelandet **Användaren har bjudits in** visas. 
7.  När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Testa inloggningen innan du påbörjar MFA-installationen
1.  Använd ditt testanvändarnamn och lösenordet för att logga in på [Azure Portal](https://portal.azure.com/).
2.  Observera att du kan få åtkomst till Azure Portal bara genom att använda dina inloggningsuppgifter. Ingen ytterligare autentisering krävs.
3.  Logga ut.

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Skapa en princip för villkorsstyrd åtkomst som kräver MFA
1.  Logga in på din [Azure-portalen](https://portal.azure.com/) som säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.
2.  Välj **Azure Active Directory** i Azure Portal. 
3.  Välj **Villkorsstyrd åtkomst** i avsnittet **Säkerhet** på sidan **Azure Active Directory**.
4.  Välj **Ny princip** i verktygsfältet högst upp på sidan **Villkorsstyrd åtkomst**.
5.  Skriv **Kräver MFA för B2B-portalåtkomst** i textrutan **Namn** på sidan **Ny**.
6.  Välj **Användare och grupper** i avsnittet **Tilldelningar**.
7.  Välj **Välj användare och grupper** på sidan **Användare och grupper** och välj sedan **Alla gästanvändare (förhandsversion)**.

    ![Skärmbild som visar att välja alla gästanvändare](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Välj **Done** (Klar).
10. Välj **Molnappar** i avsnittet **Tilldelningar** på sidan **Ny**.
11. Välj **Välj appar** på sidan **Molnappar** och välj sedan **Välj**.

    ![Skärmbild som visar sidan appar för molnet och väljer alternativet](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Välj **Microsoft Azure-hantering** på sidan **Välj** och välj sedan **Välj**.

    ![Skärmbild som visar Microsoft Azure Management-app har valts](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Välj **Klar** på sidan **Molnappar**.
14. Välj **Bevilja** i avsnittet **Åtkomstkontroller** på sidan **Ny**.
15. Välj **Bevilja åtkomst** på sidan **Bevilja**, markera kryssrutan **Kräv multifaktorautentisering** och välj sedan **Välj**.

    ![Skärmbild som visar den kräver multifaktorautentisering alternativet](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. Välj **På** under **Aktivera princip**.

    ![Skärmbild som visar alternativet Aktivera aktiverat](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Välj **Skapa**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Simulera inloggningen med hjälp av What If-alternativet

1.  Välj **What If** på sidan **Villkorsstyrd åtkomst – Principer**. 

    ![Skärmbild som visar var du ska välja vad om alternativet](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Välj **Användare**, marker din testgästanvändare och välj sedan **Välj**.

    ![Skärmbild som visar en gästanvändare som valts](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Välj **Molnappar**.
4.  Välj **Välj appar** på sidan **Molnappar** och klicka sedan på **Välj**. Välj **Microsoft Azure-hantering** i listan över program och klicka sedan på **Välj**. 

    ![Skärmbild som visar Microsoft Azure Management-app har valts](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Välj **Klar** på sidan **Molnappar**.
6.  Välj **What If** och verifiera att den nya principen visas under **Utvärderingsresultat** på fliken **Principer som gäller**.

    ![Skärmbild som visar var du ska välja vad om alternativet](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorsstyrd åtkomst
1.  Använd ditt testanvändarnamn och lösenordet för att logga in på [Azure Portal](https://portal.azure.com/).
2.  Du bör se en begäran om ytterligare autentiseringsmetoder. Observera att det kan ta lite tid innan principen träder i kraft.

    ![Skärmbild som visar mer information krävs för meddelande](media/tutorial-mfa/mfa-required.png)
 
3.  Logga ut.

## <a name="clean-up-resources"></a>Rensa resurser
När testanvändaren inte längre behövs kan du ta bort den och principen för villkorsstyrd åtkomst för testning.
1.  Logga in på [Azure Portal](https://portal.azure.com/) som Azure AD-administratör.
2.  Välj **Azure Active Directory** i den vänstra rutan.
3.  Under **Hantera** väljer du **Användare**.
4.  Välj testanvändaren och välj sedan **Ta bort användare**.
5.  Välj **Azure Active Directory** i den vänstra rutan.
6.  Välj **Villkorsstyrd åtkomst** under **Säkerhet**.
7.  Välj snabbmenyn (...) för testprincipen i listan **Principnamn** och välj sedan **Ta bort**. Bekräfta genom att välja **Ja**.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en princip för villkorsstyrd åtkomst som kräver att gästanvändare använder MFA när de loggar in till någon av dina molnappar. Läs mer om att lägga till gästanvändare för samarbete i [Lägga till B2B-samarbetsanvändare för Azure Active Directory i Azure Portal](add-users-administrator.md).
