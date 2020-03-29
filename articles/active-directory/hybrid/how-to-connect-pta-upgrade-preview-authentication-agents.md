---
title: Azure AD Connect – direktautentisering – uppgradera auth-agenter | Microsoft-dokument
description: I den här artikeln beskrivs hur du uppgraderar din Azure Active Directory (Azure AD) direktautentiseringskonfiguration.
services: active-directory
keywords: Azure AD Connect Pass-through-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 494ccc3b90b8c249ee935087dcf0f0b5264b02ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60386779"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory Direktautentisering: Autentiseringsagenter för förhandsgranskning av uppgradering

## <a name="overview"></a>Översikt

Den här artikeln är för kunder som använder Azure AD Pass-through Autentisering via förhandsversion. Vi uppgraderade nyligen (och bytte namn) till autentiseringsagenten. Du måste _manuellt_ uppgradera förhandsversionen av autentiseringsagenter som är installerade på dina lokala servrar. Den här manuella uppgraderingen är endast en engångsåtgärd. Alla framtida uppdateringar av autentiseringsagenter är automatiska. Skälen till uppgraderingen är följande:

- Förhandsversionerna av autentiseringsagenter kommer inte att få några ytterligare säkerhets- eller buggfixar.
-   Förhandsgranskningsversionerna av autentiseringsagenter kan inte installeras på ytterligare servrar, för hög tillgänglighet.

## <a name="check-versions-of-your-authentication-agents"></a>Kontrollera versioner av dina autentiseringsagenter

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Steg 1: Kontrollera var dina autentiseringsagenter är installerade

Så här kontrollerar du var dina autentiseringsagenter är installerade:

1. Logga in på [Administrationscentret](https://aad.portal.azure.com) för Azure Active Directory med autentiseringsuppgifterna för den globala administratören för din klient.
2. Välj **Azure Active Directory** i vänsternavigering.
3. Välj **Azure AD Connect**. 
4. Välj **Direktautentisering**. Det här bladet visar servrarna där dina autentiseringsagenter är installerade.

![Administrationscenter för Azure Active Directory – Bladet Direktautentisering](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Steg 2: Kontrollera versionerna av dina autentiseringsagenter

Så här kontrollerar du versionerna av dina autentiseringsagenter på varje server som identifieras i föregående steg:

1. Gå till **Kontrollpanelen -> Program -> program och funktioner** på den lokala servern.
2. Om det finns en post för "**Microsoft Azure AD Connect Authentication Agent**" behöver du inte vidta några åtgärder på den här servern.
3. Om det finns en post för "**Microsoft Azure AD Application Proxy Connector**" måste du uppgradera manuellt på den här servern.

![Förhandsgranska version av autentiseringsagent](./media/how-to-connect-pta-upgrade-preview-authentication-agents/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Metodtips att följa innan du startar uppgraderingen

Innan du uppgraderar ska du se till att följande artiklar finns på plats:

1. **Skapa globalt administratörskonto för molnet:** Uppgradera inte utan att ha ett globalt administratörskonto för molnet som ska användas i nödsituationer där dina direktautentiseringsagenter inte fungerar korrekt. Läs mer om hur du [lägger till ett globalt administratörskonto för molnet](../active-directory-users-create-azure-portal.md). Att göra det här steget är kritiskt och ser till att du inte blir utelåst från din klient.
2.  **Säkerställ hög tillgänglighet:** Om den inte har slutförts tidigare installerar du en andra fristående autentiseringsagent för att ge hög tillgänglighet för [inloggningsbegäranden](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)med hjälp av dessa instruktioner .

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Uppgradera autentiseringsagenten på din Azure AD Connect-server

Du behöver uppgradera Azure AD Connect innan du uppgraderar autentiseringsagenten på samma server. Följ dessa steg på både dina primära och mellanlagring av Azure AD Connect-servrar:

1. **Uppgradera Azure AD Connect:** Följ den här [artikeln](how-to-upgrade-previous-version.md) och uppgradera till den senaste Azure AD Connect-versionen.
2. **Avinstallera förhandsversionen av Authentication Agent:** Hämta [det här PowerShell-skriptet](https://aka.ms/rmpreviewagent) och kör det som administratör på servern.
3. **Hämta den senaste versionen av autentiseringsagenten (version 1.5.389.0 eller senare):** Logga in på [Azure Active Directory-administrationscentret](https://aad.portal.azure.com) med klientens globala administratörsautentiseringsuppgifter. Välj **Azure Active Directory -> Azure AD Connect -> Direktautentisering -> Download agent**. Acceptera [användarvillkoren](https://aka.ms/authagenteula) och ladda ned den senaste versionen av autentiseringsagenten. Du kan också ladda ner autentiseringsagenten [härifrån](https://aka.ms/getauthagent).
4. **Installera den senaste versionen av autentiseringsagenten:** Kör den körbara nedladdningen i steg 3. Ange autentiseringsuppgifter för din klients globala administratör när du uppmanas att göra det.
5. **Kontrollera att den senaste versionen har installerats:** Som tidigare visas, gå till **Kontrollpanelen -> Program -> program och funktioner** och kontrollera att det finns en post för "**Microsoft Azure AD Connect Authentication Agent**".

>[!NOTE]
>Om du kontrollerar bladet Direktautentisering i [Administrationscentret](https://aad.portal.azure.com) för Azure Active Directory efter att du har slutfört föregående steg visas två autentiseringsagentposter per server – en post som visar autentiseringsagenten som **aktiv** och den andra som **Inaktiv**. Detta _förväntas_. **Inaktiv** posten tas automatiskt bort efter några dagar.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Uppgradera autentiseringsagenten på andra servrar

Följ dessa steg för att uppgradera autentiseringsagenter på andra servrar (där Azure AD Connect inte är installerat):

1. **Avinstallera förhandsversionen av Authentication Agent:** Hämta [det här PowerShell-skriptet](https://aka.ms/rmpreviewagent) och kör det som administratör på servern.
2. **Hämta den senaste versionen av autentiseringsagenten (version 1.5.389.0 eller senare):** Logga in på [Azure Active Directory-administrationscentret](https://aad.portal.azure.com) med klientens globala administratörsautentiseringsuppgifter. Välj **Azure Active Directory -> Azure AD Connect -> Direktautentisering -> Download agent**. Acceptera användarvillkoren och ladda ner den senaste versionen.
3. **Installera den senaste versionen av autentiseringsagenten:** Kör den körbara nedladdningen i steg 2. Ange autentiseringsuppgifter för din klients globala administratör när du uppmanas att göra det.
4. **Kontrollera att den senaste versionen har installerats:** Som tidigare visas, gå till **Kontrollpanelen -> Program -> Program och funktioner** och kontrollera att det finns en post som heter **Microsoft Azure AD Connect Authentication Agent**.

>[!NOTE]
>Om du kontrollerar bladet Direktautentisering i [Administrationscentret](https://aad.portal.azure.com) för Azure Active Directory efter att du har slutfört föregående steg visas två autentiseringsagentposter per server – en post som visar autentiseringsagenten som **aktiv** och den andra som **Inaktiv**. Detta _förväntas_. **Inaktiv** posten tas automatiskt bort efter några dagar.

## <a name="next-steps"></a>Nästa steg
- [**Felsöka**](tshoot-connect-pass-through-authentication.md) - Lär dig hur du löser vanliga problem med funktionen.
