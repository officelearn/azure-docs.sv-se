---
title: Snabb start – blockera åtkomst när en sessions risk identifieras med Azure Active Directory Identity Protection | Microsoft Docs
description: I den här snabb starten får du lära dig hur du kan konfigurera en Azure Active Directory (Azure AD) identitets skydds princip för villkorlig åtkomst för att blockera inloggningar baserat på session risker.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: quickstart
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb1e29735a860f5dc3b6ce8996af9fcd4962871
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335315"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Snabbstart: Blockera åtkomst när en sessionsgräns identifieras med Azure Active Directory Identity Protection  

Om du vill skydda din miljö kan du blockera misstänkta användare från att logga in. Azure Active Directory (Azure AD) Identity Protection analyserar varje inloggning och beräknar sannolikheten för att ett inloggnings försök inte utfördes av en legitim ägare till ett användar konto. Sannolikheten (låg, medium, hög) anges i form av ett beräknat värde som kallas inloggnings risk nivå. Genom att ange risk villkor för inloggning kan du konfigurera en princip för villkorlig åtkomst för inloggnings risker för att svara på speciella risk nivåer för inloggning. 

Den här snabb starten visar hur du konfigurerar en princip för villkorlig åtkomst för inloggnings risker som blockerar inloggning när en medelhög och större inloggnings risk nivå har upptäckts. 

![Skapa princip](./media/quickstart-sign-in-risk-policy/1004.png)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar 

För att kunna slutföra scenariot i den här självstudien behöver du:

- **Åtkomst till en Azure AD Premium P2-utgåva** – Azure AD Identity Protection är en Azure AD Premium P2-funktion. 
- **Identitets skydd** – scenariot i den här snabb starten kräver att identitets skydd aktive ras. Om du inte vet hur du aktiverar identitets skydd, se [aktivera Azure Active Directory Identity Protection](../identity-protection/enable.md).
- **Tor webbläsare** – [webbläsaren Tor](https://www.torproject.org/projects/torbrowser.html.en) är utformad för att hjälpa dig att bevara din sekretess online. Identitets skydd identifierar en inloggning från en Tor webbläsare som inloggningar **från anonyma IP-adresser**, som har medelhög risk nivå. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](../reports-monitoring/concept-risk-events.md).  
- **Ett test konto med namnet Alain Charon** – om du inte vet hur du ska skapa ett test konto går du [till Lägg till en ny användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testa din inloggning 

Målet med det här steget är att se till att ditt test konto har åtkomst till din klient med hjälp av Tor-webbläsaren.

**Så här testar du inloggningen:**

1. Logga in på [Azure Portal](https://portal.azure.com) som **Alain Charon**.
2. Logga ut. 

## <a name="create-your-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst 

Scenariot i den här snabb starten använder en inloggning från en Tor webbläsare för att skapa ett identifierat **inloggnings program från en anonym IP-adress** risk händelse. Risk nivån för den här risk händelsen är medels Tor. För att svara på den här risk händelsen ställer du in risk villkoret på medel. 

I det här avsnittet visas hur du skapar den nödvändiga villkorliga åtkomst principen för inloggnings risker. Ange följande i principen:

|Inställning |Value|
|---     | --- |
| Användare  | Alain Charon  |
| Villkor | Inloggnings risk, medium och över |
| Kontroller | Blockera åtkomst |

![Skapa princip](./media/quickstart-sign-in-risk-policy/201.png)

**Så här konfigurerar du en princip för villkorlig åtkomst:**

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Gå till [sidan Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
3. Klicka på **inloggnings risk princip**i avsnittet **konfigurera** på sidan **Azure AD Identity Protection** .
4. På sidan princip i avsnittet tilldelningar  klickar du på **användare**.
5. På sidan **användare** klickar du på **Välj användare**.
6. På sidan **Välj användare** väljer du **Alain Charon**och klickar sedan på **Välj**.
7. På sidan **användare** klickar du på **Slutför**. 
8. På sidan princip i avsnittet tilldelningar  klickar du på **villkor**.
9. På sidan **villkor** klickar du på **inloggnings risk**.
10. På sidan **inloggnings risker** väljer du **medium och över**, och klickar sedan på **Välj**. 
11. På sidan **villkor** klickar du på **Slutför**.
12. Klicka på **åtkomst**i avsnittet **kontroller** på sidan princip.
13. På **åtkomst** sidan klickar du på **Tillåt åtkomst**, väljer **Kräv Multi-Factor Authentication**och klickar sedan på **Välj**.
14. På sidan princip klickar du på **Spara**.  

## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorlig åtkomst

Testa principen genom att försöka logga in på [Azure Portal](https://portal.azure.com) som **Alan Charon** med hjälp av webbläsaren Tor. Ditt inloggnings försök ska blockeras av den villkorliga åtkomst principen.

![Multifaktorautentisering](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver tar du bort test användaren, Tor webbläsaren och inaktiverar den villkorliga åtkomst principen för inloggnings risker:

- Om du inte vet hur du tar bort en Azure AD-användare, se [hur du lägger till eller tar bort användare](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Instruktioner för att ta bort Tor-webbläsaren finns i [Avinstallera](https://tb-manual.torproject.org/uninstalling/).
