---
title: Säkerhetsvägledning för Azure Multi Factor Authentication – Azure Active Directory
description: Det här dokumentet ger vägledning om hur du använder Azure MFA med Azure-konton
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e42234e9fcdcfe3ee5ce975babbe03b64a750e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846835"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Säkerhetsvägledning för användning av Azure Multi-Factor Authentication med Azure AD-konton

Tvåstegsverifiering är det självklara valet för de flesta organisationer som vill förbättra sin autentiseringsprocess. Azure Multi-Factor Authentication (MFA) hjälper företag att uppfylla sina säkerhets- och efterlevnadskrav samtidigt som de tillhandahåller en enkel inloggningsupplevelse för sina användare. Den här artikeln innehåller några tips som du bör tänka på när du planerar för att anta Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Distribuera Azure MFA i molnet

Det finns två sätt att [aktivera Azure MFA för alla dina användare](howto-mfa-getstarted.md).

* Köpa licenser för varje användare (Antingen Azure MFA, Azure AD Premium eller Enterprise Mobility + Security)
* Skapa en Multifaktorauth-leverantör och betala per användare eller per autentisering

### <a name="licenses"></a>Licenser

![Tillämpa licenser på användare, aktivera, meddela](./media/multi-factor-authentication-security-best-practices/ems.png)

Om du har Azure AD Premium- eller Enterprise Mobility + Security-licenser har du redan Azure MFA. Din organisation behöver inget extra för att utöka verifieringsfunktionen i två steg till alla användare. Du behöver bara tilldela en licens till en användare och sedan kan du aktivera MFA.

När du konfigurerar multifaktorautentisering bör du tänka på följande tips:

* Skapa inte en Multifaktor-Auth-provider per autentisering. Om du gör det kan det sluta med att du betalar för verifieringsförfrågningar från användare som redan har licenser.
* Om du inte har tillräckligt med licenser för alla användare kan du skapa en Multi-Factor Auth-leverantör per användare för att täcka resten av organisationen. 
* Azure AD Connect krävs bara om du synkroniserar din lokala Active Directory-miljö med en Azure AD-katalog. Om du använder en Azure AD-katalog som inte är synkroniserad med en lokal instans av Active Directory behöver du inte Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Multifaktor-Auth-provider

![Autentiseringsprovider för flera faktorer](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Om du inte har licenser som innehåller Azure MFA kan du [skapa en MFA Auth Provider](concept-mfa-authprovider.md).

När du skapar Auth Provider måste du välja en katalog och tänka på följande information:

* Du behöver inte en Azure AD-katalog för att skapa en Multi-Factor Auth Provider, men du får fler funktioner med en. Följande funktioner aktiveras när du associerar Auth Provider med en Azure AD-katalog:
  * Utöka tvåstegsverifieringen till alla dina användare
  * Erbjud dina globala administratörer ytterligare funktioner, till exempel hanteringsportalen, anpassade hälsningar och rapporter.
* Om du synkroniserar din lokala Active Directory-miljö med en Azure AD-katalog behöver du DirSync eller Azure AD Sync. Om du använder en Azure AD-katalog som inte är synkroniserad med en lokal instans av Active Directory behöver du inte DirSync eller Azure AD Sync.
* Välj den konsumtionsmodell som bäst passar ditt företag. När du har valt användningsmodellen kan du inte ändra den. De två modellerna är:
  * Per autentisering: debiterar dig för varje verifiering. Använd den här modellen om du vill ha tvåstegsverifiering för alla som har åtkomst till en viss app, inte för specifika användare.
  * Per aktiverad användare: debiterar dig för varje användare som du aktiverar för Azure MFA. Använd den här modellen om du har några användare med Azure AD Premium- eller Enterprise Mobility Suite-licenser och vissa utan.

### <a name="supportability"></a>Support

Eftersom de flesta användare är vana vid att bara använda lösenord för att autentisera, är det viktigt att ditt företag ger medvetenhet till alla användare om denna process. Den här medvetenheten kan minska sannolikheten för att användarna ringer din helpdesk för mindre problem relaterade till MFA. Det finns dock vissa scenarier där det är nödvändigt att tillfälligt inaktivera MFA. Använd följande riktlinjer för att förstå hur du hanterar dessa scenarier:

* Träna din tekniska supportpersonal att hantera scenarier där användaren inte kan logga in eftersom mobilappen eller telefonen inte får ett meddelande eller telefonsamtal. Teknisk support kan [möjliggöra en engångsförsening](howto-mfa-mfasettings.md#one-time-bypass) för att tillåta en användare att autentisera en enda gång genom att "kringgå" tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder.
* Tänk dig funktionen [Betrodda IPs](howto-mfa-mfasettings.md#trusted-ips) i Azure MFA som ett sätt att minimera tvåstegsverifiering. Med den här funktionen kan administratörer för en hanterad eller federerad klient kringgå tvåstegsverifiering för användare som loggar in från företagets lokala intranät. Funktionerna är tillgängliga för Azure AD-klienter som har Azure AD Premium-, Enterprise Mobility Suite- eller Azure Multi-Factor Authentication-licenser.

## <a name="best-practices-for-an-on-premises-deployment"></a>Metodtips för en lokal distribution

Om ditt företag har beslutat att utnyttja sin egen infrastruktur för att aktivera MFA måste du [distribuera en lokal Azure Multi-Factor Authentication Server](howto-mfaserver-deploy.md). MFA-serverkomponenterna visas i följande diagram:

![Standardkomponenterna för](./media/multi-factor-authentication-security-best-practices/server.png) \*MFA-server \*Inte installeras som standard *Installerad men inte aktiverad som standard

Azure Multi-Factor Authentication Server kan skydda molnresurser och lokala resurser med hjälp av federationen. Du måste ha AD FS och ha den federerad med din Azure AD-klientorganisation.
När du konfigurerar MultiFaktor Authentication Server bör du tänka på följande:

* Om du skyddar Azure AD-resurser med hjälp av AD FS (Active Directory Federation Services) utförs det första verifieringssteget lokalt med AD FS. Det andra steget utförs lokalt genom att anspråket tillämpas.
* Du behöver inte installera Azure Multi-Factor Authentication Server på AD FS-federationsservern. Multifaktorautentiseringskortet för AD FS måste dock vara installerat på en Windows Server 2012 R2 som kör AD FS. Du kan installera servern på en annan dator så länge det är en version som stöds och installera AD FS-kortet separat på AD FS-federationsservern. 
* Installationsguiden för multifaktorautentisering AD FS-kort skapar en säkerhetsgrupp som heter PhoneFactor-administratörer i Active Directory och lägger sedan till ditt AD FS-tjänstkonto i den här gruppen. Verifiera att gruppen PhoneFactor Admins verkligen har skapats i domänkontrollanten och att AD FS-tjänstkontot är medlem av den här gruppen. Om det behövs lägger du till AD FS-tjänstkontot i gruppen PhoneFactor Admins på domänkontrollanten manuellt.

### <a name="user-portal"></a>Användarportalen

Användarportalen tillåter självbetjäningsfunktioner och ger en fullständig uppsättning funktioner för användaradministration. Den körs på en IIS-webbplats (Internet Information Server). Använd följande riktlinjer för att konfigurera den här komponenten:

* Använda IIS 6 eller senare
* Installera och registrera ASP.NET v2.0.507207
* Se till att den här servern kan distribueras i ett perimeternätverk

### <a name="app-passwords"></a>Lösenord för appar

Om din organisation är federerad för SSO med Azure AD och du kommer att använda Azure MFA bör du vara medveten om följande information:

* Applösenordet verifieras av Azure AD och kringgår därför federationen. Federation används bara när du ställer in applösenord.
* För federerade (SSO) användare lagras lösenord i organisations-ID. Om användaren lämnar företaget måste den informationen flöda till organisations-ID med DirSync. Det kan ta upp till tre timmar att synkronisera kontot, vilket försenar inaktivera/ta bort applösenord i Azure AD.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Det finns ingen lokal autentiseringsloggnings-/granskningsfunktion tillgänglig för applösenord.
* Vissa avancerade arkitektoniska konstruktioner kan kräva att du använder en kombination av organisationsanvändarnamn och lösenord och applösenord när du använder tvåstegsverifiering med klienter, beroende på var de autentiserar. För klienter som autentiserar mot en lokal infrastruktur använder du ett organisationsanvändarnamn och lösenord. För klienter som autentiserar mot Azure AD använder du applösenordet.
* Som standard kan användare inte skapa applösenord. Om du behöver tillåta användare att skapa applösenord väljer du alternativet **Tillåt användare att skapa applösenord för att logga in på program som inte är webbläsarprogram.**

## <a name="additional-considerations"></a>Ytterligare överväganden

Använd den här listan för ytterligare överväganden och vägledning för varje komponent som distribueras lokalt:

* Konfigurera [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md) i Azure Multi-Factor Authentication.
* Konfigurera [RADIUS-autentisering](howto-mfaserver-dir-radius.md) på Azure MFA-servern.
* Konfigurera Azure MFA Server med [IIS-autentisering](howto-mfaserver-iis.md).
* Konfigurera Azure MFA Server med [Windows-autentisering](howto-mfaserver-windows.md).
* Konfigurera Azure MFA Server med [LDAP-autentisering](howto-mfaserver-dir-ldap.md).
* Konfigurera Azure MFA Server med [Fjärrskrivbordsgateway och Azure Multi Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md).
* Konfigurera synkronisering mellan Azure MFA Server och Active Directory för [Windows Server](howto-mfaserver-dir-ad.md).
* [Distribuera webbtjänsten för Azure Multi Factor Authentication Server Mobile App](howto-mfaserver-deploy-mobileapp.md).
* [Avancerad VPN-konfiguration med Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) för Cisco ASA-, Citrix Netscaler- och Juniper/Pulse Secure VPN-enheter med LDAP eller RADIUS.

## <a name="next-steps"></a>Nästa steg

Den här artikeln belyser några metodtips för Azure MFA, men det finns andra resurser som du också kan använda när du planerar din MFA-distribution. Listan nedan har några viktiga artiklar som kan hjälpa dig under denna process:

* [Rapporter i Azure Multi Factor-autentisering](howto-mfa-reporting.md)
* [Erfarenheten av registrering i två steg](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Vanliga frågor och svar om Azure Multi Factor-autentisering](multi-factor-authentication-faq.md)
