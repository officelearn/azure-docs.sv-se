---
title: Snabbstart – blockera åtkomst när en session risk identifieras med Azure Active Directory Identity Protection | Microsoft Docs
description: I den här snabbstarten får du lära dig hur du kan konfigurera en Azure Active Directory (Azure AD) Identity Protection inloggningsrisk villkorlig åtkomst för att blockera inloggningar baserat på sessionen risker.
services: active-directory
keywords: identitetsskydd, villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce2dc95aae2ba4677da81ce6af45b8a715000e10
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210347"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-identity-protection"></a>Snabbstart: Blockera åtkomst när en session risk identifieras med Azure Active Directory Identity Protection  

Du kanske vill blockera misstänkta användare från att logga in för att hålla din miljö skyddad. Azure Active Directory (Azure AD) Identity Protection analyserar varje inloggning och beräknar sannolikheten att en inloggning försök inte har utförts av är tillförlitligt ägare för ett användarkonto. Sannolikheten (låg, medelhög och hög) anges i form av ett beräknat värde med namnet risknivå för inloggning. Du kan konfigurera principer för villkorlig åtkomst inloggningsrisk för att svara på specifika inloggningsrisk nivåer genom att ange villkoret inloggningsrisk. 

Den här snabbstarten visar hur du konfigurerar en villkorlig åtkomstprincip som blockerar för inloggningsrisk loggar in när ett medium och ovan inloggningsrisk nivå har identifierats. 

![Skapa princip](./media/quickstart-sign-in-risk-policy/1004.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.



## <a name="prerequisites"></a>Förutsättningar 

För att kunna slutföra scenariot i den här självstudien behöver du:

- **Åtkomst till en Azure AD Premium P2-versionen** – Azure AD Identity Protection är en funktion i Azure AD Premium P2. 

- **Identitetsskydd** -scenariot i den här snabbstarten kräver Identity Protection är aktiverat. Om du inte vet hur du aktiverar du Identity Protection, se [aktiverar Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Tor Browser** – [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) är utformat för att bevara sekretessen online. Identity Protection identifierar en inloggning från en Tor Browser som **inloggningar från anonyma IP-adresser**, som har en Medelrisk-nivå. Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](../reports-monitoring/concept-risk-events.md).  

- **Ett testkonto kallas Alain Charon** – om du inte vet hur du skapar ett testkonto finns [lägga till en ny användare](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).


## <a name="test-your-sign-in"></a>Testa din inloggning 

Målet med det här steget är att se till att ditt testkonto har åtkomst till din klient med Tor Browser.

**Så här testar din inloggning:**

1. Logga in på din [Azure-portalen](https://portal.azure.com) som **Alain Charon**.

2. Logga ut. 


## <a name="create-your-conditional-access-policy"></a>Skapa principer för villkorlig åtkomst 

Scenariot i den här snabbstarten använder en inloggning från en Tor Browser för att generera en identifierad **inloggningar från anonyma IP-adresser** riskhändelsen. Risknivån för den här riskhändelsen är medium. Om du vill svara på den här riskhändelsen kan ange du inloggningsrisk villkoret medium. 

Det här avsnittet visar hur du skapar principen för villkorlig åtkomst krävs inloggningsrisk. Ange i din princip:

|Inställning |Värde|
|---     | --- |
| Användare  | Alain Charon  |
| Villkor | Inloggningsrisk, medel och högre |
| Kontroller | Blockera åtkomst |
 

![Skapa princip](./media/quickstart-sign-in-risk-policy/201.png)

 


**Konfigurera principer för villkorlig åtkomst:**

1. Logga in på din [Azure-portalen](https://portal.azure.com) som global administratör.

2. Gå till den [Azure AD Identity Protection-sidan](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/Overview).
 
3. På den **Azure AD Identity Protection** sidan den **konfigurera** klickar du på **inloggning riskprincipen**.
 
4. På sidan för principer i den **tilldelningar** klickar du på **användare**.

5. På den **användare** klickar du på **Välj användare**.

6. På den **Välj användare** väljer **Alain Charon**, och klicka sedan på **Välj**.

7. På den **användare** klickar du på **klar**. 

8. På sidan för principer i den **tilldelningar** klickar du på **villkor**.

9. På den **villkor** klickar du på **inloggningsrisk**.

10. På den **inloggningsrisk** väljer **medel och högre**, och klicka sedan på **Välj**. 

11. På den **villkor** klickar du på **klar**.

12. På sidan för principer i den **kontroller** klickar du på **åtkomst**.

13. På den **åtkomst** klickar du på **Tillåt åtkomst**väljer **kräva multifaktorautentisering**, och klicka sedan på **Välj**.

14. På policysidan **spara**.  


## <a name="test-your-conditional-access-policy"></a>Testa din princip för villkorsstyrd åtkomst

Om du vill testa din princip, försöker logga in på din [Azure-portalen](https://portal.azure.com) som **Alan Charon** med Tor Browser. Dina inloggningsförsök ska blockeras av din princip för villkorlig åtkomst.

![Multi-Factor Authentication](./media/quickstart-sign-in-risk-policy/203.png)


## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort testanvändare Tor Browser och inaktivera den villkorliga åtkomstprincipen för inloggningsrisk:

- Om du inte vet hur du tar bort en Azure AD-användare kan se [lägga till eller ta bort användare](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Anvisningar för att ta bort Tor Browser finns i [avinstallerar](https://tb-manual.torproject.org/uninstalling/).


