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
ms.openlocfilehash: 0c903e3378e06734a8785531c1a16c695d4b6c21
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74814931"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementera lösenordshashsynkronisering med Azure AD Connect-synkronisering
Den här artikeln innehåller information du behöver för att synkronisera dina lösenord från en lokal Active Directory-instans till en molnbaserad Azure Active Directory (Azure AD)-instans.

## <a name="how-password-hash-synchronization-works"></a>Så här fungerar hash-synkronisering
Active Directory domain service lösenord ska lagras i form av en representation för hash-värde, av faktiska användarens lösenord. Ett hash-värde är ett resultat av en enkelriktad matematisk funktion (den *hash-algoritm*). Det finns ingen metod för att återställa resultatet av en envägsfunktion till versionen av ett lösenord med oformaterad text. 

Om du vill synkronisera ditt lösenord extraherar Azure AD Connect-synkronisering lösenords-hash från en lokal Active Directory-instans. Behandling av extra säkerhet tillämpas på lösenordets hash-värde innan den synkroniseras till tjänsten Azure Active Directory-autentisering. Lösenord synkroniseras på basis av per användare och i kronologisk ordning.

Det faktiska dataflödet av processen för Lösenordssynkronisering hash liknar synkronisering av användardata. Dock synkroniseras lösenord oftare än fönstret standard directory synkronisering för andra attribut. Processen för Lösenordssynkronisering hash körs varannan minut. Du kan inte ändra frekvensen för den här processen. När du synkroniserar ett lösenord, skrivs det befintliga lösenordet i molnet.

Första gången du aktiverar funktionen lösenord hash-synkronisering utförs en inledande synkronisering av lösenorden för alla omfattade användare. Du kan inte explicit definiera en delmängd av lösenord som du vill synkronisera. Men om det finns flera kopplingar är det möjligt att inaktivera hash-synkronisering av lösen ord för vissa anslutningar, men inte andra som använder cmdleten [set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) .

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
3. När lösenordshash-synkroniseringsagent har krypterade kuvert, används [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) och salt att generera en nyckel för att dekryptera den mottagna data tillbaka till sitt ursprungliga MD4-format. Lösenordshash-synkroniseringsagent har aldrig åtkomst till lösenordet i klartext. Lösenord hash-synkronisering agentens användningen av MD5 gäller enbart för replikering protokollkompatibilitet med domänkontrollanten och den används endast lokalt mellan domänkontrollanten och lösenordshash-synkroniseringsagent.
4. Lösenordshash-synkroniseringsagent expanderar 16 byte binary lösenordets hash-värde till 64 byte först konverterar en hash till en 32-bytes hexadecimal sträng, som sedan konvertera den här strängen tillbaka till binärformat med UTF-16-kodning.
5. Lösenordshash-synkroniseringsagent lägger till en per användare salt, som består av en längd i 10-byte-salt till 64-byte binary vilket ytterligare skyddar den ursprungliga hashen.
6. Lösenord hash-synkroniseringsagenten kombinerar sedan MD4-hash plus den per användaren salt och indata det till den [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) funktion. 1000 iterationer av den [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) nycklad hashalgoritm används. 
7. Lösenord hash-synkroniseringsagenten tar emot den resulterande 32 byte-hashen och sammanfogar både den per användaren salt och antalet SHA256 iterationer till den (för användning av Azure AD), sedan överför strängen från Azure AD Connect till Azure AD via SSL.</br> 
8. När en användare försöker logga in på Azure AD och anger sitt lösenord, körs lösenordet via samma MD4 + salt + PBKDF2 + HMAC-SHA256-processen. Om den resulterande hashen matchar hashen lagras i Azure AD, användaren har angett rätt lösenord och autentiseras.

> [!NOTE]
> Den ursprungliga MD4-hashen överförs inte till Azure AD. I stället överförs SHA256-hash för den ursprungliga MD4-hashen. Därför om hash-värdet lagras i Azure AD hämtas kan den inte användas i en lokal pass-the-hash-attack.

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

Om en användare är inom omfånget för synkronisering av lösen ords-hash är moln kontots lösen ord inställt på att *aldrig upphöra att gälla*.

Du kan fortsätta att logga in på cloud services med hjälp av synkroniserade lösenord som har upphört att gälla i din lokala miljö. Lösenordet cloud uppdateras nästa gång du ändrar lösenordet i den lokala miljön.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Offentlig för hands version av funktionen *EnforceCloudPasswordPolicyForPasswordSyncedUsers*

Om det finns synkroniserade användare som bara interagerar med Azure AD-integrerade tjänster och måste också uppfylla en princip för lösen ords giltighet, kan du tvinga dem att följa din Azure AD-princip för lösen ords giltighet genom att aktivera funktionen *EnforceCloudPasswordPolicyForPasswordSyncedUsers* .

När *EnforceCloudPasswordPolicyForPasswordSyncedUsers* är inaktiverat (vilket är standardinställningen) anger Azure AD Connect attributet PasswordPolicies för synkroniserade användare till "DisablePasswordExpiration". Detta görs varje gång en användares lösen ord synkroniseras och instruerar Azure AD att ignorera lösen ordets giltighets princip för lösen ord för den användaren. Du kan kontrol lera värdet för attributet med hjälp av Azure AD PowerShell-modulen med följande kommando:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Om du vill aktivera funktionen EnforceCloudPasswordPolicyForPasswordSyncedUsers kör du följande kommando med hjälp av MSOnline PowerShell-modulen:

`Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers -Enable $true`

När den är aktive rad går Azure AD inte till varje synkroniserad användare för att ta bort `DisablePasswordExpiration`-värdet från attributet PasswordPolicies. I stället är värdet inställt på `None` under nästa Lösenordssynkronisering för varje användare nästa gång de ändrar sitt lösen ord i lokal AD.  

Vi rekommenderar att du aktiverar EnforceCloudPasswordPolicyForPasswordSyncedUsers innan du aktiverar synkronisering av lösen ords-hash, så att den inledande synkroniseringen av lösen ords-hashar inte lägger till värdet `DisablePasswordExpiration` till attributet PasswordPolicies för användarna.

Standard lösen ords principen för Azure AD kräver att användare ändrar sina lösen ord var 90: e dag. Om din princip i AD också är 90 dagar ska de två principerna matcha. Men om AD-principen inte är 90 dagar kan du uppdatera lösen ords principen för Azure AD så att den matchar med hjälp av kommandot Set-MsolPasswordPolicy PowerShell.

Azure AD har stöd för en separat princip för förfallo datum för lösen ord per registrerad domän.

Varningar: om det finns synkroniserade konton som måste ha lösen ord som inte upphör att gälla i Azure AD måste du uttryckligen lägga till `DisablePasswordExpiration`-värdet i PasswordPolicies-attributet för användarobjektet i Azure AD.  Det kan du göra genom att köra följande kommando.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Den här funktionen finns nu i offentlig för hands version.

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-on-next-logon"></a>Offentlig för hands version av synkronisering av temporära lösen ord och "tvinga lösen ord vid nästa inloggning"

Det är vanligt att tvinga en användare att ändra sina lösen ord under sin första inloggning, särskilt efter att administratörs lösen ordet har återställts.  Det kallas vanligt vis att ange ett "tillfälligt" lösen ord och slutförs genom att markera flaggan "användaren måste byta lösen ord vid nästa inloggning" på ett användar objekt i Active Directory (AD).
  
Funktionen för temporära lösen ord hjälper till att säkerställa att överföringen av ägarskapet för autentiseringsuppgiften har slutförts vid första användningen, för att minimera tiden i vilken mer än en person har kännedom om den autentiseringsuppgiften.

Om du vill ha stöd för tillfälliga lösen ord i Azure AD för synkroniserade användare kan du aktivera funktionen *ForcePasswordResetOnLogonFeature* genom att köra följande kommando på Azure AD Connect-servern:

`Set-ADSyncAADCompanyFeature  -ForcePasswordResetOnLogonFeature $true`

> [!NOTE]
> Att tvinga en användare att ändra sina lösen ord vid nästa inloggning kräver en lösen ords ändring samtidigt.  AD Connect hämtar inte flaggan påtvinga lösen ords ändringar. Det kompletterar den identifierade lösen ords ändringen som sker under synkroniseringen av lösen ords-hash.

> [!CAUTION]
> Om du inte aktiverar självbetjäning för återställning av lösen ord (SSPR) i Azure AD-användare får du en förvirrande upplevelse när de återställer sitt lösen ord i Azure AD och försöker sedan logga in i Active Directory med det nya lösen ordet, eftersom det nya lösen ordet inte är giltigt i Active Directory . Du bör endast använda den här funktionen när tillbakaskrivning av SSPR och lösen ord är aktiverat på klienten.

> [!NOTE]
> Den här funktionen finns nu i offentlig för hands version.

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

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Synkronisering av lösenords-hash för Azure AD Domain Services

Om du använder Azure AD Domain Services för att tillhandahålla äldre autentisering för program och tjänster som behöver använda Kerberos, LDAP eller NTLM, ingår vissa ytterligare processer i flödet för Lösenordssynkronisering. Azure AD Connect använder ytterligare följande process för att synkronisera Password-hashar till Azure AD för användning i Azure AD Domain Services:

> [!IMPORTANT]
> Azure AD Connect bör endast installeras och konfigureras för synkronisering med lokala AD DS-miljöer. Det finns inte stöd för att installera Azure AD Connect i en Azure AD DS-hanterad domän för att synkronisera objekt tillbaka till Azure AD.
>
> Azure AD Connect synkroniserar bara äldre lösen ords-hashar när du aktiverar Azure AD DS för Azure AD-klienten. Följande steg används inte om du bara använder Azure AD Connect för att synkronisera en lokal AD DS-miljö med Azure AD.
>
> Om dina äldre program inte använder NTLM-autentisering eller enkla LDAP-bindningar, rekommenderar vi att du inaktiverar hash-synkronisering av NTLM-lösenord för Azure AD DS. Mer information finns i [inaktivera svaga chiffersviter och hash-synkronisering av NTLM-autentiseringsuppgifter](../../active-directory-domain-services/secure-your-domain.md).

1. Azure AD Connect hämtar den offentliga nyckeln för klient Azure AD Domain Servicess instansen.
1. När en användare ändrar sitt lösen ord lagrar den lokala domänkontrollanten resultatet av lösen ords ändringen (hash-värden) i två attribut:
    * *unicodePwd* för NTLM-lösenords-hash.
    * *supplementalCredentials* för Kerberos-lösenords-hash.
1. Azure AD Connect identifierar lösen ords ändringar via Directory Replication-kanal (attributändringar som behöver replikeras till andra domänkontrollanter).
1. För varje användare vars lösen ord har ändrats utför Azure AD Connect följande steg:
    * Genererar en slumpmässig AES 256-bitars symmetrisk nyckel.
    * Genererar en slumpmässig initierings vektor som krävs för den första avrundade krypteringen.
    * Extraherar Kerberos-lösenords-hashvärden från *supplementalCredentials* -attributen.
    * Kontrollerar inställningen för *SyncNtlmPasswords* Azure AD Domain Services av säkerhets konfiguration.
        * Om den här inställningen är inaktive rad genererar en slumpmässig, hög entropi NTLM-hash (skiljer sig från användarens lösen ord). Denna hash kombineras sedan med exakta Kerberos-lösenords-hashvärden från attributet *supplementalCrendetials* till en data struktur.
        * Om aktive rad kombineras värdet för attributet *unicodePwd* med de extraherade Kerberos-lösenords-hasharna från attributet *supplementalCredentials* till en data struktur.
    * Krypterar den enkla data strukturen med hjälp av den symmetriska AES-nyckeln.
    * Krypterar den symmetriska AES-nyckeln med klientens Azure AD Domain Services offentliga nyckel.
1. Azure AD Connect skickar den krypterade AES symmetriska nyckeln, den krypterade data strukturen som innehåller lösen ordets hash-värden och initierings vektorn till Azure AD.
1. Azure AD lagrar den krypterade AES symmetriska nyckeln, den krypterade data strukturen och initierings vektorn för användaren.
1. Azure AD push-överför den krypterade AES symmetriska nyckeln, den krypterade data strukturen och initierings vektorn med hjälp av en intern synkronisering över en krypterad HTTP-session till Azure AD Domain Services.
1. Azure AD Domain Services hämtar den privata nyckeln för klient organisationens instans från Azure Key Vault.
1. För varje krypterad data uppsättning (som representerar en enskild användares lösen ords ändring) utför Azure AD Domain Services följande steg:
    * Använder den privata nyckeln för att dekryptera den symmetriska AES-nyckeln.
    * Använder den symmetriska AES-nyckeln med initierings vektorn för att dekryptera den krypterade data strukturen som innehåller lösen ordets hash-värden.
    * Skriver de Kerberos-lösenords-hashar som den tar emot till Azure AD Domain Services domänkontrollanten. Hasharna sparas i användarobjektet *supplementalCredentials* -attribut som är krypterade till den Azure AD Domain Services domänkontrollantens publika nyckel.
    * Azure AD Domain Services skriver det NTLM-lösenord som det fick till Azure AD Domain Services domänkontrollanten. Hashen sparas i objektet för attributet *unicodePwd* som är krypterat till den Azure AD Domain Services domänkontrollantens publika nyckel.

## <a name="enable-password-hash-synchronization"></a>Aktivera hashsynkronisering för lösenord

>[!IMPORTANT]
>Om du migrerar från AD FS (eller andra tekniker för federation) till synkronisering av Lösenordshash, rekommenderar vi att du följer våra detaljerad Distributionsguide publicerade [här](https://aka.ms/adfstophsdpdownload).

När du installerar Azure AD Connect med hjälp av den **standardinställningar** , synkronisering av lösenordshash är automatiskt aktiverat. Mer information finns i [komma igång med Azure AD Connect med standardinställningar](how-to-connect-install-express.md).

Om du använder anpassade inställningar när du installerar Azure AD Connect finns synkronisering av lösenordshash på sidan för användaren. Mer information finns i [anpassad installation av Azure AD Connect](how-to-connect-install-custom.md).

![Aktivera synkronisering av lösenordshash](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

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
* [Azure AD Connect-synkronisering: anpassa synkroniseringsalternativ](how-to-connect-sync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md)
* [Hämta en stegvis distributionsplan för att migrera från AD FS till synkronisering av Lösenordshash](https://aka.ms/authenticationDeploymentPlan)
