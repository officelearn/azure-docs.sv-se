---
title: Azure AD-direktautentisering - Snabbstart | Microsoft Docs
description: Den här artikeln beskriver hur du kommer igång med Azure Active Directory (Azure AD)-direktautentisering.
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
ms.date: 07/26/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1b5640b790b07050336a990a06b66e5f89fcf768
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308617"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Azure Active Directory-direktautentisering: Snabbstart för

## <a name="deploy-azure-ad-pass-through-authentication"></a>Distribuera Azure AD-direktautentisering

Azure Active Directory (Azure AD)-direktautentisering kan användarna att logga in på både lokala och molnbaserade program genom att använda samma lösenord. Direktautentisering loggar användarna in genom att verifiera sina lösenord direkt mot den lokala Active Directory.

>[!IMPORTANT]
>Om du migrerar från AD FS (eller andra tekniker för federation) till direktautentisering, rekommenderar vi att du följer våra detaljerad Distributionsguide publicerade [här](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx).

Följ dessa instruktioner för att distribuera direktautentisering på din klient:

## <a name="step-1-check-the-prerequisites"></a>Steg 1: Kontrollera krav

Se till att följande krav är uppfyllda.

### <a name="in-the-azure-active-directory-admin-center"></a>I Azure Active Directory Administrationscenter

1. Skapa ett molnbaserad globalt administratörskonto på Azure AD-klienten. På så sätt kan du hantera konfigurationen av din klient bör din lokala tjänster misslyckas eller inte tillgänglig. Lär dig mer om [att lägga till en molnbaserad globala administratörskonto](../active-directory-users-create-azure-portal.md). Du har slutfört det här steget är viktigt att se till att du inte blir utelåst från klientorganisationen.
2. Lägg till en eller flera [anpassade domännamn](../active-directory-domains-add-azure-portal.md) till Azure AD-klienten. Användarna kan logga in med något av dessa domännamn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en server som kör Windows Server 2012 R2 eller senare för att köra Azure AD Connect. Lägg till servern i samma Active Directory-skog som användare vars lösenord du måste verifiera.
2. Installera den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) på den server som anges i föregående steg. Om du redan har Azure AD Connect körs kan du kontrollera att versionen är 1.1.750.0 eller senare.

    >[!NOTE]
    >Azure AD Connect-versioner 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem med synkronisering av lösenordshash. Om du _inte_ planerar att använda synkronisering av lösenordshash tillsammans med direktautentisering, läsa den [viktig information om Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Identifiera en eller flera ytterligare servrar (som kör Windows Server 2012 R2 eller senare) där du kan köra fristående Autentiseringsagenter. Dessa ytterligare servrar behövs för att garantera hög tillgänglighet för begäranden för att logga in. Lägg till servrar i samma Active Directory-skog som användare vars lösenord du måste verifiera.

    >[!IMPORTANT]
    >I produktionsmiljöer rekommenderar vi att du har minst 3 Autentiseringsagenter som körs på din klient. Det finns en systemgränsen på 12 Autentiseringsagenter per klient. Och bästa praxis är att behandla alla servrar som kör Autentiseringsagenter som nivå 0-system (se [referens](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Om det finns en brandvägg mellan dina servrar och Azure AD måste du konfigurera följande objekt:
   - Se till att Autentiseringsagenter kan göra *utgående* begäranden till Azure AD via följande portar:
   
    | Portnummer | Hur den används |
    | --- | --- |
    | **80** | Hämtar listor över återkallade certifikat (CRL) vid verifiering av SSL-certifikatet |
    | **443** | Hanterar all utgående kommunikation med tjänsten |
   
    Om din brandvägg tillämpar regler enligt ursprungliga användarna, kan du öppna dessa portar för trafik från Windows-tjänster som körs som en nätverkstjänst.
   - Om din brandvägg eller proxyserver kan DNS-lista över tillåtna, lista över tillåtna anslutningar till  **\*. msappproxy.net** och  **\*. servicebus.windows.net**. Om den inte tillåter åtkomst till den [Azure datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.
   - Din Autentiseringsagenter behöver åtkomst till **login.windows.net** och **login.microsoftonline.com** för inledande registrering. Öppna din brandvägg för dessa URL: er samt.
   - Certifikatsverifiering, avblockera i följande webbadresser: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, och  **www.microsoft.com:80**. Eftersom dessa URL: er används för certifikatsverifiering med andra Microsoft-produkter som du kan redan ha dessa URL: er avblockerad.

## <a name="step-2-enable-the-feature"></a>Steg 2: Aktivera funktionen

Aktivera direktautentisering via [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>Du kan aktivera direktautentisering på Azure AD Connect primär eller fristående server. Vi rekommenderar starkt att du aktiverar det från den primära servern. Om du ställer in en Azure AD Connect-mellanlagringsserver i framtiden kommer du **måste** Fortsätt med att välja direktautentisering som alternativet inloggning, välja ett annat alternativ kommer **inaktivera** Direktautentisering på klient- och åsidosättning inställningen i den primära servern.

Om du installerar Azure AD Connect för första gången, väljer du den [anpassade installationssökväg](active-directory-aadconnect-get-started-custom.md). På den **användarinloggning** väljer **direktautentisering** som den **inloggningsmetod**. Åtgärden lyckades, är en Autentiseringsagenten för direktautentisering installerad på samma server som Azure AD Connect. Dessutom är funktionen direktautentisering aktiverad på klienten.

![Azure AD Connect: Användarinloggning](./media/active-directory-aadconnect-sso/sso3.png)

Om du redan har installerat Azure AD Connect med hjälp av den [Snabbinstallation](active-directory-aadconnect-get-started-express.md) eller [anpassad installation](active-directory-aadconnect-get-started-custom.md) sökvägen markerar den **ändra användarinloggning** uppgiften på Azure AD Ansluta och väljer **nästa**. Välj sedan **direktautentisering** som metod för inloggning. När åtgärden har slutförts, en Autentiseringsagenten för direktautentisering är installerad på samma server som Azure AD Connect och funktionen är aktiverad på klienten.

![Azure AD Connect: Ändra användarinloggning](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>Direktautentisering är en funktion på klientnivå. Att aktivera den påverkar inloggning för användare i _alla_ de hanterade domänerna i din klient. Om du växlar från Active Directory Federation Services (AD FS) till direktautentisering, bör du vänta minst 12 timmar innan du stänger av AD FS-infrastrukturen. Den här väntetid är att säkerställa att användarna kan hålla inloggning till Exchange ActiveSync under övergången. Kolla in vår detaljerad Distributionsguide som publicerats för mer hjälp om hur du migrerar från AD FS till direktautentisering [här](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx).

## <a name="step-3-test-the-feature"></a>Steg 3: Testa funktionen

Följ dessa instruktioner för att kontrollera att du har aktiverat direktautentisering korrekt:

1. Logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med autentiseringsuppgifterna för global administratör för din klient.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**.
4. Kontrollera att den **direktautentisering** funktionen visas som **aktiverad**.
5. Välj **direktautentisering**. Den **direktautentisering** rutan visas de servrar där autentisering-agenter är installerade.

![Azure Active Directory Administrationscenter: Azure AD Connect-fönstret](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Azure Active Directory Administrationscenter: direktautentisering fönstret](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

I det här skedet kan användare från de hanterade domänerna i din klient logga in med hjälp av direktautentisering. Användare från federerade domäner fortsätter dock att logga in med hjälp av AD FS eller en annan federationsleverantör som du tidigare har konfigurerat. Om du konverterar en domän från federerad som hanteras, börjar alla användare från domänen automatiskt loggar in med hjälp av direktautentisering. Funktionen direktautentisering påverkar inte molnexklusiva användare.

## <a name="step-4-ensure-high-availability"></a>Steg 4: Garantera hög tillgänglighet

Om du planerar att distribuera direktautentisering i en produktionsmiljö bör du installera ytterligare fristående Autentiseringsagenter. Installera de här autentiseringsagenter på servrarna _andra_ än en aktiva Azure AD Connect. Den här konfigurationen ger hög tillgänglighet för begäranden för användare logga in.

>[!IMPORTANT]
>I produktionsmiljöer rekommenderar vi att du har minst 3 Autentiseringsagenter som körs på din klient. Det finns en systemgränsen på 12 Autentiseringsagenter per klient. Och bästa praxis är att behandla alla servrar som kör Autentiseringsagenter som nivå 0-system (se [referens](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Följ dessa instruktioner för att hämta programvaran för autentiseringsagent:

1. Ladda ned den senaste versionen av Autentiseringsagenten (version 1.5.193.0 eller senare), logga in på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) med global administratörsbehörighet för din klient.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**väljer **direktautentisering**, och välj sedan **ladda ned Agent**.
4. Välj den **acceptera villkoren och hämta** knappen.

![Azure Active Directory Administrationscenter: knappen ladda ned Agent för autentisering](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Azure Active Directory Administrationscenter: ladda ned Agent-fönstret](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Du kan också direkt [ladda ned programvaran Autentiseringsagenten](https://aka.ms/getauthagent). Granska och Godkänn den Autentiseringsagenten [användningsvillkoren](https://aka.ms/authagenteula) _innan_ installerar den.

Det finns två sätt att distribuera en fristående autentiseringsagent:

Först måste kan du göra det interaktivt genom att bara köra den hämtade körbara autentiseringsagent och att tillhandahålla autentiseringsuppgifterna för din klient global administratör när du uppmanas.

Dessutom kan du skapa och köra ett distributionsskript för obevakad. Detta är användbart när du vill distribuera flera Autentiseringsagenter samtidigt eller installera Autentiseringsagenter på Windows-servrar som inte har aktiverat användargränssnittet eller som du inte kommer åt via ett fjärrskrivbord. Här följer instruktioner om hur du använder den här metoden:

1. Kör följande kommando för att installera en Agent för autentisering: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. Du kan registrera agenten autentisering med vår tjänst med hjälp av Windows PowerShell. Skapa ett PowerShell-autentiseringsuppgifter objekt `$cred` som innehåller en global administratörsanvändarnamn och lösenord för din klient. Kör följande kommando ersätter *\<användarnamn\>* och  *\<lösenord\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Gå till **C:\Program Files\Microsoft Azure AD Connect-Autentiseringsagenten** och kör följande skript med den `$cred` objektet som du skapade:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

## <a name="next-steps"></a>Nästa steg
- [Migrera från AD FS till direktautentisering](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx) -en detaljerad vägledning för att migrera från AD FS (eller andra tekniker för federation) till direktautentisering.
- [Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md): Lär dig hur du konfigurerar funktionen för smarta kontoutelåsning på din klient för att skydda användarkonton.
- [Aktuella begränsningar](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Läs mer om vilka scenarier som stöds med den direktautentisering och vilka som inte är.
- [Teknisk djupdykning](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Förstå hur funktionen direktautentisering fungerar.
- [Vanliga frågor och svar](active-directory-aadconnect-pass-through-authentication-faq.md): få svar på vanliga frågor och svar.
- [Felsöka](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direktautentisering.
- [Djupgående om säkerhet](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): få teknisk information om funktionen direktautentisering.
- [Azure AD sömlös SSO](active-directory-aadconnect-sso.md): Mer information om den här tilläggsfunktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktionbegäran.

