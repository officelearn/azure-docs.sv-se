---
title: Konfigurerbara tokenlivslängder i Azure Active Directory | Microsoft Docs
description: Lär dig hur du ställer in livslängd för token som utfärdas av Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7b0242a8e3745a0014e5c2a1289ca2bc8c85c75
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484551"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Konfigurerbara tokenlivslängder i Azure Active Directory (förhandsversion)

Du kan ange livslängden för en token utfärdad av Azure Active Directory (AD Azure). Du kan ange livslängd för token för alla appar i din organisation, för ett program med flera innehavare (flera organisation) eller för en specifik huvudtjänst i organisationen.

> [!IMPORTANT]
> Efter att höra från våra kunder i förhandsversionen planerar vi att ersätta den här funktionen med en ny funktion i Azure Active Directory villkorlig åtkomst.  När den nya funktionen är klart kommer gälla den här funktionen slutligen upphör att efter en meddelandeperiod.  Om du använder konfigurerbara livslängd för Token-princip måste förberedas för att växla till den nya funktionen för villkorlig åtkomst när den är tillgänglig. 

En principobjektet representerar en uppsättning regler som tillämpas på enskilda program eller på alla program i en organisation i Azure AD. Varje Principtyp har en unik struktur, där en uppsättning egenskaper som tillämpas på objekt som de har tilldelats.

Du kan ange en princip som standardprincipen för din organisation. Principen tillämpas på alla program i organisationen, så länge det inte åsidosätts av en princip med högre prioritet. Du kan också tilldela en princip till specifika program. Efter prioritet varierar beroende på typen av.

> [!NOTE]
> Konfigurerbara livslängd för uppdateringstoken principen stöds inte för SharePoint Online.  Även om du har möjlighet att skapa den här principen via PowerShell, SharePoint Online kommer inte är medveten om den här principen. Referera till den [SharePoint Online-bloggen](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) mer information om hur du konfigurerar timeout för inaktiv session.
>* Standardlivstid för SharePoint Online åtkomsttoken är 1 timme. 
>* Standard max inaktiv tid för SharePoint Online uppdateringstoken är 90 dagar.

## <a name="token-types"></a>Typer av token

Du kan ange livslängd för token-principer för uppdateringstoken, åtkomst-token, session token och ID-token.

### <a name="access-tokens"></a>Åtkomsttokens

Klienter använder åtkomsttoken för att få åtkomst till en skyddad resurs. En åtkomsttoken kan användas endast för en specifik kombination av användare, klienten och resursen. Åtkomsttoken gick inte att återkalla och är giltiga till deras utgångsdatum. En skadliga aktörer som har fått en åtkomsttoken kan använda det för omfattningen av dess livslängd. Justera livslängden för en åtkomsttoken är en kompromiss mellan förbättra systemets prestanda och öka hur lång tid att klienten behåller åtkomst efter användarens konto har inaktiverats. Förbättrad prestanda uppnås genom att minska antalet gånger som en klient behöver skaffa en ny åtkomsttoken.  Standardvärdet är 1 timme - efter 1 timme klienten måste använda uppdateringstoken (vanligtvis tyst) få en ny uppdateringstoken och få åtkomst till token. 

### <a name="refresh-tokens"></a>Uppdatera token

När en klient får en åtkomsttoken för att få åtkomst till en skyddad resurs, får klienten också en uppdateringstoken. Uppdateringstoken används för att hämta nya åtkomst/uppdatera token par när den aktuella åtkomst-token upphör att gälla. En uppdateringstoken är bunden till en kombination av användar- och klienten. En uppdateringstoken kan vara [återkallas när som helst](access-tokens.md#token-revocation), och denna tokens giltighet kontrolleras varje gång används aktuellt token.  Uppdatera token återkallas inte när de används för att hämta nya åtkomsttoken – det är dock bästa praxis att ta bort den gamla token på ett säkert sätt när du hämtar en ny. 

Det är viktigt att göra skillnad mellan konfidentiella klienter och offentliga klienter, eftersom detta påverkar hur länge uppdateringstoken kan användas. Mer information om olika typer av klienter finns i [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Tokenlivslängder med konfidentiell klient uppdateringstoken
Konfidentiella klienter är program som kan på ett säkert sätt att lagra ett klientlösenord (hemligt). De kan visa att förfrågan kommer från säker klientprogrammet och inte från en skadliga aktörer. Till exempel är en webbapp en konfidentiell klient eftersom den kan lagra en klienthemlighet på webbservern. Den visas inte. Eftersom dessa flöden är säkrare, standard-livslängd för uppdateringstoken som utfärdats till dessa flöden är `until-revoked`kan inte ändras med hjälp av Grupprincip och kommer inte att återkallas om frivillig lösenordsåterställning.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Tokenlivslängder med uppdaterings-tokens för offentlig klient

Offentliga klienter kan inte på ett säkert sätt lagra ett klientlösenord (hemligt). En iOS/Android-app kan exempelvis Förvräng en hemlighet från resursägare, så att den anses vara en offentlig klient. Du kan ange principer för resurser för att förhindra uppdaterings-tokens från offentliga klienter som är äldre än en angiven period från att erhålla ett nytt åtkomst/uppdatera token par. (Gör du genom att använda egenskapen uppdatera Token inaktiva Maxtid (`MaxInactiveTime`).) Du kan också använda principer för att ställa in en period utöver som accepteras inte längre uppdaterings-tokens. (Gör du genom att använda egenskapen uppdatera Token Max-Age.) Du kan justera livslängden för en uppdateringstoken för att styra när och hur ofta användaren måste ange autentiseringsuppgifter, i stället för att göra en obevakad återautentiseras, när du använder en offentlig klientprogram igen.

### <a name="id-tokens"></a>ID-tokens
ID-token som skickas till webbplatser och ursprungliga klienter. ID-token innehåller profilinformation om en användare. Ett ID-token som är kopplad till en specifik kombination av användar- och klienten. ID-token anses giltiga till deras utgångsdatum. Vanligtvis är ett webbprogram matchar en användare är sessionens livstid i programmet till livslängden för ID-token som utfärdats för användaren. Du kan justera livslängden för en ID-token för att styra hur ofta webbprogrammet upphör att gälla den program-sessionen och hur ofta det krävs att användaren autentiseras med Azure AD (tyst eller interaktivt).

### <a name="single-sign-on-session-tokens"></a>Sessionen för enkel inloggning för token
När en användare autentiseras med Azure AD upprättas en enkel inloggning (SSO) session med användarens webbläsare och Azure AD. SSO-token i form av en cookie representerar den här sessionen. Observera att sessionstoken SSO inte är bunden till en specifik resurs/client-program. SSO-session token kan återkallas och valideras kontrolleras varje gång de används.

Azure AD använder två typer av token för SSO-session: permanent eller ickebeständig. Beständiga session token lagras som beständiga cookies i webbläsaren. Ickebeständig session token lagras som sessionscookies. (Sessionscookies förstörs när webbläsaren stängs.) Vanligtvis lagras en ickebeständig sessionstoken. Men när användaren väljer den **vill förbli inloggad** kryssrutan under autentiseringen en beständig sessionstoken lagras.

Ickebeständig session token har en livslängd på 24 timmar. Beständiga token har en livslängd på 180 dagar. Helst en SSO-sessionstoken används inom sin giltighetstid förlängs giltighetsperioden en annan 24 timmar eller 180 dagar, beroende på typen av token. Om en SSO-sessionstoken inte används inom sin giltighetstid, betraktas den upphört att gälla och accepteras inte längre.

Du kan använda en princip för att ställa in efter första sessionstoken utfärdades utöver som sessionstoken längre accepteras. (Gör du genom att använda egenskapen Session Token Max-Age.) Du kan justera livslängden för en sessionstoken för att styra när och hur ofta en användare krävs för att ange autentiseringsuppgifter, i stället för som tyst autentiseras, när du använder ett webbprogram igen.

### <a name="token-lifetime-policy-properties"></a>Egenskaper för princip för livslängd för token
En livslängd för token-princip är en typ av grupprincipobjekt som innehåller regler för livslängd för token. Du kan använda egenskaperna för principen för att styra angivna tokenlivslängder. Om ingen princip har angetts, tillämpar livslängd standardvärdet i systemet.

### <a name="configurable-token-lifetime-properties"></a>Konfigurerbara livslängd för token-egenskaper
| Egenskap  | Princip för egenskapssträng | Påverkar | Standard | Minimum | Maximal |
| --- | --- | --- | --- | --- | --- |
| Livslängd för åtkomst-Token |AccessTokenLifetime |Åtkomsttoken, ID-token, SAML2-token |1 timme |10 minuter |1 dag |
| Uppdatera Token inaktiva Maxtid |MaxInactiveTime |Uppdatera token |90 dagar |10 minuter |90 dagar |
| Single-Factor uppdatera Token maximal ålder |MaxAgeSingleFactor |Uppdatera token (för alla användare) |Fram till återkallas |10 minuter |Fram till återkallas<sup>1</sup> |
| Uppdatering av flera faktorer Token maximal ålder |MaxAgeMultiFactor |Uppdatera token (för alla användare) |Fram till återkallas |10 minuter |Fram till återkallas<sup>1</sup> |
| Single-Factor Session Token maximal ålder |MaxAgeSessionSingleFactor<sup>2</sup> |Session-token (permanent eller ickebeständig) |Fram till återkallas |10 minuter |Fram till återkallas<sup>1</sup> |
| Flera faktorer Session Token maximal ålder |MaxAgeSessionMultiFactor<sup>3</sup> |Session-token (permanent eller ickebeständig) |Fram till återkallas |10 minuter |Fram till återkallas<sup>1</sup> |

* <sup>1</sup>365 dagar är maxlängden explicit som kan ställas in för dessa attribut.

### <a name="exceptions"></a>Undantag
| Egenskap  | Påverkar | Standard |
| --- | --- | --- |
| Uppdatera Token Max Age (utfärdats för federerade användare som har inte tillräckligt med återkallningsinformation<sup>1</sup>) |Uppdatera token (utfärdats för federerade användare som har inte tillräckligt med återkallningsinformation<sup>1</sup>) |12 timmar |
| Uppdatera Token inaktiva Maxtid (utfärdats för konfidentiella klienter) |Uppdatera token (utfärdats för konfidentiella klienter) |90 dagar |
| Uppdatera Token Max Age (utfärdats för konfidentiella klienter) |Uppdatera token (utfärdats för konfidentiella klienter) |Fram till återkallas |

* <sup>1</sup>federerade användare som har inte tillräckligt med återkallningsinformation omfattar alla användare som inte har attributet ”LastPasswordChangeTimestamp” synkroniseras. Dessa användare får den här korta Max Age eftersom AAD inte kan verifiera när du ska återkalla token som är knutna till en gammal autentiseringsuppgift (till exempel ett lösenord som har ändrats) och måste checka in mer ofta för att se till att användar- och associerade token är fortfarande i bra  Ständiga. Innehavaradministratörer måste för att förbättra den här ska se till att de synkroniserar attributet ”LastPasswordChangeTimestamp” (Detta kan ställas in på användarobjektet med hjälp av Powershell eller via AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Principutvärdering och prioritering
Du kan skapa och tilldela sedan en livslängd för token-princip till ett visst program, för din organisation och till tjänstens huvudnamn. Flera principer kan gälla för ett visst program. Dessa regler: livslängd för token-principen som påverkas

* Om en princip är explicit tilldelade till tjänstens huvudnamn, tillämpas den.
* Om ingen princip uttryckligen har tilldelats till tjänstens huvudnamn, tillämpas en princip som uttryckligen har tilldelats till den överordnade organisationen för tjänstens huvudnamn.
* Om ingen princip har uttryckligen tilldelats till tjänstens huvudnamn eller i organisationen, tillämpas den princip som tilldelats programmet.
* Om ingen princip har tilldelats till tjänstens huvudnamn, organisationen eller programobjektet, tillämpas standardvärdena. (Se tabellen i [konfigurerbara livslängd för uppdateringstoken egenskaper](#configurable-token-lifetime-properties).)

Mer information om relationen mellan programobjekt och tjänstobjekt finns [program och tjänstobjekt i Azure Active Directory](app-objects-and-service-principals.md).

En token giltigheten utvärderas när används aktuellt token. Principen med den högsta prioriteten på det program som används börjar gälla.

Alla tidsintervallen som används här formateras enligt C# [TimeSpan](https://msdn.microsoft.com/library/system.timespan) objekt – D.HH:MM:SS.  Så 80 dagar och 30 minuter blir `80.00:30:00`.  Ledande D kan släppas om noll, så 90 minuter skulle vara `00:90:00`.  

> [!NOTE]
> Här är ett exempelscenario.
>
> En användare vill få åtkomst till två webbprogram: Webbprogram A och Web Application B.
> 
> Faktorer:
> * Båda webbprogram finns i samma överordnade organisation.
> * Token livslängd princip 1 med ett Session Token Max Age på åtta timmar har angetts som standard för den överordnade organisationen.
> * Webbprogram A är ett webbprogram med normal användning och är inte länkad till alla principer.
> * Web Application B används för mycket känsliga processer. Dess tjänstens huvudnamn är länkad till Token livslängd princip 2, som har en Session Token Max Age 30 minuter.
>
> Kl. 12:00, användaren startar en ny webbläsarsession och försöker få åtkomst till webbprogrammet A. Användaren omdirigeras till Azure AD och uppmanas att logga in. Detta skapar en cookie som har en sessionstoken i webbläsaren. Användaren omdirigeras tillbaka till webbprogrammet A med ett ID-token som används att få åtkomst till programmet.
>
> Klockan 12:15 försöker användaren få åtkomst till Web Application B. Webbläsaren omdirigerar till Azure AD, som identifierar sessions-cookie. Web Application B tjänstens huvudnamn är kopplad till Token livslängd princip 2, men det är också en del av den överordnade organisationen med standard Token livslängd princip 1. Token livslängd princip 2 börjar gälla eftersom principer som är länkade till tjänstens huvudnamn har högre prioritet än standardprinciper för organisationen. Sessionstoken ursprungligen utfärdades under de senaste 30 minuterna, så att det ska vara giltigt. Användaren omdirigeras tillbaka till Web Application B med en ID-token som ger dem åtkomst.
>
> Klockan 13:00 försöker användaren få åtkomst till webbprogrammet A. Användaren omdirigeras till Azure AD. Web Application A inte är länkad till alla principer, men eftersom den är i en organisation med standard Token livslängd princip 1 principen träder i kraft. Sessions-cookie som ursprungligen utfärdades under de senaste åtta timmarna har identifierats. Användaren omdirigeras tyst tillbaka till webbprogrammet A med en ny ID-token. Användaren behöver inte autentisera.
>
> Omedelbart efteråt försöker användaren få åtkomst till Web Application B. Användaren omdirigeras till Azure AD. Som börjar tidigare, Token livslängd princip 2 gälla. Eftersom token utfärdats mer än 30 minuter sedan, uppmanas användaren att ange sina autentiseringsuppgifter för inloggning igen. En helt ny sessionstoken och ID-token som utfärdas. Användaren kan sedan komma åt Web Application B.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurerbara egenskapen principinformation
### <a name="access-token-lifetime"></a>Livslängd för åtkomst-Token
**sträng:** AccessTokenLifetime

**Påverkar:** Åtkomsttoken, ID-token

**Sammanfattning:** Den här principen styr hur länge åtkomst och ID-token för den här resursen anses giltiga. Minska egenskapen livslängd för åtkomst-Token minskar du risken för en åtkomst-token eller ID-token som används av en skadlig aktör under en längre tid. (Dessa token kan inte återkallas.) En kompromiss är att prestanda påverkas negativt, eftersom token som måste ersättas oftare.

### <a name="refresh-token-max-inactive-time"></a>Uppdatera Token inaktiva Maxtid
**sträng:** MaxInactiveTime

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr hur gammal en uppdateringstoken kan vara innan en klient inte längre använda den för att hämta ett nytt åtkomst/uppdatera token par när du försöker komma åt den här resursen. Eftersom en ny uppdateringstoken vanligtvis returneras när en uppdateringstoken används förhindrar den här principen åtkomst om klienten försöker komma åt en resurs med hjälp av den aktuella uppdateringstoken under den angivna tidsperioden.

Den här principen tvingar de användare som inte har varit aktiva på deras klient att autentiseras på nytt om du vill hämta en ny uppdateringstoken.

Uppdatera Token inaktiva Maxtid-egenskapen måste anges till ett lägre värde än den enda faktor Token Max Age och Multi-Factor uppdatera Token Max Age-egenskaper.

### <a name="single-factor-refresh-token-max-age"></a>Single-Factor uppdatera Token maximal ålder
**sträng:** MaxAgeSingleFactor

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr som hur lång tid en användare kan använda en uppdateringstoken för att få ett nytt åtkomst/uppdatera token par när de senast autentiserade har genom att använda endast en enda faktor. När en användare autentiseras och tar emot en ny uppdateringstoken, kan användaren använda uppdatering tokenflöde för den angivna tidsperioden. (Detta gäller så länge som den aktuella uppdateringstoken inte har återkallats och det inte inte används under längre tid än tid som inaktiva.) I det här läget tvingas om användaren att autentiseras på nytt för att ta emot en ny uppdateringstoken.

Minska maximal ålder tvingas användare att autentisera oftare. Eftersom single-factor authentication anses vara mindre säkert än multifaktorautentisering, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller mindre än egenskapen Multi-Factor uppdatera Token Max Age.

### <a name="multi-factor-refresh-token-max-age"></a>Uppdatering av flera faktorer Token maximal ålder
**sträng:** MaxAgeMultiFactor

**Påverkar:** Uppdatera token

**Sammanfattning:** Den här principen styr som hur lång tid en användare kan använda en uppdateringstoken för att hämta ett nytt åtkomst/uppdatera token par efter de senaste autentiseras har med hjälp av flera faktorer. När en användare autentiseras och tar emot en ny uppdateringstoken, kan användaren använda uppdatering tokenflöde för den angivna tidsperioden. (Detta gäller så länge som den aktuella uppdateringstoken inte har återkallats och det inte är oanvänd under längre tid än tid som inaktiva.) I det här läget tvingas användare att autentiseras på nytt för att ta emot en ny uppdateringstoken.

Minska maximal ålder tvingas användare att autentisera oftare. Eftersom single-factor authentication anses vara mindre säkert än multifaktorautentisering, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller större än egenskapen enda faktor uppdatera Token Max Age.

### <a name="single-factor-session-token-max-age"></a>Single-Factor Session Token maximal ålder
**sträng:** MaxAgeSessionSingleFactor

**Påverkar:** Session-token (permanent eller ickebeständig)

**Sammanfattning:** Den här principen styr som hur lång tid en användare kan använda en sessionstoken få ett nytt ID och sessionstoken när de senast autentiserade har genom att använda endast en enda faktor. När en användare autentiseras och får en ny session-token, kan användaren använda session tokenflöde för den angivna tidsperioden. (Detta gäller så länge som den aktuella sessionstoken inte har återkallats och inte har upphört att gälla.) Om användaren tvingas att autentiseras på nytt för att ta emot en ny sessionstoken efter den angivna tidsperioden.

Minska maximal ålder tvingas användare att autentisera oftare. Eftersom single-factor authentication anses vara mindre säkert än multifaktorautentisering, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller mindre än egenskapen Multi-Factor Session Token Max-Age.

### <a name="multi-factor-session-token-max-age"></a>Flera faktorer Session Token maximal ålder
**sträng:** MaxAgeSessionMultiFactor

**Påverkar:** Session-token (permanent eller ickebeständig)

**Sammanfattning:** Den här principen styr hur lång tid en användare kan använda en sessionstoken få ett nytt ID och sessionen token efter den senaste gången de har autentiseras med hjälp av flera faktorer. När en användare autentiseras och får en ny session-token, kan användaren använda session tokenflöde för den angivna tidsperioden. (Detta gäller så länge som den aktuella sessionstoken inte har återkallats och inte har upphört att gälla.) Om användaren tvingas att autentiseras på nytt för att ta emot en ny sessionstoken efter den angivna tidsperioden.

Minska maximal ålder tvingas användare att autentisera oftare. Eftersom single-factor authentication anses vara mindre säkert än multifaktorautentisering, rekommenderar vi att du ställer in den här egenskapen till ett värde som är lika med eller större än egenskapen enda faktor Session Token Max-Age.

## <a name="example-token-lifetime-policies"></a>Exempel på principer livslängd för token
Många scenarier är möjliga i Azure AD när du kan skapa och hantera tokenlivslängder för appar, tjänstens huvudnamn och din organisation. I det här avsnittet ska går vi igenom några vanliga scenarion för principer som kan hjälpa dig att införa nya regler för:

* Tokenlivstid
* Token inaktiva Maxtid
* Token maximal ålder

I exemplen, kan du lära dig hur du:

* Hantera en organisations standardprincipen
* Skapa en princip för webbinloggning
* Skapa en princip för en inbyggd app som anropar ett webb-API
* Hantera en avancerad princip

### <a name="prerequisites"></a>Förutsättningar
I följande exempel har du skapa, uppdatera, länka och ta bort principer för appar, tjänstens huvudnamn och din organisation. Om du är nybörjare på Azure AD, rekommenderar vi att du lär dig mer om [skaffa en Azure AD-klient](quickstart-create-new-tenant.md) innan du fortsätter med de här exemplen.  

Utför följande steg för att komma igång:

1. Hämta senaste [Azure AD PowerShell-modulen offentliga förhandsversionen](https://www.powershellgallery.com/packages/AzureADPreview).
2. Kör den `Connect` kommando för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gång startar du en ny session.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Kör följande kommando om du vill se alla principer som har skapats i din organisation. Kör det här kommandot när du har de flesta åtgärderna i följande scenarier. Kör kommandot hjälper dig också att få den ** ** av dina principer.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Exempel: Hantera en organisations standardprincipen
I det här exemplet skapar du en princip som gör att användare kan logga in mer sällan i hela organisationen. Gör detta genom att skapa en livslängd för token-princip för enda faktor uppdatera token, som används i hela organisationen. Principen tillämpas för varje program i din organisation och varje tjänstens huvudnamn som inte redan har en princip.

1. Skapa en princip för livslängd för token.

    1.  Ange den enda faktor Uppdateringstoken till ”tills-återkallas”. Token inte upphör att gälla förrän åtkomst har återkallats. Skapa principdefinitionen för följande:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Om du vill skapa principen, kör du följande kommando:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Se din nya princip och för att få principens **ObjectId**, kör du följande kommando:

        ```powershell
        Get-AzureADPolicy
        ```

2. Uppdatera principen.

    Du kan välja den första principen som du anger i det här exemplet inte är så strikta som din tjänst kräver. Ange din enda faktor uppdatera Token upphör att gälla om två dagar genom att köra följande kommando:

    ```powershell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Exempel: Skapa en princip för webbinloggning

I det här exemplet skapar du en princip som kräver att användarna ska autentisera oftare i webbappen. Den här principen anger livslängden för åtkomst-ID-token och maxåldern av en sessionstoken i Multi-Factor Authentication-till tjänstens huvudnamn på din webbapp.

1. Skapa en princip för livslängd för token.

    Den här principen för webbinloggning, anger den åtkomst/ID livslängd för uppdateringstoken och token ålder max single-factor-session till två timmar.

    1.  Om du vill skapa principen, kör du följande kommando:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Se din nya princip och för att få principen **ObjectId**, kör du följande kommando:

        ```powershell
        Get-AzureADPolicy
        ```

2.  Tilldela principen till tjänstens huvudnamn. Du måste också hämta den **ObjectId** för tjänstens huvudnamn. 

    1.  Om du vill se din organisations tjänstens huvudnamn, kan du fråga antingen den [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) eller [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Du kan också testa detta i den [Azure AD Graph-testaren](https://graphexplorer.azurewebsites.net/), och [Microsoft Graph-testaren](https://developer.microsoft.com/graph/graph-explorer) med hjälp av Azure AD-konto.

    2.  När du har den **ObjectId** för tjänstens huvudnamn, kör du följande kommando:

        ```powershell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Exempel: Skapa en princip för en inbyggd app som anropar ett webb-API
I det här exemplet skapar du en princip som kräver att användarna ska autentisera mindre ofta. Principen förlängs också hur lång tid som en användare kan vara inaktiv innan användaren måste autentiseras på nytt. Principen tillämpas på webb-API. När den inbyggda appen begär webb-API som en resurs, används den här principen.

1. Skapa en princip för livslängd för token.

    1.  Om du vill skapa en strikt princip för ett webb-API, kör du följande kommando:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Se din nya princip och för att få principen **ObjectId**, kör du följande kommando:

        ```powershell
        Get-AzureADPolicy
        ```

2. Tilldela principen till din webb-API. Du måste också hämta den **ObjectId** av ditt program. Det bästa sättet att hitta din app **ObjectId** är att använda den [Azure-portalen](https://portal.azure.com/).

   När du har den **ObjectId** för din app, kör du följande kommando:

        ```powershell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Exempel: Hantera en avancerad princip
I det här exemplet skapar du några principer, om du vill veta hur prioritet systemet fungerar. Du kan också lära dig hur du hanterar flera principer som tillämpas på flera objekt.

1. Skapa en princip för livslängd för token.

    1.  Om du vill skapa en standardprincip för organisationen som anger livslängd för en faktor uppdatera Token till 30 dagar, kör du följande kommando:

        ```powershell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Se din nya princip och för att få principens **ObjectId**, kör du följande kommando:

        ```powershell
        Get-AzureADPolicy
        ```

2. Tilldela principen till ett huvudnamn för tjänsten.

    Nu kan ha du en princip som gäller för hela organisationen. Du kanske vill bevara den här 30-dagars-principen för en specifik huvudtjänst, men ändra standardprincipen för organisationen och den övre gränsen för ”tills-återkallas”.

    1.  Om du vill se din organisations tjänstens huvudnamn, kan du fråga antingen den [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) eller [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Du kan också testa detta i den [Azure AD Graph-testaren](https://graphexplorer.azurewebsites.net/), och [Microsoft Graph-testaren](https://developer.microsoft.com/graph/graph-explorer) med hjälp av Azure AD-konto.

    2.  När du har den **ObjectId** för tjänstens huvudnamn, kör du följande kommando:

            ```powershell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Ange den `IsOrganizationDefault` flaggan till false:

    ```powershell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Skapa en ny organisation standardprincip:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Nu har du den ursprungliga principen som är länkad till din tjänst och den nya principen anges som standardprincip för din organisation. Det är viktigt att komma ihåg att principer som tillämpas på tjänstens huvudnamn har högre prioritet än standardprinciper för organisationen.

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
| <code>&#8209;Definition</code> |Matris med Stringified.json som innehåller alla hanteringsprincipregler (MPR). | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Sträng med namnet på principen. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Om värdet är true anger du principen som organisationens standardprincipen. Om värdet är false ingenting. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ av principen. För tokenlivslängder, använder du alltid ”TokenLifetimePolicy”. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Valfritt] |Anger ett alternativt ID för principen. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Hämtar alla Azure AD-principer eller en angiven princip.

```powershell
Get-AzureADPolicy
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> [Valfritt] |**ObjectId (Id)** för den princip som du vill. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Hämtar alla appar och tjänstens huvudnamn som är länkade till en princip.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** för den princip som du vill. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Uppdaterar en befintlig princip.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** för den princip som du vill. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Sträng med namnet på principen. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Valfritt] |Matris med Stringified.json som innehåller alla hanteringsprincipregler (MPR). |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Valfritt] |Om värdet är true anger du principen som organisationens standardprincipen. Om värdet är false ingenting. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Valfritt] |Typ av principen. För tokenlivslängder, använder du alltid ”TokenLifetimePolicy”. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Valfritt] |Anger ett alternativt ID för principen. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Tar bort den angivna principen.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** för den princip som du vill. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Principer för program
Du kan använda följande cmdletar för principer för program.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Länkar den angivna principen till ett program.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** av programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** av principen. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Hämtar den princip som tilldelats till ett program.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** av programmet. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Tar bort en princip från ett program.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** av programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** av principen. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Huvudprinciper för tjänst
Du kan använda följande cmdletar för huvudprinciper för tjänst.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Länkar den angivna principen till ett huvudnamn för tjänsten.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** av programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** av principen. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Hämtar princip länkad till det angivna tjänsthuvudnamnet.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** av programmet. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Tar bort principen från den angivna tjänsten huvudnamn.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametrar | Beskrivning | Exempel |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** av programmet. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** av principen. | `-PolicyId <ObjectId of Policy>` |
