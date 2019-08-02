---
title: Översikt över Microsoft Azure Security code Analysis-dokumentation
description: Den här artikeln är en översikt över tillägget för säkerhets kod analys
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718293"
---
# <a name="about-microsoft-security-code-analysis"></a>Om Microsofts säkerhets kod analys

**Tillägget Microsoft Security code Analysis** ger team möjlighet att sömlöst integrera säkerhets kod analyser i sina Azure DevOps CI/CD-pipelines som rekommenderas av [sdl-experter (Secure Development Lifecycle)](https://www.microsoft.com/securityengineering/sdl/practices) på Microsoft. Säkerheten är förenklad genom ett konsekvent UX som sammanfattar komplexiteten med att köra olika verktyg. Med en NuGet-baserad leverans av verktygen behöver team inte längre hantera installationen eller uppdateringen av verktyget. Med kommando rad och grundläggande verktyg för att bygga uppgifter kan alla användare, från ett gurus-verktyg till de vardagliga utvecklarna, ha så lite eller lika mycket kontroll över verktygen som de vill. Team kan också dra nytta av kraftfulla funktioner för post bearbetning, till exempel publicerings loggar för kvarhållning, vilket ger möjlighet till användbara rapporter för utvecklare som prioriterar & att konfigurera Bygg brytningar vid regressioner.

## <a name="why-microsoft-security-code-analysis"></a>Varför Microsofts säkerhets kod analys

### <a name="security-simplified"></a>Förenklad säkerhet

Att lägga till säkerhets kod analys verktyg i din Azure DevOps-pipeline är lika enkelt som att lägga till nya aktiviteter. Anpassa dem eller gå till standardvärdena. Aktiviteterna körs som en del av din DevOps-pipeline och genererar loggar som beskriver alla typer av resultat.

### <a name="clean-builds"></a>Rensa versioner

När du har åtgärdat de första problemen som har rapporter ATS av verktygen kan du konfigurera tillägget att avbryta versioner vid nya problem. Att ställa in kontinuerlig integrerings version på varje pull-begäran har aldrig varit detta enkelt!

### <a name="set-it-and-forget-it"></a>Ange den och Glöm den

Build-aktiviteter och-verktyg kan ställas in så att de hålls uppdaterade (och är som standard). Om det finns en uppdaterad version av verktyget behöver du inte hämta och installera det. Det här tillägget tar hand om det åt dig. 

>>>
### <a name="under-the-hood"></a>Under huven

Bygg aktiviteterna för Microsoft Security code Analysis-tillägget sammanfattar de komplexa funktionerna i:
  - Kör verktyg för säkerhets statisk analys och
  - Bearbetning av resultaten från loggfiler för att skapa en sammanfattnings rapport eller bryta skapandet.
>>>

## <a name="security-code-analysis-toolset"></a>Analys verktyg för säkerhets kod

Tillägget Microsoft Security code Analysis är enkelt att komma åt dig, de senaste versionerna av viktiga analys verktyg. Tillägget innehåller både Microsoft-verktyg för intern och öppen källkod. Verktygen hämtas automatiskt på den molnbaserade agenten när du har konfigurerat & köra pipelinen med motsvarande build-uppgift. Nedan visas en uppsättning verktyg som är tillgängliga i tillägget idag. Håll koll på mer och skicka förslag på verktyg som kan läggas till.

### <a name="anti-malware-scanner"></a>Skanner mot skadlig kod

Den program vara som skapas av program vara mot skadlig kod ingår nu i tillägget Microsoft Security code analysis. Den måste köras på en build-agent där Windows Defender redan är installerat. Mer information finns på [webbplatsen för Defender](https://aka.ms/defender) 

### <a name="binskim"></a>BinSkim

BinSkim är en portabel körbar fil (PE) som validerar kompilator/länkar-inställningar och andra säkerhetsrelaterade binära egenskaper. Bygg uppgiften tillhandahåller ett kommando rads gränssnitt runt programmet BinSkim. exe. BinSkim är ett verktyg med öppen källkod och mer information finns [på BinSkim på GitHub](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>Skanner för autentiseringsuppgifter

Lösen ord och andra hemligheter som lagras i käll koden är för närvarande ett betydande problem. För att läsa in autentiseringsuppgifter är ett eget statiskt analys verktyg som identifierar autentiseringsuppgifter, hemligheter, certifikat och annat känsligt innehåll i din källkod och dina build-utdata.

### <a name="microsoft-security-risk-detection"></a>Microsofts säkerhets risk identifiering

Identifiering av säkerhets risker är Microsofts unika molnbaserade fuzzly testing-tjänst för att identifiera sårbarhets skydds fel i program vara. Den här tjänsten kräver en separat onboarding-process. Mer information finns [på MSRD på docs.Microsoft.com](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>Roslyn-analyser

Microsofts kompilator-integrerade verktyg för statisk analys för analys av hanterad kodC# (och VB). Mer information finns [i Roslyn-analyser på docs.Microsoft.com](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

TSLint är ett utöknings Bart statiskt analys verktyg som kontrollerar TypeScript-kod för läsbarhet, hanterbarhet och funktions fel. Det är mycket enkelt att använda moderna redigerings program och build-system och kan anpassas med dina egna luddfria regler, konfigurationer och formaterade versioner. TSLint är ett verktyg med öppen källkod och mer information finns [på TSLint på GitHub](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>Analys och efter bearbetning av resultat

Microsoft Security code Analysis-tillägget har också tre användbara uppgifter efter bearbetning för att hjälpa dig att bearbeta och analysera resultaten som finns i säkerhets verktygs aktiviteterna. De läggs vanligt vis till i pipelinen efter alla andra verktygs uppgifter.

### <a name="publish-security-analysis-logs"></a>Publicera säkerhets analys loggar
Med build-aktiviteten publicera säkerhets analys loggar bevaras loggfilerna för säkerhets verktygen som körs under versionen för undersökning och uppföljning.

De kan publiceras till Azure-serverns artefakter (som en zip-fil) eller kopior till en tillgänglig fil resurs från din privata build-agent.

### <a name="security-report"></a>Säkerhets rapport
Säkerhets rapportens build-aktivitet tolkar de loggfiler som skapats av säkerhets verktygen som körs under skapandet och skapar en enda sammanfattande rapport fil med alla problem som har påträffats av analys verktygen.

Uppgiften kan konfigureras för att rapportera resultat för specifika verktyg eller för alla verktyg, och du kan också välja vilken nivå av problem (fel eller fel och varningar) som ska rapporteras.

### <a name="post-analysis-build-break"></a>Efter analys (Bygg rast)
Med Bygg aktiviteten efter analys kan kunden mata in en versions rast och inte bygga om en eller flera analys verktyg rapporterar om resultat eller problem i koden.

Uppgiften kan konfigureras för att bryta ned build för problem som upptäckts av specifika verktyg eller för alla verktyg, och som även baseras på allvarlighets graden för problem som upptäckts (fel eller varningar).

>[!NOTE]
>Enskilda Bygg aktiviteter lyckas, efter design, så länge verktyget har slutförts, oavsett om det finns några brister eller inte så att versionen kan köras för att slutföras, vilket gör att alla verktyg kan köras.

## <a name="next-steps"></a>Nästa steg

Instruktioner om hur du registrerar och installerar säkerhets kod analys finns i vår onboarding- [och installations guide](security-code-analysis-onboard.md)

Mer information om hur du konfigurerar build-aktiviteter finns i vår [konfigurations guide](security-code-analysis-customize.md)

Om du har fler frågor om tillägget och de verktyg som erbjuds [kontrollerar du sidan med vanliga frågor och svar.](security-code-analysis-faq.md)