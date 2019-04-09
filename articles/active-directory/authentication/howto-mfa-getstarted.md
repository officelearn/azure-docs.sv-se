---
title: Planera och kör en distribution för Azure Multi-Factor Authentication – Azure Active Directory
description: Microsoft Azure Multi-Factor Authentication distributionsplanering
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b91af553c402cc1cb241e51e2bb2289bf45b1825
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269035"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication"></a>Planera en molnbaserad Azure Multi-Factor Authentication

Personer som ansluter till organisationens resurser i allt mer komplicerade scenarier. Personer ansluta från organisationsägda personliga och offentliga enheter och inaktivera företagsnätverket använder Smartphones, surfplattor, datorer och bärbara datorer, ofta på flera plattformar. I den här uppkopplad, flera enheter och flera plattformar är säkerheten för användarkonton viktigare än någonsin. Lösenord, oavsett deras komplexitet, används alla enheter, nätverk och plattformar som inte längre räcker för att säkerställa säkerheten för användarkontot, särskilt när användarna tenderar att återanvändning av lösenord för konton. Avancerade nätfiske och andra social engineering attacker kan leda till användarnamn och lösenord som publicerats och sålda artiklar över mörka Internet.

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) hjälper dig att skydda åtkomsten till data och program. Det ger ett extra lager av säkerhet med hjälp av en andra formen av autentisering. Organisationer kan använda [villkorlig åtkomst](../conditional-access/overview.md) göra lösningen efter deras specifika behov.

## <a name="prerequisites"></a>Förutsättningar

Det finns nödvändiga problem som du bör överväga innan du startar en distribution av Azure Multi-Factor Authentication.

| Scenario | Krav |
| --- | --- |
| **Endast molnbaserad** identity-miljö med modern autentisering | **Inga ytterligare nödvändiga uppgifter** |
| **Hybrid** identity-scenarier | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) har distribuerats och användaridentiteter synkroniserat eller federerat med lokala Active Directory Domain Services med Azure Active Directory. |
| Lokala äldre program som publicerats för molnåtkomst | Azure AD [Application Proxy](../manage-apps/application-proxy.md) har distribuerats. |
| Med hjälp av Azure MFA med RADIUS-autentisering | En [nätverksprincipserver (NPS)](howto-mfa-nps-extension.md) har distribuerats. |
| Användarna har Microsoft Office 2010 eller tidigare eller Apple Mail för iOS 11 eller tidigare | Uppgradera till [Microsoft Office 2013 eller senare](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) och Apple mail för iOS 12 eller senare. Villkorlig åtkomst stöds inte av äldre autentiseringsprotokoll. |

## <a name="plan-user-rollout"></a>Planera distribution av användaren

Din distributionsplan för MFA bör innehålla en pilotdistribution följt av distribution vågor som ligger inom din support-kapacitet. Börja distribuera genom att tillämpa principer för villkorlig åtkomst till en liten grupp pilotanvändare. När du har utvärderat effekten på pilotanvändare, processen som används och beteenden för registrering, kan du lägga till flera grupper i principen eller lägga till fler användare i de befintliga grupperna.

### <a name="user-communications"></a>Användarkommunikation

Det är viktigt att informera användarna i planerad kommunikation om kommande ändringar, Registreringskrav för Azure MFA och alla nödvändiga användaråtgärder. Vi rekommenderar att kommunikation har utvecklats tillsammans med våra representanter från inom din organisation, till exempel en kommunikation, ändringshantering och personal avdelningar.

Microsoft tillhandahåller [kommunikationsmallar](https://aka.ms/mfatemplates) och [slutanvändardokumentation](../user-help/security-info-setup-signin.md) att utkast för kommunikationen. Du kan hänvisa användarna till [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) att registrera direkt genom att välja den **säkerhetsinformation** länkar på sidan.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Azure Multi-Factor Authentication distribueras genom tvingande principer med villkorlig åtkomst. En [princip för villkorlig åtkomst](../conditional-access/overview.md) kan kräva att användare att utföra multifaktorautentisering när vissa villkor är uppfyllda som:

* Alla användare, en specifik användare, en grupp- eller tilldelade rollen
* Specifikt molnprogram som används
* Enhetsplattform
* Tillståndet för enhet
* Nätverksplats eller geografiskt placerade IP-adress
* Klientprogram
* Inloggningsrisk (kräver Identity Protection)
* Kompatibel enhet
* Hybrid Azure AD-domänansluten enhet
* Godkända klientprogram
 

Använd anpassningsbara affischer och e-postmallar i [multifaktorautentisering distributionen material] lansera multifaktorautentisering för din organisation. (https://www.microsoft.com/en-us/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Aktivera Multi-Factor Authentication med villkorlig åtkomst

Principer för villkorlig åtkomst framtvinga registrering, kräver oregistrerade användare att slutföra registreringen vid första inloggningen, en viktig säkerhetsaspekt.


[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) bidrar både en registreringsprincip för för och automatiserade risk av och relevanta reparationsåtgärder principer i Azure Multi-Factor Authentication-artikel. Principer kan skapas för att tvinga lösenordsändringar när det finns risk för komprometterade identitet eller kräva MFA när en inloggning som anses vara riskfyllda med följande [händelser](../reports-monitoring/concept-risk-events.md):

* Läckta autentiseringsuppgifter
* Inloggningar från anonyma IP-adresser
* Omöjliga resor till ovanliga platser
* Inloggningar från okända platser
* Inloggningar från angripna enheter
* Inloggningar från IP-adresser med misstänkta aktiviteter

Några av de riskhändelser som identifierats av Azure Active Directory Identity Protection sker i realtid och vissa kräver offline bearbetning. Administratörer kan välja att blockera användare som uppvisar riskfyllda beteenden och åtgärda manuellt, kräva ändring av lösenordet eller kräva multifaktorautentisering som en del av deras principer för villkorlig åtkomst.

## <a name="define-network-locations"></a>Definiera nätverksplatser

Vi rekommenderar att organisationer använder villkorlig åtkomst för att definiera sina nätverk med hjälp av [namngivna platser](../conditional-access/location-condition.md#named-locations). Om din organisation använder Identity Protection kan du använda riskbaserade principer i stället för namngivna platser.

### <a name="configuring-a-named-location"></a>Konfigurera en namngiven plats

1. Öppna **Azure Active Directory** i Azure portal
2. Klicka på **villkorlig åtkomst**
3. Klicka på **namngivna platser**
4. Klicka på **ny plats**
5. I den **namn** fältet, ange ett beskrivande namn
6. Välj om du definierar platsen med hjälp av IP-intervall eller länder/regioner
   1. Om du använder IP-intervall
      1. Bestäm om du vill markera platsen som betrott. Inloggning från en betrodd plats minskar en användares inloggningsrisk. Bara markera den här platsen som betrodd om du känner till IP-intervall som angetts är etablerade och trovärdiga inom din organisation.
      2. Ange de IP-intervall
   2. Om du använder länder/regioner
      1. Expandera den nedrullningsbara menyn och markera de länder eller regioner som du vill ange för den här namngivna platsen.
      2. Bestäm om du vill inkludera okända områden. Okända områden är IP-adresser som inte kan mappas till ett land/region.
7. Klicka på **Skapa**

## <a name="plan-authentication-methods"></a>Planera autentiseringsmetoder

Administratörer kan välja den [autentiseringsmetoder](../authentication/concept-authentication-methods.md) att de vill göra tillgängliga för användare. Det är viktigt att mer än en enkel metod så att användarna har en tillgänglig säkerhetskopieringsmetod om främsta metod är inte tillgänglig. Följande metoder är tillgängliga för administratörer att aktivera:

### <a name="notification-through-mobile-app"></a>Meddelande via mobilapp

Ett push-meddelande skickas till Microsoft Authenticator-appen på din mobila enhet. Användaren visar meddelandet och väljer **Godkänn** att slutföra verifieringen. Push-meddelanden via en mobilapp ange alternativet minst störande för användarna. De är också det mest pålitliga och säkra alternativet eftersom de använder en dataanslutning i stället för telefoni.

### <a name="verification-code-from-mobile-app"></a>Verifieringskod från mobilapp

En mobilapp som Microsoft Authenticator-appen genererar en ny OATH-Verifieringskod med 30 sekunders mellanrum. Användaren anger verifieringskoden i i inloggningsgränssnittet. Mobilapp-alternativet kan användas om telefonen har ett data- eller mobila signal eller inte.

### <a name="call-to-phone"></a>Samtal till telefon

Ett automatiserat röstsamtal placeras för användaren. Användaren svarar anropa och tryckningar **#** på att godkänna sin autentisering. Anropet till telefon är en bra metod för meddelande eller verifieringen kod från en mobil app.

### <a name="text-message-to-phone"></a>Textmeddelande till telefon

Ett textmeddelande som innehåller en Verifieringskod skickas till användaren, uppmanas du att ange verifieringskoden i inloggningsgränssnittet.

### <a name="choose-verification-options"></a>Välj verifieringsalternativ

1. Bläddra till **Azure Active Directory**, **användare**, **Multifaktorautentisering**.

   ![Ansluter till Multi-Factor Authentication-portalen från bladet för Azure AD-användare i Azure-portalen](media/howto-mfa-getstarted/users-mfa.png)

1. I den nya fliken som öppnas bläddrar du till **tjänstinställningar**.
1. Under **verifieringsalternativ**, kontrollera alla rutorna för metoder som är tillgängliga för användare.

   ![Konfigurera verifieringsmetoder i inställningar-fliken Multi-Factor Authentication-tjänsten](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klicka på **Spara**.
1. Stäng den **tjänstinställningar** fliken.

## <a name="plan-registration-policy"></a>Planera registreringsprincip

Administratörer måste bestämma hur användare registrerar sina metoder. Organisationer bör [aktivera den nya kombinerade registrerings-upplevelsen](howto-registration-mfa-sspr-combined.md) återställa för Azure MFA och lösenordsåterställning via självbetjäning (SSPR). SSPR kan användarna återställa sina lösenord på ett säkert sätt på samma sätt som de använder för multifaktorautentisering. Vi rekommenderar detta kombineras registrering för närvarande i förhandsversion, eftersom det är en bra upplevelse för användare, med möjlighet att registrera en gång för båda tjänsterna. Aktivera samma metoder för SSPR och Azure MFA kan användarna att vara registrerad för att använda båda funktionerna.

### <a name="registration-with-identity-protection"></a>Registrering med Identity Protection

Om din organisation använder Azure Active Directory Identity Protection, [konfigurera MFA-registreringsprincip](../identity-protection/howto-mfa-policy.md) att uppmana användarna att registrera nästa gång de loggar in interaktivt.

### <a name="registration-without-identity-protection"></a>Registrering utan identity Protection

Om din organisation inte har licenser som aktiverar du Identity Protection, uppmanas användarna att registrera dig nästa gång MFA krävs vid inloggning. Användare kan inte registreras för MFA om de inte använder program som skyddas med MFA. Det är viktigt att hämta alla användare som registrerade så att obehöriga inte kan gissa lösenord för en användare och registrera för MFA för deras räkning effektivt tar kontroll över kontot.

#### <a name="enforcing-registration"></a>Att framtvinga registrering

Följande kan principer för villkorlig åtkomst tvinga användare att registrera sig för Multifaktorautentisering

1. Skapa en grupp, lägga till alla användare som för närvarande inte registrerad.
2. Använder villkorlig åtkomst måste använda multifaktorautentisering för den här gruppen för åtkomst till alla resurser.
3. Med jämna mellanrum, gruppmedlemskapet att omvärdera och ta bort användare som har registrerat från gruppen.

Du kan identifiera registrerade och icke-registrerade Azure MFA-användare med PowerShell-kommandon som förlitar sig på den [MSOnline PowerShell-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identifiera registrerade användare

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identifiera icke-registrerade användare

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

## <a name="plan-conditional-access-policies"></a>Planera principer för villkorlig åtkomst

Om du vill planera strategin princip för villkorlig åtkomst som avgör när MFA och andra kontroller som krävs, referera till [vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md).

Det är viktigt att du förhindrar att av misstag låsas ute från Azure AD-klienten. Du kan minimera effekten av den här oavsiktliga bristen på administrativ åtkomst genom att [skapar två eller flera konton för åtkomst vid akutfall i din klient](../users-groups-roles/directory-emergency-access.md) och utelämnar dem från din princip för villkorlig åtkomst.

### <a name="create-conditional-access-policy"></a>Skapa princip för villkorlig åtkomst

1. Logga in på den [Azure-portalen](https://portal.azure.com) med hjälp av ett globalt administratörskonto.
1. Bläddra till **Azure Active Directory**, **villkorlig åtkomst**.
1. Välj **ny princip**.
1. Ange ett beskrivande namn för principen.
1. Under **användare och grupper**:
   * På den **inkludera** fliken den **alla användare** alternativknapp
   * På den **undanta** fliken, markerar du kryssrutan för **användare och grupper** och välj dina konton för åtkomst vid akutfall.
   * Klicka på **Klar**.
1. Under **Molnappar**väljer den **alla molnappar** alternativknappen.
   * DU KAN OCKSÅ: På den **undanta** -fliken, Välj molnappar som din organisation inte kräver MFA för.
   * Klicka på **Klar**.
1. Under **villkor** avsnittet:
   * DU KAN OCKSÅ: Om du har aktiverat Azure Identity Protection kan välja du att utvärdera inloggningsrisk som en del av principen.
   * DU KAN OCKSÅ: Om du har konfigurerat betrodda platser eller namngivna platser, kan du ange om du vill inkludera eller exkludera dessa platser från principen.
1. Under **bevilja**, se till att den **bevilja åtkomst** alternativknappen har valts.
    * Markera kryssrutan för **kräva multifaktorautentisering**.
    * Klicka på **Välj**.
1. Hoppa över den **Session** avsnittet.
1. Ange den **aktiverar principen** växla till **på**.
1. Klicka på **Skapa**.

![Skapa en princip för villkorlig åtkomst för att aktivera MFA för användare av Azure i pilotgrupp](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>Planera integrering med lokala system

Vissa äldre och lokala program som inte autentiserar direkt mot Azure AD kräver ytterligare åtgärder för att använda MFA, inklusive:

* Äldre lokala program, som kommer att behöva använda Application proxy.
* En lokal RADIUS-program, som måste du använda MFA-adapter med NPS-server.
* En lokal AD FS-program, som kommer att behöva använda MFA-adapter med AD FS 2016.

Program som autentiserar direkt med Azure AD och har modern autentisering (WS-Fed, SAML, OAuth, OpenID Connect) kan göra användning av principer för villkorlig åtkomst direkt.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Använda Azure MFA med Azure AD Application Proxy

Program som förvaras lokalt kan publiceras till din Azure AD-klient [Azure AD Application Proxy](../manage-apps/application-proxy.md) och kan dra nytta av Azure Multi-Factor Authentication om de är konfigurerade för att använda Azure AD förautentisering.

Dessa program omfattas av principer för villkorlig åtkomst som tillämpar Azure Multi-Factor Authentication, precis som alla andra Azure AD-integrerade program.

På samma sätt, om Azure Multi-Factor Authentication tillämpas för alla användarinloggningar, lokala program som publicerats med Azure AD Application Proxy ska skyddas.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrera Azure Multi-Factor Authentication med Network Policy Server

Nätverksprincipserver (NPS)-tillägget för Azure MFA lägger till funktioner för molnbaserad MFA till din infrastruktur för autentisering med hjälp av befintliga servrar. Med NPS-tillägget du kan lägga till telefonsamtal, textmeddelande eller app telefonverifiering till ditt befintliga autentiseringsflödet. Den här integrationen har följande begränsningar:

* Endast authenticator-appen push-meddelanden och röstsamtal kan användas med CHAPv2-protokollet.
* Principer för villkorlig åtkomst kan inte användas.

NPS-tillägget som fungerar som ett kort mellan RADIUS- och molnbaserade Azure MFA för att tillhandahålla en andra faktor autentisering att skydda [VPN](howto-mfa-nps-extension-vpn.md), [Remote Desktop Gateway-anslutningar](howto-mfa-nps-extension-rdg.md), eller andra RADIUS-kompatibla program. Användare att registrera dig för Azure MFA i den här miljön efterfrågas alla autentiseringsförsök, bristen på medelvärde principer för villkorlig åtkomst MFA krävs alltid.

#### <a name="implementing-your-nps-server"></a>Implementera NPS-Server

Om du har en NPS-instans som har distribuerats och används redan, referera till [integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). Om du konfigurerar NPS för första gången, se [nätverksprincipserver (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) anvisningar. Felsökningsanvisningar finns i artikeln [åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Förbered NPS för användare som inte har registrerats för MFA

Välj vad som händer när användare som inte har registrerats med MFA försöker autentisera. Använda registerinställningen `REQUIRE_USER_MATCH` i registersökvägen `HKLM\Software\Microsoft\AzureMFA` att styra hur funktionen. Den här inställningen har ett enda konfigurationsalternativ.

| Nyckel | Värde | Standard |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | SANT / FALSKT | Inte har angetts (motsvarar SANT) |

Syftet med den här inställningen är att avgöra vad du ska göra när en användare inte har registrerats för MFA. Effekter av att ändra den här inställningen visas i tabellen nedan.

| Inställningar | Användarstatus för MFA | Effekter |
| --- | --- | --- |
| Nyckeln finns inte | Inte registrerad | MFA-kontrollen är misslyckades |
| Värdet SANT / inte har angetts | Inte registrerad | MFA-kontrollen är misslyckades |
| Nyckel har angetts till False | Inte registrerad | Autentisering utan MFA |
| Nyckeln inställd på False eller True | Har registrerats | Måste autentisera med MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrera med Active Directory Federation Services

Om din organisation är federerad med Azure AD, kan du använda [Azure Multi-Factor Authentication för att skydda resurser för AD FS](multi-factor-authentication-get-started-adfs.md), både lokalt och i molnet. Azure MFA kan du minska lösenord och är ett säkrare sätt att autentisera. Från och med Windows Server 2016 kan konfigurera du nu Azure MFA för primär autentisering.

Till skillnad från med AD FS i Windows Server 2012 R2 AD FS 2016 Azure MFA-adapter integreras direkt med Azure AD och kräver inte en lokal Azure MFA server. Azure MFA-kort som är inbyggd i Windows Server 2016 och behöver inte en ytterligare information.

När du använder Azure MFA med AD FS 2016 och målprogrammet är omfattas av principen för villkorlig åtkomst, finns det fler överväganden:

* Villkorlig åtkomst är tillgänglig när programmet är en förlitande part i Azure AD federerad med AD FS 2016.
* Villkorlig åtkomst är inte tillgänglig när programmet är en förlitande part till AD FS 2016 och hanteras eller federerad med AD FS 2016.
* Villkorlig åtkomst är inte heller tillgängliga när AD FS 2016 är konfigurerade för att använda Azure MFA som primär autentiseringsmetod.

#### <a name="ad-fs-logging"></a>AD FS-loggning

Standard AD FS 2016 loggning i både Windows-säkerhetsloggen och AD FS-administratören-loggen innehåller information om begäranden om autentisering och deras lyckats eller misslyckats. Händelseloggdata inom dessa händelser indikerar om Azure MFA användes. Till exempel kan AD FS-granskning händelse-ID 1200 innehålla:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Förnya och hantera certifikat

På varje AD FS-servern i den lokala datorn Mina Store debiteras en självsignerat Azure MFA-certifikatet benämnt OU = Microsoft AD FS Azure MFA, som innehåller certifikatets förfallodatum. Kontrollera giltighetsperioden för det här certifikatet på varje AD FS-servern för att fastställa den upphör att gälla.

Om giltighetsperioden för certifikat är snart upphör att gälla, [generera och verifiera ett nytt MFA-certifikat på varje AD FS-servern](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Följande riktlinjer detaljerad information om hur du hanterar Azure MFA-certifikaten på AD FS-servrarna. När du konfigurerar AD FS med Azure MFA, certifikat som genererats via den `New-AdfsAzureMfaTenantCertificate` PowerShell-cmdlet är giltiga i 2 år. Förnya och installera de förnyade certifikaten före förfallodatumet till ovoid störningar i MFA-tjänsten.

## <a name="implement-your-plan"></a>Implementera din Plan

Nu när du har planerat din lösning, kan du implementera genom att följa stegen nedan:

1. Uppfyll alla nödvändiga krav
   1. Distribuera [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) för alla hybridscenarion
   1. Distribuera [Azure AD Application Proxy](../manage-apps/application-proxy.md) för på den lokala appar publicerats för molnåtkomst
   1. Distribuera [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) för någon RADIUS-autentisering
   1. Se till att användare har uppgraderat till versioner som stöds av Microsoft Office med modern autentisering aktiverad
1. Konfigurera den valda [autentiseringsmetoder](#choose-verification-options)
1. Definiera din [med namnet nätverksplatser](../conditional-access/location-condition.md#named-locations)
1. Välj grupper att börja integrera MFA.
1. Konfigurera din [principer för villkorlig åtkomst](#create-conditional-access-policy)
1. Konfigurera MFA-registreringsprincip
   1. [Kombinerade MFA och SSPR](howto-registration-mfa-sspr-combined.md)
   1. Med [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Skicka användarkommunikation och få användarna att registrera sig på [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Håll koll på vem som har registrerats](#identify-non-registered-users)

## <a name="manage-your-solution"></a>Hantera din lösning

Rapporter för Azure MFA

Azure Multi-Factor Authentication ger rapporter via Azure portal:

| Rapport | Plats | Beskrivning |
| --- | --- | --- |
| Användning och bedrägeri aviseringar | Azure AD > inloggningar | Innehåller information om totala användning, Användarsammanfattning och information om användare; samt en historik över bedrägerivarningar som skickats under det angivna datumintervallet. |

## <a name="troubleshoot-mfa-issues"></a>Felsöka problem med MFA

Hitta lösningar på vanliga problem med Azure MFA på den [felsökning av Azure Multi-Factor Authentication-artikeln](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) på Microsoft Support Center.

## <a name="next-steps"></a>Nästa steg

* [Vad är autentiseringsmetoder?](concept-authentication-methods.md)
* [Aktivera konvergerade registrering för Azure Multi-Factor Authentication och Azure AD lösenordsåterställning via självbetjäning](concept-registration-mfa-sspr-converged.md)
* Varför har en användare uppmanas eller uppmanas inte att utföra MFA? Se avsnittet [rapporten för Azure AD-inloggningar i rapporterna i Azure Multi-Factor Authentication-dokumentet](howto-mfa-reporting.md#azure-ad-sign-ins-report).
