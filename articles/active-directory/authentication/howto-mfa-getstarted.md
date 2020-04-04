---
title: Distribuera Azure Multi Factor-autentisering – Azure Active Directory
description: Microsoft Azure MultiFaktorautentiseringsdistributionsplanering
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ae58482ced524958ffcdd6094ae57856d088eaf
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653962"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Planera en molnbaserad distribution av Azure Multi-Factor Authentication

Människor ansluter till organisationsresurser i allt mer komplicerade scenarier. Personer ansluter från organisationsägda, personliga och offentliga enheter på och utanför företagsnätverket med hjälp av smarta telefoner, surfplattor, datorer och bärbara datorer, ofta på flera plattformar. I den här alltid anslutna världen med flera enheter och flera plattformar är säkerheten för användarkonton viktigare än någonsin. Lösenord, oavsett komplexitet, som används på olika enheter, nätverk och plattformar är inte längre tillräckliga för att garantera säkerheten för användarkontot, särskilt när användare tenderar att återanvända lösenord över konton. Sofistikerade nätfiskeattacker och andra sociala ingenjörsattacker kan leda till att användarnamn och lösenord publiceras och säljs över den mörka webben.

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) hjälper till att skydda åtkomsten till data och program. Det ger ett extra säkerhetslager med hjälp av en andra form av autentisering. Organisationer kan använda [villkorlig åtkomst](../conditional-access/overview.md) för att få lösningen att passa deras specifika behov.

## <a name="prerequisites"></a>Krav

Innan du startar en distribution av Azure Multi-Factor Authentication finns det nödvändiga objekt som bör beaktas.

| Scenario | Krav |
| --- | --- |
| **Molnbaserad identitetsmiljö** med modern autentisering | **Inga ytterligare nödvändiga uppgifter** |
| **Scenarier** för hybrididentitet | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) distribueras och användaridentiteter synkroniseras eller federeras med de lokala Active Directory Domain Services med Azure Active Directory. |
| Lokala äldre program som publicerats för molnåtkomst | Azure [AD-programproxy](../manage-apps/application-proxy.md) distribueras. |
| Använda Azure MFA med RADIUS-autentisering | En [NPS (Network Policy Server)](howto-mfa-nps-extension.md) distribueras. |
| Användare har Microsoft Office 2010 eller tidigare, eller Apple Mail för iOS 11 eller tidigare | Uppgradera till [Microsoft Office 2013 eller senare](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) och Apple-e-post för iOS 12 eller senare. Villkorlig åtkomst stöds inte av äldre autentiseringsprotokoll. |

## <a name="plan-user-rollout"></a>Planera användarutrullning

MFA-distributionsplanen bör innehålla en pilotdistribution följt av distributionsvågor som finns inom din supportkapacitet. Börja distributionen med att tillämpa principerna för villkorlig åtkomst på en liten grupp pilotanvändare. När du har utvärderat effekten på pilotanvändarna, process som används och registreringsbeteenden kan du antingen lägga till fler grupper i principen eller lägga till fler användare i de befintliga grupperna.

### <a name="user-communications"></a>Användarkommunikation

Det är viktigt att informera användare, i planerad kommunikation, om kommande ändringar, Azure MFA-registreringskrav och eventuella nödvändiga användaråtgärder. Vi rekommenderar att kommunikation utvecklas i samarbete med representanter inifrån din organisation, till exempel en kommunikation, ändringshantering eller personalavdelningar.

Microsoft tillhandahåller [kommunikationsmallar](https://aka.ms/mfatemplates) och [slutanvändardokumentation](../user-help/security-info-setup-signin.md) för att hjälpa dig att utforma din kommunikation. Du kan skicka [https://myprofile.microsoft.com](https://myprofile.microsoft.com) användare till att registrera sig direkt genom att välja **länkarna säkerhetsinformation** på den sidan.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Azure Multifaktorautentisering distribueras genom att tillämpa principer med villkorlig åtkomst. En [princip för villkorlig åtkomst](../conditional-access/overview.md) kan kräva att användare utför multifaktorautentisering när vissa villkor är uppfyllda, till exempel:

* Alla användare, en viss användare, medlem i en grupp eller tilldelad roll
* Specifika molnprogram som används
* Enhetsplattform
* Enhetens tillstånd
* Nätverksplats eller geografisk IP-adress
* Klientprogram
* Inloggningsrisk (kräver identitetsskydd)
* Kompatibel enhet
* Hybrid Azure AD-ansluten enhet
* Godkänt klientprogram

Använd anpassningsbara affischer och e-postmallar i distributionsmaterial för [multifaktorautentisering](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) för att distribuera multifaktorautentisering till din organisation.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Aktivera multifaktorautentisering med villkorlig åtkomst

Principer för villkorlig åtkomst tillämpar registrering, vilket kräver att oregistrerade användare slutför registreringen vid första inloggningen, vilket är en viktig säkerhetsövervägande.

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) bidrar med både en registreringsprincip för och automatiska principer för riskidentifiering och reparation till Azure Multi-Factor Authentication story. Principer kan skapas för att tvinga lösenordsändringar när det finns ett hot om komprometterade identitet eller kräver MFA när en inloggning bedöms riskabelt av följande [händelser:](../reports-monitoring/concept-risk-events.md)

* Läckta autentiseringsuppgifter
* Inloggningar från anonyma IP-adresser
* Omöjliga resor till ovanliga platser
* Inloggningar från okända platser
* Inloggningar från angripna enheter
* Inloggningar från IP-adresser med misstänkta aktiviteter

Vissa av de riskidentifieringar som identifieras av Azure Active Directory Identity Protection inträffar i realtid och vissa kräver offlinebearbetning. Administratörer kan välja att blockera användare som uppvisar riskfyllda beteenden och åtgärda manuellt, kräver en lösenordsändring eller kräver en multifaktorautentisering som en del av sina principer för villkorlig åtkomst.

## <a name="define-network-locations"></a>Definiera nätverksplatser

Vi rekommenderar att organisationer använder villkorlig åtkomst för att definiera sitt nätverk med [namngivna platser](../conditional-access/location-condition.md#named-locations). Om din organisation använder Identity Protection kan du överväga att använda riskbaserade principer i stället för namngivna platser.

### <a name="configuring-a-named-location"></a>Konfigurera en namngiven plats

1. Öppna **Azure Active Directory** i Azure-portalen
2. Välj **Säkerhet**
3. Under **Hantera**väljer du **Namngivna platser**
4. Välj **ny plats**
5. Ange ett meningsfullt namn i fältet **Namn**
6. Välj om du definierar platsen med *ip-intervall* eller *länder/regioner*
   1. Om du använder *IP-intervall*
      1. Bestäm om du vill *markera som betrodd plats*. Inloggning från en betrodd plats minskar en användares inloggningsrisk. Markera endast den här platsen som betrodd om du vet att IP-intervallen som anges är etablerade och trovärdiga i din organisation.
      2. Ange IP-intervall
   2. Om du använder *länder/regioner*
      1. Expandera rullgardinsmenyn och välj de länder eller regioner som du vill definiera för den här namngivna platsen.
      2. Bestäm om okända områden ska *inkluderas*. Okända områden är IP-adresser som inte kan mappas till ett land/en region.
7. Välj **Skapa**

## <a name="plan-authentication-methods"></a>Planera autentiseringsmetoder

Administratörer kan välja de [autentiseringsmetoder](../authentication/concept-authentication-methods.md) som de vill göra tillgängliga för användare. Det är viktigt att tillåta mer än en enda autentiseringsmetod så att användarna har en säkerhetskopieringsmetod tillgänglig om deras primära metod inte är tillgänglig. Följande metoder är tillgängliga för administratörer att aktivera:

### <a name="notification-through-mobile-app"></a>Avisering via mobilapp

Ett push-meddelande skickas till Microsoft Authenticator-appen på din mobila enhet. Användaren visar meddelandet och väljer **Godkänn** för fullständig verifiering. Push-meddelanden via en mobilapp är det minst påträngande alternativet för användarna. De är också det mest tillförlitliga och säkra alternativet eftersom de använder en dataanslutning i stället för telefoni.

> [!NOTE]
> Om din organisation har personal som arbetar i eller reser till Kina fungerar inte **metoden Avisering via mobilapp** på **Android-enheter** i det landet. Alternativa metoder bör göras tillgängliga för dessa användare.

### <a name="verification-code-from-mobile-app"></a>Verifieringskod från mobilapp

En mobilapp som Microsoft Authenticator-appen genererar en ny OATH-verifieringskod var 30:e sekund. Användaren anger verifieringskoden i inloggningsgränssnittet. Alternativet för mobilapp kan användas oavsett om telefonen har en data- eller mobilsignal eller mobilsignal.

### <a name="call-to-phone"></a>Ringa till telefon

Ett automatiserat röstsamtal görs till användaren. Användaren besvarar samtalet och **#** trycker på telefonens knappsats för att godkänna deras autentisering. Ring till telefon är en bra säkerhetskopieringsmetod för meddelande eller verifieringskod från en mobilapp.

### <a name="text-message-to-phone"></a>Sms till telefon

Ett textmeddelande som innehåller en verifieringskod skickas till användaren, användaren uppmanas att ange verifieringskoden i inloggningsgränssnittet.

### <a name="choose-verification-options"></a>Välj verifieringsalternativ

1. Bläddra till **Azure Active Directory**, **Användare**, **Multifaktorautentisering**.

   ![Komma åt multifaktorautentiseringsportalen från Azure AD-användare i Azure-portalen](media/howto-mfa-getstarted/users-mfa.png)

1. På den nya fliken som öppnar bläddra till **tjänstinställningar**.
1. Under **verifieringsalternativ**kontrollerar du alla rutor för metoder som är tillgängliga för användare.

   ![Konfigurera verifieringsmetoder på fliken Tjänstinställningar för multifaktorautentisering](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klicka på **Spara**.
1. Stäng fliken **tjänstinställningar.**

## <a name="plan-registration-policy"></a>Planera registreringspolicy

Administratörer måste bestämma hur användarna ska registrera sina metoder. Organisationer bör [aktivera den nya kombinerade registreringsupplevelsen](howto-registration-mfa-sspr-combined.md) för Azure MFA och självbetjäningslösenordsåterställning (SSPR). SSPR tillåter användare att återställa sitt lösenord på ett säkert sätt med samma metoder som de använder för multifaktorautentisering. Vi rekommenderar den här kombinerade registreringen, som för närvarande är i offentlig förhandsversion, eftersom det är en fantastisk upplevelse för användarna, med möjlighet att registrera sig en gång för båda tjänsterna. Om du aktiverar samma metoder för SSPR och Azure MFA kan användarna registreras för att använda båda funktionerna.

### <a name="registration-with-identity-protection"></a>Registrering med identitetsskydd

Om din organisation använder Azure Active Directory Identity Protection [konfigurerar du MFA-registreringsprincipen](../identity-protection/howto-mfa-policy.md) så att användarna uppmanas att registrera sig nästa gång de loggar in interaktivt.

### <a name="registration-without-identity-protection"></a>Registrering utan identitetsskydd

Om din organisation inte har licenser som aktiverar identitetsskydd uppmanas användarna att registrera sig nästa gång MFA krävs vid inloggning. Användare kanske inte är registrerade för MFA om de inte använder program som skyddas med MFA. Det är viktigt att få alla användare registrerade så att dåliga aktörer inte kan gissa lösenordet för en användare och registrera sig för MFA för deras räkning, effektivt ta kontroll över kontot.

#### <a name="enforcing-registration"></a>Verkställa registrering

Med hjälp av följande steg kan en princip för villkorlig åtkomst tvinga användare att registrera sig för multifaktorautentisering

1. Skapa en grupp, lägg till alla användare som för närvarande inte är registrerade.
2. Med villkorlig åtkomst framtvingar du multifaktorautentisering för den här gruppen för åtkomst till alla resurser.
3. Regelbundet, omvärdera gruppmedlemskapet och ta bort användare som har registrerat sig från gruppen.

Du kan identifiera registrerade och icke-registrerade Azure MFA-användare med PowerShell-kommandon som är beroende av [MSOnline PowerShell-modulen](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identifiera registrerade användare

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identifiera icke-registrerade användare

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertera användare från MFA per användare till villkorsstyrd åtkomstbaserad MFA

Om användarna har aktiverats med hjälp av azure multifaktorautentisering per användare kan följande PowerShell hjälpa dig att göra konverteringen till villkorlig åtkomstbaserad Azure Multi-Factor-autentisering.

Kör det här PowerShell i ett ISE-fönster eller spara som ett . PS1-filen ska köras lokalt.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Vi har nyligen ändrat beteende och PowerShell skript ovan i enlighet därmed. Tidigare sparade skriptet mfa-metoderna, inaktiverade MFA och återställde metoderna. Detta är inte längre nödvändigt nu när standardbeteendet för inaktivera inte rensar metoderna.

## <a name="plan-conditional-access-policies"></a>Planera principer för villkorlig åtkomst

Om du vill planera principstrategin för villkorlig åtkomst, som avgör när MFA och andra kontroller krävs, läser du [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md).

Det är viktigt att du förhindrar att bli oavsiktligt utelåst från din Azure AD-klientorganisation. Du kan minska effekten av den här oavsiktliga bristen på administrativ åtkomst genom [att skapa två eller flera konton för nödåtkomst i din klientorganisation](../users-groups-roles/directory-emergency-access.md) och utesluta dem från principen villkorlig åtkomst.

### <a name="create-conditional-access-policy"></a>Skapa princip för villkorlig åtkomst

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett globalt administratörskonto.
1. Bläddra till **Azure Active Directory** > **Security** > **Conditional Access**.
1. Välj **Ny princip**.
   ![Skapa en princip för villkorlig åtkomst för att aktivera MFA för Azure-portalanvändare i pilotgruppen](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Ange ett meningsfullt namn för din policy.
1. Under **användare och grupper:**
   * Välj alternativknappen Alla **användare** på fliken **Inkludera**
   * Markera kryssrutan för **Användare och grupper** på fliken **Uteslut** och välj dina konton för nödåtkomst.
   * Klicka på **Klar**.
1. Under **Cloud-appar**väljer du alternativknappen **Alla molnappar.**
   * VALFRITT: På fliken **Uteslut** väljer du molnappar som din organisation inte kräver MFA för.
   * Klicka på **Klar**.
1. Under **Villkor** avsnitt:
   * VALFRITT: Om du har aktiverat Azure Identity Protection kan du välja att utvärdera inloggningsrisk som en del av principen.
   * VALFRITT: Om du har konfigurerat betrodda platser eller namngivna platser kan du ange att inkludera eller utesluta dessa platser från principen.
1. Kontrollera att alternativknappen **Bevilja åtkomst** är markerad under **Bevilja.**
    * Markera kryssrutan **Kräv multifaktorautentisering**.
    * Klicka på **Markera**.
1. Hoppa över avsnittet **Session.**
1. Ange **växlingsknappen Aktivera** princip till **På**.
1. Klicka på **Skapa**.

## <a name="plan-integration-with-on-premises-systems"></a>Planera integration med lokala system

Vissa äldre och lokala program som inte autentiserar direkt mot Azure AD kräver ytterligare steg för att använda MFA, inklusive:

* Äldre lokala program, som måste använda programproxy.
* Lokala RADIUS-program, som måste använda MFA-kort med NPS-servern.
* Lokala AD FS-program, som måste använda MFA-kort med AD FS 2016 eller nyare.

Program som autentiserar direkt med Azure AD och har modern autentisering (WS-Fed, SAML, OAuth, OpenID Connect) kan använda principer för villkorlig åtkomst direkt.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Använda Azure MFA med Azure AD-programproxy

Program som finns lokalt kan publiceras till din Azure AD-klient via [Azure AD Application Proxy](../manage-apps/application-proxy.md) och kan dra nytta av Azure Multi-Factor Authentication om de är konfigurerade för att använda Azure AD-förautentisering.

Dessa program omfattas av principer för villkorlig åtkomst som framtvingar Azure Multi-Factor Authentication, precis som alla andra Azure AD-integrerade program.

På samma sätt, om Azure Multi-Factor Autentisering tillämpas för alla användare inloggningar, lokala program som publiceras med Azure AD Application Proxy kommer att skyddas.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integrera Azure Multi Factor-autentisering med nätverksprincipserver

NPS-tillägget (Network Policy Server) för Azure MFA lägger till molnbaserade MFA-funktioner i autentiseringsinfrastrukturen med hjälp av dina befintliga servrar. Med NPS-tillägget kan du lägga till telefonsamtal, sms eller telefonappverifiering i ditt befintliga autentiseringsflöde. Denna integration har följande begränsningar:

* Med CHAPv2-protokollet stöds endast autentiseringsappknappsmeddelanden och röstsamtal.
* Det går inte att tillämpa principer för villkorlig åtkomst.

NPS-tillägget fungerar som ett kort mellan RADIUS och molnbaserade Azure MFA för att tillhandahålla en andra autentiseringsfaktor för att skydda [VPN,](howto-mfa-nps-extension-vpn.md) [Fjärrskrivbordsgatewayanslutningar](howto-mfa-nps-extension-rdg.md)eller andra RADIUS-program. Användare som registrerar sig för Azure MFA i den här miljön kommer att utmanas för alla autentiseringsförsök, bristen på principer för villkorlig åtkomst innebär att MFA alltid krävs.

#### <a name="implementing-your-nps-server"></a>Implementera NPS-servern

Om du redan har en NPS-instans distribuerad och används refererar du till [Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). Om du konfigurerar NPS för första gången läser du [NPS (Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) för instruktioner. Felsökningsvägledning finns i artikeln [Lös felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Förbereda NPS för användare som inte är registrerade för MFA

Välj vad som händer när användare som inte är registrerade med MFA försöker autentisera. Använd registerinställningen `REQUIRE_USER_MATCH` i registersökvägen `HKLM\Software\Microsoft\AzureMFA` för att styra funktionsbeteendet. Den här inställningen har ett enda konfigurationsalternativ.

| Nyckel | Värde | Default |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | SANT / FALSKT | Inte inställd (motsvarande SANT) |

Syftet med den här inställningen är att avgöra vad som ska göra när en användare inte är registrerad för MFA. Effekterna av att ändra den här inställningen visas i tabellen nedan.

| Inställningar | MFA-status för användare | Effekter |
| --- | --- | --- |
| Nyckeln finns inte | Inte registrerad | MFA utmaning misslyckas |
| Värdet inställt på Sant/inte inställt | Inte registrerad | MFA utmaning misslyckas |
| Nyckel inställd på Falskt | Inte registrerad | Autentisering utan MFA |
| Nyckel inställd på Falskt eller Sant | Registrerad | Måste autentisera med MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrera med Active Directory Federation Services

Om din organisation är federerad med Azure AD kan du använda [Azure Multi-Factor Authentication för att skydda AD FS-resurser](multi-factor-authentication-get-started-adfs.md), både lokalt och i molnet. Med Azure MFA kan du minska lösenorden och tillhandahålla ett säkrare sätt att autentisera. Från och med Windows Server 2016 konfigurera du nu Azure MFA för primär autentisering.

Till skillnad från AD FS i Windows Server 2012 R2 integreras AD FS 2016 Azure MFA-kortet direkt med Azure AD och kräver inte en lokal Azure MFA-server. Azure MFA-kortet är inbyggt i Windows Server 2016 och det finns inget behov av ytterligare installation.

När du använder Azure MFA med AD FS 2016 och målprogrammet omfattas av principen för villkorlig åtkomst finns det ytterligare överväganden:

* Villkorlig åtkomst är tillgänglig när programmet är en förlitande part till Azure AD, federerad med AD FS 2016 eller nyare.
* Villkorlig åtkomst är inte tillgänglig när programmet är en förlitande part till AD FS 2016 eller AD FS 2019 och hanteras eller federeras med AD FS 2016 eller AD FS 2019.
* Villkorlig åtkomst är inte heller tillgänglig när AD FS 2016 eller AD FS 2019 är konfigurerad för att använda Azure MFA som primär autentiseringsmetod.

#### <a name="ad-fs-logging"></a>AD FS-loggning

Standard AD FS 2016 och 2019 som loggar in både i Windows säkerhetslogg och AD FS-administratörsloggen innehåller information om autentiseringsbegäranden och deras framgång eller misslyckande. Händelseloggdata i dessa händelser anger om Azure MFA har använts. Ett AD FS-granskningshändelse-ID 1200 kan till exempel innehålla:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Förnya och hantera certifikat

På varje AD FS-server, i den lokala datorn My Store, kommer det att finnas ett självsignerat Azure MFA-certifikat med titeln OU=Microsoft AD FS Azure MFA, som innehåller certifikatets utgångsdatum. Kontrollera giltighetsperioden för det här certifikatet på varje AD FS-server för att fastställa utgångsdatumet.

Om giltighetstiden för dina certifikat närmar sig förfallodatum [genererar och verifierar du ett nytt MFA-certifikat på varje AD FS-server](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Följande vägledning beskriver hur du hanterar Azure MFA-certifikat på dina AD FS-servrar. När du konfigurerar AD FS med Azure MFA är certifikaten som genereras via PowerShell-cmdlet giltiga i `New-AdfsAzureMfaTenantCertificate` 2 år. Förnya och installera de förnyade certifikaten före utgångsdatumet för äggformade störningar i MFA-tjänsten.

## <a name="implement-your-plan"></a>Implementera din plan

Nu när du har planerat din lösning kan du implementera genom att följa stegen nedan:

1. Uppfylla alla nödvändiga förutsättningar
   1. Distribuera [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) för alla hybridscenarier
   1. Distribuera [Azure AD-programproxy](../manage-apps/application-proxy.md) för i alla lokala appar som publiceras för molnåtkomst
   1. Distribuera [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) för alla RADIUS-autentiseringar
   1. Se till att användarna har uppgraderat till versioner av Microsoft Office som stöds med modern autentisering aktiverad
1. Konfigurera valda [autentiseringsmetoder](#choose-verification-options)
1. Definiera dina [namngivna nätverksplatser](../conditional-access/location-condition.md#named-locations)
1. Välj grupper som ska börja distribuera MFA.
1. Konfigurera [principer för villkorlig åtkomst](#create-conditional-access-policy)
1. Konfigurera din MFA-registreringsprincip
   1. [Kombinerad MFA och SSPR](howto-registration-mfa-sspr-combined.md)
   1. Med [identitetsskydd](../identity-protection/howto-mfa-policy.md)
1. Skicka användarkommunikation och få användare att registrera sig på[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Håll reda på vem som är registrerad](#identify-non-registered-users)

> [!TIP]
> Användare av myndighetsmoln kan registrera sig på[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Hantera din lösning

Rapporter för Azure MFA

Azure Multi Factor Authentication tillhandahåller rapporter via Azure-portalen:

| Rapport | Location | Beskrivning |
| --- | --- | --- |
| Användnings- och bedrägerivarningar | Azure AD > inloggningar | Innehåller information om övergripande användning, användarsammanfattning och användarinformation. samt en historik över bedrägerivarningar som lämnats in under det angivna datumintervallet. |

## <a name="troubleshoot-mfa-issues"></a>Felsöka MFA-problem

Hitta lösningar på vanliga problem med Azure MFA i [artikeln Felsökning av Azure Multi Factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) i Microsoft Support Center.

## <a name="next-steps"></a>Nästa steg

* [Vad är autentiseringsmetoder?](concept-authentication-methods.md)
* [Aktivera konvergerad registrering för Azure Multi-Factor Authentication och Azure AD självbetjäningslösenordsåterställning](concept-registration-mfa-sspr-converged.md)
* Varför blev en användare ombedd att utföra MFA? Se avsnittet [Azure AD-inloggningsrapport i dokumentet Rapporter i Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
