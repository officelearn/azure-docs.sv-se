---
title: Azure AD Connect - direktautentisering - uppgradering auth agenter | Microsoft Docs
description: Den här artikeln beskrivs hur du uppgraderar konfigurationen av direktautentisering för Azure Active Directory (AD Azure).
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: be76965e99a20c1f7164187255e26f6463926c2f
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214735"
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Azure Active Directory-direktautentisering: Uppgradera förhandsversionen Autentiseringsagenter

## <a name="overview"></a>Översikt

Den här artikeln är avsedd för kunder med hjälp av Azure AD-direktautentisering via förhandsversion. Vi nyligen uppgraderat (och nya namn som visar) autentiseringsagent programvaran. Du behöver _manuellt_ uppgradera förhandsversioner av Autentiseringsagenter installerad på dina lokala servrar. Den här manuell uppgradering är en engångsåtgärd endast. Alla framtida uppdateringar av Autentiseringsagenter sker automatiskt. Varför du bör uppgradera är följande:

- Förhandsversioner av Autentiseringsagenter får inte några ytterligare säkerhet eller felkorrigeringar.
-   Förhandsversioner av Autentiseringsagenter kan inte installeras på flera servrar för hög tillgänglighet.

## <a name="check-versions-of-your-authentication-agents"></a>Kontrollera versioner av dina Autentiseringsagenter

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Steg 1: Kontrollera där autentisering-agenter är installerade

Följ dessa steg om du vill kontrollera där autentisering-agenter är installerade:

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med autentiseringsuppgifterna för Global administratör för din klient.
2. Välj **Azure Active Directory** i det vänstra navigeringsfönstret.
3. Välj **Azure AD Connect**. 
4. Välj **direktautentisering**. Det här bladet visar de servrar där autentisering-agenter är installerade.

![Azure Active Directory Administrationscenter - direktautentisering bladet](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Steg 2: Kontrollera vilka versioner av dina Autentiseringsagenter

Följ de här instruktionerna för att kontrollera vilka versioner av dina Autentiseringsagenter på varje server som identifierades i föregående steg:

1. Gå till **Kontrollpanelen -> program -> program och funktioner** på den lokala servern.
2. Om det finns en post för ”**Microsoft Azure AD Connect-Autentiseringsagenten**”, du behöver inte vidta några åtgärder för den här servern.
3. Om det finns en post för ”**Microsoft Azure AD Application Proxy Connector**”, måste du manuellt uppgradera på den här servern.

![Förhandsversionen av Autentiseringsagenten](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Metodtips att följa innan du startar uppgraderingen

Kontrollera att du har följande objekt på plats innan du uppgraderar:

1. **Skapa molnbaserad globala administratörskonto**: inte uppgradera utan en endast molnbaserad Global administratör-konto du använder i nödfall situationer där direkt autentisering agenterna inte fungerar korrekt. Lär dig mer om [att lägga till ett globalt administratörskonto molnbaserad](../active-directory-users-create-azure-portal.md). Utför det här steget är mycket viktigt och ser till att du inte blir utelåst från klientorganisationen.
2.  **Garantera hög tillgänglighet**: Om du inte slutföra tidigare, installera en andra fristående autentiseringsagent att tillhandahålla hög tillgänglighet för inloggningsförfrågningar, med hjälp av dessa [instruktioner](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Uppgradera agenten autentisering på din Azure AD Connect-server

Du måste uppgradera Azure AD Connect innan du uppgraderar Autentiseringsagenten på samma server. Följ dessa steg på både den primära servern och mellanlagring den Azure AD Connect-servrar:

1. **Uppgradera Azure AD Connect**: Följ den här [artikeln](./active-directory-aadconnect-upgrade-previous-version.md) och uppgradera till den senaste versionen av Azure AD Connect.
2. **Avinstallera förhandsversionen av Autentiseringsagenten**: hämta [den här PowerShell.skript](https://aka.ms/rmpreviewagent) och köra den som en administratör på servern.
3. **Hämta den senaste versionen av Autentiseringsagenten (versioner 1.5.193.0 eller senare)**: Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med Global administratörsbehörighet för din klient. Välj **Azure Active Directory -> Azure AD Connect -> direktautentisering -> ladda ned agenten**. Acceptera den [användarvillkoren](https://aka.ms/authagenteula) och hämta den senaste versionen av Autentiseringsagenten. Du kan också hämta Autentiseringsagenten från [här](https://aka.ms/getauthagent).
4. **Installera den senaste versionen av Autentiseringsagenten**: kör den körbara filen som hämtades i steg3. Ange autentiseringsuppgifterna för din klient Global administratör när du tillfrågas.
5. **Kontrollera att den senaste versionen har installerats**: enligt beskrivningen tidigare kan du gå till **Kontrollpanelen -> program -> program och funktioner** och kontrollera att det finns en post för ”**Microsoft Azure AD Connect Autentiseringsagenten**”.

>[!NOTE]
>Om du markerar bladet direktautentisering på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) när du har slutfört föregående steg, visas två autentiseringsagent poster per server – en post som visar autentiseringen Agent som **Active** och den andra as **inaktiva**. Det här är _förväntat_. Den **inaktiv** post tas automatiskt bort efter några dagar.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Uppgradera Autentiseringsagenten på andra servrar

Följ dessa steg om du vill uppgradera Autentiseringsagenter på andra servrar (där Azure AD Connect är inte installerat):

1. **Avinstallera förhandsversionen av Autentiseringsagenten**: hämta [den här PowerShell.skript](https://aka.ms/rmpreviewagent) och köra den som en administratör på servern.
2. **Hämta den senaste versionen av Autentiseringsagenten (versioner 1.5.193.0 eller senare)**: Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med Global administratörsbehörighet för din klient. Välj **Azure Active Directory -> Azure AD Connect -> direktautentisering -> ladda ned agenten**. Godkänn villkoren och hämta den senaste versionen.
3. **Installera den senaste versionen av Autentiseringsagenten**: kör den körbara filen som hämtades i steg 2. Ange autentiseringsuppgifterna för din klient Global administratör när du tillfrågas.
4. **Kontrollera att den senaste versionen har installerats**: enligt beskrivningen tidigare kan du gå till **Kontrollpanelen -> program -> program och funktioner** och kontrollera att det finns en post med namnet **Microsoft Azure AD Connect Autentiseringsagenten**.

>[!NOTE]
>Om du markerar bladet direktautentisering på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) när du har slutfört föregående steg, visas två autentiseringsagent poster per server – en post som visar autentiseringen Agent som **Active** och den andra as **inaktiva**. Det här är _förväntat_. Den **inaktiv** post tas automatiskt bort efter några dagar.

## <a name="next-steps"></a>Nästa steg
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
