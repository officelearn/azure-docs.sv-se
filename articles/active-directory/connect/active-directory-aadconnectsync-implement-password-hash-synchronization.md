---
title: Implementera hash Lösenordssynkronisering med Azure AD Connect-synkronisering | Microsoft Docs
description: Innehåller information om hur hash-synkronisering av lösenord fungerar och hur du ställer in.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: abe439cc91a003137c116f57c0cc8bbb61430114
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593460"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementera hash Lösenordssynkronisering med Azure AD Connect-synkronisering
Den här artikeln innehåller information som du behöver att synkronisera användarlösenord från en lokal Active Directory-instans till en molnbaserad Azure Active Directory (Azure AD)-instans.

## <a name="what-is-password-hash-synchronization"></a>Vad är synkronisering av lösenords-hash
Sannolikheten att du är blockerade från att få jobbet gjort, på grund av ett nytt lösenord är relaterad till ett antal olika lösenord måste du komma ihåg. Flera lösenord måste du komma ihåg, desto högre sannolikhet att glömma en. Frågor och anrop om lösenordsåterställning och andra problem med lösenord kräver mest supportavdelningen resurser.

Synkronisering av lösenords-hash är en funktion som används för att synkronisera användarlösenord från en lokal Active Directory-instans till en molnbaserad Azure AD-instans.
Använd denna funktion för att logga in på Azure AD-tjänster som Office 365, Microsoft Intune, CRM Online och Azure Active Directory Domain Services (Azure AD DS). Du loggar in till tjänsten genom att använda samma lösenord som du använder för att logga in på din lokala Active Directory-instans.

![Vad är Azure AD Connect?](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch1.png)

Genom att minska antalet lösenord som behöver användarna bara en. Synkronisering av lösenords-hash kan du:

* Förbättra användarnas produktivitet.
* Minska supportkostnaderna.  

Även om du vill använda [Federation med Active Directory Federation Services (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), du kan du konfigurera synkronisering av lösenords-hash till en säkerhetskopia om det inte går att AD FS-infrastrukturen.

Synkronisering av lösenords-hash är ett tillägg till funktionen katalog synkronisering implementeras av Azure AD Connect-synkronisering. Om du vill använda hash-synkronisering av lösenord i din miljö måste du:

* Installera Azure AD Connect.  
* Konfigurera katalogsynkronisering mellan din lokala Active Directory och Azure Active Directory-instans.
* Aktivera synkronisering av lösenords-hash.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

> [!NOTE]
> Mer information om Azure Active Directory Domain Services konfigurerats för FIPS och hash-Lösenordssynkronisering finns i ”synkronisering av lösenords-hash- och FIPS” senare i den här artikeln.
>
>

## <a name="how-password-hash-synchronization-works"></a>Så här fungerar Lösenordssynkronisering för hash
Active Directory domain service lösenord ska lagras i form av en hash-värdet representation av faktiska användarens lösenord. Ett hash-värde är ett resultat av en enkelriktad matematisk funktion (den *hash-algoritm*). Det finns ingen metod för att återställa resultatet av en envägs funktion till oformaterad text-versionen av ett lösenord. Du kan inte använda lösenords-hash för att logga in på ditt lokala nätverk.

Om du vill synkronisera ditt lösenord, extraherar Azure AD Connect-synkronisering lösenords-hash från lokala Active Directory-instans. Behandling av extra säkerhet tillämpas lösenords-hash innan det synkroniseras till Azure Active Directory authentication-tjänsten. Lösenord synkroniseras på per användare och i kronologisk ordning.

Det faktiska dataflödet för synkroniseringsprocessen lösenord hash liknar synkronisering av användardata, till exempel DisplayName eller e-postadresser. Dock synkroniseras lösenord oftare än fönstret standard directory synkronisering för andra attribut. Lösenord hash synkroniseringsprocessen körs varannan minut. Du kan inte ändra frekvensen för den här processen. När du synkroniserar lösenord över det befintliga lösenordet i molnet.

Första gången du aktiverar funktionen lösenord hash-synkronisering utförs en inledande synkronisering av lösenord för användare i omfånget. Du kan inte explicit definiera en delmängd av lösenord som du vill synkronisera.

När du ändrar ett lösenord för lokala synkroniseras uppdaterade lösenord, oftast på bara några minuter.
Funktionen lösenord hash-synkronisering försöker automatiskt misslyckade synkroniseringsförsök. Om ett fel uppstår vid ett försök att synkronisera ett lösenord, loggas ett fel i din Loggboken.

Synkronisering av lösenord har ingen inverkan på den användare som för tillfället är inloggad.
Den aktuella cloud service sessionen är omedelbart påverkas inte av en synkroniserade lösenordsändring som uppstår när du är inloggad på en tjänst i molnet. När Molntjänsten kräver att autentisera igen, måste du dock ange ditt nya lösenord.

En användare måste ange sina autentiseringsuppgifter för företaget en gång för att autentisera till Azure AD, oavsett om de har loggat in till företagsnätverket. Dessa mönster kan minimeras, men om användaren väljer Behåll mig signerade (KMSI) kryssrutan vid inloggning. Det här alternativet anger en sessions-cookie som kringgår autentisering under en kort period. KMSI beteende kan aktiveras eller inaktiveras av Azure AD-administratör.

> [!NOTE]
> Lösenordssynkronisering stöds endast för objekt typ användare i Active Directory. Det finns inte stöd för iNetOrgPerson objekttypen.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Detaljerad beskrivning av hur hash-synkronisering av lösenord fungerar
Nedan beskrivs djupgående så här fungerar Lösenordssynkronisering hash mellan Active Directory och Azure AD.

![Detaljerad lösenord flöde](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch3.png)


1. Varannan minut, lösenord hash-synkronisering agenten på AD Connect-servern begär lagrade lösenordshashvärden (attributet unicodePwd) från en Domänkontrollant via vanlig [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) replikering protokoll som används för att synkronisera data mellan domänkontrollanter. Kontot måste ha Replikera katalogändringar och replikera Directory alla ändringar AD behörigheter (som standard på installation) att lösenordet hashvärden.
2. Innan du skickar, krypterar domänkontrollanten MD4 lösenordets hash-värde med hjälp av en nyckel som är en [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) hash för RPC-nyckeln och en salt. Den sedan skickar resultatet till hash-lösenordssynkroniseringsagenten via RPC. Domänkontrollanten skickar också saltet till agenten för synkronisering med hjälp av protokollet DC replikering så att agenten ska kunna dekryptera kuvertet.
3.  När hash lösenordssynkroniseringsagenten har krypterade kuvertet, används [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) och salt att generera en nyckel för att dekryptera den mottagna data tillbaka till dess ursprungliga MD4-format. På någon punkt har hash lösenordssynkroniseringsagenten åtkomst till lösenordet i klartext. Lösenord hash-synkronisering agentens användningen av MD5 gäller enbart för replikering protokollet kompatibilitet med domänkontrollanten och används bara på lokala mellan domänkontrollanten och hash-lösenordssynkroniseringsagenten.
4.  Hash-lösenordssynkroniseringsagenten expanderar 16 byte binära lösenords-hash till 64 byte först konverterar hash till en 32-byte hexadecimala strängen, som sedan konvertera den här strängen tillbaka till binär kodning i UTF-16.
5.  Lösenordssynkroniseringsagenten för hash-lägger till en per användare salt, som består av en längd på 10 byte-salt till 64 byte binär att ytterligare skydda ursprungliga hash.
6.  Hash-lösenordssynkroniseringsagenten kombinerar sedan MD4-hash plus per användare salt och anger det i den [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) funktion. 1000 iterationer av den [HMAC SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) nycklad hashalgoritm används. 
7.  Hash-lösenordssynkroniseringsagenten tar den resulterande 32-byte-hashen, sammanfogar både den per användare salt och antalet SHA256 iterationer för att den (för användning av Azure AD), sedan överför strängen från Azure AD Connect till Azure AD via SSL.</br> 
8.  När en användare försöker logga in på Azure AD och anger sitt lösenord, kör lösenordet med samma MD4 + salt + PBKDF2 + HMAC SHA256-processen. Om den resulterande hashen matchar hashen lagras i Azure AD kan användaren har angett rätt lösenord och autentiseras. 

>[!Note] 
>Den ursprungliga MD4-hashen skickas inte till Azure AD. SHA256-hash för den ursprungliga MD4-hashen överförs i stället. Därför om lagras i Azure AD erhålls, kan den inte användas i en lokal pass-the-hash-attack.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Så här fungerar Lösenordssynkronisering hash med Azure Active Directory Domain Services
Du kan också använda funktionen lösenord hash-synkronisering för att synkronisera dina lokala lösenord till [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). I det här scenariot autentiserar användarna i molnet med metoderna som är tillgängliga i din lokala Active Directory-instans i Azure Active Directory Domain Services-instans. Erfarenhet av det här scenariot är ungefär som att använda Active Directory Migration Tool (ADMT) i en lokal miljö.

### <a name="security-considerations"></a>Säkerhetsöverväganden
När du synkroniserar lösenord exponeras inte oformaterad text-versionen av ditt lösenord till Azure AD synkroniseringsfunktionen lösenord hash eller någon av tillhörande tjänster.

Användarautentisering utförs mot Azure AD i stället för mot organisationens egen Active Directory-instans. Om din organisation har frågor om lösenordsdata som i någon formuläret lämnar lokalerna bör du överväga att det faktum att SHA256 lösenordsdata lagras i Azure AD - är ett hash-värde i den ursprungliga MD4-hashen--avsevärt säkrare än vad som lagras i Active Directory. Vara dessutom eftersom SHA256-hash inte kan dekrypteras så den kan inte återförts till organisationens Active Directory-miljö och visas som ett giltigt lösenord i en attack med pass-the-hash.





### <a name="password-policy-considerations"></a>Överväganden om principer lösenord
Det finns två typer av principer för lösenord som påverkas genom att aktivera synkronisering av lösenords-hash:

* Principen för komplexitet för lösenord
* Förfalloprincipen för lösenord

#### <a name="password-complexity-policy"></a>Principen för komplexitet för lösenord  
När lösenord hash-synkronisering har aktiverats principändringar komplexitet lösenordsprinciper i din lokala Active Directory-instans komplexiteten i molnet för synkroniserade användare. Du kan använda alla giltiga lösenord från din lokala Active Directory-instans för att få åtkomst till Azure AD-tjänster.

> [!NOTE]
> Lösenord för användare som skapas direkt i molnet regleras fortfarande lösenordsprinciper som definierats i molnet.

#### <a name="password-expiration-policy"></a>Förfalloprincipen för lösenord  
Om en användare finns i omfånget för synkronisering av lösenords-hash, molnet kontolösenord anges till *aldrig går ut*.

Du kan fortsätta att logga in på dina molntjänster med ett synkroniserade lösenord som har upphört att gälla i din lokala miljö. Lösenordet molnet uppdateras nästa gång du ändrar lösenordet i den lokala miljön.

#### <a name="account-expiration"></a>Kontots giltighetstid
Om din organisation använder attributet accountExpires som en del av hantering av användarkonton, Tänk på att det här attributet inte är synkroniserat till Azure AD. Därför ett utgånget Active Directory-konto i en miljö som konfigurerats för synkronisering av lösenords-hash kommer fortfarande att vara aktiv i Azure AD. Vi rekommenderar att en arbetsflödesåtgärd ska utlösa ett PowerShell-skript som inaktiverar användarens Azure AD-kontot om kontot har upphört att gälla. Däremot när kontot är aktiverat bör Azure AD-instansen vara aktiverat.

### <a name="overwrite-synchronized-passwords"></a>Skriv över synkroniserade lösenord
En administratör återställa manuellt ditt lösenord med hjälp av Windows PowerShell.

I detta fall kan det nya lösenordet åsidosätter lösenordet synkroniserade och alla lösenordsprinciper som definierats i molnet tillämpas på det nya lösenordet.

Om du ändrar lösenordet lokalt igen, det nya lösenordet har synkroniserats till molnet och åsidosätter den uppdaterade lösenordet.

Synkronisering av lösenord har ingen inverkan på Azure användare är inloggad. Den aktuella cloud service sessionen är omedelbart påverkas inte av en synkroniserade lösenordsändring som uppstår när du är inloggad på en tjänst i molnet. KMSI utökar varaktigheten för denna skillnad. När Molntjänsten kräver att autentisera igen, måste du ange ditt nya lösenord.

### <a name="additional-advantages"></a>Ytterligare fördelar

- Synkronisering av lösenords-hash är oftast enklare att implementera än en federationstjänst. Det kräver inte några ytterligare servrar och eliminerar beroendet av en federationstjänst som hög tillgänglighet att autentisera användare. 
- Synkronisering av lösenords-hash kan även aktiveras förutom federation. Den kan användas som reserv om ett avbrott inträffar i din federationstjänst.












## <a name="enable-password-hash-synchronization"></a>Aktivera synkronisering av lösenords-hash
När du installerar Azure AD Connect med hjälp av den **standardinställningar** alternativet lösenord hash-synkronisering aktiveras automatiskt. Mer information finns i [komma igång med Azure AD Connect med standardinställningar](active-directory-aadconnect-get-started-express.md).

Om du använder anpassade inställningar när du installerar Azure AD Connect är synkronisering av lösenords-hash tillgänglig på sidan för användaren. Mer information finns i [anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

![Aktivera synkronisering av lösenords-hash](./media/active-directory-aadconnect-get-started-custom/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Synkronisering av lösenords-hash- och FIPS
Om servern har låsts enligt FIPS Federal Information Processing Standard (), har MD5 inaktiverats.

**Utför följande steg om du vill aktivera MD5 hash-synkronisering av lösenord:**

1. Gå till %programfiles%\Azure AD Sync\Bin.
2. Öppna miiserver.exe.config.
3. Gå till noden configuration-körning i slutet av filen.
4. Lägg till följande nod: `<enforceFIPSPolicy enabled="false"/>`
5. Spara ändringarna.

Referens är den här fragment hur det ska se ut:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Information om säkerhet och FIPS finns [AAD-synkronisering av lösenord, kryptering och FIPS-kompatibilitet](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Felsöka Lösenordssynkronisering för hash
Om du har problem med synkronisering av lösenords-hash, se [Felsöka Lösenordssynkronisering för hash-](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).

## <a name="next-steps"></a>Nästa steg
* [Azure AD Connect-synkronisering: anpassa synkroniseringsalternativ](active-directory-aadconnectsync-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
