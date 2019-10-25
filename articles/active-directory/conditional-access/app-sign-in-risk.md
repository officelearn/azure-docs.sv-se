---
title: Snabb start – blockera åtkomst när en sessionsgräns identifieras med Azure Active Directory villkorlig åtkomst | Microsoft Docs
description: I den här snabb starten får du lära dig hur du kan konfigurera en princip för villkorlig åtkomst för Azure Active Directory (Azure AD) för att blockera inloggningar baserat på risker i sessioner.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 619f1ea3bae001d25eb520f43da33ca94a3160c8
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880344"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Snabb start: blockera åtkomst när en sessionsgräns identifieras med Azure Active Directory villkorlig åtkomst  

Om du vill hålla din miljö skyddad kan du blockera misstänkta användare från inloggning. [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyserar varje inloggning och beräknar sannolikheten för att ett inloggnings försök inte utfördes av en legitim ägare till ett användar konto. Sannolikheten (låg, medium, hög) anges i form av ett beräknat värde som kallas [inloggnings risk nivåer](conditions.md#sign-in-risk). Genom att ange risk villkor för inloggning kan du konfigurera en princip för villkorlig åtkomst som svarar på speciella risk nivåer för inloggning.

Den här snabb starten visar hur du konfigurerar en [princip för villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) som blockerar en inloggning när en konfigurerad risk nivå för inloggning har upptäckts.

![Skapa princip](./media/app-sign-in-risk/1000.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra scenariot i den här självstudien behöver du:

- **Åtkomst till en Azure AD Premium P2-version** – medan villkorlig åtkomst är en Azure AD Premium P1-funktion behöver du en P2-utgåva eftersom scenariot i den här snabb starten kräver identitets skydd.
- **Identitets skydd** – scenariot i den här snabb starten kräver att identitets skydd aktive ras. Om du inte vet hur du aktiverar identitets skydd, se [aktivera Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md).
- **Tor webbläsare** – [webbläsaren Tor](https://www.torproject.org/projects/torbrowser.html.en) är utformad för att hjälpa dig att bevara din sekretess online. Identitets skydd identifierar en inloggning från en Tor webbläsare som inloggningar från anonyma IP-adresser, som har medelhög risk nivå. Mer information finns i [Azure Active Directory risk identifieringar](../reports-monitoring/concept-risk-events.md).  
- **Ett test konto med namnet Alain Charon** – om du inte vet hur du skapar ett test konto läser du [lägga till molnbaserade användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testa din inloggning

Målet med det här steget är att se till att ditt test konto har åtkomst till din klient med hjälp av Tor-webbläsaren.

**Så här testar du inloggningen:**

1. Logga in på [Azure Portal](https://portal.azure.com) som **Alain Charon**.
1. Logga ut.

## <a name="create-your-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Scenariot i den här snabb starten använder en inloggning från en Tor webbläsare för att generera ett identifierat **inloggnings program från anonyma IP-adresser** risk identifiering. Risk nivån för denna risk identifiering är medels Tor. För att svara på den här identifieringen av risker ställer du in risk villkoret på medel. I en produktions miljö bör du ange risk villkor för inloggning, antingen till hög eller till medium och hög.

I det här avsnittet visas hur du skapar den nödvändiga principen för villkorlig åtkomst. Ange följande i principen:

| Inställning | Värde |
| --- | --- |
| Användare och grupper | Alain Charon  |
| Molnappar | Alla molnappar |
| Inloggnings risk | Medium |
| Ömsesidigt | Blockera åtkomst |

![Skapa princip](./media/app-sign-in-risk/130.png)

**Så här konfigurerar du en princip för villkorlig åtkomst:**

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, säkerhets administratör eller en administratör för villkorlig åtkomst.
1. Klicka på **Azure Active Directory**i Azure Portal i det vänstra navigerings fältet.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Klicka på **villkorlig åtkomst**i avsnittet **säkerhet** på sidan **Azure Active Directory** .

   ![Villkorlig åtkomst](./media/app-sign-in-risk/03.png)

1. Klicka på **Lägg till**i verktygsfältet högst upp på sidan för **villkorlig åtkomst** .

   ![Namn](./media/app-sign-in-risk/108.png)

1. På sidan **nytt** i text rutan **namn** skriver du **blockera åtkomst för medelhög risk nivå**.

   ![Namn](./media/app-sign-in-risk/104.png)

1. I avsnittet **tilldelning** klickar du på **användare och grupper**.

   ![Användare och grupper](./media/app-sign-in-risk/06.png)

1. På sidan **användare och grupper** :

   ![Villkorlig åtkomst](./media/app-sign-in-risk/107.png)

   1. Klicka på **Välj användare och grupper**och välj sedan **användare och grupper**.
   1. Klicka på **Välj**.
   1. På sidan **Välj** väljer du **Alain Charon**och klickar sedan på **Välj**.
   1. På sidan **användare och grupper** klickar du på **Slutför**.
1. Klicka på **molnappar**.

   ![Molnappar](./media/app-sign-in-risk/08.png)

1. På sidan **Cloud Apps** :

   ![Villkorlig åtkomst](./media/app-sign-in-risk/109.png)

   1. Klicka på **alla molnappar**.
   1. Klicka på **Klar**.
1. Klicka på **villkor**.

   ![Åtkomst kontroller](./media/app-sign-in-risk/19.png)

1. På sidan **villkor** :

   ![Inloggnings risk nivå](./media/app-sign-in-risk/21.png)

   1. Klicka på **inloggnings risk**.
   1. Som **Konfigurera**klickar du på **Ja**.
   1. Som inloggnings risk nivå väljer du **medel**.
   1. Klicka på **Välj**.
   1. På sidan **villkor** klickar du på **Slutför**.
1. I avsnittet **åtkomst kontroller** klickar du på **bevilja**.

   ![Åtkomst kontroller](./media/app-sign-in-risk/10.png)

1. På sidan **beviljande** :

   ![Villkorlig åtkomst](./media/app-sign-in-risk/105.png)

   1. Välj **blockera åtkomst**.
   1. Klicka på **Välj**.
1. I avsnittet **Aktivera princip** klickar du **på på**.

   ![Aktivera princip](./media/app-sign-in-risk/18.png)

1. Klicka på **Skapa**.

## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat din princip för villkorlig åtkomst vill du förmodligen veta om den fungerar som förväntat. Som ett första steg använder du den villkorliga åtkomsten **vad gör om-princip verktyget** för att simulera en inloggning av test användaren. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.  

När du kör **verktyget vad händer om-princip** för det här scenariot ska **blockera åtkomsten för medelhög risk** listas under **principer som ska gälla**.

![Användare](./media/app-sign-in-risk/117.png)

**Så här utvärderar du principen för villkorlig åtkomst:**

1. På sidan [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) går du till menyn högst upp och klickar på **What If**.  

   ![Tänk om](./media/app-sign-in-risk/14.png)

1. Klicka på **användare**, Välj **Alan Charon** på sidan **användare** och klicka sedan på **Välj**.

   ![Användare](./media/app-sign-in-risk/116.png)

1. Som **inloggnings risk**väljer du **medel**.

   ![Användare](./media/app-sign-in-risk/119.png)

1. Klicka på **What If**.

## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorlig åtkomst

I föregående avsnitt har du lärt dig hur du utvärderar en simulerad inloggning. Förutom en simulering bör du också testa principen för villkorlig åtkomst för att se till att den fungerar som förväntat.

Testa principen genom att försöka logga in på [Azure Portal](https://portal.azure.com) som **Alan Charon** med hjälp av webbläsaren Tor. Ditt inloggnings försök ska blockeras av den villkorliga åtkomst principen.

![Multifaktorautentisering](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort test användaren, Tor webbläsaren och principen för villkorlig åtkomst:

- Om du inte vet hur du tar bort en Azure AD-användare kan du läsa [ta bort användare från Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Om du vill ta bort principen väljer du principen och klickar sedan på **ta bort** i verktygsfältet snabb åtkomst.

   ![Multifaktorautentisering](./media/app-sign-in-risk/33.png)

- Instruktioner för att ta bort Tor-webbläsaren finns i [Avinstallera](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kräv användnings villkor för att godkännas](require-tou.md)
> [kräva MFA för vissa appar](app-based-mfa.md)
