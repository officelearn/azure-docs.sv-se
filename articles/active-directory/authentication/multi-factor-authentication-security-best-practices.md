---
title: Rekommenderade säkerhetsmetoder för MFA | Microsoft Docs
description: Det här dokumentet innehåller metodtips runt med hjälp av Azure MFA med Azure-konton
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 0fd90c4e59fa64c24ecfa6d7d8f23e025210e078
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Rekommenderade säkerhetsmetoder för att använda Azure Multi-Factor Authentication med Azure AD-konton

Tvåstegsverifiering är det bästa valet för de flesta organisationer som vill utöka sina autentiseringsprocessen. Azure Multi-Factor Authentication (MFA) hjälper företag att uppfylla sina krav för säkerhet och efterlevnad och ger en enkel inloggning för sina användare. Den här artikeln beskriver några tips som du bör tänka på när du planerar för införandet av Azure MFA.

## <a name="deploy-azure-mfa-in-the-cloud"></a>Distribuera Azure MFA i molnet

Det finns två sätt att aktivera Azure MFA för alla användare.

* Köpa licenser för varje användare (antingen Azure MFA, Azure AD Premium eller Enterprise Mobility + Security)
* Skapa en leverantör av Multifaktorautent och betala per användare eller per autentisering

### <a name="licenses"></a>Licenser
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Om du har Azure AD Premium eller Enterprise Mobility + Security licenser, har du redan Azure MFA. Din organisation behöver inte något ytterligare funktioner att utökas i två steg verifiering för alla användare. Du behöver bara tilldela en licens till en användare och sedan kan du aktivera MFA.

När du konfigurerar Multifaktorautentisering du tänka på följande:

* Skapa inte en per autentisering leverantör av Multifaktorautent. Om du vill kan du få betala för verifiering begäranden från användare som redan har licenser.
* Om du inte har tillräckligt många licenser för alla användare kan skapa du en användarspecifik leverantör av Multifaktorautent så att den täcker resten av din organisation. 
* Azure AD Connect är bara obligatoriskt om du synkroniserar lokala Active Directory-miljön med en Azure AD-katalog. Om du använder Azure AD-katalog som inte är synkroniserad med en lokal instans av Active Directory kan behöver du inte Azure AD Connect.

### <a name="multi-factor-auth-provider"></a>Leverantör av Multifaktorautent
![Leverantör av Multifaktorautent](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Om du inte har licenser som innehåller Azure MFA kan du skapa en leverantör av Multifaktorautent MFA. 

När du skapar Auth-providern, måste du markera en katalog och beakta följande information:

* Du behöver inte en Azure AD-katalog för att skapa en leverantör av Multifaktorautent, men du får fler funktioner med en. Följande funktioner är aktiverade när du kopplar Auth-providern till en Azure AD-katalog:  
  * Utöka tvåstegsverifiering för alla användare  
  * Erbjuda dina globala administratörer ytterligare funktioner, till exempel hanteringsportalen, anpassade helg och rapporter.
* Om du synkroniserar lokala Active Directory-miljön med en Azure AD-katalog måste DirSync och AAD Sync. Om du använder Azure AD-katalog som inte är synkroniserad med en lokal instans av Active Directory, behöver du inte DirSync och AAD Sync.
* Välj förbrukning modell som bäst passar din verksamhet. När du har valt användningsmodell kan du inte ändra den. De två modellerna är:
  * Per autentisering: debiterar för varje kontroll. Använd den här modellen om du vill tvåstegsverifiering för alla som har åtkomst till en viss app, inte för specifika användare.
  * Per aktiverad användare: debiterar för varje användare som du aktiverar för Azure MFA. Använd den här modellen om du har några användare med Azure AD Premium eller Enterprise Mobility Suite licenser och vissa utan.

### <a name="supportability"></a>Support
Eftersom de flesta användare är bekanta dig med bara lösenord för att autentisera, är det viktigt att företaget ger medvetenhet för alla användare om den här processen. Den här medvetenhet kan minska sannolikheten att användarna kontakta supportavdelningen för mindre allvarliga problem som rör MFA. Det finns dock vissa scenarier där tillfälligt inaktivera MFA krävs. Använd följande riktlinjer för att förstå hur du hanterar dessa scenarier:

* Träna din tekniska supportpersonal för att hantera scenarier där användaren kan inte logga in eftersom mobilappen eller phone inte tar emot ett meddelande eller telefonsamtal. Teknisk support kan [aktivera en engångsförbikoppling](howto-mfa-mfasettings.md#one-time-bypass) så att en användare autentiseras en gång genom att ”kringgå” tvåstegsverifiering. Förbikopplingen är tillfällig och upphör att gälla efter ett angivet antal sekunder.
* Överväg att i [tillförlitliga IP-adresser kapaciteten](howto-mfa-mfasettings.md#trusted-ips) i Azure MFA som ett sätt att minimera tvåstegsverifiering. Med den här funktionen kan administratörer för en hanterad eller federerade klient kringgå tvåstegsverifiering för användare som loggar in från företagets lokalt intranät. Funktionerna är tillgängliga för Azure AD-klienter som har licenser för Azure AD Premium eller Enterprise Mobility Suite Azure Multi-Factor Authentication.

## <a name="best-practices-for-an-on-premises-deployment"></a>Metodtips för en lokal distribution
Om ditt företag beslutat att använda sin egen infrastruktur för att aktivera MFA, måste du distribuera ett Azure Multi-Factor Authentication-servern lokalt. MFA-Server-komponenterna visas i följande diagram:

![Standard MFA-serverkomponenter: konsolen, Synkroniseringsmotorn, hanteringsportalen, Molntjänsten](./media/multi-factor-authentication-security-best-practices/server.png) \*inte installeras som standard \** installerad men inte är aktiverad som standard

Azure Multi-Factor Authentication-Server kan skydda molnet resurser och lokala resurser med hjälp av federationstjänsten. Du måste ha AD FS och har den federerade med Azure AD-klienten.
När du konfigurerar servern för flerfunktionsautentisering, Överväg följande information:

* Om du skydda Azure AD-resurser med hjälp av Active Directory Federation Services (AD FS), och sedan på det första verifieringssteget utförs lokalt med hjälp av AD FS. Det andra steget utförs lokalt genom att anspråket tillämpas.
* Du behöver installera Azure Multi-Factor Authentication-servern din AD FS-federationsserver. Multi-Factor Authentication-adaptern för AD FS måste vara installerad på en Windows Server 2012 R2 som kör AD FS. Du kan installera server på en annan dator, så länge som det är en version som stöds och installera AD FS-adaptern separat på din AD FS-federationsserver. 
* Installationsguiden för multi-Factor Authentication AD FS-adaptern skapar en säkerhetsgrupp med namnet PhoneFactor Admins i Active Directory och lägger sedan till AD FS-tjänstkontot i den här gruppen. Verifiera att gruppen PhoneFactor Admins verkligen har skapats i domänkontrollanten och att AD FS-tjänstkontot är medlem av den här gruppen. Om det behövs lägger du till AD FS-tjänstkontot i gruppen PhoneFactor Admins på domänkontrollanten manuellt.

### <a name="user-portal"></a>Användarportalen
Användarportalen kan självbetjäning funktioner och innehåller en fullständig uppsättning funktioner för administration av användaren. Det körs i en webbplats för Internet Information Server (IIS). Använd följande riktlinjer för att konfigurera den här komponenten:

* Använd IIS 6 eller senare
* Installera och registrera ASP.NET v2.0.507207
* Se till att den här servern kan distribueras i ett perimeternätverk

### <a name="app-passwords"></a>Applösenord
Om din organisation är federerat för enkel inloggning med Azure AD och du kommer att använda Azure MFA måste vara medveten om följande information:

* Applösenord verifieras av Azure AD och kringgår därför federation. Federation används bara när du ställer in applösenord.
* Lösenord lagras i organisations-id för den federerade (SSO) användare. Om användaren lämnar företaget, har denna information kan flöda till organisationens id via DirSync. Inaktivering/borttagning av konto kan ta upp till tre timmar att synkroniseringen, vilket fördröjningar inaktivering/borttagning av applösenord i Azure AD.
* Inställningar för lokal klientåtkomstkontroll respekteras inte av applösenord.
* Ingen lokal autentisering loggning/granskning funktion är tillgänglig för applösenord.
* Vissa avancerade arkitektur Designer kräva med en kombination av organisationens användarnamn och lösenord och applösenord med tvåstegsverifiering med klienter, beroende på var de autentiseras. För klienter som autentiseras mot en lokal infrastruktur, använder du en organisations användarnamn och lösenord. För klienter som autentiseras mot Azure AD, använder du applösenordet.
* Användare kan inte skapa applösenord som standard. Om du vill tillåta användare att skapa applösenord, Välj den **låta användarna skapa applösenord för att logga in på icke-webbläsarprogram** alternativet.

## <a name="additional-considerations"></a>Ytterligare överväganden
Använd den här listan ytterligare överväganden och anvisningar för varje komponent som distribuerats lokalt:

- Konfigurera [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md) i Azure Multi-Factor Authentication.
- Konfigurera [RADIUS-autentisering](howto-mfaserver-dir-radius.md) på Azure MFA-servern.
- Installera och konfigurera Azure MFA-Server med [IIS-autentisering](howto-mfaserver-iis.md).
- Installera och konfigurera Azure MFA-Server med [Windows-autentisering](howto-mfaserver-windows.md).
- Installera och konfigurera Azure MFA-Server med [LDAP-autentisering](howto-mfaserver-dir-ldap.md).
- Installera och konfigurera Azure MFA-Server med [Remote Desktop Gateway och Azure Multi-Factor Authentication-servern med hjälp av RADIUS](howto-mfaserver-nps-rdg.md).
- Installera och konfigurera synkronisering mellan Azure MFA-Server och [Windows Server Active Directory](howto-mfaserver-dir-ad.md).
- [Distribuera webbtjänsten Azure Multi-Factor Authentication Server Mobile App](howto-mfaserver-deploy-mobileapp.md).
- [Avancerad VPN-konfiguration med Azure Multi-Factor Authentication](howto-mfaserver-nps-vpn.md) för Cisco ASA, Citrix Netscaler och Juniper/Pulse Secure VPN-installationer med hjälp av LDAP- eller RADIUS.

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar några rekommendationer för Azure MFA, finns men det andra resurser som du kan också använda när du planerar distributionen av MFA. I listan nedan har vissa viktiga artiklar som kan hjälpa dig under den här processen:

* [Rapporter i Azure Multi-Factor Authentication](howto-mfa-reporting.md)
* [Tvåstegsverifiering verifiering registrering experience](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)
* [Vanliga frågor om Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)

