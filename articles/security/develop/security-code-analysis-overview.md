---
title: Dokumentationsöversikt för Microsoft Security Code Analysis
description: Den här artikeln är en översikt över tillägget Microsoft Security Code Analysis
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851476"
---
# <a name="about-microsoft-security-code-analysis"></a>Om Analys av Microsoft-säkerhetskod

Med tillägget Microsoft Security Code Analysis kan team lägga till säkerhetskodanalys i sina Azure DevOps-pipelines för kontinuerlig integrering och leverans (CI/CD). Den här analysen rekommenderas av [SDL-experter (Secure Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/practices) på Microsoft.

En konsekvent användarupplevelsen förenklar säkerheten genom att dölja komplexiteten i att köra verktyg. Med NuGet-baserad leverans av verktygen behöver teamen inte längre hantera installationen eller uppdateringen av verktyg. Med både kommandorad och grundläggande gränssnitt för att skapa uppgifter kan alla användare ha så mycket kontroll över verktygen som de vill.

Team kan också använda kraftfulla funktioner för efterbearbetning, till exempel:

- Publicera loggar för kvarhållning.
- Generera användbara, utvecklarfokuserade rapporter.
- Konfigurera byggavbrott på regressionstester.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Varför ska jag använda Microsoft Security Code Analysis?

### <a name="security-simplified"></a>Förenklad säkerhet

Det är lika enkelt att lägga till Microsoft Security Code Analysis-verktyg i Azure DevOps-pipelinen som att lägga till nya uppgifter. Anpassa aktiviteterna eller använd deras standardbeteende. Uppgifter körs som en del av din Azure DevOps-pipeline och producerar loggar som beskriver många typer av resultat.

### <a name="clean-builds"></a>Rena byggen

När du har åtgärdat de första problemen som rapporterats av verktygen kan du konfigurera tillägget så att det går sönder bygger på nya problem.Det är enkelt att ställa in kontinuerlig integrering som bygger på varje pull-begäran.

### <a name="set-it-and-forget-it"></a>Ställ in den och glöm det

Som standard håller sig byggaktiviteterna och verktygen uppdaterade. Om det finns en uppdaterad version av ett verktyg behöver du inte hämta och installera det. Tillägget tar hand om uppdateringen för dig.

### <a name="under-the-hood"></a>Under huven

Tilläggets bygguppgifter döljer komplexiteten i:
  - Kör verktyg för statisk analys av säkerhet.
  - Bearbeta resultaten från loggfiler för att skapa en sammanfattningsrapport eller bryta versionen.

## <a name="microsoft-security-code-analysis-tool-set"></a>Verktygsuppsättning för Microsoft Security Code Analysis

Tillägget Microsoft Security Code Analysis gör de senaste versionerna av viktiga analysverktyg lättillgängliga för dig. Tillägget innehåller både Microsoft-hanterade verktyg och verktyg med öppen källkod.

Dessa verktyg hämtas automatiskt till den molnbaserade agenten när du har använt motsvarande bygguppgift för att konfigurera och köra pipelinen.

I det här avsnittet visas den uppsättning verktyg som för närvarande är tillgängliga i tillägget. Håll utkik efter tillägg av fler verktyg. Skicka oss också dina förslag på verktyg som du vill att vi ska lägga till.

### <a name="anti-malware-scanner"></a>Anti-Malware Scanner

Bygguppgiften för Anti-Malware Scanner ingår nu i tillägget Microsoft Security Code Analysis. Den här aktiviteten måste köras på en byggagent som redan har Windows Defender installerat. Mer information finns på [webbplatsen Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim (400)

BinSkim är en lättviktsskanner (Portable Executable) som validerar kompilatorinställningar, länkningsinställningar och andra säkerhetsrelevanta egenskaper för binära filer. Den här bygguppgiften innehåller ett kommandoradsomslag runt konsolprogrammet binskim.exe. BinSkim är ett verktyg med öppen källkod. Mer information finns i [BinSkim på GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Skanner för autentiseringsuppgifter

Lösenord och andra hemligheter som lagras i källkoden är ett betydande problem. Autentiseringsläsare scanner är ett eget statiskt analysverktyg som hjälper till att lösa detta problem. Verktyget identifierar autentiseringsuppgifter, hemligheter, certifikat och annat känsligt innehåll i källkoden och byggutdata.

### <a name="microsoft-security-risk-detection"></a>Identifiering av säkerhetsrisker för Microsoft

Microsoft Security Risk Detection (MSRD) är en molnbaserad tjänst för fuzz-testning. Den identifierar erploerbara säkerhetsfel i programvara. Den här tjänsten kräver en separat prenumeration och aktivering. Mer information finns i [MSRD Developer Center](https://docs.microsoft.com/security-risk-detection/).

### <a name="roslyn-analyzers"></a>Roslyn-analysverktyg

Roslyn Analyzers är Microsofts kompilatorintegrerat verktyg för att statiskt analysera hanterad C# och Visual Basic-kod. Mer information finns i [Roslyn-baserade analysatorer](https://docs.microsoft.com/dotnet/standard/analyzers/).

### <a name="tslint"></a>TSLint (tslint)

TSLint är ett utökningsbart statiskt analysverktyg som kontrollerar TypeScript-kod för läsbarhet, underhåll och funktionsfel. Det är brett stöd av moderna redaktörer och bygga system. Du kan anpassa den med dina egna luddregler, konfigurationer och formatters. TSLint är ett verktyg med öppen källkod. Mer information finns i [TSLint på GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analys och efterbehandling av resultat

Tillägget Microsoft Security Code Analysis har också tre efterbearbetningsuppgifter. Dessa uppgifter hjälper dig att analysera resultaten som hittats av säkerhetsverktygsuppgifterna. När de läggs till i en pipeline följer dessa aktiviteter vanligtvis alla andra verktygsuppgifter.

### <a name="publish-security-analysis-logs"></a>Publicera säkerhetsanalysloggar

Aktiviteten Publicera säkerhetsanalysloggar bevarar loggfilerna för de säkerhetsverktyg som körs under bygget. Du kan läsa dessa loggar för undersökning och uppföljning.

Du kan publicera loggfilerna till Azure Artifacts som en ZIP-fil. Du kan också kopiera dem till en tillgänglig filresurs från din privata byggagent.

### <a name="security-report"></a>Säkerhetsrapport

Aktiviteten Säkerhetsrapport bygger tolkar loggfilerna. Dessa filer skapas av de säkerhetsverktyg som körs under uppbyggnaden. Bygguppgiften skapar sedan en enda sammanfattningsrapportfil. Den här filen visar alla problem som hittas av analysverktygen.

Du kan konfigurera den här uppgiften för att rapportera resultat för specifika verktyg eller för alla verktyg. Du kan också välja vilken problemnivå som ska rapporteras, till exempel fel eller både fel och varningar.

### <a name="post-analysis-build-break"></a>Efteranalys (byggpaus)

Med aktiviteten Efter analysversion kan du injicera en byggbrytning som avsiktligt gör att en version misslyckas. Du injicerar en byggbrytning om ett eller flera analysverktyg rapporterar problem i koden.

Du kan konfigurera den här uppgiften för att bryta bygget för problem som hittas av specifika verktyg eller alla verktyg. Du kan också konfigurera den baserat på allvarlighetsgraden för problem som hittades, till exempel fel eller varningar.

>[!NOTE]
>Avsiktligt lyckas varje bygguppgift om aktiviteten slutförs. Detta gäller oavsett om ett verktyg hittar problem eller inte, så att bygget kan slutföras genom att låta alla verktyg köras.

## <a name="next-steps"></a>Nästa steg

Instruktioner om hur du ar ombord och installerar Microsofts säkerhetskodsanalys finns i vår [introduktions- och installationsguide](security-code-analysis-onboard.md).

Mer information om hur du konfigurerar bygguppgifterna finns i vår [konfigurationsguide](security-code-analysis-customize.md) eller [YAML-konfigurationsguide](yaml-configuration.md).

Om du har ytterligare frågor om förlängningen och de verktyg som erbjuds, kolla in vår [FAQ sida](security-code-analysis-faq.md).
