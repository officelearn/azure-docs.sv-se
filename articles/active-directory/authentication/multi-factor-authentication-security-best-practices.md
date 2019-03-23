---
title: Säkerhetsvägledning för Azure Multi-Factor Authentication - Azure Active Directory
description: Det här dokumentet innehåller vägledning för Azure MFA med Azure-konton
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 436b7899b1a9d4f9cab1ca2581ff9b5b162de8ac
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368220"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Säkerhetsvägledning för att använda Azure Multi-Factor Authentication med Azure AD-konton

Tvåstegsverifiering är det önskade valet för de flesta organisationer som vill förbättra sina autentiseringsprocessen. Azure Multi-Factor Authentication (MFA) hjälper företag att uppfylla sina krav på säkerhet och efterlevnad samtidigt som det ger en enkel inloggning för sina användare. Den här artikeln beskriver några tips som du bör tänka på när du planerar för användningen av Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Distribuera Azure MFA i molnet

Det finns två sätt att [aktivera Azure MFA för alla användare](howto-mfa-getstarted.md).

* Köpa licenser för varje användare (antingen Azure MFA, Azure AD Premium eller Enterprise Mobility + Security)
* Skapa en Multi-Factor Auth-Provider och betala per användare eller per autentisering

### <a name="licenses"></a>Licenser

![Använda licenser till användare, aktivera, meddela](./media/multi-factor-authentication-security-best-practices/ems.png)

Om du har Azure AD Premium eller Enterprise Mobility + Security-licenser, har du redan Azure MFA. Din organisation behöver inte något mer att utöka kapaciteten för tvåstegsverifiering verifiering för alla användare. Du behöver bara tilldela en licens till en användare och sedan kan du aktivera MFA.

När du ställer in Multifaktorautentisering måste du tänka på följande:

* Skapa inte en per autentisering Multi-Factor Auth-Provider. Om du gör det, kan du få betala för begäranden från användare som redan har licenser.
* Om du inte har tillräckligt med licenser för alla användare, kan du skapa en per användare Multi-Factor Auth-Provider för att täcka resten av din organisation. 
* Azure AD Connect är endast krävs om du synkroniserar din lokala Active Directory-miljö med Azure AD-katalog. Om du använder Azure AD-katalog som inte är synkroniserad med en lokal instans av Active Directory kan behöver du inte Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Leverantör av Multifaktorautent

![Multi-Factor Authentication Provider](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Om du inte har licenser som täcks av Azure MFA, kan du [skapa en MFA Auth-Provider](concept-mfa-authprovider.md).

När du skapar Auth-Provider, måste du markera en katalog och ha följande information:

* Du behöver inte en Azure AD-katalog för att skapa en Multi-Factor Auth-Provider, men du får fler funktioner med en. Följande funktioner aktiveras när du kopplar Auth-Provider till Azure AD-katalog:
  * Utöka tvåstegsverifiering för alla användare
  * Erbjuda dina globala administratörer ytterligare funktioner, till exempel hanteringsportalen, anpassade hälsningar och rapporter.
* Om du synkroniserar din lokala Active Directory-miljö med Azure AD-katalog måste du DirSync eller AAD Sync. Om du använder Azure AD-katalog som inte är synkroniserad med en lokal instans av Active Directory kan behöver du inte DirSync eller AAD Sync.
* Välj förbrukningsmodell som bäst passar din verksamhet. Du kan inte ändra när du har valt användningsmodell. De två modellerna är:
  * Per autentisering: debiteras du för varje verifiering. Använd den här modellen om du vill tvåstegsverifiering för alla som har åtkomst till en viss app, inte för specifika användare.
  * Per aktiverad användare: debiteras du för varje användare som du gör det möjligt för Azure MFA. Använd den här modellen om du har några användare med Azure AD Premium eller Enterprise Mobility Suite-licenser och vissa utan.

### <a name="supportability"></a>Support

Eftersom de flesta användare är vana vid att använda endast lösenord för autentisering, är det viktigt att ditt företag informerar för alla användare om den här processen. Den här medvetenhet kan minska risken för att användare kontakta supportavdelningen för mindre allvarliga problem som rör MFA. Det finns dock vissa scenarier där tillfälligt inaktivera MFA är nödvändigt. Använd följande riktlinjer för att förstå hur du hanterar dessa scenarier:

* Utbilda personalen teknisk support för att hantera scenarier där användaren kan inte logga in eftersom i mobilappen eller telefon inte tar emot en avisering eller ett telefonsamtal. Teknisk support kan [aktivera en engångsförbikoppling](howto-mfa-mfasettings.md#one-time-bypass) så att en användare att autentisera en gång genom att ”kringgå” tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder.
* Överväg att den [tillförlitliga IP-adresser funktionen](howto-mfa-mfasettings.md#trusted-ips) i Azure MFA som ett sätt att minimera tvåstegsverifiering. Med den här funktionen kan administratörer för en hanterad eller federerade klient kringgå tvåstegsverifiering för användare som loggar in från företagets lokalt intranät. Funktionerna är tillgängliga för Azure AD-klienter som har licenser för Azure AD Premium eller Enterprise Mobility Suite Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Metodtips för en lokal distribution

Om företaget bestämde sig för att använda en egen infrastruktur för att aktivera MFA, måste du [distribuera en Azure Multi-Factor Authentication Server lokalt](howto-mfaserver-deploy.md). MFA Server-komponenterna visas i följande diagram:

![MFA Server standardkomponenterna](./media/multi-factor-authentication-security-best-practices/server.png) \*inte installeras som standard \** installerad men inte är aktiverad som standard

Azure Multi-Factor Authentication Server kan skydda molnet resurser och lokala resurser med federation. Du måste har AD FS och har den federerade med Azure AD-klienten.
När du konfigurerar Multi-Factor Authentication Server, Överväg följande information:

* Om du skydda Azure AD-resurser med hjälp av Active Directory Federation Services (AD FS), och sedan på det första verifieringssteget utförs lokalt med hjälp av AD FS. Det andra steget utförs lokalt genom att anspråket tillämpas.
* Du behöver inte installera Azure Multi-Factor Authentication Server din AD FS-federationsserver. Men måste Multi-Factor Authentication-adaptern för AD FS installeras på en Windows Server 2012 R2 som kör AD FS. Du kan installera servern på en annan dator, så länge det är en version som stöds och installera AD FS-adaptern separat på AD FS-federationsservern. 
* Installationsguiden för multi-Factor Authentication AD FS-adaptern skapar en säkerhetsgrupp med namnet PhoneFactor Admins i Active Directory och lägger sedan till din AD FS-tjänstkontot till den här gruppen. Verifiera att gruppen PhoneFactor Admins verkligen har skapats i domänkontrollanten och att AD FS-tjänstkontot är medlem av den här gruppen. Om det behövs lägger du till AD FS-tjänstkontot i gruppen PhoneFactor Admins på domänkontrollanten manuellt.

### <a name="user-portal"></a>Användarportalen

Användarportalen kan självbetjäningsfunktioner och tillhandahåller en fullständig uppsättning funktioner för administration av användaren. Den körs i en Internet Information Server (IIS)-webbplats. Använd följande riktlinjer för att konfigurera den här komponenten:

* Använd IIS 6 eller senare
* Installera och registrera ASP.NET v2.0.507207
* Se till att den här servern kan distribueras i ett perimeternätverk

### <a name="app-passwords"></a>Applösenord

Om din organisation är federerad för enkel inloggning med Azure AD och du kommer att använda Azure MFA kan vara medveten om följande information:

* Applösenord verifieras av Azure AD och kringgår därför federation. Federation används endast när du konfigurerar applösenord.
* För federerade (SSO)-användare lagras lösenord i organisations-ID. Om användaren lämnar företaget, har den info flöda till organisationens ID via DirSync. Inaktiveringen eller borttagningen kan ta upp till tre timmar att sync, vilket försenar inaktiveringen eller borttagningen av applösenord i Azure AD.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Ingen lokal autentisering loggning/granskning funktionen är tillgänglig för applösenord.
* Vissa avancerade arkitekturritningar kan behöva använda en kombination av organisationens användarnamn och lösenord och lösenord när du använder tvåstegsverifiering med klienter, beroende på där de autentiseras. För klienter som autentiseras mot en lokal infrastruktur, skulle du använda ett organisationens användarnamn och lösenord. För klienter som autentiseras mot Azure AD, använder du applösenordet.
* Som standard det går inte att användare skapar applösenord. Om du vill tillåta användare att skapa applösenord, väljer den **Tillåt användare att skapa applösenord för att logga in på icke-webbläsarprogram** alternativet.

## <a name="additional-considerations"></a>Ytterligare överväganden

Använd den här listan ytterligare överväganden och anvisningar för varje komponent som är distribuerat lokalt:

* Konfigurera [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md) i Azure Multi-Factor Authentication.
* Konfigurera [RADIUS-autentisering](howto-mfaserver-dir-radius.md) på Azure MFA-servern.
* Installera och konfigurera Azure MFA Server med [IIS-autentisering](howto-mfaserver-iis.md).
* Installera och konfigurera Azure MFA Server med [Windows-autentisering](howto-mfaserver-windows.md).
* Installera och konfigurera Azure MFA Server med [LDAP-autentisering](howto-mfaserver-dir-ldap.md).
* Installera och konfigurera Azure MFA Server med [fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md).
* Installera och konfigurera synkronisering mellan Azure MFA-servern och [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
* [Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](howto-mfaserver-deploy-mobileapp.md).
* [Avancerade VPN-konfiguration med Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) för Cisco ASA, Citrix Netscaler och Juniper/Pulse Secure VPN-installationer med hjälp av LDAP- eller RADIUS.

## <a name="next-steps"></a>Nästa steg

Den här artikeln lyfter fram några Metodtips för Azure MFA, finns men det andra resurser som du kan också använda när du planerar distributionen av MFA. I listan nedan har vissa viktiga artiklar som kan hjälpa dig under den här processen:

* [Rapporter i Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [Registrering för tvåstegsverifiering verifieringsupplevelse](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Vanliga frågor om Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
