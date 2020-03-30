---
title: Referens för felkod
titleSuffix: Azure AD B2C
description: En lista över de felkoder som kan returneras av Azure Active Directory B2C-tjänsten.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b09b3c19ab1c5b23e56e25afc1d9631cd1caa68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188672"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Felkoder: Azure Active Directory B2C

Följande fel kan returneras av Azure Active Directory B2C-tjänsten.

| Felkod | Meddelande |
| ---------- | ------- |
| `AADB2C90002` | CORS-resursen{0}' ' returnerade inte en 404:a. |
| `AADB2C90006` | Den omdirigera URI '{0}' som anges i begäran{1}registreras inte för klient-ID ' '. |
| `AADB2C90007` | Programmet som är associerat{0}med klient-ID ' har inga registrerade omdirigerings-URI:er. |
| `AADB2C90008` | Begäran innehåller inte en klient-ID-parameter. |
| `AADB2C90010` | Begäran innehåller ingen scopeparameter. |
| `AADB2C90011` | Klient-ID{0}' ' som anges i begäran{1}matchar inte klient-ID ' ' registrerat i principen. |
| `AADB2C90012` | Scopet{0}' ' som anges i begäran stöds inte. |
| `AADB2C90013` | Den begärda{0}svarstypen ' ' som anges i begäran stöds inte. |
| `AADB2C90014` | Det begärda{0}svarsläget ' ' som anges i begäran stöds inte. |
| `AADB2C90016` | Den begärda klientpåståendetypen '{0}'{1}matchar inte den förväntade typen ' '. |
| `AADB2C90017` | Klientpåståendet i begäran är ogiltigt:{0} |
| `AADB2C90018` | Klient-ID{0}' ' som anges i begäran{1}registreras inte i klienten ' '. |
| `AADB2C90019` | Nyckelbehållaren med id '{0}{1}' i klienten ' ' har ingen giltig nyckel. Orsak: {2}. |
| `AADB2C90021` | Den tekniska{0}profilen ' ' finns{1}inte i{2}klientens policy ' ' ' ' . |
| `AADB2C90022` | Det gick inte att returnera{0}metadata för{1}principen ' ' i klienten ' '. |
| `AADB2C90023` | Profilen{0}' ' innehåller inte den{1}metadatanyckel som krävs ' '. |
| `AADB2C90025` | Profilen{0}' '{1}i principen{2}' ' ' ' ' innehåller{3}inte den kryptografiska nyckel som krävs . |
| `AADB2C90027` | Grundläggande autentiseringsuppgifter{0}som angetts för ' ' är ogiltiga. Kontrollera att autentiseringsuppgifterna är korrekta och att åtkomsten har beviljats av resursen. |
| `AADB2C90028` | Klientcertifikatet som{0}angetts för ' ' är ogiltigt. Kontrollera att certifikatet är korrekt, innehåller en privat nyckel och att åtkomst har beviljats av resursen. |
| `AADB2C90031` | Princip{0}' ' anger inte en standardanvändarresa. Kontrollera att principen eller dess överordnade anger en standardanvändarresa som en del av ett förlitande partiavsnitt. |
| `AADB2C90035` | Tjänsten är inte tillgänglig för tillfället. Försök igen efter några minuter. |
| `AADB2C90036` | Begäran innehåller ingen uri för att omdirigera användaren till att publicera utloggning. Ange en URI i parameterfältet post_logout_redirect_uri. |
| `AADB2C90037` | Ett fel uppstod vid bearbetning av begäran. Kontakta administratören för den webbplats som du försöker komma åt. |
| `AADB2C90039` | Begäran innehåller ett klientpåstående, men{0}den angivna{1}principen ' ' i klienten ' saknar en client_secret i RelyingPartyPolicy. |
| `AADB2C90040` | Användarresa{0}' ' innehåller inget steg för skicka anspråk. |
| `AADB2C90043` | Uppmaningen som ingår i begäran innehåller ogiltiga värden. Förväntad "ingen", "inloggning", "samtycke" eller "select_account". |
| `AADB2C90044` | Påståendet "{0}" stöds inte av anspråkslösaren "{1}". |
| `AADB2C90046` | Vi har problem med att ladda ditt nuvarande tillstånd. Du kanske vill prova att starta sessionen från början. |
| `AADB2C90047` | Resursen{0}' ' innehåller skriptfel som förhindrar att den läses in. |
| `AADB2C90048` | Ett ohanterat undantag har inträffat på servern. |
| `AADB2C90051` | Inga lämpliga skadeleverantörer hittades. |
| `AADB2C90052` | Ogiltigt användarnamn eller lösenord. |
| `AADB2C90053` | Det gick inte att hitta en användare med den angivna autentiseringsidentien. |
| `AADB2C90054` | Ogiltigt användarnamn eller lösenord. |
| `AADB2C90055` | Scopet{0}' ' som tillhandahålls i begäranhttps://example.com/calendar.readmåste ange en resurs, till exempel ' '. |
| `AADB2C90057` | Det angivna programmet är inte konfigurerat för att tillåta OAuth Implicit-flödet. |
| `AADB2C90058` | Det angivna programmet är inte konfigurerat för att tillåta offentliga klienter. |
| `AADB2C90067` | Uri ' har ett{0}ogiltigt format efter utloggning. Ange en https-baseradhttps://example.com/returnURL som ' ' eller för inbyggda klienter använder den inbyggda IETF-klienten URI 'urn:ietf:wg:oauth:2.0:oob'. |
| `AADB2C90068` | Det angivna programmet med{0}ID ' ' är inte giltigt mot den här tjänsten. Använd ett program som skapats via B2C-portalen och försök igen. |
| `AADB2C90075` | Skadeutbytet{0}' ' '{1}som anges i steg '{2}' returnerade HTTP-felsvar med Kod ' ' och Reason '{3}'. |
| `AADB2C90077` | Användaren har ingen befintlig sessions- och begärandetparameter har värdet '{0}'. |
| `AADB2C90079` | Klienter måste skicka en client_secret när de löser in ett konfidentiellt bidrag. |
| `AADB2C90080` | Det angivna bidraget har upphört att gälla. Autentisera igen och försök igen. Aktuell tid: {0}, Bevilja {1}utfärdad tid: , {2}Bevilja glidande fönster utgångsdatum: . |
| `AADB2C90081` | Den angivna client_secret matchar inte det förväntade värdet för den här klienten. Korrigera client_secret och försök igen. |
| `AADB2C90083` | Parametern för begäran {0}saknas: . |
| `AADB2C90084` | Offentliga kunder bör inte skicka en client_secret vid inlösen av ett offentligt förvärvat bidrag. |
| `AADB2C90085` | Tjänsten har påträffat ett internt fel. Reauthenticate och försök igen. |
| `AADB2C90086` | Den medföljande grant_type [{0}] stöds inte. |
| `AADB2C90087` | Det angivna bidraget har inte utfärdats för den här versionen av protokollslutpunkten. |
| `AADB2C90088` | Det angivna bidraget har inte utfärdats för denna slutpunkt. Verkligt värde: {0} och förväntat värde:{1} |
| `AADB2C90092` | Det angivna programmet med{0}ID ' '{1}är inaktiverat för klienten ' '. Aktivera programmet och försök igen. |
| `AADB2C90107` | Programmet med ID{0}' ' kan inte hämta en ID-token antingen på grund av att openid-scopet inte angavs i begäran eller om programmet inte är auktoriserat för det. |
| `AADB2C90108` | Orchestration-steget{0}' ' anger inte en CpimIssuerTechnicalProfileReferenceId när en förväntades. |
| `AADB2C90110` | Scopeparametern måste innehålla "openid" när du begär en response_type som innehåller "id_token". |
| `AADB2C90111` | Ditt konto har låsts. Kontakta supportpersonen för att låsa upp den och försök sedan igen. |
| `AADB2C90114` | Ditt konto är tillfälligt låst för att förhindra obehörig användning. Försök igen senare. |
| `AADB2C90115` | När du begär response_type "kod" måste scopeparametern innehålla ett resurs- eller klient-ID för åtkomsttoken och "openid" för ID-token. Dessutom inkludera "offline_access" för uppdateringstoken. |
| `AADB2C90117` | Scopet{0}' ' som anges i begäran stöds inte. |
| `AADB2C90118` | Användaren har glömt sitt lösenord. |
| `AADB2C90120` | Den maxåldersparameter '{0}' som anges i begäran är ogiltig. Max ålder måste vara ett{1}heltal{2}mellan ' ' och ' ' ' inclusive. |
| `AADB2C90122` | Indata{0}för ' ' som togs emot i begäran har misslyckats HTTP-begäran validering. Kontrollera att indata inte innehåller tecken som < eller &. |
| `AADB2C90128` | Kontot som är associerat med det här bidraget finns inte längre. Reauthenticate och försök igen. |
| `AADB2C90129` | Det beviljade bidraget har återkallats. Reauthenticate och försök igen. |
| `AADB2C90145` | Inga overifierade telefonnummer har hittats och principen tillåter inte att en användare har angett nummer. |
| `AADB2C90146` | Scopet{0}' ' som anges i begäran anger mer än en resurs för en åtkomsttoken, som inte stöds. |
| `AADB2C90149` | Script{0}' ' misslyckades med att läsa in. |
| `AADB2C90151` | Användaren har överskridit det maximala antalet för återförsök för multifaktorautentisering. |
| `AADB2C90152` | En begäran om undersökning med flera faktorer kunde inte hämta ett svar från tjänsten. |
| `AADB2C90154` | En begäran om flerfaktorsverifiering kunde inte hämta ett sessions-ID från tjänsten. |
| `AADB2C90155` | En begäran om flerfaktorsverifiering{0}har misslyckats med anledning " ". |
| `AADB2C90156` | En valideringsbegäran med flera faktorer{0}misslyckades med orsak ' '. |
| `AADB2C90157` | Användaren har överskridit det maximala antalet för återförsök för ett självförsäkrat steg. |
| `AADB2C90158` | En självberätad valideringsbegäran{0}har misslyckats med anledning ' '. |
| `AADB2C90159` | En självförsäkrad verifieringsbegäran{0}har misslyckats med förnuftet " ". |
| `AADB2C90161` | Ett självförsäkrat sändningssvar har{0}misslyckats med förnuftet ' '. |
| `AADB2C90165` | Det går inte att hitta{0}DET IL-initierande meddelandet med id ' i tillstånd. |
| `AADB2C90168` | HTTP-omdirigera begäran innehåller inte den{0}nödvändiga parametern ' ' för en signerad begäran. |
| `AADB2C90178` | Signeringscertifikatet '{0}' har ingen privat nyckel. |
| `AADB2C90182` | Den medföljande code_verifier matchar inte tillhörande code_challenge |
| `AADB2C90183` | Den medföljande code_verifier är ogiltig |
| `AADB2C90184` | Den medföljande code_challenge_method stöds inte. Värden som stöds är enkla eller S256 |
| `AADB2C90188` | Den tekniska PROFILEN{0}för SAML ' ' anger{1}en PartnerEntity-URL för{2}' ', men det går inte att hämta metadata med orsak ' '. |
| `AADB2C90194` | Anspråk{0}' ' som angetts för innehavartoken finns inte i de tillgängliga anspråken. Tillgängliga anspråk{1}' '. |
| `AADB2C90205` | Det här programmet har inte tillräcklig behörighet mot den här webbresursen för att utföra åtgärden. |
| `AADB2C90206` | En time out har inträffat initiering klienten. |
| `AADB2C90208` | Parametern id_token_hint som anges har upphört att gälla. Ange en annan token och försök igen. |
| `AADB2C90209` | Den angivna parametern id_token_hint innehåller ingen accepterad målgrupp. Giltiga målgruppsvärden: '{0}'. Ange en annan token och försök igen. |
| `AADB2C90210` | Det gick inte att validera den angivna parametern id_token_hint. Ange en annan token och försök igen. |
| `AADB2C90211` | Begäran innehöll en ofullständig tillståndscookie. |
| `AADB2C90212` | Begäran innehöll en ogiltig tillståndscookie. |
| `AADB2C90220` | Nyckelbehållaren i{0}klienten ' '{1}med lagringsidentifieraren ' finns men innehåller inget giltigt certifikat. Certifikatet kan ha upphört att gälla eller så kan certifikatet aktiveras i framtiden (nbf). |
| `AADB2C90223` | Ett fel har uppstått som sanerar CORS-resursen. |
| `AADB2C90224` | Resursägarflödet har inte aktiverats för programmet. |
| `AADB2C90225` | Användarnamnet eller lösenordet som anges i begäran är ogiltigt. |
| `AADB2C90226` | Det angivna tokenutbytet stöds endast via HTTP POST. |
| `AADB2C90232` | Den angivna parametern id_token_hint innehåller ingen godkänd utfärdare. Giltiga emittenter:{0}' '. Ange en annan token och försök igen. |
| `AADB2C90233` | Den angivna parametern id_token_hint misslyckades signaturvalidering. Ange en annan token och försök igen. |
| `AADB2C90235` | Den angivna id_token har upphört att gälla. Ange en annan token och försök igen. |
| `AADB2C90237` | Den angivna id_token innehåller ingen giltig målgrupp. Giltiga målgruppsvärden: '{0}'. Ange en annan token och försök igen. |
| `AADB2C90238` | Den angivna id_token innehåller ingen giltig utfärdare. Giltiga emittentvärden: '{0}'. Ange en annan token och försök igen. |
| `AADB2C90239` | Den angivna id_token misslyckade signaturvalideringen. Ange en annan token och försök igen. |
| `AADB2C90240` | Den id_token som tillhandahålls är felaktig och kunde inte tolkas. Ange en annan token och försök igen. |
| `AADB2C90242` | Den tekniska PROFILEN{0}för SAML ' ' anger PartnerEntity{1}CDATA som inte kan läsas in av orsak ' '. |
| `AADB2C90243` | IDP:s klientnyckel/hemlighet är inte korrekt konfigurerad. |
| `AADB2C90244` | Det finns för många förfrågningar just nu. Vänta en tid och försök igen. |
| `AADB2C90248` | Resursägarflödet kan endast användas av program som skapats via B2C-administratörsportalen. |
| `AADB2C90250` | Den allmänna inloggningsslutpunkten stöds inte. |
| `AADB2C90255` | Det skadeväxling som angavs{0}i den tekniska profilen ' ' slutfördes inte som förväntat. Du kanske vill prova att starta sessionen från början. |
| `AADB2C90261` | Anspråksutbytet{0}' ' som{1}anges i steg ' ' returnerade HTTP-felsvar som inte kunde tolkas. |
| `AADB2C90272` | Parametern id_token_hint har inte angetts i begäran. Ange token och försök igen. |
| `AADB2C90273` | Ett ogiltigt svar{0}mottogs : ' ' |
| `AADB2C90274` | Providermetadata anger inte en enda utloggningstjänst eller slutpunktsbindningen är inte en av urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect' eller 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST'. |
| `AADB2C90276` | Begäran är inte förenlig med{0}kontrollinställningen{1}' ':{2}' '{3}i technicalProfile ' ' ' för policy ' ' tenant '{4}'. |
| `AADB2C90277` | Orkestreringssteget '{0}{1}' för{2}användarresa ' ' av principen ' ' innehåller ingen innehållsdefinitionsreferens. |
| `AADB2C90279` | Det angivna klient-ID'{0}' matchar inte klient-ID som utfärdade bidraget. |
| `AADB2C90284` | Ansökan med identifierare{0}' ' har inte beviljats samtycke och kan inte användas för lokala konton. |
| `AADB2C90285` | Det gick inte{0}att hitta programmet med identifieraren ' ' . |
| `AADB2C90288` | UserJourney med id{0}' ' refererade{1}i TechnicalProfile '{2}' för uppdateringstokeninlösen för klienten ' ' finns inte i principen '{3}' eller någon av dess basprinciper. |
| `AADB2C90289` | Vi påträffade ett fel när vi anslöt till identitetsprovidern. Försök igen senare. |
| `AADB2C90296` | Programmet har inte konfigurerats korrekt. Kontakta administratören för den webbplats som du försöker komma åt. |
| `AADB2C99005` | Begäran innehåller en ogiltig scope-parameter som{0}innehåller ett ogiltigt tecken ' '. |
| `AADB2C99006` | Azure AD B2C kan inte hitta tilläggsappen med app-ID '{0}'. Besök https://go.microsoft.com/fwlink/?linkid=851224 gärna för mer information. |
| `AADB2C99011` | Metadatavärdet '{0}' har inte angetts i{1}TechnicalProfile{2}' ' ' i policy ' '. |
| `AADB2C99013` | Kombinationen grant_type [{0}] och token_type [{1}] stöds inte. |
| `AADB2C99015` | Profilen{0}' '{1}i principen{2}' ' ' ' ' ' saknar alla InputClaims som krävs för resursägare lösenordsautentiseringsflöde. |
