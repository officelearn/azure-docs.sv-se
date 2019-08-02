---
title: Microsoft Azure anpassnings guide för säkerhets kod analys
description: Den här artikeln är att anpassa aktiviteterna i tillägget för säkerhets kod analys
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718358"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>Instruktioner: Konfigurera & Anpassa Bygg aktiviteterna

Den här sidan beskriver i detalj de konfigurations alternativ som är tillgängliga i varje build-åtgärd, från och med aktiviteter för analys verktyg för säkerhets koder följt av bearbetnings uppgifterna

## <a name="anti-malware-scanner-task"></a>Aktivitet för skanner för program mot skadlig kod

> [!NOTE]
> En version av program mot skadlig kod kräver en build-agent med Windows Defender aktiverat, vilket är sant på "värdbaserad VS2017" och senare build-agenter. (Den kommer inte att köras på den äldre/VS2015 "värdbaserade agenten") Det går inte att uppdatera signaturer för de här agenterna, men signaturen ska alltid vara relativt aktuell, mindre än tre timmar gamla.

Skärm bild och information om hur du konfigurerar nedan.

![Anpassa build-uppgiften för antivirus program](./media/security-tools/5-add-anti-malware-task600.png) 

- Inställningar för typ = **Basic**
- Med Type = **Custom**kan kommando rads argument anges för att anpassa genomsökningen.

Windows Defender använder Windows Update-klienten för att ladda ned och installera signaturer. Om det inte går att uppdatera signaturen på din build-agent kommer HRESULT-felkoden troligen från Windows Update. Mer information om Windows Update fel och begränsningar finns på [den här sidan](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) och på [TechNet-sidan](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)

## <a name="binskim-task"></a>BinSkim-uppgift

> [!NOTE]
> Som en förutsättning för att köra BinSkim-aktiviteten bör din version uppfylla ett av nedanstående villkor.
>    - Din version genererar binära artefakter från hanterad kod.
>    - Du har binära artefakter som du vill analysera med BinSkim.

Skärm bild och information om hur du konfigurerar nedan. 

![BinSkim-installation](./media/security-tools/7-binskim-task-details.png)  
1. Ange build-konfigurationen till debug för att skapa * **. pdb** -felsökning. De används av BinSkim för att mappa problem som upptäckts i den binära utdata tillbaka till käll koden. 
2. Välj typ = **grundläggande** & funktion = **analysera** för att undvika att söka och skapa en egen kommando rad. 
3. **Mål** – en eller flera specificerare för en fil, ett katalog eller ett filter mönster som matchar en eller flera binärfiler som ska analyseras. 
    - Flera mål ska avgränsas med ett **semikolon (;)** . 
    - Kan vara en enskild fil eller innehålla jokertecken.
    - Kataloger bör alltid avslutas med\*
    - Exempel:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. Om du väljer Type = **kommando rad** 
     - Kontrol lera att det första argumentet för **BinSkim. exe** är verbet som **analyseras** med hjälp av fullständiga sökvägar eller sökvägar i förhållande till käll katalogen.
     - För **kommando rads** indatatypen ska flera mål avgränsas med ett blank steg.
     - Du kan utelämna fil parametern **/o** eller **/output** den kommer att läggas till för dig eller ersättas. 
     - **Standard kommando rads konfiguration** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > Efterföljande \* är viktigt när du anger en katalog eller kataloger för målet. 

Mer information om BinSkim om kommando rads argument, regler efter ID eller slut koder finns i [användar handboken för BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)

## <a name="credential-scanner-task"></a>Uppgift för genomsökning av autentiseringsuppgifter
Skärm bild och information om hur du konfigurerar nedan.
 
![Anpassning av autentiseringsuppgifter för läsare](./media/security-tools/3-taskdetails.png)

De tillgängliga alternativen är 
  - **Utdataformat** – TSV/CSV/SARIF/prefast
  - **Verktygs version** Rekommenderas Nya
  - **Genomsök mapp** – mappen i din lagrings plats för att genomsöka
  - **Sökvägar filtyp** -alternativ för att hitta Sök efter-filen som används för genomsökning.
  - **Undertrycks fil** – en [JSON](https://json.org/) -fil kan användas för att ignorera problem i utgående logg (mer information finns i avsnittet resurser). 
  - **Utförliga utdata** – själv för klar Ande 
  - **Batchstorlek** – antalet samtidiga trådar som används för att köra skannrar för autentiseringsuppgifter parallellt. Standardvärdet är 20 (värdet måste vara mellan 1 och 2147483647).
  - **Matchnings-timeout** – hur lång tid det tar att försöka lägga till en Sök förpassning innan kontrollen överges. 
  - **Fil genomsökningens Läs buffertstorlek** – buffertstorlek vid läsning av innehåll i byte. (Standardvärdet är 524288) 
  - **Maximalt antal lästa fil läsningar byte** -maximalt antal byte som ska läsas från en specifik fil under innehålls analysen. (Standardvärdet är 104857600) 
  - **Kör den här uppgiften** (under **kontroll alternativ**) – anger när aktiviteten ska köras. Välj anpassade villkor om du vill ange mer komplexa villkor. 
  - **Version** – Bygg uppgifts version i Azure DevOps. Används inte ofta. 

## <a name="microsoft-security-risk-detection-task"></a>Uppgift om Microsofts säkerhets risk identifiering
> [!NOTE]
> Du måste skapa och konfigurera ett konto med tjänsten för risk identifiering för att kunna använda den här uppgiften. Den här tjänsten kräver en separat onboarding-process. Det är inte "plug-and-Play" som de flesta av de andra aktiviteterna i det här tillägget. Se [Microsofts säkerhets risk identifiering](https://aka.ms/msrddocs) och [Microsofts säkerhets risk identifiering: ](https://docs.microsoft.com/security-risk-detection/how-to/) Anvisningar.

Information om hur du konfigurerar nedan.

Ange de data som krävs. varje alternativ har hjälp med att hovra text.
   - **Namn på Azure DevOps-tjänstens slut punkt för MSRD**: Om du har skapat en allmän typ av Azure DevOps-tjänstens slut punkt för att lagra MSRD-instansens URL (som du har registrerat till) och REST API åtkomsttoken, kan du välja den tjänst slut punkten. Annars klickar du på länken hantera för att skapa och konfigurera en ny tjänst slut punkt för den här MSRD-aktiviteten. 
   - **Konto-ID**: Det är ett GUID som kan hämtas från MSRD-kontots URL.
   - **URL: er till binärfiler**: En semikolonavgränsad lista med offentligt tillgängliga URL: er (som används av den suddiga datorn för att ladda ned binärfilerna).
   - **URL: er för**startfilerna: En semikolonavgränsad lista med offentligt tillgängliga URL: er (som används av den suddiga datorn för att hämta frön). Det här fältet är valfritt om startfilerna laddas ned tillsammans med binärfilerna.
   - **Typ av operativ system plattform**: OS-plattformens typ (Windows eller Linux) av datorer för att köra det suddiga jobbet.
   - **Windows-utgåva/Linux-utgåva**: OS-versionen av datorerna som ska köra det suddiga jobbet. Du kan skriva över standardvärdet om datorerna har en annan operativ system version.
   - **Paket installations skript**: Ange att skriptet ska köras på en test dator för att installera test mål programmet och dess beroenden innan du skickar det suddiga jobbet.
   - **Jobb överförings parametrar**:
       - **Dirigerings katalog**: Sökväg till katalogen på den suddiga datorn som innehåller frön.
       - **Seed-tillägg**: Tilläggets fil namns tillägg
       - **Körbar test driv rutin**: Sökvägen till den körbara mål filen på den suddiga datorn.
       - Körnings **arkitektur för test driv rutin**: Den körbara filens fil arkitektur (x86 eller amd64).
       - **Argument för test driv rutin**: Kommando rads argumenten som skickas till den körbara test mål filen. Observera att symbolen "% testfile%", inklusive de dubbla citat tecknen, ersätts automatiskt med den fullständiga sökvägen till mål filen. test driv rutinen förväntas parsas och krävs.
       - **Test driv rutins processen avslutas vid test slut för ande**: Markera om du vill avbryta test driv rutinen vid slut för ande. Avmarkera om test driv rutinen måste tvingas stängas.
       - **Maximal varaktighet (i sekunder)** : Ange en uppskattning av den längsta förväntade förväntade tiden som krävs för att mål programmet ska kunna parsa indatafilen. Den mer exakta uppskattningen, desto mer effektiv den fuzza körningen.
       - **Test driv rutinen kan köras flera gånger**: Kontrol lera om test driv rutinen kan köras upprepade gånger utan att beroende på ett beständigt/delat globalt tillstånd.
       - **Du kan byta namn på test driv rutin**: Kontrol lera om den körbara test driv rutinen kan byta namn och fortfarande fungera korrekt.
       - **Fuzzing-programmet körs som en enda operativ system process**: Kontrol lera om test driv rutinen körs under en enda operativ system process; Avmarkera om test driv rutinen skapar ytterligare processer.


## <a name="roslyn-analyzers-task"></a>Roslyn för analys uppgifter
> [!NOTE]
> Som ett krav för att köra Roslyn Analyzer-uppgiften bör din version uppfylla följande villkor.
>  - Din build-definition innehåller den inbyggda MSBuild-eller VSBuild build-uppgiften för C# att kompilera (eller VB) kod. Den här uppgiften använder sig av indata och utdata för den specifika build-uppgiften för att köra MSBuild-kompileringen igen med Roslyn-analyserare aktiverade.
>  - Build-agenten som kör den här bygg aktiviteten har Visual Studio 2017 v 15.5 eller senare installerat (kompilator version 2.6. x).
>

Information om hur du konfigurerar nedan.

De tillgängliga alternativen är 
- **Ruleset** – sdl krävs, sdl rekommenderas, eller så kan du använda en anpassad ruleset.
- **Analys version** Rekommenderas Nya
- **Kompilator varningar undertrycks fil** – en textfil med en lista med varnings-ID: n som ska ignoreras. 
- **Kör den här uppgiften** (under **kontroll alternativ**) – anger när aktiviteten ska köras. Välj**anpassade villkor**om du vill ange mer komplexa villkor. 

> [!NOTE]
> - Roslyn-analyser är compiler-integrerade och kan endast köras som en del av CSC. exe-kompileringen. Den här uppgiften kräver därför uppspelning/körning av compile-kommandot som kördes tidigare i versionen. Detta görs genom att fråga VSTS efter aktivitets loggarna för MSBuild-skapande (det finns inget annat sätt för uppgiften att på ett tillförlitligt sätt Hämta kommando raden för MSBuild-kompilering från build-definitionen. vi övervägde att lägga till en fri hands text ruta så att användarna kan ange sina kommando rader men det skulle vara svårt att hålla dessa aktuella och synkroniserade med huvud versionen). Anpassade versioner kräver uppspelning av hela uppsättningen kommandon, inte bara compile-kommandon, och det är inte enkelt/tillförlitligt att aktivera Roslyn-analyser i dessa fall. 
> - Roslyn-analyser är integrerade med kompileraren och kräver att kompileringen anropas. Den här bygg aktiviteten implementeras genom att C# kompilera om projekt som redan har skapats med bara MSBuild/VSBuild build-uppgiften, i samma build/build-definition, men i det här fallet med analyserarna aktiverade. Om den här build-aktiviteten körs på samma agent som den ursprungliga Bygg aktiviteten, kommer utdatan från den ursprungliga MSBuild/VSBuild-build-uppgiften att skrivas över i mappen sources, genom utdata från den här bygg aktiviteten. Skapandet av utdata är detsamma, men vi rekommenderar att du kör MSBuild, kopierar utdata till den fristående katalogen för artefakter och kör sedan Roslyn.
>

För ytterligare resurser för Roslyn för analys av uppgifter [Roslyn för analys av aktiviteter på Microsoft docs](https://docs.microsoft.com/dotnet/standard/analyzers/)

Det Analyzer-paket som har installerats och används av den här build-uppgiften finns [här](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 

## <a name="tslint-task"></a>TSLint-uppgift

Mer information om TSLint finns på [TSLint GitHub lagrings platsen](https://github.com/palantir/tslint)
>[!NOTE] 
>Som du kanske är medveten om är TSLint inaktuell i 2019 (Källa: [TSLint GitHub lagrings platsen](https://github.com/palantir/tslint)) Teamet undersöker för närvarande [ESLint](https://github.com/eslint/eslint) som ett alternativ och skapar en ny uppgift för ESLint i översikten.

## <a name="publish-security-analysis-logs-task"></a>Aktiviteten publicera säkerhets analys loggar
Skärm bild och information om hur du konfigurerar nedan.

![Anpassa publicering av säkerhets analys](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Artefakt namn** -kan vara vilken sträng identifierare som helst
- **Artefakt typ** – du kan publicera loggar på Azure-DevOps-servern eller till en fil resurs som är tillgänglig för build-agenten. 
- **Verktyg** – du kan välja att bevara loggar för enskilda/specifika verktyg eller välja **alla verktyg** för att bevara alla loggar. 

## <a name="security-report-task"></a>Säkerhets rapports aktivitet
Skärm bild och information om hur du konfigurerar nedan.  
![Konfigurera efter analys](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **Rapporter** – Välj vilka rapport filer som ska skapas. en skapas i varje format **konsol**, **TSV**och/eller **HTML** 
- **Verktyg** – Välj de verktyg i definitions definitionen för vilka du vill ha en sammanfattning av problem som upptäckts. För varje valt verktyg kan det finnas ett alternativ för att välja om du vill visa fel eller både fel och varningar i rapporten. 
- **Avancerade alternativ** – du kan välja att logga en varning eller ett fel (och inte utföra aktiviteten) om det inte finns några loggar för något av de valda verktygen.
Du kan anpassa mappen grundläggande loggar där loggar ska hittas, men det är inte ett typiskt scenario. 

## <a name="post-analysis-task"></a>Uppgift efter analys
Skärm bild och information om hur du konfigurerar nedan.

![Anpassa efter analys](./media/security-tools/a-post-analysis600.png) 
- **Verktyg** – Välj de verktyg i din build-definition för vilka du vill mata in en versions brytning utifrån resultaten. För varje valt verktyg kan det finnas ett alternativ för att välja om du bara vill bryta vid fel eller både fel och varningar. 
- **Rapport** – du kan välja att skriva resultaten som påträffas och orsaka att bygget går till i fönstret för Azure DevOps-konsolen och logg filen. 
- **Avancerade alternativ** – du kan välja att logga en varning eller ett fel (och inte utföra aktiviteten) om det inte finns några loggar för något av de valda verktygen.

## <a name="next-steps"></a>Nästa steg

Om du har fler frågor om tillägget och de verktyg som erbjuds [kontrollerar du sidan med vanliga frågor och svar.](security-code-analysis-faq.md)


