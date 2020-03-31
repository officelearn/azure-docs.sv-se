---
title: Azure AD-direktautentisering – snabbstart | Microsoft-dokument
description: I den här artikeln beskrivs hur du kommer igång med Azure Active Directory (Azure AD) Direktautentisering.
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
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc45033cdf1bdaa6d4ecd6ab58cc7f90ff9c1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331417"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Direktautentisering i Azure Active Directory: Snabbstart

## <a name="deploy-azure-ad-pass-through-authentication"></a>Distribuera Azure AD-direktautentisering

Azure Active Directory (Azure AD) Direktautentisering gör att användarna kan logga in på både lokala och molnbaserade program med samma lösenord. Direktautentisering signerar användare genom att validera deras lösenord direkt mot lokala Active Directory.

>[!IMPORTANT]
>Om du migrerar från AD FS (eller annan federationsteknik) till Direktautentisering rekommenderar vi starkt att du följer vår detaljerade distributionsguide som publiceras [här.](https://aka.ms/adfstoPTADPDownload)

Följ dessa instruktioner för att distribuera direktautentisering på din klient:

## <a name="step-1-check-the-prerequisites"></a>Steg 1: Kontrollera förutsättningarna

Se till att följande förutsättningar är på plats.

### <a name="in-the-azure-active-directory-admin-center"></a>I administrationscentret för Azure Active Directory

1. Skapa ett globalt administratörskonto endast för molnet på din Azure AD-klientorganisation. På så sätt kan du hantera konfigurationen av din klient om dina lokala tjänster misslyckas eller blir otillgängliga. Läs mer om hur du [lägger till ett globalt administratörskonto för molnet](../active-directory-users-create-azure-portal.md). Att slutföra det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
2. Lägg till ett eller flera [anpassade domännamn](../active-directory-domains-add-azure-portal.md) i din Azure AD-klientorganisation. Användarna kan logga in med ett av dessa domännamn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en server som kör Windows Server 2012 R2 eller senare för att köra Azure AD Connect. Om den inte redan är aktiverad [aktiverar du TLS 1.2 på servern](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Lägg till servern i samma Active Directory-skog som de användare vars lösenord du behöver validera.
2. Installera den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) på servern som identifierades i föregående steg. Om du redan har Azure AD Connect igång kontrollerar du att versionen är 1.1.750.0 eller senare.

    >[!NOTE]
    >Azure AD Connect-versionerna 1.1.557.0, 1.1.558.0, 1.1.561.0 och 1.1.614.0 har ett problem relaterat till synkronisering av lösenord hash.0. Om du _inte_ tänker använda synkronisering av lösenordsh hash-synkronisering i samband med direktautentisering läser du [azure AD Connect-versionsanteckningarna](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Identifiera en eller flera ytterligare servrar (med Windows Server 2012 R2 eller senare, med TLS 1.2 aktiverat) där du kan köra fristående autentiseringsagenter. Dessa ytterligare servrar behövs för att säkerställa hög tillgänglighet för begäranden om att logga in. Lägg till servrarna i samma Active Directory-skog som de användare vars lösenord du behöver validera.

    >[!IMPORTANT]
    >I produktionsmiljöer rekommenderar vi att du har minst 3 autentiseringsagenter som körs på din klientorganisation. Det finns en systemgräns på 40 autentiseringsagenter per klient. Och som bästa praxis, behandla alla servrar som kör autentiseringsagenter som nivå 0-system (se [referens).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

4. Om det finns en brandvägg mellan servrarna och Azure AD konfigurerar du följande objekt:
   - Se till att autentiseringsagenter kan göra *utgående* begäranden till Azure AD via följande portar:

     | Portnummer | Hur den används |
     | --- | --- |
     | **80** | Hämtar listorna för återkallade certifikat (CRL: er) när TLS/SSL-certifikatet valideras |
     | **443** | Hanterar all utgående kommunikation med tjänsten |
     | **8080** (tillval) | Autentiseringsagenter rapporterar sin status var tionde minut över port 8080, om port 443 inte är tillgänglig. Den här statusen visas på Azure AD-portalen. Port 8080 används _inte_ för användarloggningar. |
     
     Om brandväggen tillämpar regler enligt de ursprungliga användarna öppnar du dessa portar för trafik från Windows-tjänster som körs som en nätverkstjänst.
   - Om brandväggen eller proxyn tillåter DNS-vitlistning, vitlista anslutningar till ** \*.msappproxy.net** och ** \*.servicebus.windows.net**. Om inte, tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som uppdateras varje vecka.
   - Dina autentiseringsagenter behöver åtkomst till **login.windows.net** och **login.microsoftonline.com** för första registrering. Öppna brandväggen för dessa webbadresser också.
   - För certifikatvalidering avblockerar du följande webbadresser: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**och **www\.microsoft.com:80**. Eftersom dessa url:er används för certifikatvalidering med andra Microsoft-produkter kanske du redan har dessa url:er blockerade.

## <a name="step-2-enable-the-feature"></a>Steg 2: Aktivera funktionen

Aktivera direktautentisering via [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>Du kan aktivera direktautentisering på den primära azure AD Connect-servern eller mellanlagringsservern. Vi rekommenderar starkt att du aktiverar den från den primära servern. Om du konfigurerar en Azure AD Connect-mellanlagringsserver i framtiden **måste** du fortsätta att välja Direktautentisering som inloggningsalternativ. Om du väljer ett annat alternativ **inaktiveras** direktautentisering på klienten och inställningen i den primära servern åsidosätts.

Om du installerar Azure AD Connect för första gången väljer du den [anpassade installationssökvägen](how-to-connect-install-custom.md). På sidan **Använda inloggning** väljer du **Direktautentisering** som **inloggningsmetod**. När en direktautentiseringsagent har slutförts installeras den på samma server som Azure AD Connect. Dessutom är direktautentiseringsfunktionen aktiverad på din klientorganisation.

![Azure AD Connect: Användarloggning](./media/how-to-connect-pta-quick-start/sso3.png)

Om du redan har installerat Azure AD Connect med hjälp av [expressinstallationen](how-to-connect-install-express.md) eller den [anpassade installationssökvägen](how-to-connect-install-custom.md) väljer du aktiviteten **Ändra användarloggning** på Azure AD Connect och väljer sedan **Nästa**. Välj sedan **Direktautentisering** som inloggningsmetod. När en direktautentiseringsagent har slutförts installeras på samma server som Azure AD Connect och funktionen är aktiverad på din klientorganisation.

![Azure AD Connect: Ändra inloggning från användare](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Direktautentisering är en funktion på klientnivå. Om du aktiverar den påverkas inloggningen för användare på _alla_ hanterade domäner i din klientorganisation. Om du byter från AD FS (Active Directory Federation Services) till Direktautentisering bör du vänta minst 12 timmar innan du stänger av AD FS-infrastrukturen. Den här väntetiden är att se till att användarna kan fortsätta logga in på Exchange ActiveSync under övergången. Mer hjälp om hur du migrerar från AD FS till Direktautentisering finns i vår detaljerade distributionsplan som publiceras [här](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Steg 3: Testa funktionen

Följ dessa instruktioner för att kontrollera att du har aktiverat direktautentisering korrekt:

1. Logga in på [Administrationscentret](https://aad.portal.azure.com) för Azure Active Directory med globala administratörsbehörighet för din klient.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**.
4. Kontrollera att **funktionen Direktautentisering** visas som **aktiverad**.
5. Välj **Direktautentisering**. I fönstret **Direktautentisering** visas de servrar där dina autentiseringsagenter är installerade.

![Administrationscenter för Azure Active Directory: Azure AD Connect-fönstret](./media/how-to-connect-pta-quick-start/pta7.png)

![Administrationscenter för Azure Active Directory: Fönstret Direktautentisering](./media/how-to-connect-pta-quick-start/pta8.png)

I det här skedet kan användare från alla hanterade domäner i din klient inloggade med hjälp av direktautentisering. Användare från federerade domäner fortsätter dock att logga in med ad fs eller en annan federationsleverantör som du tidigare har konfigurerat. Om du konverterar en domän från federerad till hanterad börjar alla användare från den domänen automatiskt logga in med hjälp av Direktautentisering. Funktionen Direktautentisering påverkar inte användare som bara är molnet.

## <a name="step-4-ensure-high-availability"></a>Steg 4: Säkerställa hög tillgänglighet

Om du planerar att distribuera direktautentisering i en produktionsmiljö bör du installera ytterligare fristående autentiseringsagenter. Installera dessa autentiseringsagenter på _andra_ servrar än den som kör Azure AD Connect. Den här inställningen ger dig hög tillgänglighet för användarloggningsbegäranden.

>[!IMPORTANT]
>I produktionsmiljöer rekommenderar vi att du har minst 3 autentiseringsagenter som körs på din klientorganisation. Det finns en systemgräns på 40 autentiseringsagenter per klient. Och som bästa praxis, behandla alla servrar som kör autentiseringsagenter som nivå 0-system (se [referens).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

Installera flera direktautentiseringsagenter säkerställer hög tillgänglighet, men inte deterministisk belastningsutjämning mellan autentiseringsagenterna. Ta reda på hur många autentiseringsagenter du behöver för din klientorganisation genom att ta hänsyn till den högsta och genomsnittliga belastningen av inloggningsbegäranden som du förväntar dig att se på din klientorganisation. Som ett riktmärke kan en enda autentiseringsagent hantera 300 till 400 autentiseringar per sekund på en vanlig 4-kärnig CPU, 16 GB RAM-server.

Om du vill uppskatta nätverkstrafiken använder du följande storleksvägledning:
- Varje begäran har en nyttolaststorlek på (0,5 K + 1K * num_of_agents) byte; dvs data från Azure AD till autentiseringsagenten. Här anger "num_of_agents" antalet autentiseringsagenter som är registrerade på din klientorganisation.
- Varje svar har en nyttolaststorlek på 1K-byte. dvs data från autentiseringsagenten till Azure AD.

För de flesta kunder är totalt tre autentiseringsagenter tillräckliga för hög tillgänglighet och kapacitet. Du bör installera autentiseringsagenter nära domänkontrollanterna för att förbättra inloggningstiden.

Börja med att följa dessa instruktioner för att ladda ner autentiseringsagentens programvara:

1. Om du vill hämta den senaste versionen av autentiseringsagenten (version 1.5.193.0 eller senare) loggar du in på [Azure Active Directory-administrationscentret](https://aad.portal.azure.com) med klientens globala administratörsautentiseringsuppgifter.
2. Välj **Azure Active Directory** i den vänstra rutan.
3. Välj **Azure AD Connect**, välj **Direktautentisering**och välj sedan **Download Agent**.
4. Välj knappen **Acceptera villkor & hämta.**

![Administrationscenter för Azure Active Directory: Knappen Hämta autentiseringsagent](./media/how-to-connect-pta-quick-start/pta9.png)

![Administrationscenter för Azure Active Directory: Fönstret Hämta agent](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>Du kan också direkt [ladda ner autentiseringsagenten.](https://aka.ms/getauthagent) Granska och acceptera autentiseringsagentens [användarvillkor](https://aka.ms/authagenteula) _innan_ du installerar den.

Det finns två sätt att distribuera en fristående autentiseringsagent:

Först kan du göra det interaktivt genom att bara köra den nedladdade autentiseringsagentens körbara och tillhandahålla klientens globala administratörsautentiseringsuppgifter när du uppmanas.

För det andra kan du skapa och köra ett obevakat distributionsskript. Detta är användbart när du vill distribuera flera autentiseringsagenter samtidigt, eller installera autentiseringsagenter på Windows-servrar som inte har användargränssnitt aktiverat eller som du inte kan komma åt med Fjärrskrivbord. Här är instruktionerna om hur du använder den här metoden:

1. Kör följande kommando för att `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`installera en autentiseringsagent: .
2. Du kan registrera autentiseringsagenten med vår tjänst med Windows PowerShell. Skapa ett PowerShell-autentiseringsobjekt `$cred` som innehåller ett globalt administratörsanvändarnamn och lösenord för din klient. Kör följande kommando och ersätt * \<\> användarnamn* och * \<lösenord:\>*

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
3. Gå till **C:\Program Files\Microsoft Azure AD Connect Authentication** `$cred` Agent och kör följande skript med objektet som du skapade:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Om en autentiseringsagent är installerad på en virtuell dator kan du inte klona den virtuella datorn för att konfigurera en annan autentiseringsagent. Den här metoden **stöds inte**.

## <a name="step-5-configure-smart-lockout-capability"></a>Steg 5: Konfigurera smart utelåsningsfunktion

Smart Lockout hjälper till att låsa ute dåliga aktörer som försöker gissa användarnas lösenord eller använda brute-force metoder för att komma in. Genom att konfigurera inställningar för smart utelåsning i Azure AD och/eller lämpliga utelåsningsinställningar i lokal Active Directory kan attacker filtreras bort innan de når Active Directory. Läs [den här artikeln](../authentication/howto-password-smart-lockout.md) om du vill veta mer om hur du konfigurerar inställningar för smartutelåsning på din klientorganisation för att skydda dina användarkonton.

## <a name="next-steps"></a>Nästa steg
- [Migrera från AD FS till Direktautentisering](https://aka.ms/adfstoptadp) – En detaljerad guide för att migrera från AD FS (eller annan federationsteknik) till direktautentisering.
- [Smart Utelåsning:](../authentication/howto-password-smart-lockout.md)Lär dig hur du konfigurerar smart utelåsningsfunktionen på din klient för att skydda användarkonton.
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Lär dig vilka scenarier som stöds med direktautentisering och vilka som inte är det.
- [Teknisk djupdykning](how-to-connect-pta-how-it-works.md): Förstå hur funktionen Direktautentisering fungerar.
- [Vanliga frågor och svar](how-to-connect-pta-faq.md): Hitta svar på vanliga frågor.
- [Felsöka:](tshoot-connect-pass-through-authentication.md)Lär dig hur du löser vanliga problem med direktautentiseringsfunktionen.
- [Djupdykning i säkerhet](how-to-connect-pta-security-deep-dive.md): Få teknisk information om funktionen Direktautentisering.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Läs mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory Forum för att fila nya funktionsbegäranden.
