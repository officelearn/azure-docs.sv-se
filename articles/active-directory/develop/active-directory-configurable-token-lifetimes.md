---
title: Konfigurerbara livstider för Azure AD-token
titleSuffix: Microsoft identity platform
description: Lär dig hur du ställer in livs längder för token som utfärdats av Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 23283a44f78522d2b589993c11b494092352cbb6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478373"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Konfigurerbara livstider för token i Azure Active Directory (för hands version)

Du kan ange livslängd för ett token som utfärdas av Azure Active Directory (Azure AD). Du kan ange token-livslängd för alla program i din organisation, för ett program med flera klientorganisationer eller för en specifik huvudtjänst i organisationen.

> [!IMPORTANT]
> Efter att ha hört från kunder under för hands versionen har vi implementerat [hanterings funktioner för autentisering](https://go.microsoft.com/fwlink/?linkid=2083106) i Azure AD villkorlig åtkomst. Du kan använda den här nya funktionen för att konfigurera livstid för uppdateringstoken genom att ange inloggnings frekvens. Efter den 30 maj 2020 kommer ingen ny klient att kunna använda konfigurerings bara livs längds princip för token för att konfigurera sessioner och uppdatera tokens. Utfasningen sker inom flera månader efter det, vilket innebär att vi slutar att respektera befintliga sessioner för session och uppdatering av token. Du kan fortfarande konfigurera livstid för åtkomsttoken efter utfasningen.

I Azure AD representerar ett princip objekt en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje princip typ har en unik struktur med en uppsättning egenskaper som tillämpas på objekt som de är tilldelade till.

Du kan ange en princip som standard princip för din organisation. Principen tillämpas på alla program i organisationen, så länge den inte åsidosätts av en princip med högre prioritet. Du kan också tilldela en princip till vissa program. Prioritetsordningen varierar efter princip typ.

> [!NOTE]
> Konfigurations bara livs längds princip för token gäller endast för mobila och Station ära klienter som har åtkomst till SharePoint Online-och OneDrive för företag-resurser och gäller inte för webbläsarfönster.
> Om du vill hantera livs längden för webb läsar sessioner för SharePoint Online och OneDrive för företag, använder du funktionen [livs längd för session för villkorlig åtkomst](../conditional-access/howto-conditional-access-session-lifetime.md) . Se [SharePoint Online-bloggen](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) om du vill veta mer om hur du konfigurerar tids gränser för inaktiv session.

## <a name="token-types"></a>Token-typer

Du kan ange livs längds principer för token för uppdateringstoken, åtkomsttoken, SAML-token, tokens och ID-token.

### <a name="access-tokens"></a>Åtkomsttokens

Klienter använder åtkomsttoken för att få åtkomst till en skyddad resurs. En åtkomsttoken kan bara användas för en speciell kombination av användare, klient och resurs. Åtkomsttoken kan inte återkallas och är giltiga tills deras förfallo datum har passerats. En skadlig aktör som har fått en åtkomsttoken kan använda den för sin livs längd. Att justera livs längden för en åtkomsttoken är en kompromiss mellan att förbättra systemets prestanda och att öka den tid som klienten behåller åtkomsten efter att användarens konto har inaktiverats. Förbättrad system prestanda uppnås genom att minska antalet gånger som en klient behöver skaffa en ny åtkomsttoken.  Standardvärdet är 1 timme – efter 1 timme, klienten måste använda uppdateringstoken till (normalt tyst) Hämta en ny uppdateringstoken och åtkomsttoken. 

### <a name="saml-tokens"></a>SAML-token

SAML-token används av många webbaserade SAAS-program och hämtas med hjälp av Azure Active Directorys SAML2-protokollets slut punkt. De används också av program som använder WS-Federation. Standard livstiden för token är 1 timme. Från ett programs perspektiv anges giltighets perioden för token av NotOnOrAfter-värdet för `<conditions …>` elementet i token. När giltighets perioden för token har upphört måste klienten initiera en ny autentiseringsbegäran, som ofta uppfylls utan interaktiv inloggning som ett resultat av sessionstoken för enkel inloggning (SSO).

Värdet för NotOnOrAfter kan ändras med hjälp av `AccessTokenLifetime` parametern i en `TokenLifetimePolicy` . Den ställs in på den livstid som kon figurer ATS i principen om det finns någon, plus en klock skev på fem minuter.

Observera att mottagar bekräftelse NotOnOrAfter som anges i `<SubjectConfirmationData>` elementet inte påverkas av konfigurationen för token livs längd. 

### <a name="refresh-tokens"></a>Uppdatera token

När en klient får åtkomst-token för åtkomst till en skyddad resurs får klienten också en uppdateringstoken. Uppdateringstoken används för att hämta nya token för åtkomst/uppdatering när den aktuella åtkomsttoken upphör att gälla. En uppdateringstoken är kopplad till en kombination av användare och klient. En uppdateringstoken kan [återkallas när som helst](access-tokens.md#token-revocation), och giltighets tiden för token kontrol leras varje gång token används.  Uppdaterade token återkallas inte när de används för att hämta nya åtkomsttoken – det är bäst att ta bort den gamla token på ett säkert sätt när du får en ny. 

Det är viktigt att skilja mellan konfidentiella klienter och offentliga klienter, eftersom detta påverkar hur länge uppdaterade token kan användas. Mer information om olika typer av klienter finns i [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Livs längd för token med konfidentiella klient uppdaterings-token
Konfidentiella klienter är program som säkert kan lagra ett klient lösen ord (hemligt). De kan bevisa att förfrågningar kommer från det skyddade klient programmet och inte från en skadlig aktör. En webbapp är till exempel en konfidentiell klient eftersom den kan lagra en klient hemlighet på webb servern. Den exponeras inte. Eftersom de här flödena är säkrare, går det inte att ändra standard livstiden för uppdateringstoken som utfärdats till dessa flöden `until-revoked` genom att använda principen och kommer inte att återkallas för frivilliga lösen ords återställning.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Livs längd för token med offentliga klient uppdaterings-token

Offentliga klienter kan inte lagra ett klient lösen ord på ett säkert sätt (hemligt). En iOS/Android-app kan till exempel inte obfuscate en hemlighet från resurs ägaren, så den betraktas som en offentlig klient. Du kan ange principer för resurser för att förhindra att uppdateringstoken från offentliga klienter som är äldre än en angiven period hämtar ett nytt nyckel par för åtkomst/uppdatering. (Om du vill göra detta använder du egenskapen Refresh token Max inaktive rad ( `MaxInactiveTime` ).) Du kan också använda principer för att ange en period utanför vilken uppdateringstoken inte längre accepteras. (Om du vill göra detta använder du egenskapen Refresh token max ålder.) Du kan justera livs längden för en uppdateringstoken för att styra när och hur ofta användaren måste ange autentiseringsuppgifter igen, i stället för att tyst autentiseras igen när du använder ett offentligt klient program.

> [!NOTE]
> Egenskapen max ålder är den tid som en enskild token kan användas. 

### <a name="id-tokens"></a>ID-tokens
ID-token skickas till webbplatser och interna klienter. ID-tokens innehåller profil information om en användare. En ID-token är kopplad till en speciell kombination av användare och klient. ID-token anses giltiga tills de upphör att gälla. Vanligt vis matchar ett webb program en användares sessions livs längd i programmet till livs längden för den ID-token som utfärdats för användaren. Du kan justera livs längden för en ID-token för att styra hur ofta webb programmet ska upphöra med programsessionen och hur ofta den kräver att användaren autentiseras igen med Azure AD (antingen tyst eller interaktivt).

### <a name="single-sign-on-session-tokens"></a>Token för enkel inloggning
När en användare autentiseras med Azure AD upprättas en enkel inloggnings session (SSO) med användarens webbläsare och Azure AD. SSO-token i form av en cookie representerar den här sessionen. SSO-sessionstoken är inte kopplat till ett specifik resurs-/klient program. SSO-sessionstoken kan återkallas och deras giltighet kontrol leras varje gång de används.

Azure AD använder två typer av SSO-sessionstoken: beständiga och inte permanenta. Beständig sessionstoken lagras som beständiga cookies av webbläsaren. Token för inte beständig session lagras som sessionscookies. (Sessionscookies förstörs när webbläsaren stängs.) Vanligt vis lagras en beständig sessionstoken. Men när användaren markerar kryss rutan **Behåll mig inloggad** under autentiseringen, lagras en beständig sessionstoken.

Token för inte beständig session har en livs längd på 24 timmar. Permanenta token har en livs längd på 90 dagar. När en SSO-token används i sin giltighets period, utökas giltighets perioden till ett till 24 timmar eller 90 dagar, beroende på tokentyp. Om en SSO-token inte används inom sin giltighets tid betraktas det som förfallet och godkänns inte längre.

Du kan använda en princip för att ställa in tiden efter att den första sessionstoken har utfärdats utanför vilken sessionstoken inte längre accepteras. (Om du vill göra det använder du egenskapen token för sessionstoken max ålder.) Du kan justera livs längden för en sessions-token för att styra när och hur ofta en användare krävs för att ange autentiseringsuppgifter igen, i stället för att bli tyst autentiserad, när du använder ett webb program.

### <a name="token-lifetime-policy-properties"></a>Princip egenskaper för token livstid
En livs längds princip för token är en typ av princip objekt som innehåller livs längds regler för token. Använd egenskaperna för principen för att kontrol lera angivna livstider för token. Om ingen princip har angetts tillämpar systemet standard livstid svärdet.

### <a name="configurable-token-lifetime-properties"></a>Egenskaper för konfigurerbar token-livstid
| Egenskap | Princip egenskaps sträng | Nätverk | Default | Minimum | Maximal |
| --- | --- | --- | --- | --- | --- |
| Livstid för åtkomsttoken |AccessTokenLifetime<sup>2</sup> |Åtkomsttoken, ID-token, SAML2-token |1 timme |10 minuter |1 dag |
| Maximal inaktiv tid för uppdateringstoken |MaxInactiveTime |Uppdatera token |90 dagar |10 minuter |90 dagar |
| Högsta ålder för token för enkel uppdatering |MaxAgeSingleFactor |Uppdatera tokens (för alla användare) |Tills den har återkallats |10 minuter |Till och med återkalla<sup>1</sup> |
| Högsta ålder för Multi-Factor Refresh-token |MaxAgeMultiFactor |Uppdatera tokens (för alla användare) |Tills den har återkallats |10 minuter |Till och med återkalla<sup>1</sup> |
| Högsta ålder för token för token för en session |MaxAgeSessionSingleFactor |Token för sessioner (beständiga och inte permanenta) |Tills den har återkallats |10 minuter |Till och med återkalla<sup>1</sup> |
| Högsta ålder för Multi-Factor session |MaxAgeSessionMultiFactor |Token för sessioner (beständiga och inte permanenta) |Tills den har återkallats |10 minuter |Till och med återkalla<sup>1</sup> |

* <sup>1</sup>365 dagar är den maximala explicita längden som kan anges för dessa attribut.
* <sup>2</sup> För att säkerställa att Microsoft Teams webb klienten fungerar rekommenderar vi att du håller AccessTokenLifetime till mer än 15 minuter för Microsoft Teams.

### <a name="exceptions"></a>Undantag
| Egenskap | Nätverk | Default |
| --- | --- | --- |
| Uppdatera token max ålder (utfärdat för federerade användare som har otillräcklig åter kallelse information<sup>1</sup>) |Uppdatera tokens (utfärdat för federerade användare som har otillräcklig återkallnings information<sup>1</sup>) |12 timmar |
| Maximal inaktiv tid för uppdateringstoken (utfärdat för konfidentiella klienter) |Uppdatera tokens (utfärdat för konfidentiella klienter) |90 dagar |
| Maximal ålder för uppdateringstoken (utfärdat för konfidentiella klienter) |Uppdatera tokens (utfärdat för konfidentiella klienter) |Tills den har återkallats |

* <sup>1</sup> Federerade användare som har otillräcklig information om återkallade certifikat inkluderar alla användare som inte har attributet "LastPasswordChangeTimestamp" synkroniserat. De här användarna får den här kort maximala åldern eftersom AAD inte kan verifiera när token som är kopplade till en gammal autentiseringsuppgift (till exempel ett lösen ord som har ändrats) måste kontrol leras oftare för att se till att användaren och tillhör ande tokens fortfarande är i ett lyckat position. För att förbättra den här upplevelsen måste klient administratörerna se till att de synkroniserar attributet "LastPasswordChangeTimestamp" (detta kan anges för användarobjektet med PowerShell eller via AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Princip utvärdering och prioritering
Du kan skapa och tilldela en livs längd princip för token till ett särskilt program, till din organisation och till tjänstens huvud namn. Flera principer kan gälla för ett enskilt program. Den token-princip för token som börjar gälla följer dessa regler:

* Om en princip uttryckligen tilldelas till tjänstens huvud namn, tillämpas den.
* Om ingen princip tilldelas explicit till tjänstens huvud namn tillämpas en princip som uttryckligen tilldelas till den överordnade organisationen för tjänstens huvud namn.
* Om ingen princip har tilldelats till tjänstens huvud namn eller till organisationen tillämpas principen som tilldelats programmet.
* Om ingen princip har tilldelats till tjänstens huvud namn, organisation eller program objekt, tillämpas standardvärdena. (Mer information finns i tabellen i [konfigurations bara livs längds egenskaper för token](#configurable-token-lifetime-properties).)

Mer information om relationen mellan program objekt och tjänst huvud objekt finns i [program-och tjänst huvud objekt i Azure Active Directory](app-objects-and-service-principals.md).

Giltighet för token utvärderas vid den tidpunkt då token används. Principen med den högsta prioriteten för det program som används börjar gälla.

Alla tidsintervallen som används här är formaterade enligt C# [TimeSpan](/dotnet/api/system.timespan) -objekt-D. hh: mm: SS.  Så 80 dagar och 30 minuter skulle vara det `80.00:30:00` .  Den inledande D-funktionen kan tas bort om den är noll, så 90 minuter `00:90:00` .  

> [!NOTE]
> Här är ett exempel scenario.
>
> En användare vill ha åtkomst till två webb program: webb program A och webb program B.
> 
> Förhållanden
> * Båda webb programmen finns i samma överordnade organisation.
> * Token för token 1 med en sessionstoken max ålder på åtta timmar har angetts som överordnad organisations standard.
> * Webb program A är ett webb program som används ofta och inte är länkat till några principer.
> * Webb program B används för mycket känsliga processer. Tjänstens huvud namn är länkat till livs längd policy 2, som har en högsta ålder på en token på 30 minuter.
>
> Vid 12:00 PM startar användaren en ny webbläsarsession och försöker komma åt webb program A. Användaren omdirigeras till Azure AD och uppmanas att logga in. Detta skapar en cookie som har en sessionstoken i webbläsaren. Användaren omdirigeras tillbaka till webb program A med en ID-token som ger användaren åtkomst till programmet.
>
> Vid 12:15 PM försöker användaren komma åt webb program B. Webbläsaren omdirigerar till Azure AD, som identifierar sessions-cookien. Webb program B:s tjänstens huvud namn är länkat till livs längd policy 2, men är också en del av den överordnade organisationen, med standard livstid för token 1. Livs längd för token-princip 2 börjar gälla eftersom principer som är kopplade till tjänstens huvud namn har högre prioritet än organisationens standard principer. Sessionstoken utfärdades ursprungligen under de senaste 30 minuterna, så det anses vara giltigt. Användaren omdirigeras tillbaka till webb program B med en ID-token som ger åtkomst till dem.
>
> Vid 1:00 PM försöker användaren komma åt webb program A. Användaren omdirigeras till Azure AD. Webb program A är inte länkad till några principer, men eftersom den finns i en organisation med token för standard-token 1 börjar den tillämpas. Sessions-cookien som ursprungligen utfärdades under de senaste åtta timmarna har upptäckts. Användaren omdirigeras tyst tillbaka till webb program A med en ny ID-token. Användaren behöver inte autentiseras.
>
> Omedelbart efteråt försöker användaren komma åt webb program B. Användaren omdirigeras till Azure AD. Som tidigare börjar token för token-princip 2 gälla. Eftersom token utfärdades mer än 30 minuter sedan uppmanas användaren att ange sina inloggnings uppgifter igen. En ny session-token och ID-token utfärdas. Användaren kan sedan komma åt webb program B.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurerbar princip egenskaps information
### <a name="access-token-lifetime"></a>Livstid för åtkomsttoken
**Sträng:** AccessTokenLifetime

**Påverkar:** Åtkomsttoken, ID-token, SAML-token

**Sammanfattning:** Den här principen styr hur länge åtkomst-och ID-token för den här resursen betraktas som giltiga. Att minska livs längden för åtkomsttoken minskar risken för att en åtkomsttoken eller ID-token används av en skadlig aktör under en längre tid. (De här token kan inte återkallas.) Kompromissen är att prestanda påverkas negativt, eftersom token måste ersättas oftare.

### <a name="refresh-token-max-inactive-time"></a>Maximal inaktiv tid för uppdateringstoken
**Sträng:** MaxInactiveTime

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur gammal en uppdateringstoken kan vara innan en klient inte längre kan använda den för att hämta ett nytt nyckel par för åtkomst/uppdatering vid försök att få åtkomst till den här resursen. Eftersom en ny uppdateringstoken vanligt vis returneras när en uppdateringstoken används, förhindrar den här principen åtkomst om klienten försöker få åtkomst till en resurs med hjälp av den aktuella uppdateringstoken under den angivna tids perioden.

Den här principen tvingar användare som inte har varit aktiva på sin klient att autentiseras igen för att hämta en ny uppdateringstoken.

Egenskapen för maximal inaktiv tid för uppdateringstoken måste anges till ett lägre värde än max åldern för token för en token och Multi-Factor Refresh-token.

### <a name="single-factor-refresh-token-max-age"></a>Högsta ålder för token för enkel uppdatering
**Sträng:** MaxAgeSingleFactor

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en uppdateringstoken för att hämta ett nytt nyckel par för åtkomst/uppdatering efter att de senast autentiserades genom att använda en enda faktor. När en användare autentiserar och tar emot en ny uppdateringstoken kan användaren använda det för att uppdatera token-flödet under den angivna tids perioden. (Detta är sant så länge den aktuella uppdateringstoken inte har återkallats och är inte kvar längre än den inaktiva tiden.) Då tvingas användaren att autentisera igen för att ta emot en ny uppdateringstoken.

Att minska den högsta åldern tvingar användare att autentisera sig oftare. Eftersom autentisering med en faktor anses vara mindre säker än Multi-Factor Authentication, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller lägre än den högsta ålders egenskapen för Multi-Factor Refresh-token.

### <a name="multi-factor-refresh-token-max-age"></a>Högsta ålder för Multi-Factor Refresh-token
**Sträng:** MaxAgeMultiFactor

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en uppdateringstoken för att hämta ett nytt nyckel par för åtkomst/uppdatering efter att de senast autentiserades med hjälp av flera faktorer. När en användare autentiserar och tar emot en ny uppdateringstoken kan användaren använda det för att uppdatera token-flödet under den angivna tids perioden. (Detta är sant så länge den aktuella uppdateringstoken inte återkallas och inte används längre än den inaktiva tiden.) Då tvingas användarna att autentisera igen för att få en ny uppdateringstoken.

Att minska den högsta åldern tvingar användare att autentisera sig oftare. Eftersom autentisering med en faktor anses vara mindre säker än Multi-Factor Authentication, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller större än max ålders egenskapen för en token för en enskild faktor.

### <a name="single-factor-session-token-max-age"></a>Högsta ålder för token för token för en session
**Sträng:** MaxAgeSessionSingleFactor

**Påverkar:** Token för sessioner (beständiga och inte permanenta)

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en sessionstoken för att hämta ett nytt ID och sessionstoken efter att de senast autentiserades genom att använda en enda faktor. När en användare autentiserar och tar emot en ny sessionstoken, kan användaren använda sessionens token-flöde under den angivna tids perioden. (Detta är sant så länge den aktuella sessionstoken inte har återkallats och har inte gått ut.) Efter den angivna tids perioden tvingas användaren att autentisera igen för att ta emot en ny sessionstoken.

Att minska den högsta åldern tvingar användare att autentisera sig oftare. Eftersom autentisering med en faktor anses vara mindre säker än Multi-Factor Authentication, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller mindre än max ålders egenskapen för Multi-Factor session-token.

### <a name="multi-factor-session-token-max-age"></a>Högsta ålder för Multi-Factor session
**Sträng:** MaxAgeSessionMultiFactor

**Påverkar:** Token för sessioner (beständiga och inte permanenta)

**Sammanfattning:** Den här principen styr hur länge en användare kan använda en sessionstoken för att hämta ett nytt ID och sessionstoken efter den senaste gången de autentiserades genom att använda flera faktorer. När en användare autentiserar och tar emot en ny sessionstoken, kan användaren använda sessionens token-flöde under den angivna tids perioden. (Detta är sant så länge den aktuella sessionstoken inte har återkallats och har inte gått ut.) Efter den angivna tids perioden tvingas användaren att autentisera igen för att ta emot en ny sessionstoken.

Att minska den högsta åldern tvingar användare att autentisera sig oftare. Eftersom autentisering med en faktor anses vara mindre säker än Multi-Factor Authentication, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller större än den token för token för token med en token.

## <a name="example-token-lifetime-policies"></a>Exempel på livs längds principer för token
Många scenarier är möjliga i Azure AD när du kan skapa och hantera livs längder för token för appar, tjänstens huvud namn och din övergripande organisation. I det här avsnittet går vi igenom några vanliga princip scenarier som hjälper dig att införa nya regler för:

* Tokenlivstid
* Maximal inaktiv tid för token
* Maximal ålder för token

I exemplen får du lära dig att:

* Hantera en organisations standard princip
* Skapa en princip för webb inloggning
* Skapa en princip för en intern app som anropar ett webb-API
* Hantera en avancerad princip

### <a name="prerequisites"></a>Krav
I följande exempel kan du skapa, uppdatera, länka och ta bort principer för appar, tjänstens huvud namn och din övergripande organisation. Om du är nybörjare på Azure AD rekommenderar vi att du lär dig [hur du skaffar en Azure AD-klient](quickstart-create-new-tenant.md) innan du fortsätter med de här exemplen.  

Gör så här för att komma igång:

1. Ladda ned den senaste [Azure AD PowerShell-modulens offentliga för hands version](https://www.powershellgallery.com/packages/AzureADPreview).
2. Kör `Connect` kommandot för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gången du startar en ny session.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Kör följande kommando för att se alla principer som har skapats i din organisation. Kör det här kommandot efter de flesta åtgärder i följande scenarier. Genom att köra kommandot kan du också få * * * * av dina principer.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exempel: hantera en organisations standard princip
I det här exemplet skapar du en princip som gör det möjligt för användarna att logga in mindre ofta i hela organisationen. Det gör du genom att skapa en token för token för en token för en token som tillämpas i hela organisationen. Principen tillämpas på alla program i din organisation och för varje tjänst huvud konto som inte redan har en princip uppsättning.

1. Skapa en livs längd princip för token.

    1. Ange en token för uppdatering av en faktor till "tills den har återkallats". Token upphör inte förrän åtkomsten har återkallats. Skapa följande princip definition:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Kör följande kommando för att skapa principen:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Om du vill ta bort alla blank steg kör du följande kommando:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Om du vill se den nya principen och hämta principens **ObjectID**kör du följande kommando:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Uppdatera principen.

    Du kan bestämma att den första principen som du ställer in i det här exemplet inte är lika strikt som din tjänst kräver. Kör följande kommando för att ange att en token för uppdatering av en enskild faktor upphör att gälla om två dagar:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exempel: skapa en princip för webb inloggning

I det här exemplet skapar du en princip som kräver att användare autentiseras oftare i din webbapp. Den här principen anger livs längden för åtkomst-/ID-token och den maximala åldern för en Multi-Factor session-token till tjänstens huvud namn för din webbapp.

1. Skapa en livs längd princip för token.

    Den här principen, för webb inloggning, ställer in livs längden för Access/ID-token och maximal ålder för token för en token i två timmar.

    1. Kör följande kommando för att skapa principen:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Kör följande kommando för att se den nya principen och hämta principen **ObjectID**:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Tilldela principen till tjänstens huvud namn. Du måste också hämta **ObjectID** för tjänstens huvud namn.

    1. Använd cmdleten [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) för att se alla företagets tjänst huvud namn eller ett enda tjänst huvud namn.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. När du har tjänstens huvud namn kör du följande kommando:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exempel: skapa en princip för en intern app som anropar ett webb-API
I det här exemplet skapar du en princip som kräver att användare autentiseras mindre ofta. Principen utvärderar också hur lång tid en användare kan vara inaktiv innan användaren måste autentiseras igen. Principen tillämpas på webb-API: et. När den interna appen begär webb-API: t som en resurs, tillämpas den här principen.

1. Skapa en livs längd princip för token.

    1. Kör följande kommando för att skapa en strikt princip för ett webb-API:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Kör följande kommando för att se den nya principen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Tilldela principen till ditt webb-API. Du måste också hämta **ObjectID** för ditt program. Använd cmdleten [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) för att hitta appens **ObjectId**eller Använd [Azure Portal](https://portal.azure.com/).

    Hämta **ObjectID** för appen och tilldela principen:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Exempel: hantera en avancerad princip
I det här exemplet skapar du några principer för att lära dig hur prioritets systemet fungerar. Du lär dig också hur du hanterar flera principer som tillämpas på flera objekt.

1. Skapa en livs längd princip för token.

    1. Om du vill skapa en organisations standard princip som ställer in livs längden för token för en enskild faktor på 30 dagar kör du följande kommando:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Kör följande kommando för att se den nya principen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Tilldela principen till ett huvud namn för tjänsten.

    Nu har du en princip som gäller för hela organisationen. Du kanske vill bevara denna 30-dagars princip för en tjänst huvud namn, men ändra organisationens standard princip till den övre gränsen för "tills-REVOKE".

    1. Om du vill se alla företagets tjänst huvud namn använder du cmdleten [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. När du har tjänstens huvud namn kör du följande kommando:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Ställ in `IsOrganizationDefault` flaggan på false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Skapa en ny organisations standard princip:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Du har nu den ursprungliga principen som är länkad till tjänstens huvud namn och den nya principen anges som organisationens standard princip. Det är viktigt att komma ihåg att principer som tillämpas på tjänstens huvud namn har prioritet över organisationens standard principer.

## <a name="cmdlet-reference"></a>Cmdlet-referens

### <a name="manage-policies"></a>Hantera principer

Du kan använda följande cmdletar för att hantera principer.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Skapar en ny princip.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matris med stringified-JSON som innehåller alla princip regler. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Sträng för princip namnet. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Om värdet är true anger principen som organisationens standard princip. Om det är falskt, gör ingenting. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ av princip. Använd alltid "TokenLifetimePolicy" för token för token. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>Valfritt |Anger ett alternativt ID för principen. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Hämtar alla Azure AD-principer eller en angiven princip.

```powershell
Get-AzureADPolicy
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code>Valfritt |**ObjectID (ID)** för den princip du vill använda. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Hämtar alla appar och tjänst huvud namn som är länkade till en princip.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för den princip du vill använda. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Uppdaterar en befintlig princip.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för den princip du vill använda. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Sträng för princip namnet. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>Valfritt |Matris med stringified-JSON som innehåller alla princip regler. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>Valfritt |Om värdet är true anger principen som organisationens standard princip. Om det är falskt, gör ingenting. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>Valfritt |Typ av princip. Använd alltid "TokenLifetimePolicy" för token för token. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>Valfritt |Anger ett alternativt ID för principen. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Tar bort den angivna principen.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för den princip du vill använda. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Användningsprinciper
Du kan använda följande cmdletar för användnings principer.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Länkar den angivna principen till ett program.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectID** för principen. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Hämtar principen som är tilldelad ett program.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för programmet. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Tar bort en princip från ett program.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectID** för principen. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Principer för tjänstens huvud namn
Du kan använda följande cmdletar för principer för tjänstens huvud namn.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Länkar den angivna principen till ett huvud namn för tjänsten.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectID** för principen. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Hämtar en princip som är länkad till det angivna huvud namnet för tjänsten.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för programmet. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Tar bort principen från det angivna huvud namnet för tjänsten.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectID (ID)** för programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectID** för principen. | `-PolicyId <ObjectId of Policy>` |

## <a name="license-requirements"></a>Licenskrav

Om du använder den här funktionen krävs en licens för Azure AD Premium P1. Information om rätt licens för dina krav finns i [jämföra allmänt tillgängliga funktioner i de kostnads fria versionerna och Premium-versionerna](https://azure.microsoft.com/pricing/details/active-directory/).

Kunder med [Microsoft 365 Business licenser](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) har också till gång till funktioner för villkorlig åtkomst.
