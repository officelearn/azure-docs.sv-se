---
title: Implementera synkronisering av lösenordsh hash med Azure AD Connect-synkronisering | Microsoft-dokument
description: Innehåller information om hur synkronisering av lösenord hash-synkronisering fungerar och hur du konfigurerar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: c41b11ab65f5710d338ce0041579e1eb4678ec42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331371"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implement password hash synchronization with Azure AD Connect sync (Implementera synkronisering av lösenordshash med Azure AD Connect-synkronisering)
Den här artikeln innehåller information som du behöver för att synkronisera dina användarlösenord från en lokal Active Directory-instans till en molnbaserad Azure Active Directory (Azure AD)-instans.

## <a name="how-password-hash-synchronization-works"></a>Så här fungerar hash-synkronisering
Active Directory-domäntjänsten lagrar lösenord i form av en hash-värderepresentation, för det faktiska användarlösenordet. Ett hash-värde är ett resultat av en enkelriktad matematisk funktion *(hash-algoritmen).* Det finns ingen metod för att återställa resultatet av en envägsfunktion till versionen av ett lösenord med oformaterad text. 

Om du vill synkronisera ditt lösenord extraherar Azure AD Connect-synkroniseringen ditt lösenordshh från den lokala Active Directory-instansen. Extra säkerhetsbearbetning tillämpas på lösenordshhenden innan den synkroniseras med Azure Active Directory-autentiseringstjänsten. Lösenord synkroniseras per användare och i kronologisk ordning.

Det faktiska dataflödet för synkroniseringen av lösenord hash-synkronisering liknar synkroniseringen av användardata. Lösenord synkroniseras dock oftare än standardfönstret för katalogsynkronisering för andra attribut. Synkroniseringsprocessen för lösenord hash körs varannan minut. Du kan inte ändra frekvensen för den här processen. När du synkroniserar ett lösenord skriver det över det befintliga molnet-lösenordet.

Första gången du aktiverar funktionen för synkronisering av lösenord hash-synkronisering, utförs en första synkronisering av lösenorden för alla användare i omfattning. Du kan inte uttryckligen definiera en delmängd av användarlösenord som du vill synkronisera. Men om det finns flera kopplingar är det möjligt att inaktivera lösenord hash-synkronisering för vissa kopplingar men inte andra som använder cmdlet för [Set-ADSyncAADPasswordSyncConfiguration.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant)

När du ändrar ett lokalt lösenord synkroniseras det uppdaterade lösenordet, oftast på några minuter.
Synkroniseringsfunktionen för lösenord hash-synkronisering försöker automatiskt misslyckade synkroniseringsförsök. Om ett fel uppstår under ett försök att synkronisera ett lösenord loggas ett fel i loggboken.

Synkroniseringen av ett lösenord påverkar inte den användare som för närvarande är inloggad.
Din aktuella molntjänstsession påverkas inte omedelbart av en synkroniserad lösenordsändring som sker, medan du är inloggad, till en molntjänst. Men när molntjänsten kräver att du autentiserar igen måste du ange ditt nya lösenord.

En användare måste ange sina företagsautentiseringsuppgifter en andra gång för att autentisera till Azure AD, oavsett om de är inloggade i sitt företagsnätverk. Det här mönstret kan dock minimeras om användaren markerar kryssrutan Håll mig inloggad (KMSI) vid inloggning. Det här valet anger en sessionscookie som kringgår autentiseringen i 180 dagar. KMSI-beteende kan aktiveras eller inaktiveras av Azure AD-administratören. Dessutom kan du minska lösenordsuppmaningar genom att aktivera [Seamless SSO](how-to-connect-sso.md), som automatiskt signerar användare när de är på sina företagsenheter som är anslutna till företagets nätverk.

> [!NOTE]
> Lösenordssynkronisering stöds bara för objekttypsanvändaren i Active Directory. Det stöds inte för iNetOrgPerson-objekttypen.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Detaljerad beskrivning av hur synkronisering av lösenord hash-synkronisering fungerar

I följande avsnitt beskrivs, djupgående, hur synkronisering av lösenord hash-synkronisering fungerar mellan Active Directory och Azure AD.

![Detaljerat lösenordsflöde](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Varannan minut begär lösenordshenschsynkroniseringsagenten på AD Connect-servern lagrade lösenords hashar (unicodePwd-attributet) från en domänkontrollant.  Den här begäran sker via det standardiserade [MS-DRSR-replikeringsprotokoll](https://msdn.microsoft.com/library/cc228086.aspx) som används för att synkronisera data mellan DC:er. Tjänstkontot måste ha replikerade katalogändringar och replikera katalogändringar alla AD-behörigheter (beviljas som standard vid installationen) för att få lösenordshashar.
2. Innan den sändas krypterar domänkontrollanten MD4-lösenordshhen med hjälp av en nyckel som är en [MD5-hash](https://www.rfc-editor.org/rfc/rfc1321.txt) för RPC-sessionsnyckeln och ett salt. Det skickar sedan resultatet till lösenordet hash synkronisering agent över RPC. Domänkontrollanten skickar också saltet till synkroniseringsagenten med hjälp av DC-replikeringsprotokollet, så att agenten kan dekryptera kuvertet.
3. När lösenord hash-synkroniseringsagenten har det krypterade kuvertet använder den [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) och saltet för att generera en nyckel för att dekryptera de mottagna data tillbaka till sitt ursprungliga MD4-format. Lösenordsh hash-synkroniseringsagenten har aldrig åtkomst till lösenordet för klartext. Lösenordshã¤nda synkroniseringsagentens användning av MD5 är strikt för replikeringsprotokollkompatibilitet med domänkontrollanten och används endast lokalt mellan domänkontrollanten och synkroniseringsagenten för lösenord hash.
4. Lösenordshämts synkroniseringsagenten utökar hash-hash-värdet för 16 byte till 64 byte genom att först konvertera hash-värdet till en hexadecimal sträng på 32 byte och sedan konvertera den här strängen tillbaka till binär med UTF-16-kodning.
5. Lösenordsh hash-synkroniseringsagenten lägger till ett salt per användare, bestående av ett 10-byte längdsalt, till binärfilen på 64 byte för att ytterligare skydda den ursprungliga hashen.
6. Lösenordshã¤ndersynkroniseringsagenten kombinerar sedan MD4-hash plus per användarsalt och matar in den i [PBKDF2-funktionen.](https://www.ietf.org/rfc/rfc2898.txt) 1000 iterationer av [HMAC-SHA256-keyed](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) hash-algoritm används. 
7. Lösenordshah-synkroniseringsagenten tar den resulterande hash-värdet på 32 byte och sammanfogar både per användarsalt och antalet SHA256-iterationer (för användning av Azure AD) och överför sedan strängen från Azure AD Connect till Azure AD via TLS.</br> 
8. När en användare försöker logga in på Azure AD och anger sitt lösenord körs lösenordet via samma MD4+salt+PBKDF2+HMAC-SHA256-process. Om den resulterande hash matchar hash som lagras i Azure AD, har användaren angett rätt lösenord och autentiseras.

> [!NOTE]
> Den ursprungliga MD4-hashen överförs inte till Azure AD. I stället överförs SHA256-hashen för den ursprungliga MD4-hashen. Om hash som lagras i Azure AD hämtas kan den därför inte användas i en lokal pass-the-hash-attack.

### <a name="security-considerations"></a>Säkerhetsöverväganden

När lösenord synkroniseras exponeras inte den oformaterade textversionen av ditt lösenord för synkroniseringsfunktionen för lösenord hash, för Azure AD eller någon av de associerade tjänsterna.

Användarautentisering sker mot Azure AD i stället för mot organisationens egen Active Directory-instans. SHA256-lösenordsdata som lagras i Azure AD - ett hash-av den ursprungliga MD4-hashen – är säkrare än det som lagras i Active Directory. Eftersom den här SHA256-hashen inte kan dekrypteras kan den inte föras tillbaka till organisationens Active Directory-miljö och visas som ett giltigt användarlösenord i en pass-the-hash-attack.

### <a name="password-policy-considerations"></a>Hänsyn till lösenordsprincip

Det finns två typer av lösenordsprinciper som påverkas av att aktivera synkronisering av lösenordsh hash:

* Princip för lösenordskomplexitet
* Princip för förfallodatum för lösenord

#### <a name="password-complexity-policy"></a>Princip för lösenordskomplexitet

När synkronisering av lösenord hash-disk är aktiverad åsidosätter lösenordskomplexitetsprinciperna i din lokala Active Directory-instans komplexitetsprinciper i molnet för synkroniserade användare. Du kan använda alla giltiga lösenord från din lokala Active Directory-instans för att komma åt Azure AD-tjänster.

> [!NOTE]
> Lösenord för användare som skapas direkt i molnet omfattas fortfarande av lösenordsprinciper enligt definitionen i molnet.

#### <a name="password-expiration-policy"></a>Princip för förfallodatum för lösenord

Om en användare omfattas av synkronisering av lösenordshã¤nning anges som standard att lösenordet för molnkontot *aldrig upphör att gälla*.

Du kan fortsätta att logga in på dina molntjänster med hjälp av ett synkroniserat lösenord som har upphört att gälla i din lokala miljö. Ditt molnlösenord uppdateras nästa gång du ändrar lösenordet i den lokala miljön.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Offentlig förhandsgranskning av funktionen *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Om det finns synkroniserade användare som bara interagerar med Azure AD-integrerade tjänster och måste också följa en princip för förfallodatum för lösenord kan du tvinga dem att följa principen om att azure AD-lösenord upphör att gälla genom att aktivera funktionen *EnforceCloudPasswordPolicyForPasswordSyncedUsers.*

När *EnforceCloudPasswordPolicyForPasswordSyncedUsers* är inaktiverat (vilket är standardinställningen) anger Azure AD Connect attributet PasswordPolicies för synkroniserade användare till "DisablePasswordExpiration". Detta görs varje gång en användares lösenord synkroniseras och instruerar Azure AD att ignorera principen för förfallodatum för molnet för den användaren. Du kan kontrollera värdet för attributet med hjälp av Azure AD PowerShell-modulen med följande kommando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Om du vill aktivera funktionen EnforceCloudPasswordPolicyForPasswordSyncedUsers kör du följande kommando med hjälp av MODULEN MSOnline PowerShell som visas nedan. Du måste skriva ja för parametern Aktivera enligt nedan:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

När azure AD har aktiverats går det inte `DisablePasswordExpiration` till varje synkroniserad användare för att ta bort värdet från attributet PasswordPolicies. I stället anges värdet `None` till vid nästa lösenordssynkronisering för varje användare när de nästa gång ändrar sitt lösenord i lokala AD.  

Det rekommenderas att aktivera EnforceCloudPasswordPolicyForPasswordSyncedUsers, innan du aktiverar lösenord hash-synkronisering, så att `DisablePasswordExpiration` den första synkroniseringen av lösenord hashar inte lägger till värdet i attributet PasswordPolicies för användarna.

Standardprincipen för Azure AD-lösenord kräver att användarna ändrar sina lösenord var 90:e dag. Om din princip i AD också är 90 dagar bör de två policyerna matchas. Men om AD-principen inte är 90 dagar kan du uppdatera Azure AD-lösenordsprincipen så att den matchar med kommandot Set-MsolPasswordPolicy PowerShell.

Azure AD stöder en separat princip för förfallodatum per registrerad domän.

Varning: Om det finns synkroniserade konton som måste ha lösenord som inte löper `DisablePasswordExpiration` ut i Azure AD måste du uttryckligen lägga till värdet i attributet PasswordPolicies för användarobjektet i Azure AD.  Du kan göra detta genom att köra följande kommando.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Den här funktionen finns i Offentlig förhandsversion just nu.
> Kommandot Set-MsolPasswordPolicy PowerShell fungerar inte på federerade domäner. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Offentlig förhandsversion av synkronisering av tillfälliga lösenord och "Force Password Change on Next Logon"

Det är typiskt att tvinga en användare att ändra sitt lösenord under sin första inloggning, särskilt efter en admin lösenordsåterställning sker.  Det är allmänt känt som att ange ett "tillfälligt" lösenord och kompletteras genom att kontrollera flaggan "Användaren måste ändra lösenord vid nästa inloggning" på ett användarobjekt i Active Directory (AD).
  
Den tillfälliga lösenordsfunktionen säkerställer att överföringen av äganderätten till autentiseringsuppgifterna slutförs vid första användningen, för att minimera hur länge mer än en individ har kunskap om den autentiseringssidan.

Om du vill stödja tillfälliga lösenord i Azure AD för synkroniserade användare kan du aktivera *ForcePasswordChangeOnLogOn-funktionen* genom att köra följande kommando på din Azure AD Connect-server:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> Att tvinga en användare att ändra sitt lösenord vid nästa inloggning kräver en lösenordsändring samtidigt.  Azure AD Connect hämtar inte flaggan force password change av sig själv. Det är ett komplement till den identifierade lösenordsändringen som sker under synkronisering av lösenord hash.it is supplemental to the detected password change that occurs during password hash sync.

> [!CAUTION]
> Du bör bara använda den här funktionen när SSPR och lösenordsåterskrivning är aktiverade på klienten.  Detta för att om en användare ändrar sitt lösenord via SSPR synkroniseras det med Active Directory.

> [!NOTE]
> Den här funktionen är i offentlig förhandsversion just nu.

#### <a name="account-expiration"></a>Kontot upphör att gälla

Om din organisation använder attributet accountExpires som en del av hantering av användarkonton synkroniseras inte det här attributet med Azure AD. Därför är ett utgånget Active Directory-konto i en miljö som konfigurerats för synkronisering av lösenord hash fortfarande aktiv i Azure AD. Vi rekommenderar att om kontot har upphört att gälla bör en arbetsflödesåtgärd utlösa ett PowerShell-skript som inaktiverar användarens Azure AD-konto (använd [cmdlet set-AzureADUser).](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) Omvänt, när kontot är aktiverat, bör Azure AD-instansen aktiveras.

### <a name="overwrite-synchronized-passwords"></a>Skriva över synkroniserade lösenord

En administratör kan återställa lösenordet manuellt med hjälp av Windows PowerShell.

I det här fallet åsidosätter det nya lösenordet ditt synkroniserade lösenord och alla lösenordsprinciper som definierats i molnet tillämpas på det nya lösenordet.

Om du ändrar ditt lokala lösenord igen synkroniseras det nya lösenordet till molnet och det åsidosätter det manuellt uppdaterade lösenordet.

Synkroniseringen av ett lösenord påverkar inte Azure-användaren som är inloggad. Din aktuella molntjänstsession påverkas inte omedelbart av en synkroniserad lösenordsändring som sker när du är inloggad på en molntjänst. KMSI förlänger varaktigheten av denna skillnad. När molntjänsten kräver att du autentiserar igen måste du ange ditt nya lösenord.

### <a name="additional-advantages"></a>Ytterligare fördelar

- I allmänhet är synkronisering av lösenord hash enklare att implementera än en federationstjänst. Det kräver inga ytterligare servrar och eliminerar beroendet av en federationstjänst med hög tillgänglig tillgång för att autentisera användare.
- Synkronisering av lösenord hash kan också aktiveras utöver federationen. Den kan användas som reserv om din federationstjänst drabbas av ett avbrott.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Synkroniseringsprocess för lösenord hash för Azure AD-domäntjänster

Om du använder Azure AD Domain Services för att tillhandahålla äldre autentisering för program och tjänster som behöver använda Kerberos, LDAP eller NTLM, är vissa ytterligare processer en del av synkroniseringsflödet för lösenord hash. Azure AD Connect använder ytterligare följande process för att synkronisera lösenordshänder till Azure AD för användning i Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det stöds inte för att installera Azure AD Connect i en Azure AD DS-hanterad domän för att synkronisera objekt tillbaka till Azure AD.
>
> Azure AD Connect synkroniserar bara äldre lösenordshÃ¤ringar när du aktiverar Azure AD DS för din Azure AD-klientorganisation. Följande steg används inte om du bara använder Azure AD Connect för att synkronisera en lokal AD DS-miljö med Azure AD.
>
> Om dina äldre program inte använder NTLM-autentisering eller LDAP-enkla bindningar rekommenderar vi att du inaktiverar NTLM-lösenordshh-synkronisering för Azure AD DS. Mer information finns i [Inaktivera svaga chiffersviter och NTLM-hash-synkronisering för autentiseringsuppgifter](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect hämtar den offentliga nyckeln för klientens instans av Azure AD Domain Services.
1. När en användare ändrar sitt lösenord lagrar den lokala domänkontrollanten resultatet av lösenordsändringen (hashar) i två attribut:
    * *unicodePwd* för NTLM-lösenordshh.
    * *kompletterandeCredentials* för Kerberos lösenord hash.
1. Azure AD Connect identifierar lösenordsändringar via katalogreplikeringskanalen (attributändringar som behöver replikeras till andra domänkontrollanter).
1. För varje användare vars lösenord har ändrats utför Azure AD Connect följande steg:
    * Genererar en slumpmässig AES 256-bitars symmetrisk nyckel.
    * Genererar en slumpmässig initieringsvektor som behövs för den första krypteringsrundan.
    * Extraherar Kerberos lösenord hashar från *supplementalCredentials* attribut.
    * Kontrollerar säkerhetskonfigurationen *syncntlmPasswords* för Azure AD Domain Services.
        * Om den här inställningen är inaktiverad genererar du en slumpmässig NTLM-hash med hög entropikum (skiljer sig från användarens lösenord). Denna hash kombineras sedan med det exakta Kerberos-lösenordet hashar från *attributet supplementalCrendetials* till en datastruktur.
        * Om det är aktiverat kombinerar värdet för *attributet unicodePwd* med det extraherade Kerberos-lösenordet hashar från *attributet supplementalCredentials* till en datastruktur.
    * Krypterar den enda datastrukturen med hjälp av AES-symmetriska nyckeln.
    * Krypterar AES-symmetrisknyckel med hjälp av klientens offentliga Azure AD Domain Services-nyckel.
1. Azure AD Connect överför den krypterade AES-symmetriska nyckeln, den krypterade datastrukturen som innehåller lösenordsharen och initieringsvektorn till Azure AD.
1. Azure AD lagrar den krypterade AES-symmetriska nyckeln, den krypterade datastrukturen och initieringsvektorn för användaren.
1. Azure AD skickar den krypterade AES-symmetriska nyckeln, den krypterade datastrukturen och initieringsvektorn med hjälp av en intern synkroniseringsmekanism över en krypterad HTTP-session till Azure AD Domain Services.
1. Azure AD Domain Services hämtar den privata nyckeln för klientens instans från Azure Key-valvet.
1. För varje krypterad uppsättning data (som representerar en enskild användares lösenordsändring) utför Azure AD Domain Services sedan följande steg:
    * Använder sin privata nyckel för att dekryptera AES-symmetriska nyckeln.
    * Använder den symmetriska AES-nyckeln med initieringsvektorn för att dekryptera den krypterade datastruktur som innehåller lösenordshedorna.
    * Skriver Kerberos-lösenordet hashar det tar emot till Azure AD Domain Services domänkontrollant. Hashes sparas i användarobjektets *tilläggCredentials* attribut som är krypterat till Azure AD Domain Services domänkontrollantens offentliga nyckel.
    * Azure AD Domain Services skriver ntlm-lösenordet hash den fått till Azure AD Domain Services domänkontrollant. Hash sparas i användarobjektets *unicodePwd-attribut* som krypteras till Azure AD Domain Services-domänkontrollantens offentliga nyckel.

## <a name="enable-password-hash-synchronization"></a>Aktivera hashsynkronisering för lösenord

>[!IMPORTANT]
>Om du migrerar från AD FS (eller annan federationsteknik) till Synkronisering av lösenord hash rekommenderar vi starkt att du följer vår detaljerade distributionsguide som publiceras [här](https://aka.ms/adfstophsdpdownload).

När du installerar Azure AD Connect med alternativet **Expressinställningar** aktiveras automatiskt synkronisering av lösenordshÃ¤nder automatiskt. Mer information finns i [Komma igång med Azure AD Connect med hjälp av snabbinställningar](how-to-connect-install-express.md).

Om du använder anpassade inställningar när du installerar Azure AD Connect är synkronisering av lösenordsh hash-tecken tillgänglig på inloggningssidan för användaren. Mer information finns i [Anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

![Aktivera synkronisering av lösenordshash](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synkronisering av lösenord hash och FIPS
Om servern har låsts enligt FIPS (Federal Information Processing Standard) inaktiveras MD5.

**Så här aktiverar du MD5 för synkronisering av lösenordshÃ¤nning:**

1. Gå till %programfiler%\Azure AD Sync\Bin.
2. Öppna miiserver.exe.config.
3. Gå till konfigurations-/körningsnoden i slutet av filen.
4. Lägg till följande nod:`<enforceFIPSPolicy enabled="false"/>`
5. Spara ändringarna.

Det här kodavsnittet är som referens:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Information om säkerhet och FIPS finns i [Azure AD-lösenord hash-synkronisering, kryptering och FIPS-efterlevnad](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Felsöka synkronisering av lösenordshÃ¶n
Om du har problem med synkronisering av lösenordshÃ¶ning läser du [Felsöka synkronisering av lösenordshÃ¶kning](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Nästa steg
* [Synkronisering av Azure AD Connect: Anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
* [Hämta en steg-för-steg-distributionsplan för migrering från ADFS till Lösenord Hash-synkronisering](https://aka.ms/authenticationDeploymentPlan)
