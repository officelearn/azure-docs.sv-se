---
title: Azure AD-direkt-autentisering - Snabbstart | Microsoft Docs
description: "Den här artikeln beskriver hur du kommer igång med Azure Active Directory (AD Azure) direkt-autentisering."
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: e0b58142a2ed17d2cd4749b33e9e80ff1a01662a
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory direkt-autentisering: Snabbstart

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Hur du distribuerar Azure AD direkt-autentisering

Azure Active Directory (AD Azure) direkt-autentisering kan användarna att logga in på både lokala och molnbaserade program med samma lösenord. Den loggar användarna in genom att verifiera sina lösenord direkt mot din lokala Active Directory.

>[!IMPORTANT]
>Om du har använt funktionen via Förhandsgranska, bör du kontrollera att du uppgraderar förhandsversioner av agenterna för autentisering med hjälp av anvisningarna [här](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Du måste följa de här anvisningarna för att distribuera direkt-autentisering:

## <a name="step-1-check-prerequisites"></a>Steg 1: Kontrollera krav

Se till att följande krav är uppfyllda:

### <a name="on-the-azure-active-directory-admin-center"></a>På Azure Active Directory Administrationscenter

1. Skapa ett globalt administratörskonto endast molnbaserad på Azure AD-klienten. På så sätt kan du hantera konfigurationen för din klient bör lokala tjänster misslyckas eller vara tillgänglig. Lär dig mer om [att lägga till ett globalt administratörskonto endast molnbaserad](../active-directory-users-create-azure-portal.md). Gör det här steget är viktigt att se till att du inte blir utelåst från din klient.
2. Lägg till en eller flera [anpassade domäner eller](../active-directory-domains-add-azure-portal.md) till Azure AD-klienten. Dina användare logga in med ett av dessa domännamn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en server som kör Windows Server 2012 R2 eller senare som ska köras Azure AD Connect. Lägg till servern i samma AD-skog som de användare vars lösenord behöver verifieras.
2. Installera den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) på den server som anges i föregående steg. Om du redan har Azure AD Connect körs, kontrollera att versionen är 1.1.644.0 eller senare.

    >[!NOTE]
    >Azure AD Connect-versioner 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem som rör **synkronisering av lösenords-hash-**. Om du _inte_ avser att använda Lösenordssynkronisering för hash-tillsammans med direkt-autentisering, läsa den [Azure AD Connect viktig information](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) vill veta mer.

3. Identifiera en ytterligare server som kör Windows Server 2012 R2 eller senare som ska köras fristående autentiseringsagent. Autentiseringsagent-version måste vara 1.5.193.0 eller senare. Den här servern behövs för att garantera hög tillgänglighet för inloggningsförfrågningar. Lägg till servern i samma AD-skog som de användare vars lösenord behöver verifieras.
4. Om det finns en brandvägg mellan servrarna och Azure AD, måste du konfigurera följande:
   - Se till att autentisering agenter kan göra **utgående** begäranden till Azure AD via följande portar:
   
   | Portnummer | Hur den används |
   | --- | --- |
   | **80** | Hämta certifikatåterkallning listor över återkallade vid verifiering av SSL-certifikatet |
   | **443** | All utgående kommunikation med vår tjänst |
   
   Om brandväggen tillämpar regler enligt ursprung användare, kan du öppna dessa portar för trafik från Windows-tjänster som körs som Nätverkstjänst.
   - Om din brandvägg eller proxyserver kan DNS-vitlistning, godkända anslutningar till  **\*. msappproxy.net** och  **\*. servicebus.windows.net**. Om inte, Tillåt åtkomst till [Azure DataCenter-IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.
   - Autentisering agenterna behöver åtkomst till **login.windows.net** och **login.microsoftonline.com** för inledande registrering, så att öppna brandväggen för dessa URL: er samt.
   - Valideringen av servercertifikatet avblockera i följande webbadresser: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** och **www.microsoft.com:80**. Dessa URL: er används för certifikatsverifiering med andra Microsoftprodukter, så du kanske redan har webbadresserna avblockerad.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Steg 2: Aktivera stöd för Exchange ActiveSync (valfritt)

Följ instruktionerna för att aktivera stöd för Exchange ActiveSync:

1. Använd [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) att köra följande kommando:
```
Get-OrganizationConfig | fl per*
```

2. Kontrollera värdet för den `PerTenantSwitchToESTSEnabled` inställningen. Om värdet är **SANT**, konfigurerats korrekt för din klient – detta är det vanligtvis för de flesta kunder. Om värdet är **FALSKT**, kör du följande kommando:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Kontrollera att värdet för den `PerTenantSwitchToESTSEnabled` är nu inställningen **SANT**. Vänta en timme innan du fortsätter till nästa steg.

Om du står inför eventuella problem i det här steget kontrollerar vår [felsökningsguide för](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues) för mer information.

## <a name="step-3-enable-the-feature"></a>Steg 3: Aktivera funktionen

Direkt-autentisering kan aktiveras med [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Direkt-autentisering kan aktiveras på Azure AD Connect primär eller fristående server. Vi rekommenderar att du aktiverar den från den primära servern.

Om du installerar Azure AD Connect för första gången, väljer du den [anpassade installationssökvägen](active-directory-aadconnect-get-started-custom.md). På den **användarinloggning** väljer **direkt autentisering** som inloggning. Åtgärden lyckades, är en direkt autentiseringsagent installerat på samma server som Azure AD Connect. Dessutom är funktionen direkt-autentisering aktiverad på din klient.

![Azure AD Connect - användarinloggning](./media/active-directory-aadconnect-sso/sso3.png)

Om du redan har installerat Azure AD Connect (med hjälp av den [Snabbinstallation](active-directory-aadconnect-get-started-express.md) eller [anpassad installation](active-directory-aadconnect-get-started-custom.md) sökväg), Välj **ändra användarens inloggning** aktivitet på Azure AD Ansluta och klicka på **nästa**. Välj sedan **direkt autentisering** som inloggning. En direkt-autentisering-agenten är installerad på samma server som Azure AD Connect på lyckades, och funktionen är aktiverad på din klient.

![Azure AD Connect - ändra användarens inloggning](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Direkt-autentisering är en funktion för klient-nivå. Aktivera den påverkar inloggning för användare i _alla_ hanterade domäner i din klient. Om du byter från AD FS direkt-autentisering, rekommenderar vi att du väntar minst 12 timmar innan du stänger av AD FS-infrastrukturen - väntetiden är att säkerställa att användarna kan hålla logga in på Exchange ActiveSync under övergång.

## <a name="step-4-test-the-feature"></a>Steg 4: Testa funktionen

Följ instruktionerna för att kontrollera att du har aktiverat direkt-autentisering korrekt:

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med Global administratörsbehörighet för din klient.
2. Välj **Azure Active Directory** i navigeringen till vänster.
3. Välj **Azure AD Connect**.
4. Kontrollera att den **direkt autentisering** funktionen visar som **aktiverad**.
5. Välj **direkt autentisering**. Det här bladet visar de servrar där autentisering-agenter är installerade.

![Azure Active Directory Administrationscenter - bladet i Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory Administrationscenter - bladet direkt-autentisering](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

I det här skedet kan användare från alla hanterade domäner i din klient logga in med hjälp av direkt-autentisering. Dock fortsätta användare från externa domäner att logga in med hjälp av Active Directory Federation Services (AD FS) eller en annan federation-provider som du tidigare har konfigurerat. Om du konverterar en domän från federerad som hanteras, starta alla användare från domänen automatiskt logga in med hjälp av direkt-autentisering. Endast molnbaserad användare påverkas inte av funktionen direkt-autentisering.

## <a name="step-5-ensure-high-availability"></a>Steg 5: Garantera hög tillgänglighet

Om du planerar att distribuera direkt autentisering i en produktionsmiljö bör du installera en fristående autentiseringsagent. Installera Agent på den här andra autentisering på en server _andra_ än en körs Azure AD Connect och första autentiseringsagent. Den här inställningen ger du hög tillgänglighet för inloggningsförfrågningar. Följ instruktionerna för att distribuera en fristående autentiseringsagent:

1. **Hämta den senaste versionen av agenten för autentisering (versioner 1.5.193.0 eller senare)**: Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med globala administratörsbehörigheter för din klient.
2. Välj **Azure Active Directory** i navigeringen till vänster.
3. Välj **Azure AD Connect** och sedan **direkt autentisering**. Och välj **Download agent**.
4. Klicka på den **accepterar villkoren och ladda ned** knappen.
5. **Installera den senaste versionen av agenten autentisering**: kör den körbara filen som hämtades i föregående steg. Ange din klient Global administratör autentiseringsuppgifter när du tillfrågas.

![Azure Active Directory Administrationscenter - knappen ladda ned Agent för autentisering](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure Active Directory Administrationscenter – hämta Agent-bladet](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Du kan också hämta autentiseringsagent från [här](https://aka.ms/getauthagent). Se till att du granskar och godkänner den autentiseringsagent [användarvillkoren](https://aka.ms/authagenteula) _innan_ installera den.

## <a name="next-steps"></a>Nästa steg
- [**Smartkort kontoutelåsning** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -konfigurerar Smart kontoutelåsning kapaciteten på din klient för att skydda användarkonton.
- [**Aktuella begränsningar** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – Lär dig vilka scenarier som stöds och vilka som inte är.
- [**Tekniska ingående** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -förstå hur funktionen fungerar.
- [**Vanliga frågor och svar** ](active-directory-aadconnect-pass-through-authentication-faq.md) -svar på vanliga frågor och svar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
- [**Säkerhet ingående** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -ytterligare djupt teknisk information om funktionen.
- [**Azure AD sömlös SSO** ](active-directory-aadconnect-sso.md) -mer information om den här funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
