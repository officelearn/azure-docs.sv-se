---
title: "Konfigurerbara token livslängd i Azure Active Directory | Microsoft Docs"
description: "Lär dig hur du ställer in livslängd för token som utfärdats av Azure AD."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: billmath
ms.custom: aaddev
ms.reviewer: anchitn
ms.openlocfilehash: eaf9e7088c8c88140ea690c13ff7e0c7026b8f86
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Konfigurerbara token livslängd i Azure Active Directory (förhandsversion)
Du kan ange livslängden för en token som utfärdas av Azure Active Directory (AD Azure). Du kan ange token livslängd för alla program i din organisation, för ett program för flera innehavare (flera organisation) eller för en specifik tjänstens huvudnamn i din organisation.

> [!NOTE]
> Den här funktionen är för närvarande i förhandsversion. Var beredd på att återställa eller ta bort alla ändringar. Funktionen är tillgänglig i alla Azure Active Directory-prenumeration under Public Preview. Men när funktionen blir allmänt tillgänglig vissa aspekter av funktionen kan kräva en [Azure Active Directory Premium](active-directory-get-started-premium.md) prenumeration.
>
>

I Azure AD representerar ett grupprincipobjekt en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation. Varje princip har ett unikt struktur med en uppsättning egenskaper som tillämpas på objekt som de har tilldelats.

Du kan ange en princip som standardprincipen för din organisation. Principen tillämpas på alla program i organisationen, så länge det inte åsidosätts av en princip med högre prioritet. Du kan också tilldela en princip till specifika program. Prioritetsordningen varierar beroende på principtypen.


## <a name="token-types"></a>Typer av token

Du kan ange principer för livslängd för token för uppdaterings-tokens, åtkomsttoken, session token och ID-token.

### <a name="access-tokens"></a>Åtkomst-token
Klienter använder åtkomsttoken att få åtkomst till en skyddad resurs. En åtkomst-token kan användas endast för en specifik kombination av användare, klienten och resursen. Åtkomst-token kan inte återkallas och är giltiga tills de upphör att gälla. En skadlig aktören som har fått en åtkomst-token kan använda det för omfattningen av dess livslängd. Justera livslängden för en åtkomst-token är en kompromiss mellan förbättra systemets prestanda och öka mängden tid att klienten behåller åtkomst efter användarens konto har inaktiverats. Förbättrad prestanda uppnås genom att minska antalet gånger som en klient behöver skaffa en ny åtkomsttoken.

### <a name="refresh-tokens"></a>Uppdatera token
När en klient får en åtkomst-token för att få åtkomst till en skyddad resurs, får klienten både en uppdateringstoken och en åtkomst-token. Uppdateringstoken som används för att hämta nya åtkomst/uppdatera token par när den aktuella åtkomst-token upphör att gälla. En uppdateringstoken är bunden till en kombination av användar- och klienten. En uppdateringstoken kan återkallas och token giltighet kontrolleras varje gång token som används.

Det är viktigt att se skillnad mellan konfidentiell och offentliga klienter. Läs mer om olika typer av klienter, [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Token livslängd med konfidentiella klienten uppdatera token
Konfidentiell klienter är program som kan lagras säkert lösenord klienten (hemliga). De kan visa att förfrågningarna kommer från klientprogrammet och inte från en skadlig aktören. Ett webbprogram är till exempel en konfidentiell klient eftersom det kan lagra en klienthemlighet på webbservern. Exponeras inte. Eftersom dessa flöden är säkrare standard livslängd för uppdaterings-tokens som utfärdats till dessa flöden är `until-revoked`, kan inte ändras med hjälp av Grupprincip och kommer inte att återkallas på frivilliga lösenordsåterställning.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Token livslängd med offentliga klienten uppdatera token

Offentliga klienter kan inte på ett säkert sätt att lagra ett klient-lösenord (hemliga). En iOS/Android-app kan till exempel obfuscate en hemlighet från resursägare så är det en offentlig klient. Du kan ange principer resurser för att förhindra uppdaterings-tokens från offentliga klienter som är äldre än en angiven period från att erhålla ett nytt åtkomst/uppdatera token par. (Gör du genom att använda egenskapen uppdatera Token inaktiva maxtid.) Du kan också använda principer för att ange en tidsperiod efter vilken uppdaterings-tokens accepteras inte längre. (Det gör du genom att använda egenskapen uppdatera Token Max Age.) Du kan justera livslängden för en uppdateringstoken för att styra när och hur ofta användaren krävs för att ange autentiseringsuppgifter, i stället för att tyst återautentiseras, när du använder en offentlig klientprogrammet.

### <a name="id-tokens"></a>ID-token
ID-token skickas till webbplatser och interna klienter. ID-token innehåller profilinformation om en användare. En ID-token är bunden till en specifik kombination av användar- och klienten. ID-token anses giltiga tills de upphör att gälla. Vanligtvis ett webbprogram matchar en användare har sessioners livstid i tillämpningsprogrammet att livslängden för ID-token som utfärdas för användaren. Du kan justera livslängden för en ID-token för att styra hur ofta webbprogrammet upphör sessionen program och hur ofta den kräver att användaren autentiseras med Azure AD (tyst eller interaktivt).

### <a name="single-sign-on-session-tokens"></a>Enkel inloggning session token
När en användare autentiseras med Azure AD upprättas en enkel inloggning (SSO) session med användarens webbläsare och Azure AD. SSO-token i form av en cookie representerar den här sessionen. Observera sessionstoken SSO inte är bunden till en specifik resurs/klientprogrammet. SSO session token kan återkallas och deras giltighet kontrolleras varje gång de används.

Azure AD använder två typer av token för SSO-session: beständiga och Uppdateringsvärdet. Beständiga session token lagras som beständiga cookies i webbläsaren. Uppdateringsvärdet session token lagras som sessions-cookies. (Sessionscookies förstörs när webbläsaren stängs.) Vanligtvis lagras Uppdateringsvärdet sessionstoken. Men när användaren väljer den **Håll mig inloggad** kryssrutan under autentiseringen beständiga sessionstoken lagras.

Uppdateringsvärdet session token har en livslängd på 24 timmar. Beständiga token har en livslängd på 180 dagar. Varje gång en SSO sessionstoken används inom sin giltighetstid förlängs giltighetsperioden annat 24 timmar eller 180 dagar, beroende på typen av token. Om en SSO sessionstoken inte används inom sin giltighetstid, anses det upphört att gälla och är inte längre att accepteras.

Du kan använda en princip för att ställa in tiden efter den första sessionstoken utfärdades utöver som sessionstoken längre accepteras. (Gör du genom att använda egenskapen Session Token Max Age.) Du kan justera livslängden för en sessionstoken att styra när och hur ofta en användare krävs för att ange autentiseringsuppgifter, i stället för tyst verifierats när du använder ett webbprogram.

### <a name="token-lifetime-policy-properties"></a>Egenskaper för livslängd för token
En princip för livslängd för token är en typ av grupprincipobjekt som innehåller regler för livslängd för token. Använd egenskaperna för principen för att styra angivna token livslängd. Om ingen princip har angetts, tillämpar systemet livstid standardvärdet.

### <a name="configurable-token-lifetime-properties"></a>Livslängd för token konfigurerbara egenskaper
| Egenskap | Princip för egenskapssträng | Påverkar | Standard | Minimum | Maximal |
| --- | --- | --- | --- | --- | --- |
| Livslängd för åtkomst-Token |AccessTokenLifetime |Åtkomsttoken, ID-token, SAML2-token |1 timme |10 minuter |1 dag |
| Uppdatera Token inaktiva Maxtid |MaxInactiveTime |Uppdatera token |90 dagar |10 minuter |90 dagar |
| Enskild faktor uppdatera Token maximal ålder |MaxAgeSingleFactor |Uppdatera token (för alla användare) |Until-revoked |10 minuter |Until-revoked<sup>1</sup> |
| Multi-Factor uppdatera Token maximal ålder |MaxAgeMultiFactor |Uppdatera token (för alla användare) |Until-revoked |10 minuter |Until-revoked<sup>1</sup> |
| Enskild faktor Session Token maximal ålder |MaxAgeSessionSingleFactor<sup>2</sup> |Sessionen token (beständiga och Uppdateringsvärdet) |Until-revoked |10 minuter |Until-revoked<sup>1</sup> |
| Multi-Factor Session Token maximal ålder |MaxAgeSessionMultiFactor<sup>3</sup> |Sessionen token (beständiga och Uppdateringsvärdet) |Until-revoked |10 minuter |Until-revoked<sup>1</sup> |

* <sup>1</sup>365 dagar är maxlängden explicit som kan anges för dessa attribut.
* <sup>2</sup>om **MaxAgeSessionSingleFactor** är inte ange det här värdet tar den **MaxAgeSingleFactor** värde. Om varken parametern anges tar egenskapen standardvärdet (förrän har återkallats).
* <sup>3</sup>om **MaxAgeSessionMultiFactor** är inte ange det här värdet tar den **MaxAgeMultiFactor** värde. Om varken parametern anges tar egenskapen standardvärdet (förrän har återkallats).

### <a name="exceptions"></a>Undantag
| Egenskap | Påverkar | Standard |
| --- | --- | --- |
| Uppdatera Token Max Age (utfärdas för federerade användare har inte tillräckligt återkallningsinformation<sup>1</sup>) |Uppdatera token (utfärdas för federerade användare har inte tillräckligt återkallningsinformation<sup>1</sup>) |12 timmar |
| Uppdatera Token inaktiva Maxtid (utfärdats för konfidentiell klienter) |Uppdatera token (utfärdats för konfidentiell klienter) |90 dagar |
| Uppdatera Token Max Age (utfärdats för konfidentiell klienter) |Uppdatera token (utfärdats för konfidentiell klienter) |Until-revoked |

* <sup>1</sup>externa användare har inte tillräckligt återkallningsinformation innehåller alla användare som inte har attributet ”LastPasswordChangeTimestamp” synkroniseras. Dessa användare får den här korta Max Age eftersom AAD inte går att kontrollera när återkalla token som är knutna till gamla autentiseringsuppgifter (till exempel ett lösenord som har ändrats) och måste checka in mer ofta så att användare och associerade token är fortfarande i god position. För att förbättra upplevelsen innehavaradministratörer se till att de synkroniserar attributet ”LastPasswordChangeTimestamp” (Detta kan ställas in på användarobjekt med hjälp av Powershell eller via AADSync).

### <a name="policy-evaluation-and-prioritization"></a>För principutvärdering och prioritering
Du kan skapa och tilldela sedan en livslängd för token-principen till ett visst program, för din organisation och till tjänstens huvudnamn. Flera principer kan tillämpas på ett visst program. Dessa regler: principen livslängd för token som påverkas

* Om en princip tilldelas explicit till tjänstens huvudnamn, tillämpas.
* Om ingen princip uttryckligen har tilldelats till tjänstens huvudnamn, tillämpas en princip som uttryckligen har tilldelats organisationen överordnade för tjänstens huvudnamn.
* Om ingen princip uttryckligen har tilldelats till tjänstens huvudnamn eller till organisationen, tillämpas principen som tilldelats programmet.
* Om ingen princip har tilldelats till tjänstens huvudnamn, organisationen eller programobjektet, tillämpas standardvärdena. (Se tabellen i [livslängd för token konfigurerbara egenskaper](#configurable-token-lifetime-properties).)

Läs mer om förhållandet mellan program och tjänstens huvudnamn objekt [program och tjänstens huvudnamn objekt i Azure Active Directory](active-directory-application-objects.md).

En token giltigheten utvärderas när token som används. Principen med den högsta prioriteten på det program som används börjar gälla.

> [!NOTE]
> Här är ett exempel.
>
> En användare vill få åtkomst till två webbprogram: A-webbprogram och Web Application B.
> 
> Faktorer:
> * Både webbprogram finns i samma överordnade organisation.
> * Token livstid princip 1 med en Session Token Max Age åtta timmar har angetts som standard för den överordnade organisationen.
> * Webbprogrammet A är en vanlig användning webbprogram och inte är länkad till alla principer.
> * Web Application B används för mycket känslig processer. Dess tjänstens huvudnamn är kopplad till Token livstid princip 2, som har en Session Token Max Age 30 minuter.
>
> Klockan 12:00, användaren startar en ny webbläsarsession och försöker få åtkomst till webbprogrammet A. Användaren omdirigeras till Azure AD och uppmanas att logga in. Detta skapar en cookie som har en sessionstoken i webbläsaren. Användaren omdirigeras till ett webbprogram med en ID-token som används att få åtkomst till programmet.
>
> Klockan 12:15 försöker användaren få åtkomst till Web Application B. Webbläsaren omdirigerar till Azure AD, som identifierar sessions-cookie. Web Application B tjänstens huvudnamn är kopplad till Token livstid princip 2, men det är också en del av den överordnade organisationen med standard Token livstid princip 1. Token livstid princip 2 börjar gälla eftersom principer som är länkade till tjänstens huvudnamn har högre prioritet än standardprinciperna för organisationen. Sessionstoken ursprungligen utfärdades under de senaste 30 minuterna, så att det ska vara giltigt. Användaren omdirigeras tillbaka till Web Application B med en ID-token som ger dem behörighet.
>
> Klockan 13:00 försöker användaren få åtkomst till webbprogrammet A. Användaren omdirigeras till Azure AD. Webbplatsen program inte är länkad till alla principer, men eftersom den är i en organisation med standard Token livstid princip 1 principen träder i kraft. Sessions-cookie som ursprungligen utfärdades under de senaste åtta timmarna har identifierats. Tyst omdirigeras användaren till webbprogrammet A med en ny ID-token. Användaren krävs inte för att autentisera.
>
> Omedelbart efteråt försöker användaren få åtkomst till Web Application B. Användaren omdirigeras till Azure AD. Som börjar tidigare, Token livstid princip 2 gälla. Eftersom token utfärdats mer än 30 minuter sedan, uppmanas användaren att ange sina inloggningsuppgifter. En helt ny sessionstoken och ID-token utfärdas. Användaren kan sedan komma åt Web Application B.
>
>

## <a name="configurable-policy-property-details"></a>Information om konfigurerbar princip
### <a name="access-token-lifetime"></a>Livslängd för åtkomst-Token
**Sträng:** AccessTokenLifetime

**Påverkar:** åtkomsttoken, ID-token

**Sammanfattning:** den här principen styr hur länge åtkomst och ID-token för den här resursen är giltiga. Minska egenskapen åtkomst livslängd för Token minskar du risken för en åtkomst-token eller ID-token som används av en skadlig aktören under en längre tidsperiod. (Dessa token kan inte återkallas.) En kompromiss är att prestanda påverkas negativt, eftersom token som behöver ersättas oftare.

### <a name="refresh-token-max-inactive-time"></a>Uppdatera Token inaktiva Maxtid
**Sträng:** MaxInactiveTime

**Påverkar:** Uppdateringstoken

**Sammanfattning:** den här principen styr hur gammal en uppdateringstoken kan vara innan en klient inte längre använda den för att hämta ett nytt åtkomst/uppdatera token par vid försök att komma åt den här resursen. Eftersom en ny uppdateringstoken vanligtvis returneras när en uppdateringstoken används förhindrar den här principen åtkomst om klienten försöker komma åt en resurs med hjälp av den aktuella uppdateringstoken under den angivna tidsperioden.

Den här principen gör att användare som inte har varit aktivt på deras klient autentiseras för att hämta en ny uppdateringstoken.

Uppdatera Token inaktiva Maxtid-egenskapen måste anges till ett lägre värde än en faktor Token Max Age och Multi-Factor uppdatera Token Max Age egenskaper.

### <a name="single-factor-refresh-token-max-age"></a>Enskild faktor uppdatera Token maximal ålder
**Sträng:** MaxAgeSingleFactor

**Påverkar:** Uppdateringstoken

**Sammanfattning:** den här principen styr hur lång tid en användare kan använda en uppdateringstoken för att få ett nytt åtkomst/uppdatera token par efter de senaste autentiseras korrekt med hjälp av bara en enda faktor. När en användare autentiseras och tar emot en ny uppdateringstoken kan använda användaren uppdatering tokenflöde för den angivna tidsperioden. (Detta är SANT så länge den aktuella uppdateringstoken inte har återkallats och det inte inte används längre än den inaktiva tiden.) Då måste användaren autentiseras för att få en ny uppdateringstoken.

Minska maximal ålder tvingar användare att autentisera oftare. Eftersom en faktor-autentisering anses vara mindre säkert än multifaktorautentisering, rekommenderar vi att du anger egenskapen till ett värde som är lika med eller mindre än egenskapen Multi-Factor uppdatera Token Max Age.

### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor uppdatera Token maximal ålder
**Sträng:** MaxAgeMultiFactor

**Påverkar:** Uppdateringstoken

**Sammanfattning:** den här principen styr hur lång tid en användare kan använda en uppdateringstoken för att få ett nytt åtkomst/uppdatera token par efter de senaste autentiseras korrekt med hjälp av flera faktorer. När en användare autentiseras och tar emot en ny uppdateringstoken kan använda användaren uppdatering tokenflöde för den angivna tidsperioden. (Detta är SANT så länge som den aktuella uppdateringstoken inte har återkallats och är inte inte används längre än den inaktiva tiden.) Då tvingas användare att autentiseras för att få en ny uppdateringstoken.

Minska maximal ålder tvingar användare att autentisera oftare. Eftersom en faktor-autentisering anses vara mindre säkert än multifaktorautentisering, rekommenderar vi att du anger egenskapen till ett värde som är lika med eller större än egenskapen enskild faktor uppdatera Token Max Age.

### <a name="single-factor-session-token-max-age"></a>Enskild faktor Session Token maximal ålder
**Sträng:** MaxAgeSessionSingleFactor

**Påverkar:** Session token (beständiga och Uppdateringsvärdet)

**Sammanfattning:** den här principen styr hur lång tid en användare kan använda en sessionstoken få ett nytt-ID och sessionstoken efter de senaste autentiseras korrekt med hjälp av bara en enda faktor. När en användare autentiseras och tar emot en ny session-token kan använda användaren session tokenflöde för den angivna tidsperioden. (Detta är SANT så länge den aktuella sessionstoken inte har återkallats och inte har upphört att gälla.) Efter en angiven tidsperiod måste användaren autentiseras för att få en ny session-token.

Minska maximal ålder tvingar användare att autentisera oftare. Eftersom en faktor-autentisering anses vara mindre säkert än multifaktorautentisering, rekommenderar vi att du anger egenskapen till ett värde som är lika med eller mindre än egenskapen Multi-Factor Session Token Max Age.

### <a name="multi-factor-session-token-max-age"></a>Multi-Factor Session Token maximal ålder
**Sträng:** MaxAgeSessionMultiFactor

**Påverkar:** Session token (beständiga och Uppdateringsvärdet)

**Sammanfattning:** den här principen styr hur lång tid en användare kan använda en sessionstoken få ett nytt-ID och session token efter den senaste gången som de har autentiseras med hjälp av flera faktorer. När en användare autentiseras och tar emot en ny session-token kan använda användaren session tokenflöde för den angivna tidsperioden. (Detta är SANT så länge den aktuella sessionstoken inte har återkallats och inte har upphört att gälla.) Efter en angiven tidsperiod måste användaren autentiseras för att få en ny session-token.

Minska maximal ålder tvingar användare att autentisera oftare. Eftersom en faktor-autentisering anses vara mindre säkert än multifaktorautentisering, rekommenderar vi att du anger egenskapen till ett värde som är lika med eller större än egenskapen enskild faktor Session Token Max Age.

## <a name="example-token-lifetime-policies"></a>Exempel på principer livslängd för token
Många scenarier är möjliga i Azure AD när du kan skapa och hantera token livslängd för appar, tjänstens huvudnamn och din organisation. I det här avsnittet går vi igenom några vanliga princip scenarier som kan hjälpa dig att införa nya regler för:

* Livslängd för token
* Token inaktiva Maxtid
* Token maximal ålder

I exemplen, kan du lära dig hur du:

* Hantera standardprincipen för en organisation
* Skapa en princip för web-inloggning
* Skapa en princip för en intern app som anropar ett webb-API
* Hantera en princip för Avancerat

### <a name="prerequisites"></a>Förutsättningar
I följande exempel du skapa, uppdatera, länkar och ta bort principer för appar, tjänstens huvudnamn och din organisation. Om du har använt Azure AD, rekommenderar vi att du lär dig mer om [hur du hämtar en Azure AD-klient](active-directory-howto-tenant.md) innan du fortsätter med de här exemplen.  

Utför följande steg för att komma igång:

1. Ladda ned senaste [Azure AD PowerShell-modulen offentliga förhandsversionen](https://www.powershellgallery.com/packages/AzureADPreview).
2. Kör den `Connect` kommando för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gång startar du en ny session.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Kör följande kommando för att se alla principer som har skapats i din organisation. Kör detta kommando efter de flesta åtgärder i följande scenarier. Kör kommandot hjälper dig också att hämta den ** ** dina principer.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exempel: Hantera standardprincipen för en organisation
I det här exemplet skapar du en princip som gör att användare kan logga in mindre ofta i hela organisationen. Om du vill göra det, skapar du en livslängd för token-princip för enskild faktor uppdatera token som används inom organisationen. Principen tillämpas på alla program i din organisation och varje tjänstens huvudnamn som inte redan har en princip.

1. Skapa en princip för livslängd för token.

    1.  Ange Uppdateringstoken som en faktor att ”tills har återkallats”. Token gälla inte förrän åtkomst har återkallats. Skapa följande principdefinitionen:

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Om du vill skapa principen, kör du följande kommando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Att se din nya princip och principen **ObjectId**, kör du följande kommando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Uppdatera principen.

    Du kan välja den första principen som du anger i det här exemplet inte är så strikta din tjänst kräver. Ange din enda faktor uppdatera Token att gälla inom två dagar genom att köra följande kommando:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exempel: Skapa en princip för web-inloggning

I det här exemplet skapar du en princip som kräver att användare autentiseras oftare i ditt webbprogram. Den här principen anger livslängd för token som åtkomst-ID och ett Multi-Factor sessionstoken maximal ålder till tjänstens huvudnamn för ditt webbprogram.

1. Skapa en princip för livslängd för token.

    Den här principen för web inloggning, anger åtkomst/ID livslängd för token och token ålder max single-factor-session till två timmar.

    1.  Om du vill skapa principen, kör du kommandot:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Se din nya principen och för att få principen **ObjectId**, kör du följande kommando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  Tilldela principen till tjänstens huvudnamn. Du måste också hämta de **ObjectId** för tjänstens huvudnamn. 

    1.  Du kan fråga om du vill se alla organisationens tjänstens huvudnamn [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). I [Azure AD Graph Explorer](https://graphexplorer.cloudapp.net/), logga in på Azure AD-kontot.

    2.  När du har den **ObjectId** av din tjänstens huvudnamn, kör du följande kommando:

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exempel: Skapa en princip för en intern app som anropar ett webb-API
I det här exemplet kan du skapa en princip som kräver att användarna autentiseras mindre ofta. Principen förlängs också hur lång tid som en användare kan vara inaktiv innan användaren måste autentiseras. Principen tillämpas på webb-API. När den inbyggda appen begär webb-API som en resurs, används den här principen.

1. Skapa en princip för livslängd för token.

    1.  Om du vill skapa en strikt princip för en webb-API, kör du följande kommando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Se din nya principen och för att få principen **ObjectId**, kör du följande kommando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Tilldela principen till ditt webb-API. Du måste också hämta de **ObjectId** för programmet. Det bästa sättet att hitta din app **ObjectId** är att använda den [Azure-portalen](https://portal.azure.com/).

   När du har den **ObjectId** för din app, kör du följande kommando:

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Exempel: Hantera en princip för Avancerat
I det här exemplet skapar du några principer för att lära dig hur systemets prioritet fungerar. Du kan också lära dig hur du hanterar flera principer som tillämpas på flera objekt.

1. Skapa en princip för livslängd för token.

    1.  Om du vill skapa en standardprincip för organisationen som anger livslängd för en faktor uppdatera Token till 30 dagar, kör du följande kommando:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Att se din nya princip och principen **ObjectId**, kör du följande kommando:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Tilldela principen till ett huvudnamn för tjänsten.

    Du har nu en princip som gäller för hela organisationen. Du kanske vill bevara 30-dagars principen för en specifik tjänstens huvudnamn, men ändra standardprincipen för organisationen till den övre gränsen för ”tills har återkallats”.

    1.  Du kan fråga om du vill se alla organisationens tjänstens huvudnamn [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). I [Azure AD Graph Explorer](https://graphexplorer.cloudapp.net/), logga in med hjälp av Azure AD-kontot.

    2.  När du har den **ObjectId** av din tjänstens huvudnamn, kör du följande kommando:

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Ange den `IsOrganizationDefault` flaggan false:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Skapa en ny organisation standardprincip:

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Nu har du den ursprungliga principen som är kopplad till tjänstens huvudnamn och den nya principen har angetts som din organisation standardprincipen. Det är viktigt att komma ihåg att principer som tillämpas på tjänstens huvudnamn har högre prioritet än standardprinciperna för organisationen.

## <a name="cmdlet-reference"></a>Cmdlet-referens

### <a name="manage-policies"></a>Hantera principer

Du kan använda följande cmdletar för att hantera principer.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Skapar en ny princip.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Matris med stringified JSON som innehåller alla principregler. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Principnamnet textsträng. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Om värdet är true anger du principen som organisationens standardprincipen. Om värdet är FALSKT får ingen effekt. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typen av princip. Token livslängd alltid använda ”TokenLifetimePolicy”. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Valfritt] |Anger ett alternativt ID för principen. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Hämtar alla Azure AD-principer eller en angiven princip.

```PowerShell
Get-AzureADPolicy
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> [Valfritt] |**Objekt-ID (Id)** på den princip som du vill använda. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Hämtar alla appar och tjänstens huvudnamn som är länkade till en princip.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** på den princip som du vill använda. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Uppdaterar en befintlig princip.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** på den princip som du vill använda. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Principnamnet textsträng. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Valfritt] |Matris med stringified JSON som innehåller alla principregler. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Valfritt] |Om värdet är true anger du principen som organisationens standardprincipen. Om värdet är FALSKT får ingen effekt. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Valfritt] |Typen av princip. Token livslängd alltid använda ”TokenLifetimePolicy”. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Valfritt] |Anger ett alternativt ID för principen. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Tar bort den angivna principen.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** på den princip som du vill använda. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Användningsprinciper
Du kan använda följande cmdletar för principer för program.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Länkar den angivna principen till ett program.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** av programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** av principen. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Hämtar den princip som har tilldelats ett program.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** av programmet. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Tar bort en princip från ett program.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** av programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** av principen. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Huvudprinciper för tjänst
Du kan använda följande cmdletar för tjänstens huvudnamn principer.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Länkar den angivna principen till ett huvudnamn för tjänsten.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** av programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** av principen. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Hämtar alla principer som är länkad till angivna tjänstens huvudnamn.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** av programmet. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Tar bort principen från den angivna tjänsten huvudnamn.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**Objekt-ID (Id)** av programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** av principen. | `-PolicyId <ObjectId of Policy>` |
