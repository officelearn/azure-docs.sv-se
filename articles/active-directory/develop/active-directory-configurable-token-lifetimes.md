---
title: Konfigurerbara Azure AD-tokenlivslängder
titleSuffix: Microsoft identity platform
description: Lär dig hur du anger livstider för token som utfärdats av Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 0b2b9dbe52a5696f21b287402fc4cbaa32b29c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263184"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Konfigurerbara tokenlivstider i Azure Active Directory (förhandsversion)

Du kan ange livslängd för ett token som utfärdas av Azure Active Directory (Azure AD). Du kan ange token-livslängd för alla program i din organisation, för ett program med flera klientorganisationer eller för en specifik huvudtjänst i organisationen.

> [!IMPORTANT]
> När vi har hört från kunder under förhandsversionen har vi implementerat funktioner för hantering [av autentiseringssessioner](https://go.microsoft.com/fwlink/?linkid=2083106) i Azure AD-villkorlig åtkomst. Du kan använda den här nya funktionen för att konfigurera uppdateringstokens livstider genom att ange inloggningsfrekvens. Efter den 1 maj 2020 kan du inte använda princip för konfigurerbar tokenlivstid för att konfigurera sessions- och uppdateringstoken. Du kan fortfarande konfigurera åtkomsttokens livstider efter utfasningen.

I Azure AD representerar ett principobjekt en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje principtyp har en unik struktur med en uppsättning egenskaper som tillämpas på objekt som de är tilldelade på.

Du kan ange en princip som standardprincip för din organisation. Principen tillämpas på alla program i organisationen, så länge den inte åsidosätts av en princip med högre prioritet. Du kan också tilldela en princip till specifika program. Prioritetsordningen varierar beroende på principtyp.

> [!NOTE]
> Konfigurerbar tokenlivstidsprincip gäller endast för mobila klienter och skrivbordsklienter som använder SharePoint Online- och OneDrive för företag-resurser och som inte gäller för webbläsarsessioner.
> Om du vill hantera livstiden för webbläsarsessioner för SharePoint Online och OneDrive för företag använder du [livstidsfunktionen för villkorlig åtkomstsession.](../conditional-access/howto-conditional-access-session-lifetime.md) Mer information om hur du konfigurerar tidsutgångar för inaktiva sessionserormar finns i [SharePoint Online.](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)

## <a name="token-types"></a>Tokentyper

Du kan ange principer för tokenlivstid för uppdateringstoken, åtkomsttoken, SAML-token, sessionstoken och ID-token.

### <a name="access-tokens"></a>Åtkomsttokens

Klienter använder åtkomsttoken för att komma åt en skyddad resurs. En åtkomsttoken kan endast användas för en specifik kombination av användare, klient och resurs. Åtkomsttoken kan inte återkallas och är giltiga tills de har upphört att gälla. En skadlig aktör som har fått en åtkomsttoken kan använda den under sin livstid. Att justera livslängden för en åtkomsttoken är en kompromiss mellan att förbättra systemets prestanda och öka den tid som klienten behåller åtkomsten när användarens konto har inaktiverats. Förbättrad systemprestanda uppnås genom att minska antalet gånger en klient behöver för att skaffa en ny åtkomsttoken.  Standardvärdet är 1 timme - efter 1 timme måste klienten använda uppdateringstoken för att (vanligtvis tyst) hämta en ny uppdateringstoken och åtkomsttoken. 

### <a name="saml-tokens"></a>SAML-token

SAML-token används av många webbaserade SAAS-program och hämtas med Hjälp av Azure Active Directorys SAML2-protokollslutpunkt. De förbrukas också av program som använder WS-Federation. Tokens livstid är 1 timme. Från ett programs perspektiv anges tokens giltighetsperiod av värdet NotOnOrAfter `<conditions …>` för elementet i token. När giltighetsperioden för token har avslutats måste klienten initiera en ny autentiseringsbegäran, som ofta uppfylls utan interaktiv inloggning som ett resultat av enstaka inloggningssessionstoken (SSO).

Värdet för NotOnOrAfter kan ändras med parametern `AccessTokenLifetime` i en `TokenLifetimePolicy`. Den ställs in på den livstid som konfigureras i principen om någon, plus en klocksnedställningsfaktor på fem minuter.

Observera att ämnesbekräftelsen NotOnOrAfter som anges i elementet `<SubjectConfirmationData>` inte påverkas av tokenlivstidskonfigurationen. 

### <a name="refresh-tokens"></a>Uppdatera token

När en klient hämtar en åtkomsttoken för att komma åt en skyddad resurs får klienten också en uppdateringstoken. Uppdateringstoken används för att hämta nya åtkomst-/uppdateringstokenpar när den aktuella åtkomsttoken upphör att gälla. En uppdateringstoken är bunden till en kombination av användare och klient. En uppdateringstoken kan [återkallas när som helst](access-tokens.md#token-revocation)och tokens giltighet kontrolleras varje gång token används.  Uppdatera token återkallas inte när de används för att hämta nya åtkomsttoken - det är dock bäst att ta bort den gamla token när du skaffar en ny. 

Det är viktigt att skilja mellan konfidentiella klienter och offentliga klienter, eftersom detta påverkar hur länge uppdateringstoken kan användas. Mer information om olika typer av klienter finns i [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Token livstidar med konfidentiella klientuppdateringstoken
Konfidentiella klienter är program som på ett säkert sätt kan lagra ett klientlösenord (hemligt). De kan bevisa att begäranden kommer från det skyddade klientprogrammet och inte från en skadlig aktör. En webbapp är till exempel en konfidentiell klient eftersom den kan lagra en klienthemlighet på webbservern. Den är inte exponerad. Eftersom dessa flöden är säkrare kan `until-revoked`standardlivstiden för uppdateringstoken som utfärdas till dessa flöden inte ändras med hjälp av principen och kommer inte att återkallas på frivilliga återställningar av lösenord.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Token livstidar med offentliga klientuppdateringstoken

Offentliga klienter kan inte lagra ett klientlösenord på ett säkert sätt (hemligt). En iOS/Android-app kan till exempel inte fördunkla en hemlighet från resursägaren, så den betraktas som en offentlig klient. Du kan ange principer för resurser för att förhindra uppdateringstoken från offentliga klienter som är äldre än en angiven period från att skaffa ett nytt tokenpar för åtkomst/uppdatering. (Det gör du genom att använda egenskapen Uppdatera token Max inaktiv tid (`MaxInactiveTime`).) Du kan också använda principer för att ange en period bortom vilken uppdateringstoken inte längre accepteras. (För att göra detta använder du egenskapen Uppdatera token maxålder.) Du kan justera livslängden för en uppdateringstoken för att styra när och hur ofta användaren måste ange autentiseringsuppgifter igen, i stället för att vara tyst omauthenticated, när du använder ett offentligt klientprogram.

> [!NOTE]
> Egenskapen Max Age är den tid som en enskild token kan användas. 

### <a name="id-tokens"></a>ID-tokens
ID-token skickas till webbplatser och inbyggda klienter. ID-token innehåller profilinformation om en användare. En ID-token är bunden till en specifik kombination av användare och klient. ID-token anses giltiga tills de löper ut. Vanligtvis matchar ett webbprogram en användares sessionslivslängd i programmet med livslängden för den ID-token som utfärdats för användaren. Du kan justera livslängden för en ID-token för att styra hur ofta webbprogrammet upphör att gälla programsessionen och hur ofta det kräver att användaren omauktiseras med Azure AD (antingen tyst eller interaktivt).

### <a name="single-sign-on-session-tokens"></a>Enkla inloggningssessionstoken
När en användare autentiserar med Azure AD upprättas en enda inloggningssession (SSO) med användarens webbläsare och Azure AD. SSO-token, i form av en cookie, representerar den här sessionen. SSO-sessionstoken är inte bunden till ett specifikt resurs-/klientprogram. SSO-sessionstoken kan återkallas och deras giltighet kontrolleras varje gång de används.

Azure AD använder två typer av SSO-sessionstoken: beständiga och icke-obeständiga. Beständiga sessionstoken lagras som beständiga cookies av webbläsaren. Icke-utspända sessionstoken lagras som sessionscookies. (Sessionscookies förstörs när webbläsaren stängs.) Vanligtvis lagras en icke-opersistent sessionstoken. Men när användaren markerar kryssrutan **Håll mig inloggad** under autentisering lagras en beständig sessionstoken.

Icke-persistent session tokens har en livstid på 24 timmar. Beständiga tokens har en livstid på 180 dagar. När som helst en SSO-sessionstoken används inom dess giltighetsperiod, förlängs giltighetstiden ytterligare 24 timmar eller 180 dagar, beroende på tokentypen. Om en SSO-sessionstoken inte används inom dess giltighetsperiod anses den ha upphört att gälla och accepteras inte längre.

Du kan använda en princip för att ställa in tiden efter att den första sessionstoken utfärdades efter vilken sessionstoken inte längre accepteras. (För att göra detta använder du egenskapen Sessionstoken maxålder.) Du kan justera livslängden för en sessionstoken för att styra när och hur ofta en användare måste ange autentiseringsuppgifter igen, i stället för att autentiseras tyst när du använder ett webbprogram.

### <a name="token-lifetime-policy-properties"></a>Egenskaper för tokenlivstidsprincip
En tokenlivsprincip är en typ av principobjekt som innehåller tokenlivslängdsregler. Använd egenskaperna för principen för att styra angivna tokenlivstider. Om ingen princip har angetts tillämpar systemet standardlivstidsvärdet.

### <a name="configurable-token-lifetime-properties"></a>Konfigurerbara egenskaper för tokenlivstid
| Egenskap | Egenskapssträng för princip | Påverkar | Default | Minimum | Maximal |
| --- | --- | --- | --- | --- | --- |
| Livstid för åtkomsttoken |AccessTokenLifetime<sup>2</sup> |Åtkomsttoken, ID-token, SAML2-token |1 timme |10 minuter |1 dag |
| Uppdatera token max inaktiv tid |MaxInactiveTime |Uppdatera token |90 dagar |10 minuter |90 dagar |
| Uppdateringstoken med en faktor max ålder |MaxAgeSingleFactor |Uppdatera token (för alla användare) |Tills återkallas |10 minuter |Tills återkallas<sup>1</sup> |
| Multifaktoruppdateringstoken max ålder |MaxAgeMultiFactor |Uppdatera token (för alla användare) |Tills återkallas |10 minuter |Tills återkallas<sup>1</sup> |
| Enfaktorssessionstoken max ålder |MaxAgeSessionSingleFactor |Sessionstoken (beständiga och icke-obeständiga) |Tills återkallas |10 minuter |Tills återkallas<sup>1</sup> |
| Multifaktorsessionstoken max ålder |MaxAgeSessionMultiFactor |Sessionstoken (beständiga och icke-obeständiga) |Tills återkallas |10 minuter |Tills återkallas<sup>1</sup> |

* <sup>1</sup>365 dagar är den maximala explicita längden som kan ställas in för dessa attribut.
* <sup>2.</sup> För att säkerställa att Microsoft Teams webbklient fungerar rekommenderar vi att AccessTokenLifetime ska vara större än 15 minuter för Microsoft Teams.

### <a name="exceptions"></a>Undantag
| Egenskap | Påverkar | Default |
| --- | --- | --- |
| Uppdatera token maxålder (utfärdas för federerade användare som inte har otillräcklig återkallningsinformation<sup>1</sup>) |Uppdatera token (utfärdas för federerade användare som inte har tillräcklig återkallningsinformation<sup>1)</sup> |12 timmar |
| Uppdatera token max inaktiv tid (utfärdad för konfidentiella klienter) |Uppdatera token (utfärdade för konfidentiella klienter) |90 dagar |
| Uppdatera token maxålder (utfärdas för konfidentiella klienter) |Uppdatera token (utfärdade för konfidentiella klienter) |Tills återkallas |

* <sup>1.</sup> Federerade användare som inte har tillräcklig återkallningsinformation inkluderar alla användare som inte har attributet "LastpasswordChangeTimestamp" synkroniserat. Dessa användare får denna korta Max Age eftersom AAD inte kan verifiera när token som är kopplade till en gammal autentiseringsuppgifter ska återkallas (till exempel ett lösenord som har ändrats) och måste checka in oftare för att säkerställa att användaren och associerade token fortfarande är i gott Stående. För att förbättra den här upplevelsen måste klientadministratörer se till att de synkroniserar attributet "LastpasswordChangeTimestamp" (detta kan ställas in på användarobjektet med Powershell eller via AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Utvärdering och prioritering av policy
Du kan skapa och sedan tilldela en tokenlivsprincip till ett visst program, till din organisation och till tjänsthuvudnamn. Flera principer kan gälla för ett visst program. Principen för tokenlivstid som träder i kraft följer dessa regler:

* Om en princip uttryckligen tilldelas tjänstens huvudnamn tillämpas den.
* Om ingen princip uttryckligen tilldelas tjänstens huvudnamn tillämpas en princip som uttryckligen tilldelats den överordnade organisationen för tjänsthuvudhuvudet.
* Om ingen princip uttryckligen tilldelas tjänstens huvudnamn eller till organisationen, tillämpas principen som tilldelats programmet.
* Om ingen princip har tilldelats tjänstens huvudnamn, organisationen eller programobjektet tillämpas standardvärdena. (Se tabellen i [egenskaper för konfigurerbar tokenlivstid](#configurable-token-lifetime-properties).)

Mer information om relationen mellan programobjekt och huvudobjekt för tjänsten finns [i Program- och tjänsthuvudobjekt i Azure Active Directory](app-objects-and-service-principals.md).

En tokens giltighet utvärderas när token används. Principen med högst prioritet för programmet som används börjar gälla.

Alla tidsspann som används här är formaterade enligt C# [TimeSpan-objektet](/dotnet/api/system.timespan) - D.HH:MM:SS.  Så 80 dagar och 30 minuter skulle vara `80.00:30:00`.  Den ledande D kan släppas om noll, så `00:90:00`90 minuter skulle vara .  

> [!NOTE]
> Här är ett exempel scenario.
>
> En användare vill komma åt två webbprogram: Webbprogram A och Webbprogram B.
> 
> Faktorer:
> * Båda webbprogrammen finns i samma överordnade organisation.
> * Token Lifetime Policy 1 med en sessionstoken maxålder på åtta timmar anges som den överordnade organisationens standard.
> * Webbprogram A är ett vanligt webbprogram och är inte länkat till några principer.
> * Webbprogram B används för mycket känsliga processer. Dess tjänsthuvudnamn är länkat till Token Lifetime Policy 2, som har en sessionstoken max ålder på 30 minuter.
>
> Klockan 12:00 startar användaren en ny webbläsarsession och försöker komma åt webbprogram A. Användaren omdirigeras till Azure AD och uppmanas att logga in. Detta skapar en cookie som har en sessionstoken i webbläsaren. Användaren omdirigeras tillbaka till webbprogram A med en ID-token som tillåter användaren att komma åt programmet.
>
> Klockan 12:15 försöker användaren komma åt webbprogram B. Webbläsaren omdirigeras till Azure AD, som identifierar sessionscookien. Web Application B:s tjänsthuvudnamn är länkat till Token Lifetime Policy 2, men det är också en del av den överordnade organisationen, med standardprincipen token lifetime 1. Token Lifetime Policy 2 börjar gälla eftersom principer som är länkade till tjänstens huvudnamn har högre prioritet än organisationens standardprinciper. Sessionstoken utfärdades ursprungligen under de senaste 30 minuterna, så den anses giltig. Användaren omdirigeras tillbaka till webbprogram B med en ID-token som ger dem åtkomst.
>
> Klockan 13:00 försöker användaren komma åt webbprogram A. Användaren omdirigeras till Azure AD. Webbprogram A är inte länkad till några principer, men eftersom det är i en organisation med standardprincipen token lifetime 1 börjar den principen gälla. Sessionscookien som ursprungligen utfärdades inom de senaste åtta timmarna identifieras. Användaren omdirigeras tyst tillbaka till webbprogram A med en ny ID-token. Användaren behöver inte autentisera.
>
> Omedelbart efteråt försöker användaren komma åt webbprogram B. Användaren omdirigeras till Azure AD. Precis som tidigare träder Token Lifetime Policy 2 i kraft. Eftersom token utfärdades för mer än 30 minuter sedan uppmanas användaren att ange sina inloggningsuppgifter igen. En helt ny sessionstoken och ID-token utfärdas. Användaren kan sedan komma åt webbprogram B.
>
>

## <a name="configurable-policy-property-details"></a>Egenskapsinformation för konfigurerbar princip
### <a name="access-token-lifetime"></a>Livstid för åtkomsttoken
**Sträng:** TillgångtillaLifetime

**Påverkar:** Åtkomsttoken, ID-token, SAML-token

**Sammanfattning:** Den här principen styr hur länge åtkomst- och ID-token för den här resursen anses vara giltiga. Om du minskar egenskapen Åtkomsttokens livstid minskar risken för att en åtkomsttoken eller ID-token används av en skadlig aktör under en längre tid. (Dessa token kan inte återkallas.) Avvägningen är att prestanda påverkas negativt, eftersom token måste ersättas oftare.

### <a name="refresh-token-max-inactive-time"></a>Uppdatera token max inaktiv tid
**Sträng:** MaxInactiveTime

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur gammal en uppdateringstoken kan vara innan en klient inte längre kan använda den för att hämta ett nytt tokenpar för åtkomst/uppdatering när du försöker komma åt den här resursen. Eftersom en ny uppdateringstoken vanligtvis returneras när en uppdateringstoken används, förhindrar den här principen åtkomst om klienten försöker komma åt en resurs med hjälp av den aktuella uppdateringstoken under den angivna tidsperioden.

Den här principen tvingar användare som inte har varit aktiva på sin klient att omauktorisera för att hämta en ny uppdateringstoken.

Egenskapen Uppdatera token Max Inaktiv tid måste anges till ett lägre värde än egenskaperna Single Factor Token Max Age och egenskaperna Multi-Factor Refresh Token Max Age.

### <a name="single-factor-refresh-token-max-age"></a>Uppdateringstoken med en faktor max ålder
**Sträng:** MaxAgeSingleFactor

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en uppdateringstoken för att få ett nytt åtkomst-/uppdateringstokenpar när de senast autentiserats med hjälp av endast en enda faktor. När en användare autentiserar och tar emot en ny uppdateringstoken kan användaren använda uppdateringstokenflödet under den angivna tidsperioden. (Detta gäller så länge den aktuella uppdateringstoken inte har återkallats och den inte lämnas oanvänd längre än den inaktiva tiden.) Då tvingas användaren att omaukta för att få en ny uppdateringstoken.

Att minska maxåldern tvingar användarna att autentisera oftare. Eftersom autentisering med en faktor anses vara mindre säker än multifaktorautentisering rekommenderar vi att du anger den här egenskapen till ett värde som är lika med eller mindre än egenskapen Multi-Factor Refresh Token Max Age.

### <a name="multi-factor-refresh-token-max-age"></a>Multifaktoruppdateringstoken max ålder
**Sträng:** MaxAgeMultiFactor

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en uppdateringstoken för att få ett nytt åtkomst-/uppdateringstokenpar när de senast autentiserats med hjälp av flera faktorer. När en användare autentiserar och tar emot en ny uppdateringstoken kan användaren använda uppdateringstokenflödet under den angivna tidsperioden. (Detta gäller så länge den aktuella uppdateringstoken inte har återkallats och den inte är oanvänd längre än den inaktiva tiden.) Då tvingas användarna att omaukta för att få en ny uppdateringstoken.

Att minska maxåldern tvingar användarna att autentisera oftare. Eftersom autentisering med en faktor anses vara mindre säker än multifaktorautentisering rekommenderar vi att du anger den här egenskapen till ett värde som är lika med eller större än egenskapen Single Factor Refresh Token Max Age.

### <a name="single-factor-session-token-max-age"></a>Enfaktorssessionstoken max ålder
**Sträng:** MaxAgeSessionSingleFactor

**Påverkar:** Sessionstoken (beständiga och icke-obeständiga)

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en sessionstoken för att få ett nytt ID och en sessionstoken efter att de senast autentiserats med hjälp av endast en enda faktor. När en användare autentiserar och tar emot en ny sessionstoken kan användaren använda sessionstokenflödet under den angivna tidsperioden. (Detta gäller så länge den aktuella sessionstoken inte har återkallats och inte har upphört att gälla.) Efter den angivna tidsperioden tvingas användaren att omauktorisera för att ta emot en ny sessionstoken.

Att minska maxåldern tvingar användarna att autentisera oftare. Eftersom autentisering med en faktor anses vara mindre säker än multifaktorautentisering rekommenderar vi att du anger den här egenskapen till ett värde som är lika med eller mindre än egenskapen Multi-Factor Session Token Max Age.

### <a name="multi-factor-session-token-max-age"></a>Multifaktorsessionstoken max ålder
**Sträng:** MaxAgeSessionMultiFactor

**Påverkar:** Sessionstoken (beständiga och icke-obeständiga)

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en sessionstoken för att få ett nytt ID och en sessionstoken efter den senaste gången de autentiserades med hjälp av flera faktorer. När en användare autentiserar och tar emot en ny sessionstoken kan användaren använda sessionstokenflödet under den angivna tidsperioden. (Detta gäller så länge den aktuella sessionstoken inte har återkallats och inte har upphört att gälla.) Efter den angivna tidsperioden tvingas användaren att omauktorisera för att ta emot en ny sessionstoken.

Att minska maxåldern tvingar användarna att autentisera oftare. Eftersom autentisering med en faktor anses vara mindre säker än multifaktorautentisering rekommenderar vi att du anger den här egenskapen till ett värde som är lika med eller större än egenskapen Single Factor Session Token Max Age.

## <a name="example-token-lifetime-policies"></a>Exempel på principer för tokenlivslängd
Många scenarier är möjliga i Azure AD när du kan skapa och hantera tokenlivstider för appar, tjänsthuvudnamn och din övergripande organisation. I det här avsnittet går vi igenom några vanliga politiska scenarier som kan hjälpa dig att införa nya regler för:

* Tokenlivstid
* Token max inaktiv tid
* Token Max ålder

I exemplen kan du lära dig hur du:

* Hantera en organisations standardprincip
* Skapa en princip för webb inloggning
* Skapa en princip för en inbyggd app som anropar ett webb-API
* Hantera en avancerad princip

### <a name="prerequisites"></a>Krav
I följande exempel skapar, uppdaterar, länkar och tar du bort principer för appar, tjänsthuvudnamn och din övergripande organisation. Om du inte har gjort det tidigare i Azure AD rekommenderar vi att du lär dig mer om hur du [skaffar en Azure AD-klient innan](quickstart-create-new-tenant.md) du fortsätter med de här exemplen.  

Så här kommer du igång:

1. Ladda ned den senaste versionen av [Azure AD PowerShell Module Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).
2. Kör `Connect` kommandot för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gång du startar en ny session.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Om du vill visa alla principer som har skapats i organisationen kör du följande kommando. Kör det här kommandot efter de flesta åtgärder i följande scenarier. Att köra kommandot hjälper dig också att få ** ** av dina principer.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exempel: Hantera en organisations standardprincip
I det här exemplet skapar du en princip som gör att användarna kan logga in mindre ofta i hela organisationen. Det gör du genom att skapa en tokenlivsprincip för uppdateringstoken för en faktor, som tillämpas i hela organisationen. Principen tillämpas på alla program i organisationen och på varje tjänsthuvudnamn som inte redan har en principuppsättning.

1. Skapa en tokenlivsprincip.

    1. Ange uppdateringstoken för en faktor till "tills den återkallas". Token upphör inte att gälla förrän åtkomsten har återkallats. Skapa följande principdefinition:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. Om du vill skapa principen kör du följande kommando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. Om du vill visa den nya principen och hämta principens **ObjectId**kör du följande kommando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Uppdatera principen.

    Du kan bestämma att den första principen som du anger i det här exemplet inte är så strikt som din tjänst kräver. Om du vill att uppdateringstoken för en faktor ska upphöra att gälla om två dagar kör du följande kommando:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exempel: Skapa en princip för webb inloggning

I det här exemplet skapar du en princip som kräver att användarna autentiserar oftare i webbappen. Den här principen anger livslängden för åtkomst-/ID-token och den högsta åldern för en multifaktorsessionstoken till tjänstens huvudnamn för webbappen.

1. Skapa en tokenlivsprincip.

    Den här principen, för webb inloggning, anger åtkomst /ID token livstid och max single-factor session token ålder till två timmar.

    1. Om du vill skapa principen kör du det här kommandot:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Om du vill visa din nya princip och hämta principen **ObjectId**kör du följande kommando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Tilldela principen till tjänstens huvudnamn. Du måste också hämta **ObjectId** för din tjänst huvudnamn.

    1. Använd cmdleten [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) för att se alla organisationens tjänsthuvudnamn eller ett enda tjänsthuvudnamn.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. Kör följande kommando när du har tjänstens huvudnamn:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exempel: Skapa en princip för en inbyggd app som anropar ett webb-API
I det här exemplet skapar du en princip som kräver att användare autentiserar mindre ofta. Principen förlänger också den tid som en användare kan vara inaktiv innan användaren måste auktorisera igen. Principen tillämpas på webb-API:et. När den inbyggda appen begär webb-API:et som en resurs tillämpas den här principen.

1. Skapa en tokenlivsprincip.

    1. Om du vill skapa en strikt princip för ett webb-API kör du följande kommando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Kör följande kommando om du vill se den nya principen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Tilldela principen till webb-API:et. Du måste också hämta **ObjectId** för ditt program. Använd [cmdleten Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) för att hitta appens **ObjectId**eller använda [Azure-portalen](https://portal.azure.com/).

    Hämta **ObjectId** för din app och tilldela principen:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Exempel: Hantera en avancerad princip
I det här exemplet skapar du några principer för att lära dig hur prioritetssystemet fungerar. Du lär dig också hur du hanterar flera principer som tillämpas på flera objekt.

1. Skapa en tokenlivsprincip.

    1. Om du vill skapa en standardprincip för organisationen som anger livslängden för enfaktorsuppdateringstoken till 30 dagar kör du följande kommando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. Kör följande kommando om du vill se den nya principen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. Tilldela principen till ett huvudnamn för tjänsten.

    Nu har du en princip som gäller för hela organisationen. Du kanske vill bevara den här 30-dagarsprincipen för ett visst tjänsthuvudnamn, men ändra organisationens standardprincip till den övre gränsen för "tills den återkallas".

    1. Om du vill visa alla organisationens tjänsthuvudnamn använder du cmdleten [Get-AzureADServicePrincipal.](/powershell/module/azuread/get-azureadserviceprincipal)

    2. Kör följande kommando när du har tjänstens huvudnamn:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. Ange `IsOrganizationDefault` flaggan till false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Skapa en ny standardprincip för organisationen:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Nu har du den ursprungliga principen kopplad till tjänstens huvudnamn och den nya principen anges som standardprincip för organisationen. Det är viktigt att komma ihåg att principer som tillämpas på tjänsthuvudnamn har prioritet framför organisationens standardprinciper.

## <a name="cmdlet-reference"></a>Cmdlet-referens

### <a name="manage-policies"></a>Hantera principer

Du kan använda följande cmdlets för att hantera principer.

#### <a name="new-azureadpolicy"></a>Ny AzureADPolicy

Skapar en ny princip.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matris med strängfäst JSON som innehåller alla principens regler. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Sträng av principnamnet. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Om värdet är true anger du principen som organisationens standardprincip. Om falskt, gör ingenting. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ av policy. Använd alltid "TokenLifetimePolicy" för tokenlivtimePolicy. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Valfritt] |Anger ett alternativt ID för principen. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Hämta AzureADPolicy
Hämtar alla Azure AD-principer eller en angiven princip.

```powershell
Get-AzureADPolicy
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code>[Valfritt] |**ObjectId (ID)** för den princip du vill använda. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Hämta AzureADPolicyAppliedObject
Hämtar alla appar och tjänsthuvudnamn som är länkade till en princip.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för den princip du vill använda. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Ange AzureADPolicy
Uppdaterar en befintlig princip.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för den princip du vill använda. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Sträng av principnamnet. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Valfritt] |Matris med strängfäst JSON som innehåller alla principens regler. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Valfritt] |Om värdet är true anger du principen som organisationens standardprincip. Om falskt, gör ingenting. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Valfritt] |Typ av policy. Använd alltid "TokenLifetimePolicy" för tokenlivtimePolicy. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Valfritt] |Anger ett alternativt ID för principen. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Ta bort AzureADPolicy
Tar bort den angivna principen.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för den princip du vill använda. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Användningsprinciper
Du kan använda följande cmdlets för programprinciper.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Tillägg-AzureADApplicationPolicy
Länkar den angivna principen till ett program.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** för principen. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Hämtar principen som har tilldelats ett program.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för programmet. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Ta bort AzureADApplicationPolicy
Tar bort en princip från ett program.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** för principen. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Tjänstens huvudpolicyer
Du kan använda följande cmdlets för tjänstens huvudprinciper.

#### <a name="add-azureadserviceprincipalpolicy"></a>Tillägg-AzureADServicePrincipalPolicy
Länkar den angivna principen till ett huvudnamn för tjänsten.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** för principen. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Hämta AzureADServicePrincipalPolicy
Hämtar en princip som är länkad till det angivna tjänstens huvudnamn.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för programmet. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Ta bort AzureADServicePrincipalPolicy
Tar bort principen från det angivna tjänstens huvudnamn.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** för programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** för principen. | `-PolicyId <ObjectId of Policy>` |
