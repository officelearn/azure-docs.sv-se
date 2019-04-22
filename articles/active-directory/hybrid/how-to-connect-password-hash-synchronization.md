---
title: Implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering | Microsoft Docs
description: Innehåller information om hur synkronisering av lösenordshash fungerar och hur du ställer in.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 146fdc3ca2af708a96e6b9a604493eb63c2e6530
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58916384"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering
Den här artikeln innehåller information du behöver för att synkronisera dina lösenord från en lokal Active Directory-instans till en molnbaserad Azure Active Directory (Azure AD)-instans.

## <a name="how-password-hash-synchronization-works"></a>Så här fungerar synkronisering av lösenordshash
Active Directory domain service lösenord ska lagras i form av en representation för hash-värde, av faktiska användarens lösenord. Ett hash-värde är ett resultat av en enkelriktad matematisk funktion (den *hash-algoritm*). Det finns ingen metod för att återställa resultatet av en envägsfunktion till versionen av ett lösenord med oformaterad text. Du kan inte använda en lösenordshash för att logga in på ditt lokala nätverk.

Om du vill synkronisera ditt lösenord extraherar Azure AD Connect-synkronisering lösenords-hash från en lokal Active Directory-instans. Behandling av extra säkerhet tillämpas på lösenordets hash-värde innan den synkroniseras till tjänsten Azure Active Directory-autentisering. Lösenord synkroniseras på basis av per användare och i kronologisk ordning.

Det faktiska dataflödet av processen för Lösenordssynkronisering hash liknar synkronisering av användardata. Dock synkroniseras lösenord oftare än fönstret standard directory synkronisering för andra attribut. Processen för Lösenordssynkronisering hash körs varannan minut. Du kan inte ändra frekvensen för den här processen. När du synkroniserar ett lösenord, skrivs det befintliga lösenordet i molnet.

Första gången du aktiverar funktionen lösenord hash-synkronisering utförs en inledande synkronisering av lösenorden för alla omfattade användare. Du kan inte explicit definiera en delmängd av lösenord som du vill synkronisera.

När du ändrar ett lösenord för lokala synkroniseras det uppdaterade lösenordet, oftast på bara några minuter.
Funktionen lösenord hash-synkronisering försöker automatiskt igen misslyckade synkroniseringsförsök. Om ett fel inträffar vid ett försök att synkronisera ett lösenord, loggas ett fel i din Loggboken.

Synkronisering av lösenord har ingen inverkan på den användare som för närvarande är inloggad.
Din session med cloud service påverkas inte direkt av en synkroniserade lösenordsändring som uppstår när du är inloggad i, en molntjänst. När Molntjänsten kräver att autentisera igen, måste du dock ange ditt nya lösenord används.

En användare måste ange sina inloggningsuppgifter en gång för att autentisera till Azure AD, oavsett om de har loggat in till företagsnätverket. Det här mönstret kan arbetskostnaderna minimeras, men om användaren väljer jag vill förbli inloggad i (KMSI) markerar du kryssrutan vid inloggning. Det här alternativet anger en sessions-cookie som kringgår autentisering i 180 dagar. KMSI beteende kan aktiveras eller inaktiveras av Azure AD-administratör. Du kan dessutom minska frågor för lösenord genom att aktivera [sömlös SSO](how-to-connect-sso.md), som loggar automatiskt användare när de är på sina företagets enheter som är anslutna till företagsnätverket.

> [!NOTE]
> Lösenordssynkronisering stöds endast för objekt typ av användare i Active Directory. Det finns inte stöd för iNetOrgPerson-objekttypen.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Detaljerad beskrivning av hur lösenordshashsynkronisering fungerar
I följande avsnitt beskrivs, djupgående, hur synkronisering av lösenordshash fungerar mellan Active Directory och Azure AD.

![Detaljerad lösenord flöde](./media/how-to-connect-password-hash-synchronization/arch3b.png)


1. Varannan minut, lösenord hash-synkroniseringsagenten på AD Connect-serverbegäranden lagras lösenords-hash (unicodePwd-attributet) från en Domänkontrollant.  Den här begäran är via standarden [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) replikering protokoll som används för att synkronisera data mellan domänkontrollanter. Kontot måste ha Replikera katalogändringar och replikera alla katalogändringar AD behörigheter (som standard på installation) att hämta lösenordet hashvärden.
2. Innan du skickar, krypterar domänkontrollanten MD4 lösenordets hash-värde med hjälp av en nyckel som är en [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) hash för RPC-nyckeln och en salt. Den skickar sedan resultatet till synkroniseringsagenten lösenordshash över RPC. Domänkontrollanten skickar även saltet till synkroniseringsagenten med hjälp av DC replikering protokollet, så att agenten kommer att kunna dekryptera kuvertet.
3.  När lösenordshash-synkroniseringsagent har krypterade kuvert, används [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) och salt att generera en nyckel för att dekryptera den mottagna data tillbaka till sitt ursprungliga MD4-format. Lösenordshash-synkroniseringsagent har aldrig åtkomst till lösenordet i klartext. Lösenord hash-synkronisering agentens användningen av MD5 gäller enbart för replikering protokollkompatibilitet med domänkontrollanten och den används endast lokalt mellan domänkontrollanten och lösenordshash-synkroniseringsagent.
4.  Lösenordshash-synkroniseringsagent expanderar 16 byte binary lösenordets hash-värde till 64 byte först konverterar en hash till en 32-bytes hexadecimal sträng, som sedan konvertera den här strängen tillbaka till binärformat med UTF-16-kodning.
5.  Lösenordshash-synkroniseringsagent lägger till en per användare salt, som består av en längd i 10-byte-salt till 64-byte binary vilket ytterligare skyddar den ursprungliga hashen.
6.  Lösenord hash-synkroniseringsagenten kombinerar sedan MD4-hash plus den per användaren salt och indata det till den [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) funktion. 1000 iterationer av den [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) nycklad hashalgoritm används. 
7.  Lösenord hash-synkroniseringsagenten tar emot den resulterande 32 byte-hashen och sammanfogar både den per användaren salt och antalet SHA256 iterationer till den (för användning av Azure AD), sedan överför strängen från Azure AD Connect till Azure AD via SSL.</br> 
8.  När en användare försöker logga in på Azure AD och anger sitt lösenord, körs lösenordet via samma MD4 + salt + PBKDF2 + HMAC-SHA256-processen. Om den resulterande hashen matchar hashen lagras i Azure AD, användaren har angett rätt lösenord och autentiseras. 

>[!Note] 
>Den ursprungliga MD4-hashen överförs inte till Azure AD. I stället överförs SHA256-hash för den ursprungliga MD4-hashen. Därför om hash-värdet lagras i Azure AD hämtas kan den inte användas i en lokal pass-the-hash-attack.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Hur synkronisering av lösenordshash fungerar med Azure Active Directory Domain Services
Du kan också använda funktionen lösenord hash-synkronisering för att synkronisera dina lokala lösenord till [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). I det här scenariot autentiserar dina användare i molnet med metoderna som är tillgängliga i din lokala Active Directory-instans i Azure Active Directory Domain Services-instans. Upplevelsen av det här scenariot är ungefär som att använda den Active Directory ADMT (Migration Tool) i en lokal miljö.

### <a name="security-considerations"></a>Säkerhetsöverväganden
Vid synkronisering av lösenord exponeras inte textformat-versionen av ditt lösenord till Azure AD synkroniseringsfunktionen lösenordshash eller någon av de associera tjänsterna.

Användarautentisering utförs mot Azure AD i stället för mot organisationens egna Active Directory-instans. SHA256 lösenord data som lagras i Azure AD – en hash av den ursprungliga MD4-hashen--är säkrare än vad som lagras i Active Directory. Ytterligare, eftersom den här SHA256-hash inte kan dekrypteras så den inte kan föras tillbaka till företagets Active Directory-miljö och visas som ett giltigt lösenord i en pass-the-hash-attack.

### <a name="password-policy-considerations"></a>Lösenord för att tänka på vid
Det finns två typer av principer för lösenord som påverkas genom att aktivera synkronisering av lösenordshash:

* Komplexiteten lösenordsprincip
* Förfalloprincipen för lösenord

#### <a name="password-complexity-policy"></a>Komplexiteten lösenordsprincip  
När synkronisering av lösenordshash har aktiverats, principändringar komplexitet lösenordsprinciper i din lokala Active Directory-instans komplexiteten i molnet för synkroniserade användare. Du kan använda alla giltiga lösenord från din lokala Active Directory-instans till Azure AD-tjänster.

> [!NOTE]
> Lösenord för användare som skapas direkt i molnet omfattas fortfarande lösenordsprinciper enligt definitionen i molnet.

#### <a name="password-expiration-policy"></a>Förfalloprincipen för lösenord  
Om en användare finns i omfattningen för synkronisering av lösenordshash, lösenordet för molnet är inställd på *upphör aldrig att gälla*.

Du kan fortsätta att logga in på cloud services med hjälp av synkroniserade lösenord som har upphört att gälla i din lokala miljö. Lösenordet cloud uppdateras nästa gång du ändrar lösenordet i den lokala miljön.

#### <a name="account-expiration"></a>Kontots giltighetstid
Om din organisation använder attributet accountExpires som en del av hantering av användarkonton, synkroniseras inte det här attributet till Azure AD. Därför kan aktiveras en Active Directory-konto som har upphört att gälla i en miljö som konfigurerats för synkronisering av lösenordshash fortfarande i Azure AD. Vi rekommenderar att en arbetsflödesåtgärd ska utlösa ett PowerShell-skript som inaktiverar användarens Azure AD-konto om konton som har upphört att gälla (Använd den [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) cmdlet). Däremot när kontot är aktiverat bör Azure AD-instansen aktiveras.

### <a name="overwrite-synchronized-passwords"></a>Skriv över synkroniserade lösenord
En administratör återställa manuellt ditt lösenord med hjälp av Windows PowerShell.

I det här fallet det nya lösenordet åsidosätter lösenordet synkroniserade och alla lösenordsprinciper som definierats i molnet som används för det nya lösenordet.

Om du ändrar ditt lösenord för lokala återigen det nya lösenordet synkroniseras till molnet och åsidosätter det uppdaterade lösenordet.

Synkronisering av lösenord har ingen inverkan på Azure-användare som är inloggad. Din session med cloud service påverkas inte direkt av en synkroniserade lösenordsändring som uppstår medan du är inloggad på en molntjänst. KMSI utökar varaktigheten för den här skillnaden. När Molntjänsten kräver att autentisera igen, måste du ange ditt nya lösenord.

### <a name="additional-advantages"></a>Ytterligare fördelar

- I allmänhet är det enklare att implementera än en federationstjänst med synkronisering av lösenordshash. Det kräver inte några ytterligare servrar och eliminerar beroendet av en högtillgänglig federationstjänsten att autentisera användare.
- Synkronisering av lösenordshash kan även aktiveras förutom federation. Den kan användas som reserv om din federationstjänst uppstår ett avbrott.

## <a name="enable-password-hash-synchronization"></a>Aktivera synkronisering av lösenordshash

>[!IMPORTANT]
>Om du migrerar från AD FS (eller andra tekniker för federation) till synkronisering av Lösenordshash, rekommenderar vi att du följer våra detaljerad Distributionsguide publicerade [här](https://aka.ms/adfstophsdpdownload).

När du installerar Azure AD Connect med hjälp av den **standardinställningar** , synkronisering av lösenordshash är automatiskt aktiverat. Mer information finns i [komma igång med Azure AD Connect med standardinställningar](how-to-connect-install-express.md).

Om du använder anpassade inställningar när du installerar Azure AD Connect finns synkronisering av lösenordshash på sidan för användaren. Mer information finns i [anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

![Aktiverar synkronisering av lösenordshash](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synkronisering av lösenordshash och FIPS
Om servern har låsts av enligt FIPS Federal Information Processing Standard (), är MD5 inaktiverad.

**Om du vill aktivera MD5 för synkronisering av lösenordshash, utför du följande steg:**

1. Gå till %programfiles%\Azure AD Sync\Bin.
2. Öppna miiserver.exe.config.
3. Gå till noden configuration/körning i slutet av filen.
4. Lägg till följande nod: `<enforceFIPSPolicy enabled="false"/>`
5. Spara ändringarna.

Referens är det här kodfragmentet hur den ska se ut:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Information om säkerhet och FIPS finns i [hash-synkronisering för Azure AD-lösenord, kryptering och FIPS-kompatibilitet](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Felsök synkronisering av lösenordshash
Om du har problem med synkronisering av lösenordshash, se [Felsök synkronisering av lösenordshash](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect-synkronisering: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
* [Hämta en stegvis distributionsplan för att migrera från AD FS till synkronisering av Lösenordshash](https://aka.ms/authenticationDeploymentPlan)
