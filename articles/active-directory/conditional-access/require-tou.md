---
title: Villkorlig åtkomst kräver användningsvillkor - Azure Active Directory
description: I den här snabbstarten får du lära dig hur du kan kräva att dina användarvillkor accepteras innan åtkomst till valda molnappar beviljas av Azure Active Directory Villkorlig åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd1b4cf554e773f49a15ac5cedcbcc5b3e710b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74380106"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Snabbstart: Kräv att användningsvillkor accepteras innan du öppnar molnappar

Innan du öppnar vissa molnappar i din miljö kanske du vill få samtycke från användare i form av att acceptera dina användarvillkor (ToU). Azure Active Directory (Azure AD) Villkorlig åtkomst ger dig:

- En enkel metod för att konfigurera ToU
- Alternativet att kräva att du godkänner dina användarvillkor via en policy för villkorlig åtkomst  

Den här snabbstarten visar hur du konfigurerar en [Azure AD-princip](../active-directory-conditional-access-azure-portal.md) för villkorlig åtkomst som kräver att en åtkomstanteckning accepteras för en vald molnapp i din miljö.

![Skapa princip](./media/require-tou/5555.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra scenariot i den här snabbstarten måste du:

- **Åtkomst till en Azure AD Premium-utgåva** – Azure AD Conditional Access är en Azure AD Premium-funktion.
- **Ett testkonto som heter Isabella Simonsen** - Om du inte vet hur du skapar ett testkonto läser [du Lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testa din inloggning

Målet med det här steget är att få ett intryck av inloggningsupplevelsen utan en princip för villkorlig åtkomst.

**Så här testar du inloggningen:**

1. Logga in på din [Azure-portal](https://portal.azure.com/) som Isabella Simonsen.
1. Logga ut.

## <a name="create-your-terms-of-use"></a>Skapa dina användarvillkor

I det här avsnittet får du möjlighet att skapa ett exempel på tou. När du skapar en åtkomstmedu väljer du ett värde för **Framtvinga med principmallar**för villkorlig åtkomst . Om du väljer **Anpassad princip** öppnas dialogrutan för att skapa en ny princip för villkorlig åtkomst så snart din åtkomstanpassning har skapats.

**Så här skapar du användningsvillkor:**

1. Skapa ett nytt dokument i Microsoft Word.
1. Skriv **Mina användarvillkor**och spara sedan dokumentet på datorn som **mytou.pdf**.
1. Logga in på din [Azure-portal](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorlig åtkomst.
1. Klicka på **Azure Active Directory**i Azure-portalen i det vänstra navigeringsfältet .

   ![Azure Active Directory](./media/require-tou/02.png)

1. Klicka på **Villkorlig åtkomst**i avsnittet **Säkerhet** på sidan Azure **Active Directory** .

   ![Villkorlig åtkomst](./media/require-tou/03.png)

1. Klicka på **Användningsvillkor**i avsnittet **Hantera.**

   ![Användningsvillkor](./media/require-tou/04.png)

1. Klicka på **Nya termer**i menyn högst upp .

   ![Användningsvillkor](./media/require-tou/05.png)

1. På sidan **Nya användarvillkor:**

   ![Användningsvillkor](./media/require-tou/112.png)

   1. Skriv **Min tou**i textrutan **Namn** .
   1. Skriv **Min tou**i textrutan **Visningsnamn** .
   1. Ladda upp dina användarvillkor PDF-fil.
   1. Som **språk**väljer du **engelska**.
   1. När **kräv att användare ska expandera användningsvillkoren**väljer du **På**.
   1. Välj **Anpassad princip**som **framtvinga med principmallar för villkorlig åtkomst**.
   1. Klicka på **Skapa**.

## <a name="create-your-conditional-access-policy"></a>Skapa principen villkorlig åtkomst

Det här avsnittet visar hur du skapar den obligatoriska principen för villkorlig åtkomst. Scenariot i den här snabbstarten använder:

- Azure-portalen som platshållare för en molnapp som kräver att din ToU accepteras. 
- Provanvändaren för att testa principen villkorlig åtkomst.  

I din policy anger du:

| Inställning | Värde |
| --- | --- |
| Användare och grupper | Isabella Simonsen |
| Molnappar | Microsoft Azure-hantering |
| Bevilja åtkomst | Min TOU |

![Skapa princip](./media/require-tou/1234.png)

**Så här konfigurerar du principen för villkorlig åtkomst:**

1. Skriv **Kräv tou för Isabella**i textrutan **Namn** på den **nya** sidan .

   ![Namn](./media/require-tou/71.png)

1. Klicka på Användare **och grupper**i avsnittet **Tilldelning** .

   ![Användare och grupper](./media/require-tou/06.png)

1. På sidan **Användare och grupper:**

   ![Användare och grupper](./media/require-tou/24.png)

   1. Klicka på **Markera användare och grupper**och välj sedan Användare och **grupper**.
   1. Klicka på **Markera**.
   1. På sidan **Välj** väljer du **Isabella Simonsen**och klickar sedan på **Välj**.
   1. Klicka på **Klar**på sidan **Användare och grupper** .
1. Klicka på **Molnappar**.

   ![Molnappar](./media/require-tou/08.png)

1. På sidan **Molnappar:**

   ![Välj molnappar](./media/require-tou/26.png)

   1. Klicka på **Välj appar**.
   1. Klicka på **Markera**.
   1. Välj Microsoft Azure **Management**på sidan **Välj** och klicka sedan på **Välj**.
   1. Klicka på **Klar**på sidan **Molnappar** .
1. Klicka på **Bevilja**i avsnittet **Access-kontroller** .

   ![Åtkomstkontroller](./media/require-tou/10.png)

1. På **grant-sidan:**

   ![Bevilja](./media/require-tou/111.png)

   1. Välj **Bevilja åtkomst**.
   1. Välj **Min TOU**.
   1. Klicka på **Markera**.
1. Klicka på **På**i avsnittet **Aktivera princip** .

   ![Aktivera princip](./media/require-tou/18.png)

1. Klicka på **Skapa**.

## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat principen villkorlig åtkomst vill du förmodligen veta om den fungerar som förväntat. Som ett första steg använder du verktyget Villkorlig åtkomst vad händer om-principverktyget för att simulera en inloggning av testanvändaren. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.  

Om du vill initiera verktyget **Vad händer om-principens** utvärdering anger du:

- **Isabella Simonsen** som användare
- **Microsoft Azure Management** som molnapp

Om du klickar på **Vad händer** skapas en simuleringsrapport som visar:

- **Kräv TOU för Isabella** enligt **policyer som gäller**
- **Min tou** som **Grant Controls**.

![Vad händer om principverktyg](./media/require-tou/79.png)

**Så här utvärderar du principen för villkorlig åtkomst:**

1. På sidan [Villkorlig åtkomst - Principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) klickar du på Vad händer **om**på menyn högst upp .  

   ![What If](./media/require-tou/14.png)

1. Klicka på **Användare**, välj **Isabella Simonsen**och klicka sedan på **Välj**.

   ![Användare](./media/require-tou/15.png)

1. Så här väljer du en molnapp:

   ![Molnappar](./media/require-tou/16.png)

   1. Klicka på **Molnappar**.
   1. Klicka på **Välj appar**på **sidan Molnappar**.
   1. Klicka på **Markera**.
   1. Välj Microsoft Azure **Management**på sidan **Välj** och klicka sedan på **Välj**.
   1. Klicka på **Klar**på sidan molnappar .
1. Klicka på **Vad händer om**.

## <a name="test-your-conditional-access-policy"></a>Testa principen för villkorlig åtkomst

I föregående avsnitt har du lärt dig hur du utvärderar en simulerad inloggning. Förutom en simulering bör du också testa principen för villkorlig åtkomst för att säkerställa att den fungerar som förväntat.

Testa din princip genom att försöka logga in på din [Azure-portal](https://portal.azure.com) med ditt **Isabella Simonsen-testkonto.** Du bör se en dialogruta som kräver att du accepterar dina användarvillkor.

![Användningsvillkor](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort testanvändaren och principen villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare läser du [Ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Om du vill ta bort principen markerar du principen och klickar sedan på **Ta bort** i verktygsfältet snabbåtkomst.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- Om du vill ta bort användningsvillkoren markerar du det och klickar sedan på **Ta bort termer** i verktygsfältet överst.

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräv MFA för specifika appar](app-based-mfa.md)
> [Blockera åtkomst när en sessionsrisk upptäcks](app-sign-in-risk.md)
