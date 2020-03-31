---
title: Felsöka azure data lake analytics U-SQL-jobbfel på grund av .NET Framework 4.7.2-uppgradering
description: Felsöka U-SQL-jobbfel på grund av uppgraderingen till .NET Framework 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213587"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics uppgraderar till .NET Framework v4.7.2

Standardkörningen för Azure Data Lake Analytics uppgraderas från .NET Framework v4.5.2 till .NET Framework v4.7.2. Den här ändringen medför en liten risk för att bryta ändringar om din U-SQL-kod använder anpassade sammansättningar och de anpassade sammansättningarna använder .NET-bibliotek.

Den här uppgraderingen från .NET Framework 4.5.2 till version 4.7.2 innebär att .NET Framework som distribueras i en U-SQL-körning (standardkörningen) nu alltid är 4.7.2. Det finns inget alternativ sida vid sida för .NET Framework-versioner.

När den här uppgraderingen till .NET Framework 4.7.2 är klar körs systemets hanterade kod som version 4.7.2, användarbibliotek som U-SQL-anpassade sammansättningar körs i det bakåtkompatibla läge som är lämpligt för den version som sammansättningen har genereras för.

- Om din montering dll genereras för version 4.5.2, kommer det distribuerade ramverket att behandla dem som 4.5.2-bibliotek, förutsatt (med några få undantag) 4.5.2 semantik.
- Du kan nu använda anpassade U-SQL-sammansättningar som använder sig av version 4.7.2-funktioner om du inriktar dig på .NET Framework 4.7.2.

På grund av den här uppgraderingen till .NET Framework 4.7.2 finns det en potential att införa bryta ändringar i dina U-SQL-jobb som använder .NET-anpassade sammansättningar. Vi föreslår att du söker efter problem med bakåtkompatibilitet med hjälp av proceduren nedan.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Så här kontrollerar du om det finns problem med bakåtkompatibilitet

Kontrollera om det finns problem med att bryta bakåtkompatibilitet genom att köra .NET-kompatibilitetskontrollerna på lysset NET-koden i de anpassade U-SQL-sammansättningarna.

> [!Note]
> Verktyget identifierar inte faktiska brottsändringar. Den identifierar bara så kallade .NET API:er som kan (för vissa indata) orsaka problem. Om du får ett meddelande om ett problem kan koden fortfarande vara bra, men du bör kontrollera mer information.

1. Kör bakåtkompatibilitetskontrollen på dina .NET-DLL-filer antingen genom att
   1. Använda Visual Studio-tillägget på [.NET Portability Analyzer Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Hämta och använda det fristående verktyget från [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Instruktioner för att köra fristående verktyg finns på [GitHub dotnetapiport bryta ändringar](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. För 4.7.2. kompatibilitet, `read isRetargeting == True` identifierar möjliga problem.
2. Om verktyget anger om din kod kan påverkas av någon av de möjliga bakåtkompileringskompatibiliteter (några vanliga exempel på inkompatibiliteter visas nedan), kan du ytterligare kontrollera genom att
   1. Analysera din kod och identifiera om din kod skickar värden till de påverkade API:erna
   1. Utför en körningskontroll. Körningsdistributionen görs inte sida vid sida i ADLA. Du kan utföra en körningskontroll före uppgraderingen med VisualStudios lokala körning med ett lokalt .NET Framework 4.7.2 mot en representativ datauppsättning.
3. Om du verkligen påverkas av en bakåtkompatibilitet, vidta nödvändiga åtgärder för att åtgärda det (till exempel åtgärda dina data eller kodlogik).

I de flesta fall bör du inte påverkas av bakåtkompatibilitet.

## <a name="timeline"></a>Tidslinje

Du kan söka efter distributionen av den nya körningen här [Körningsfelsökning](runtime-troubleshoot.md)och genom att titta på alla tidigare lyckade jobb.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Vad händer om jag inte kan få min kod granskad i tid

Du kan skicka jobbet mot den gamla runtime-versionen (som är byggd med inriktning 4.5.2), men på grund av bristen på .NET Framework sida vid sida-funktioner, kommer det fortfarande bara att köras i 4.5.2 kompatibilitetsläge. Du kan fortfarande stöta på några av problemen med bakåtkompatibilitet på grund av det här beteendet.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Vilka är de vanligaste problem med bakåtkompatibilitet som du kan stöta på

De vanligaste bakåtkompatibiliteter som checker sannolikt kommer att identifiera är (vi genererade denna lista genom att köra checker på våra egna interna ADLA-jobb), vilka bibliotek påverkas (observera: att du kan ringa biblioteken endast indirekt, därför är det viktigt att vidta nödvändiga åtgärder #1 för att kontrollera om dina jobb påverkas) och eventuella åtgärder för att åtgärda. Obs: I nästan alla fall för våra egna jobb visade sig varningarna vara falska positiva på grund av de smala naturerna hos de flesta brytande förändringar.

- Egenskapen IAsyncResult.CompletedSynchronously måste vara korrekt för att den resulterande uppgiften ska kunna slutföras
  - När du anropar TaskFactory.FromAsync måste implementeringen av egenskapen IAsyncResult.CompletedSynchronously vara korrekt för att den resulterande aktiviteten ska slutföras. Det vill säga egenskapen måste returnera sant om, och endast om, genomförandet slutförts synkront. Tidigare har egenskapen inte kontrollerats.
  - Påverkade bibliotek: mscorlib, System.Threading.Tasks
  - Föreslagen åtgärd: Se till att TaskFactory.FromAsync returnerar korrekt korrekt

- DataObject.GetData hämtar nu data som UTF-8
  - För appar som är inriktade på .NET Framework 4 eller som körs i .NET Framework 4.5.1 eller tidigare versioner hämtar DataObject.GetData HTML-formaterade data som en ASCII-sträng. Därför representeras icke-ASCII-tecken (tecken vars ASCII-koder är större än 0x7F) av två slumpmässiga tecken.#N##N#För appar som är inriktade på .NET Framework 4.5 `DataObject.GetData` eller senare och körs på .NET Framework 4.5.2, HTML-formaterade data som UTF-8, som representerar tecken som är större än 0x7F korrekt.
  - Påverkade bibliotek: Glo
  - Föreslagen åtgärd: Se till att data hämtas är det format du vill ha

- XmlWriter kastar på ogiltiga surrogatpar
  - För appar som är inriktade på .NET Framework 4.5.2 eller tidigare versioner genererar det inte alltid ett undantag om du skriver ett ogiltigt surrogatpar med undantagsrebackningshantering. För appar som är inriktade på .NET Framework 4.6, `ArgumentException`kastar ett ogiltigt surrogatpar ett .
  - Påverkade bibliotek: System.Xml, System.Xml.ReaderWriter
  - Föreslagen åtgärd: Se till att du inte skriver ett ogiltigt surrogatpar som orsakar argumentundantag

- HtmlTextWriter återger `<br/>` inte elementet korrekt
  - Med början i .NET Framework 4.6 infogas endast `HtmlTextWriter.RenderBeginTag()` ett `<BR />` (i stället för två) ringa och `HtmlTextWriter.RenderEndTag()` med ett `<BR />` element på rätt sätt.
  - Påverkade bibliotek: System.Web
  - Föreslagen åtgärd: Se till att `<BR />` du infogar hur mycket du förväntar dig att se så att inget slumpmässigt beteende ses i produktionsjobbet

- Anropa CreateDefaultAuthorizationContext med ett null-argument har ändrats
  - Implementeringen av AuthorizationContext som returnerades `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` genom en anrop till argumentet med ett null-behörighetPolicies har ändrat dess implementering i .NET Framework 4.6.
  - Påverkade bibliotek: System.IdentityModel
  - Föreslagen åtgärd: Se till att du hanterar det nya förväntade beteendet när det finns null-auktoriseringsprincip
  
- RSACng laddar nu RSA-nycklar på rätt sätt av icke-standardnyckelstorlek
  - I .NET Framework-versioner före 4.6.2 kan kunder med icke-standardiserade nyckelstorlekar för RSA-certifikat inte komma åt dessa nycklar via tilläggsmetoderna `GetRSAPublicKey()` och `GetRSAPrivateKey()` förlängningsmetoderna. A `CryptographicException` med meddelandet "Den begärda nyckelstorleken stöds inte" genereras. Med .NET Framework 4.6.2 har problemet åtgärdats. På samma `RSA.ImportParameters()` `RSACng.ImportParameters()` sätt, och nu arbetar med `CryptographicException`icke-standard nyckelstorlekar utan att kasta 's.
  - Påverkade bibliotek: mscorlib, System.Core
  - Föreslagen åtgärd: Se till att RSA-nycklar fungerar som förväntat

- Sökkolonkontroller är strängare
  - I .NET Framework 4.6.2 gjordes ett antal ändringar för att stödja tidigare sökvägar som inte stöds (både i längd och format). Kontroller för korrekt enhetsavgränsare (kolon) syntax gjordes mer korrekt, vilket hade bieffekten att blockera vissa URI-banor i några utvalda Path API:er där de brukade tolereras.
  - Påverkade bibliotek: mscorlib, System.Runtime.Extensions
  - Föreslagen åtgärd:

- Anrop till ClaimsIdentity-konstruktörer
  - Från och med .NET Framework 4.6.2 ändras hur `T:System.Security.Claims.ClaimsIdentity` `T:System.Security.Principal.IIdentity` konstruktorer med en parameter anger egenskapen. `P:System.Security.Claims.ClaimsIdentify.Actor` Om `T:System.Security.Principal.IIdentity` argumentet är `T:System.Security.Claims.ClaimsIdentity` ett objekt `P:System.Security.Claims.ClaimsIdentify.Actor` och `T:System.Security.Claims.ClaimsIdentity` egenskapen `null`för `P:System.Security.Claims.ClaimsIdentify.Actor` objektet inte är, `M:System.Security.Claims.ClaimsIdentity.Clone` kopplas egenskapen med hjälp av metoden. I Framework 4.6.1 och tidigare `P:System.Security.Claims.ClaimsIdentify.Actor` versioner är egenskapen bifogad som en befintlig referens. På grund av den här ändringen, som börjar med .NET `P:System.Security.Claims.ClaimsIdentify.Actor` Framework 4.6.2, är egenskapen för det nya `T:System.Security.Claims.ClaimsIdentity` objektet inte lika med egenskapen `P:System.Security.Claims.ClaimsIdentify.Actor` för konstruktorns `T:System.Security.Principal.IIdentity` argument. I .NET Framework 4.6.1 och tidigare versioner är den lika.
  - Påverkade bibliotek: mscorlib
  - Föreslagen åtgärd: Se till att skadeidentitet fungerar som förväntat på ny körning

- Serialisering av kontrolltecken med DataContractJsonSerializer är nu kompatibel med ECMAScript V6 och V8
  - I .NET-ramverket 4.6.2 och tidigare versioner serialerade datacontractJsonSerializer inte vissa specialkontrolltecken, till exempel \b, \f och \t, på ett sätt som var kompatibelt med ECMAScript V6- och V8-standarderna. Från och med .NET Framework 4.7 är serialisering av dessa kontrolltecken kompatibel med ECMAScript V6 och V8.
  - Påverkade bibliotek: System.Runtime.Serialization.Json
  - Föreslagen åtgärd: Se till att datacontractJsonSerializer fungerar samma beteende
