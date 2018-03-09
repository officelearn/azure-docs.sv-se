---
title: Azure AD direkt-autentisering - Snabbstart | Microsoft Docs
description: "Den här artikeln beskriver hur du kommer igång med Azure Active Directory (AD Azure) direkt-autentisering."
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
ms.date: 03/07/2018
ms.author: billmath
ms.openlocfilehash: b592eb8ca43e5bf3eebe2b0c47d8f17dbec7b238
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory direkt-autentisering: Snabbstart

## <a name="deploy-azure-ad-pass-through-authentication"></a>Distribuera Azure AD-direkt-autentisering

Azure Active Directory (AD Azure) direkt-autentisering kan användarna att logga in på både lokala och molnbaserade program genom att använda samma lösenord. Direkt-autentisering loggar användarna in genom att verifiera sina lösenord direkt mot lokala Active Directory.

>[!IMPORTANT]
>Om du använder den här funktionen via en förhandsversion, kontrollera att du uppgraderar förhandsversioner av agenterna för autentisering med hjälp av instruktionerna i [Azure Active Directory direkt-autentisering: uppgradera förhandsversionen Autentisering agenter](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Följ instruktionerna för att distribuera direkt-autentisering:

## <a name="step-1-check-the-prerequisites"></a>Steg 1: Kontrollera krav

Se till att följande krav är uppfyllda.

### <a name="in-the-azure-active-directory-admin-center"></a>I Azure Active Directory Administrationscenter

1. Skapa en endast molnbaserad globala administratörskonto på Azure AD-klienten. På så sätt kan du hantera konfigurationen för din klient bör lokala tjänster misslyckas eller vara tillgänglig. Lär dig mer om [att lägga till en molnbaserad globala administratörskonto](../active-directory-users-create-azure-portal.md). Slutför det här steget är viktigt att se till att du inte blir utelåst från din klient.
2. Lägg till en eller flera [anpassade domännamn](../active-directory-domains-add-azure-portal.md) till Azure AD-klienten. Användarna kan logga in med något av dessa domännamn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en server som kör Windows Server 2012 R2 eller senare för att köra Azure AD Connect. Lägga till servern i samma Active Directory-skog som de användare vars lösenord du måste validera.
2. Installera den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) på den server som anges i föregående steg. Om du redan har Azure AD Connect körs, kontrollera att versionen är 1.1.644.0 eller senare.

    >[!NOTE]
    >Azure AD Connect-versioner 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem med synkronisering av lösenords-hash. Om du _inte_ avser att använda Lösenordssynkronisering för hash-tillsammans med direkt-autentisering, läsa den [Azure AD Connect viktig information](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Identifiera ytterligare en server (kör Windows Server 2012 R2 eller senare) där du kan köra en fristående autentiseringsagent. Autentiseringsagent-version måste vara 1.5.193.0 eller senare. Den här ytterligare server behövs för att garantera hög tillgänglighet för begäranden att logga in. Lägga till servern i samma Active Directory-skog som de användare vars lösenord du måste validera.
4. Om det finns en brandvägg mellan servrarna och Azure AD, konfigurerar du följande objekt:
   - Se till att autentisering agenter kan göra *utgående* begäranden till Azure AD via följande portar:
   
    | Portnummer | Hur den används |
    | --- | --- |
    | **80** | Hämtar de listor över återkallade certifikat (CRL) vid verifiering av SSL-certifikatet |
    | **443** | Hanterar all utgående kommunikation med tjänsten |
   
    Om brandväggen tillämpar regler enligt ursprungliga användarna, öppna dessa portar för trafik från Windows-tjänster som körs som Nätverkstjänst.
   - Om din brandvägg eller proxyserver kan DNS-vitlistning, godkända anslutningar till  **\*. msappproxy.net** och  **\*. servicebus.windows.net**. Om inte, Tillåt åtkomst till den [IP-adressintervall för Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.
   - Autentisering agenterna behöver åtkomst till **login.windows.net** och **login.microsoftonline.com** för registreringen. Öppna brandväggen för dessa URL: er samt.
   - Valideringen av servercertifikatet avblockera i följande webbadresser: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, och  **www.microsoft.com:80**. Dessa URL: er används för certifikatsverifiering med andra Microsoft-produkter. Du kanske redan webbadresserna avblockerad.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Steg 2: Aktivera stöd för Exchange ActiveSync (valfritt)

Följ instruktionerna för att aktivera stöd för Exchange ActiveSync:

1. Använd [Exchange PowerShell](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) att köra följande kommando:
```
Get-OrganizationConfig | fl per*
```

2. Kontrollera värdet för den `PerTenantSwitchToESTSEnabled` inställningen. Om värdet är **SANT**, din klient är korrekt konfigurerad. Detta gäller vanligtvis för de flesta kunder. Om värdet är **FALSKT**, kör du följande kommando:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Kontrollera att värdet för den `PerTenantSwitchToESTSEnabled` är nu inställningen **SANT**. Vänta en timme innan du går vidare till nästa steg.

Om du står inför eventuella problem i det här steget kontrollerar du den [felsökningsguide för](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>Steg 3: Aktivera funktionen

Aktivera direkt-autentisering via [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Du kan aktivera direkt-autentisering på Azure AD Connect primär eller fristående server. Du bör aktivera det från den primära servern.

Om du installerar Azure AD Connect för första gången, väljer du den [anpassade installationssökvägen](active-directory-aadconnect-get-started-custom.md). På den **användarinloggning** väljer **direkt autentisering** som den **metod inloggning**. Åtgärden lyckades, är en direkt autentiseringsagent installerat på samma server som Azure AD Connect. Dessutom är funktionen direkt-autentisering aktiverad på din klient.

![Azure AD Connect: Användarens inloggning](./media/active-directory-aadconnect-sso/sso3.png)

Om du redan har installerat Azure AD Connect med hjälp av den [Snabbinstallation](active-directory-aadconnect-get-started-express.md) eller [anpassad installation](active-directory-aadconnect-get-started-custom.md) sökvägen markerar den **ändra användarens inloggning** aktivitet på Azure AD Ansluta och välj sedan **nästa**. Välj sedan **direkt autentisering** som metod för inloggning. En direkt autentisering agenten är installerad på samma server som Azure AD Connect på lyckades, och funktionen är aktiverad på din klient.

![Azure AD Connect: Ändra användarens inloggning](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Direkt-autentisering är en funktion för klient-nivå. Aktivera den påverkar inloggning för användare i _alla_ hanterade domäner i din klient. Om du växlar från Active Directory Federation Services (AD FS) direkt-autentisering, bör du vänta minst 12 timmar innan du stänger av AD FS-infrastrukturen. Väntetiden är att säkerställa att användarna kan hålla logga in på Exchange ActiveSync under övergången.

## <a name="step-4-test-the-feature"></a>Steg 4: Testa funktionen

Följ instruktionerna för att kontrollera att du har aktiverat direkt-autentisering korrekt:

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med global administratörsbehörighet för din klient.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**.
4. Kontrollera att den **direkt autentisering** funktionen visas som **aktiverad**.
5. Välj **direkt autentisering**. Den **direkt autentisering** rutan visas de servrar där autentisering-agenter är installerade.

![Azure Active Directory Administrationscenter: Azure AD Connect-fönstret](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory Administrationscenter: fönstret direkt-autentisering](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

I det här skedet kan användare från alla hanterade domäner i din klient logga in med hjälp av direkt-autentisering. Dock fortsätta användare från externa domäner att logga in med hjälp av AD FS eller en annan federation-provider som du tidigare har konfigurerat. Om du konverterar en domän från federerad som hanteras, starta alla användare från domänen automatiskt logga in med hjälp av direkt-autentisering. Endast molnbaserad användare påverkas inte om funktionen direkt-autentisering.

## <a name="step-5-ensure-high-availability"></a>Steg 5: Garantera hög tillgänglighet

Om du planerar att distribuera direkt autentisering i en produktionsmiljö bör du installera minst en mer fristående autentiseringsagent. Installerar dessa autentisering-agenter på servrar _andra_ än en körs Azure AD Connect. Den här inställningen ger hög tillgänglighet för inloggning användarförfrågningar.

Följ dessa instruktioner för att hämta autentiseringsagent programvaran:

1. Hämta den senaste versionen av agenten för autentisering (version 1.5.193.0 eller senare), logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med globala administratörsbehörigheter för din klient.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**väljer **direkt autentisering**, och välj sedan **hämta Agent**.
4. Välj den **accepterar villkoren och ladda ned** knappen.

![Azure Active Directory Administrationscenter: hämta autentiseringsagent knappen](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure Active Directory Administrationscenter: hämta Agent-fönstret](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Du kan också direkt hämta programmet autentiseringsagent [här](https://aka.ms/getauthagent). Granska och Godkänn den autentiseringsagent [användarvillkoren](https://aka.ms/authagenteula) _innan_ installera den.

Det finns två sätt att distribuera en fristående autentiseringsagent:

Först måste kan du göra det interaktivt genom att bara köra den hämtade autentiseringsagent körbara och anger autentiseringsuppgifter global administratör för din klient när du uppmanas.

Därefter kan du skapa och köra ett distributionsskript för obevakad. Detta är användbart när du vill distribuera flera autentisering agenter samtidigt eller installera agenter för autentisering på Windows-servrar som inte har aktiverat användargränssnittet eller som du kan inte komma åt med fjärrskrivbord. Här följer instruktioner om hur du använder den här metoden:

1. Kör följande kommando för att installera en Agent för autentisering: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Du kan registrera agenten autentisering med vår tjänst med hjälp av Windows PowerShell. Skapa ett PowerShell-autentiseringsuppgifter objekt `$cred` som innehåller en global administratörsanvändarnamn och lösenord för din klient. Kör följande kommando ersätter  *\<användarnamn\>*  och  *\<lösenord\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Gå till **C:\Program Files\Microsoft Azure AD Connect autentiseringsagent** och kör följande skript med den `$cred` objekt som du skapat:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>Nästa steg
- [Smartkort kontoutelåsning](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Lär dig hur du konfigurerar Smart kontoutelåsning kapaciteten på din klient skydda användarkonton.
- [Aktuella begränsningar](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Lär dig vilka scenarier som stöds med direkt-autentisering och vilka som inte är.
- [Tekniska ingående](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Förstå hur funktionen direkt autentisering fungerar.
- [Vanliga frågor och svar](active-directory-aadconnect-pass-through-authentication-faq.md): få svar på vanliga frågor och svar.
- [Felsöka](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direkt-autentisering.
- [Säkerhet ingående](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): Hämta teknisk information om funktionen direkt-autentisering.
- [Azure AD sömlös SSO](active-directory-aadconnect-sso.md): Lär dig mer om den här funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktioner som efterfrågas.

