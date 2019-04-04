---
title: Snabbstart – blockera åtkomst när en risk för session har identifierats med villkorlig åtkomst i Azure Active Directory | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du kan konfigurera en princip för villkorlig åtkomst för Azure Active Directory (Azure AD) för att blockera inloggningar baserat på sessionen risker.
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
ms.openlocfilehash: d5defdf2d33d32042775271fe01aba377687ae75
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891573"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Snabbstart: Blockera åtkomst när en risk för session har identifierats med villkorlig åtkomst i Azure Active Directory  

Om du vill behålla din miljö skyddad, kanske du vill blockera misstänkta användare från att logga in. [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyserar varje inloggning och beräknar sannolikheten att en inloggning försök inte har utförts av är tillförlitligt ägare för ett användarkonto. Sannolikheten (låg, medelhög och hög) anges i form av ett beräknat värde med namnet [risknivåer för inloggning](conditions.md#sign-in-risk). Du kan konfigurera principer för villkorlig åtkomst för att svara på specifika inloggningsrisk nivåer genom att ange villkoret inloggningsrisk.

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
1. Logga ut.

## <a name="create-your-conditional-access-policy"></a>Skapa principer för villkorlig åtkomst

Scenariot i den här snabbstarten använder en inloggning från en Tor Browser för att generera en identifierad **inloggningar från anonyma IP-adresser** riskhändelsen. Risknivån för den här riskhändelsen är medium. Om du vill svara på den här riskhändelsen kan ange du inloggningsrisk villkoret medium. I en produktionsmiljö bör du ange villkoret inloggningsrisk till hög eller medelhög och hög.

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst som krävs. Ange i din princip:

| Inställning | Värde |
| --- | --- |
| Användare och grupper | Alain Charon  |
| Molnappar | Alla molnappar |
| Inloggningsrisk | Medel |
| Bevilja | Blockera åtkomst |

![Skapa princip](./media/app-sign-in-risk/130.png)

**Konfigurera principer för villkorlig åtkomst:**

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.

1. I Azure portal, på det vänstra navigeringsfältet, klickar du på **Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. På den **Azure Active Directory** sidan den **Security** klickar du på **villkorlig åtkomst**.

   ![Villkorlig åtkomst](./media/app-sign-in-risk/03.png)

1. På den **villkorlig åtkomst** , i verktygsfältet högst upp, klickar du på **Lägg till**.

   ![Namn](./media/app-sign-in-risk/108.png)

1. På den **New** sidan den **namn** textrutan typ **blockera åtkomst för medelstora risknivå**.

   ![Namn](./media/app-sign-in-risk/104.png)

1. I den **tilldelning** klickar du på **användare och grupper**.

   ![Användare och grupper](./media/app-sign-in-risk/06.png)

1. På den **användare och grupper** sidan:

   ![Villkorlig åtkomst](./media/app-sign-in-risk/107.png)

   1. Klicka på **Välj användare och grupper**, och välj sedan **användare och grupper**.

   1. Klicka på **Välj**.

   1. På den **Välj** väljer **Alain Charon**, och klicka sedan på **Välj**.

   1. På den **användare och grupper** klickar du på **klar**.

1. Klicka på **Molnappar**.

   ![Molnappar](./media/app-sign-in-risk/08.png)

1. På den **Molnappar** sidan:

   ![Villkorlig åtkomst](./media/app-sign-in-risk/109.png)

   1. Klicka på **alla molnappar**.

   1. Klicka på **Klar**.

1. Klicka på **villkor**.

   ![Åtkomstkontroller](./media/app-sign-in-risk/19.png)

1. På den **villkor** sidan:

   ![Risknivå för inloggning](./media/app-sign-in-risk/21.png)

   1. Klicka på **inloggningsrisk**.

   1. Som **konfigurera**, klickar du på **Ja**.

   1. Logga in risknivå väljer **medel**.

   1. Klicka på **Välj**.

   1. På den **villkor** klickar du på **klar**.

1. I den **åtkomstkontroller** klickar du på **bevilja**.

   ![Åtkomstkontroller](./media/app-sign-in-risk/10.png)

1. På den **bevilja** sidan:

   ![Villkorlig åtkomst](./media/app-sign-in-risk/105.png)

   1. Välj **blockera åtkomst**.

   1. Klicka på **Välj**.

1. I den **aktiverar principen** klickar du på **på**.

   ![Aktivera princip](./media/app-sign-in-risk/18.png)

1. Klicka på **Skapa**.

## <a name="evaluate-a-simulated-sign-in"></a>Utvärdera en simulerad inloggning

Nu när du har konfigurerat din princip för villkorsstyrd åtkomst vill du förmodligen veta om den fungerar som förväntat. Använda villkorlig åtkomst som ett första steg **vad händer om principen verktyget** att simulera en inloggning av din testanvändare. Simuleringen uppskattar inloggningens inverkan på dina principer och genererar en simuleringsrapport.  

När du kör den **vad händer om principen verktyget** i det här scenariot i **blockera åtkomst för medelstora risknivå** bör visas under **principer som gäller**.

![Användare](./media/app-sign-in-risk/117.png)

**Utvärdera din princip för villkorlig åtkomst:**

1. På den [villkorlig åtkomst – principer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) i menyn längst upp på sidan klickar du på **vad händer om**.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Klicka på **användaren**väljer **Alan Charon** på den **användare** , och klicka sedan på **Välj**.

   ![Användare](./media/app-sign-in-risk/116.png)

1. Som **inloggningsrisk**väljer **medel**.

   ![Användare](./media/app-sign-in-risk/119.png)

1. Klicka på **vad händer om**.

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
