---
title: Säkerhets vägledning för Azure Multi-Factor Authentication – Azure Active Directory
description: Det här dokumentet innehåller vägledning kring hur du använder Azure MFA med Azure-konton
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
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846835"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Säkerhets rikt linjer för att använda Azure Multi-Factor Authentication med Azure AD-konton

Tvåstegsverifiering är det bästa valet för de flesta organisationer som vill förbättra sin autentiseringsprocess. Azure Multi-Factor Authentication (MFA) hjälper företag att uppfylla sina krav på säkerhet och efterlevnad och ger en enkel inloggnings upplevelse för användarna. Den här artikeln innehåller några tips som du bör tänka på när du planerar inför införandet av Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Distribuera Azure MFA i molnet

Det finns två sätt att [Aktivera Azure MFA för alla dina användare](howto-mfa-getstarted.md).

* Köp licenser för varje användare (antingen Azure MFA, Azure AD Premium eller Enterprise Mobility + Security)
* Skapa en Multi-Factor Auth-provider och betala per användare eller per autentisering

### <a name="licenses"></a>Licenser

![Använd licenser för användare, aktivera, meddela](./media/multi-factor-authentication-security-best-practices/ems.png)

Om du har Azure AD Premium eller Enterprise Mobility + Security licenser har du redan Azure MFA. Din organisation behöver inte något ytterligare för att utöka tvåstegsverifiering för alla användare. Du behöver bara tilldela en licens till en användare och sedan kan du aktivera MFA.

När du ställer in Multi-Factor Authentication bör du tänka på följande tips:

* Skapa inte en Multi-Factor Auth-provider per autentisering. Om du gör det kan du betala för verifierings begär Anden från användare som redan har licenser.
* Om du inte har tillräckligt med licenser för alla dina användare kan du skapa en Multi-Factor Auth-provider för att ta fram resten av din organisation. 
* Azure AD Connect krävs endast om du synkroniserar din lokala Active Directory miljö med en Azure AD-katalog. Om du använder en Azure AD-katalog som inte är synkroniserad med en lokal instans av Active Directory behöver du inte Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Multi-Factor Auth-provider

![Multi-Factor Authentication provider](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Om du inte har licenser som inkluderar Azure MFA kan du [skapa en MFA Auth-provider](concept-mfa-authprovider.md).

När du skapar auth-providern måste du välja en katalog och tänka på följande information:

* Du behöver ingen Azure AD-katalog för att skapa en Multi-Factor Auth-provider, men du får fler funktioner med en. Följande funktioner aktive ras när du kopplar auth-providern till en Azure AD-katalog:
  * Utöka tvåstegsverifiering till alla dina användare
  * Erbjud dina globala administratörer ytterligare funktioner, till exempel hanterings portalen, anpassade hälsningar och rapporter.
* Om du synkroniserar din lokala Active Directory miljö med en Azure AD-katalog behöver du DirSync eller Azure AD Sync. Om du använder en Azure AD-katalog som inte är synkroniserad med en lokal instans av Active Directory behöver du inte DirSync eller Azure AD Sync.
* Välj den förbruknings modell som passar din verksamhet bäst. När du har valt användnings modellen kan du inte ändra den. De två modellerna är:
  * Per autentisering: debiteras för varje verifiering. Använd den här modellen om du vill ha tvåstegsverifiering för alla som har åtkomst till en viss app, inte för specifika användare.
  * Per aktive rad användare: debiterar dig för varje användare som du aktiverar för Azure MFA. Använd den här modellen om du har några användare med Azure AD Premium-eller Enterprise Mobility Suite-licenser och några utan.

### <a name="supportability"></a>Support

Eftersom de flesta användare bara är vana vid att använda lösen ord för att autentisera, är det viktigt att ditt företag får kännedom om alla användare om den här processen. Den här medvetenheten kan minska sannolikheten för att användarna kontaktar supportavdelningen för mindre problem som rör MFA. Det finns dock några scenarier där det krävs tillfälligt inaktive ring av MFA. Använd följande rikt linjer för att förstå hur du hanterar dessa scenarier:

* Utbilda din tekniska support personal för att hantera scenarier där användaren inte kan logga in eftersom mobilappen eller telefonen inte får något meddelande eller telefonsamtal. Teknisk support kan [Aktivera en engångs](howto-mfa-mfasettings.md#one-time-bypass) åtgärd för att tillåta att en användare autentiserar en gång genom att "kringgå" tvåstegsverifiering. Bypass är tillfälligt och upphör att gälla efter ett angivet antal sekunder.
* Överväg den [betrodda IP-funktionen](howto-mfa-mfasettings.md#trusted-ips) i Azure MFA som ett sätt att minimera tvåstegsverifiering. Med den här funktionen kan administratörer av en hanterad eller federerad klient kringgå tvåstegsverifiering för användare som loggar in från företagets lokala intranät. Funktionerna är tillgängliga för Azure AD-klienter som har Azure AD Premium, Enterprise Mobility Suite eller Azure Multi-Factor Authentication-licenser.

## <a name="best-practices-for-an-on-premises-deployment"></a>Metod tips för en lokal distribution

Om ditt företag har beslutat att använda sin egen infrastruktur för att aktivera MFA måste du [distribuera en Azure-Multi-Factor Authentication-Server lokalt](howto-mfaserver-deploy.md). MFA Server-komponenterna visas i följande diagram:

![standard komponenterna för MFA-server som](./media/multi-factor-authentication-security-best-practices/server.png) \*inte installeras som standard \** installerat men inte aktive ras som standard

Azure Multi-Factor Authentication-server kan skydda moln resurser och lokala resurser med hjälp av Federation. Du måste ha AD FS och ha den federerad med din Azure AD-klient.
När du ställer in Multi-Factor Authentication-server bör du tänka på följande:

* Om du skyddar Azure AD-resurser med hjälp av Active Directory Federation Services (AD FS) (AD FS) utförs det första verifierings steget lokalt med hjälp av AD FS. Det andra steget utförs lokalt genom att anspråket tillämpas.
* Du behöver inte installera Azure-Multi-Factor Authentication-server din AD FS Federations Server. Multi-Factor Authentication adaptern för AD FS måste dock installeras på en Windows Server 2012 R2 som kör AD FS. Du kan installera servern på en annan dator, förutsatt att det är en version som stöds och installera AD FS adapter separat på din AD FS Federations Server. 
* Installations guiden för Multi-Factor Authentication AD FS adapter skapar en säkerhets grupp med namnet PhoneFactor admins i din Active Directory och lägger sedan till AD FS tjänst kontot i den här gruppen. Verifiera att gruppen PhoneFactor Admins verkligen har skapats i domänkontrollanten och att AD FS-tjänstkontot är medlem av den här gruppen. Om det behövs lägger du till AD FS-tjänstkontot i gruppen PhoneFactor Admins på domänkontrollanten manuellt.

### <a name="user-portal"></a>Användarportalen

Användar portalen ger självbetjänings funktioner och ger en fullständig uppsättning funktioner för användar administration. Den körs på en IIS-webbplats (Internet Information Server). Använd följande rikt linjer för att konfigurera den här komponenten:

* Använd IIS 6 eller senare
* Installera och registrera ASP.NET v-2.0.507207
* Kontrol lera att den här servern kan distribueras i ett perimeternätverk

### <a name="app-passwords"></a>Applösenord

Om din organisation är federerad för enkel inloggning med Azure AD och du kommer att använda Azure MFA bör du vara medveten om följande information:

* App-lösenordet verifieras av Azure AD och kringgår därför federationen. Federation används endast när du konfigurerar applösenord.
* För federerade (SSO) användare lagras lösen ord i organisations-ID: t. Om användaren lämnar företaget måste denna information flöda till organisations-ID med hjälp av DirSync. Det kan ta upp till tre timmar innan kontot har Aktiver ATS/tas bort, vilket fördröjer inaktive ring/borttagning av applösenord i Azure AD.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Ingen lokal autentiserings-och gransknings funktion är tillgänglig för applösenord.
* Vissa avancerade arkitektur design kan kräva att du använder en kombination av organisationens användar namn och lösen ord och applösenord när du använder tvåstegsverifiering med-klienter, beroende på var de autentiserar sig. För klienter som autentiseras mot en lokal infrastruktur använder du ett organisations användar namn och lösen ord. För klienter som autentiseras mot Azure AD använder du appens lösen ord.
* Som standard kan användarna inte skapa applösenord. Om du behöver tillåta användare att skapa applösenord väljer du alternativet **Tillåt användare att skapa applösenord för att logga in på icke-webbläsarbaserade program** .

## <a name="additional-considerations"></a>Ytterligare överväganden

Använd den här listan för ytterligare överväganden och vägledning för varje komponent som distribueras lokalt:

* Konfigurera [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md) i Azure Multi-Factor Authentication.
* Konfigurera [RADIUS-autentisering](howto-mfaserver-dir-radius.md) på Azure MFA-servern.
* Konfigurera och konfigurera Azure MFA-servern med [IIS-autentisering](howto-mfaserver-iis.md).
* Konfigurera och konfigurera Azure MFA server med Windows- [autentisering](howto-mfaserver-windows.md).
* Konfigurera och konfigurera Azure MFA-servern med [LDAP-autentisering](howto-mfaserver-dir-ldap.md).
* Konfigurera och konfigurera Azure MFA server med [Fjärrskrivbordsgateway och Azure Multi-Factor Authentication-Server med RADIUS](howto-mfaserver-nps-rdg.md).
* Konfigurera och konfigurera synkronisering mellan Azure MFA Server och [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
* [Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](howto-mfaserver-deploy-mobileapp.md).
* [Avancerad VPN-konfiguration med Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) för Cisco ASA, Citrix NetScaler och Juniper/Pulse Secure VPN-enheter med hjälp av LDAP eller RADIUS.

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs några metod tips för Azure MFA, men det finns andra resurser som du kan använda när du planerar MFA-distributionen. I listan nedan finns några viktiga artiklar som kan hjälpa dig under den här processen:

* [Rapporter i Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [Registrerings upplevelsen för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-first-time.md)
* [Vanliga frågor och svar om Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
