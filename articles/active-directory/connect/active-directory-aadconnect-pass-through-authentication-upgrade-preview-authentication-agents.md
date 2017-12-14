---
title: "Azure AD Connect: Direkt autentisering - uppgradera förhandsversionen autentisering agenter | Microsoft Docs"
description: "Den här artikeln beskriver hur du uppgraderar konfigurationen av Azure Active Directory (AD Azure) direkt-autentisering."
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: billmath
ms.openlocfilehash: 96d4e8e240d845be5f04bf0e724d91d740ad9cdf
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory direkt-autentisering: Uppgradera förhandsversionen autentisering agenter

## <a name="overview"></a>Översikt

Den här artikeln är för kunder som använder Azure AD direkt autentisering via preview. Vi nyligen uppgraderat (och byta namn) autentiseringsagent programvaran. Du behöver _manuellt_ uppgradera förhandsversionen autentisering agenter som installerats på lokala servrar. Den här manuell uppgradering är en engångsåtgärd endast. Alla framtida uppdateringar för autentisering agenter är automatisk. Skäl att uppgradera finns på följande sätt:

- Förhandsversioner av autentisering agenter får inte några ytterligare säkerhet eller felkorrigeringar.
-   Förhandsversioner av autentisering agenter kan inte installeras på flera servrar för hög tillgänglighet.

## <a name="check-versions-of-your-authentication-agents"></a>Kontrollera versioner av dina agenter för autentisering

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Steg 1: Kontrollera om autentisering-agenter är installerade

Följ dessa steg om du vill kontrollera om autentisering-agenter är installerade:

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med Global administratörsbehörighet för din klient.
2. Välj **Azure Active Directory** i navigeringen till vänster.
3. Välj **Azure AD Connect**. 
4. Välj **direkt autentisering**. Det här bladet visar de servrar där autentisering-agenter är installerade.

![Azure Active Directory Administrationscenter - bladet direkt-autentisering](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Steg 2: Kontrollera vilka versioner av dina agenter för autentisering

Följ dessa instruktioner för att kontrollera vilka versioner av dina agenter för autentisering, på varje server som anges i föregående steg:

1. Gå till **Kontrollpanelen -> program -> program och funktioner** på den lokala servern.
2. Om det finns en post för ”**ansluta autentiseringsagent för Microsoft Azure AD**”, du behöver inte vidta några åtgärder på den här servern.
3. Om det finns en post för ”**Microsoft Azure AD Application Proxy Connector**”, version 1.5.132.0 eller tidigare, måste du uppgradera manuellt på den här servern.

![Förhandsversionen av autentiseringsagent](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Bästa praxis att följa innan du startar uppgraderingen

Se till att du har följande funktioner på plats innan du uppgraderar:

1. **Skapa endast molnbaserad globala administratörskonto**: inte uppgraderar utan att behöva en endast molnbaserad globala administratörskonto för användning i nödfall där direkt autentisering agenterna inte fungerar korrekt. Lär dig mer om [att lägga till ett globalt administratörskonto endast molnbaserad](../active-directory-users-create-azure-portal.md). Gör det här steget är mycket viktigt och garanterar att du inte blir utelåst från din klient.
2.  **Säkerställa hög tillgänglighet**: Om du inte har slutförts ännu, installera en andra fristående autentiseringsagent att tillhandahålla hög tillgänglighet för inloggning begäranden med hjälp av dessa [instruktioner](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Uppgradera agenten autentisering på din Azure AD Connect-server

Du måste uppgradera Azure AD Connect innan du uppgraderar autentiseringsagent på samma server. Följ dessa steg på din primära och mellanlagring av Azure AD Connect-servrar:

1. **Uppgradera Azure AD Connect**: följer detta [artikel](./active-directory-aadconnect-upgrade-previous-version.md) och uppgradera till den senaste versionen av Azure AD Connect.
2. **Avinstallera förhandsversionen av den autentiseringsagent**: hämta [detta PowerShell-skript](https://aka.ms/rmpreviewagent) och köra den som en administratör på servern.
3. **Hämta den senaste versionen av agenten för autentisering (versioner 1.5.193.0 eller senare)**: Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med globala administratörsbehörigheter för din klient. Välj **Azure Active Directory -> Azure AD Connect -> direkt autentisering hämta agent ->**. Acceptera den [användarvillkoren](https://aka.ms/authagenteula) och ladda ned den senaste versionen av agenten för autentisering. Du kan också hämta autentiseringsagent från [här](https://aka.ms/getauthagent).
4. **Installera den senaste versionen av agenten autentisering**: kör den körbara filen som hämtades i steg3. Ange din klient Global administratör autentiseringsuppgifter när du tillfrågas.
5. **Kontrollera att den senaste versionen har installerats**: enligt beskrivningen tidigare kan du gå till **Kontrollpanelen -> program -> program och funktioner** och kontrollera att det finns en post för ”**ansluta autentiseringsagent för Microsoft Azure AD**”.

>[!NOTE]
>Om du markerar bladet direkt-autentisering på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) när slutfört föregående steg visas två autentiseringsagent poster per server – en post som visar autentiseringsagent som **Active** och andra som **inaktiv**. Detta är _förväntade_. Den **inaktiv** post automatiskt bort efter några dagar.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Uppgradera autentiseringsagent på andra servrar

Följ dessa steg om du vill uppgradera agenter för autentisering på andra servrar (där Azure AD Connect är inte installerat):

1. **Avinstallera förhandsversionen av den autentiseringsagent**: hämta [detta PowerShell-skript](https://aka.ms/rmpreviewagent) och köra den som en administratör på servern.
2. **Hämta den senaste versionen av agenten för autentisering (versioner 1.5.193.0 eller senare)**: Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med globala administratörsbehörigheter för din klient. Välj **Azure Active Directory -> Azure AD Connect -> direkt autentisering hämta agent ->**. Acceptera villkoren och hämta den senaste versionen.
3. **Installera den senaste versionen av agenten autentisering**: kör den körbara filen som hämtades i steg 2. Ange din klient Global administratör autentiseringsuppgifter när du tillfrågas.
4. **Kontrollera att den senaste versionen har installerats**: enligt beskrivningen tidigare kan du gå till **Kontrollpanelen -> program -> program och funktioner** och kontrollera att det finns en post med namnet **ansluta autentiseringsagent för Microsoft Azure AD**.

>[!NOTE]
>Om du markerar bladet direkt-autentisering på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) när slutfört föregående steg visas två autentiseringsagent poster per server – en post som visar autentiseringsagent som **Active** och andra som **inaktiv**. Detta är _förväntade_. Den **inaktiv** post automatiskt bort efter några dagar.

## <a name="next-steps"></a>Nästa steg
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
