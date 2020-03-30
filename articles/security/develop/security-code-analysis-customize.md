---
title: Anpassa Microsofts säkerhetskodsanalysuppgifter
titleSuffix: Azure
description: I den här artikeln beskrivs anpassning av uppgifterna i tillägget Microsoft Security Code Analysis
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
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499991"
---
# <a name="configure-and-customize-the-build-tasks"></a>Konfigurera och anpassa bygguppgifterna

I den här artikeln beskrivs i detalj de konfigurationsalternativ som är tillgängliga i var och en av byggaktiviteterna. Artikeln börjar med uppgifterna för verktyg för analys av säkerhetskod. Det slutar med efterbearbetningsuppgifterna.

## <a name="anti-malware-scanner-task"></a>Uppgift mot skadlig kod skanner

>[!NOTE]
> Build-uppgiften Mot Skadlig Kod kannning kräver en byggagent med Windows Defender aktiverat. Hosted Visual Studio 2017 och senare tillhandahålla en sådan agent. Bygguppgiften körs inte på den värdbaserade Visual Studio 2015-agenten.
>
> Även om signaturer inte kan uppdateras på dessa agenter, bör signaturer alltid vara mindre än tre timmar gamla.

Information om aktivitetskonfiguration visas i följande skärmbild och text.

![Konfigurera bygguppgiften för Anti-Malware Scanner](./media/security-tools/5-add-anti-malware-task600.png)

I rutan **Typ i** skärmbilden är **Basic** markerat. Välj **Anpassad** om du vill ange kommandoradsargument som anpassar genomsökningen.

Windows Defender använder Windows Update-klienten för att hämta och installera signaturer. Om signaturuppdateringen misslyckas på din byggagent kommer **HRESULT-felkoden** troligen från Windows Update.

Mer information om Windows Update-fel och hur de kan minskas finns i [Felkoder för Windows Update efter komponent](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) och TechNet-artikeln Windows Update Agent - [Felkoder](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Mer information om YAML-konfiguration för den här uppgiften finns i våra [YAML-alternativ mot skadlig kod](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim-uppgift

> [!NOTE]
> Innan du kan köra aktiviteten BinSkim måste bygget uppfylla något av följande villkor:
>  - Din version producerar binära artefakter från hanterad kod.
>  - Du har binära artefakter som har begåtts som du vill analysera med BinSkim.

Information om aktivitetskonfiguration visas i följande skärmdump och lista.

![Konfigurera lager av lager av lagerplats](./media/security-tools/7-binskim-task-details.png)

- Ange build-konfigurationen till Felsökning så att PDB-felsökningsfiler produceras. BinSkim använder dessa filer för att mappa problem i utdatabinärerna tillbaka till källkoden.
- Så här undviker du att forska och skapa en egen kommandorad:
     - Välj **Basic**i listan **Typ** .
     - Välj **Analysera**i **funktionslistan** .
- Ange en eller flera specificerare för en fil, katalog eller ett filtermönster i **Mål.** Dessa specificerare matchas till en eller flera binärfiler som ska analyseras:
    - Flera angivna mål måste avgränsas med ett semikolon (;).
    - En specificerare kan vara en enda fil eller innehålla jokertecken.
    - Katalogspecifikationer måste \\alltid sluta med *.
    - Exempel:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- Om du väljer **Kommandorad** i listan **Typ** måste du köra binskim.exe:
     - Kontrollera att de första argumenten till binskim.exe är **verbanalysen** följt av en eller flera sökvägsspecifikationer. Varje sökväg kan vara antingen en fullständig sökväg eller en sökväg i förhållande till källkatalogen.
     - Flera målbanor måste avgränsas med ett blanksteg.
     - Du kan utelämna alternativet **/o** eller **/output.** Utdatavärdet läggs till eller ersätts.
     - Standardkommandoradskonfigurationer visas på följande sätt.

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > Efterföljande \\* är viktigt om du anger kataloger för målet.

Mer information om kommandoradsargument, regler efter ID eller avslutningskoder finns i [användarhandboken för BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Mer information om YAML-konfiguration för den här uppgiften finns i våra [binSkim YAML-alternativ](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Autentiseringsskanneruppgift

Information om aktivitetskonfiguration visas i följande skärmdump och lista.

![Konfigurera aktiviteten för autentiseringsläsare](./media/security-tools/3-taskdetails.png)

Tillgängliga alternativ inkluderar:

  - **Utdataformat:** Tillgängliga värden inkluderar **TSV,** **CSV,** **SARIF**och **PREfast**.
  - **Verktygsversion:** Vi rekommenderar att du väljer **Senaste**.
  - **Sök mapp:** Den databasmapp som ska skannas.
  - **Sökare Filtyp:** Alternativen för att hitta sökarfilen som används för skanning.
  - **Suppressions File**: En [JSON-fil](https://json.org/) kan undertrycka problem i utdataloggen. Mer information om underdämningsscenarier finns i avsnittet Vanliga frågor och svar i den här artikeln.
  - **Verbose Output**: Självförklarande.
  - **Batchstorlek:** Antalet samtidiga trådar som används för att köra autentiseringsskanner. Standardvärdet är 20. Möjliga värden varierar från 1 till 2 147 483 647.
  - **Match timeout:** Hur mycket tid i sekunder att spendera försöker en sökare match innan överge checken.
  - **Filsökning läsbuffertstorlek:** Storleken i byte av bufferten som används medan innehållet läss. Standardvärdet är 524 288.  
  - **Maximal filsökning läsbyte:** Det maximala antalet byte som ska läsas från en fil under innehållsanalys. Standardvärdet är 104 857 600.
  - **Kontrollalternativ** > **Kör den här aktiviteten:** Anger när aktiviteten ska köras. Välj **Anpassade villkor** om du vill ange mer komplexa villkor.
  - **Version**: Version aktivitetsversionen i Azure DevOps. Det här alternativet används inte ofta.

Mer information om YAML-konfiguration för den här uppgiften finns i våra [YAML-alternativ för autentiseringsuppgifter](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>Uppgift för identifiering av säkerhetsrisk

> [!NOTE]
> Du måste skapa och konfigurera ett konto med MSRD-tjänsten (Microsoft Security Risk Detection) innan du använder MSRD-uppgiften. Den här tjänsten kräver en separat introduktionsprocess. Till skillnad från de flesta andra uppgifter i det här tillägget kräver den här aktiviteten en separat prenumeration med MSRD.
>
> Instruktioner finns i [Microsoft Security Risk Detection](https://aka.ms/msrddocs) och Microsoft Security Risk [Detection: How To](https://docs.microsoft.com/security-risk-detection/how-to/) instructions.

Information om hur du konfigurerar den här uppgiften visas i följande lista. För alla gränssnittselement kan du hovra över elementet för att få hjälp.

   - **Azure DevOps-tjänstslutpunktsnamn för MSRD**: En allmän typ av Azure DevOps-tjänstslutpunkt lagrar din inbyggda MSRD-instans-URL och din REST API-åtkomsttoken. Om du har skapat en sådan slutpunkt kan du ange den här. Annars väljer du länken **Hantera** för att skapa och konfigurera en ny tjänstslutpunkt för den här MSRD-uppgiften.
   - **Konto-ID:** Ett GUID som kan hämtas från MSRD-konto-URL:en.
   - **Webbadresser till binärfiler:** En semikolonavgränsad lista över offentligt tillgängliga webbadresser. Fuzzing-datorn använder dessa url:er för att hämta binärfilerna.
   - **Url:er för seed-filerna:** En semikolonavgränsad lista över offentligt tillgängliga webbadresser. Fuzzing maskinen använder dessa webbadresser för att ladda ner frön. Det är valfritt att ange det här värdet om såddfilerna hämtas tillsammans med binärfilerna.
   - **OS-plattformstyp:** Operativsystemet (OS) plattform för datorer som kör fuzzing jobbet. Tillgängliga värden är **Windows** och **Linux**.
   - **Windows Edition / Linux Edition**: OS-utgåvan av maskiner som kör fuzzing jobbet. Du kan skriva över standardvärdet om dina datorer har en annan OS-utgåva.
   - **Paketinstallationsskript:** Skriptet som ska köras på en testmaskin. Det här skriptet installerar testmålprogrammet och dess beroenden innan det luddiga jobbet skickas.
   - **Parametrar för jobbinlämning:**
       - **Seed Directory**: Sökvägen till katalogen på fuzzing maskinen som innehåller frön.
       - **Seed Extension**: Filnamn förlängning av fröna.
       - **Körbar testdrivrutin:** Sökvägen till den körbara målfilen på den luddiga datorn.
       - **Körbar testdrivrutin arkitektur:** Målkörningsfilens arkitektur. Tillgängliga värden är **x86** och **amd64**.
       - **Argument för testdrivrutin:** Kommandoradsargumenten som skickades till den körbara testfilen. Argumentet %testfile%", inklusive citattecken, ersätts automatiskt med den fullständiga sökvägen till målfilen. Den här filen tolkas av testdrivrutinen och krävs.
       - **Testdrivrutinen avslutas när testprocessen är klar:** Markera den här kryssrutan om testdrivrutinen ska avslutas när den är klar. Rensa den om testföraren behöver stängas med våld.
       - **Maximal varaktighet (i sekunder):** En uppskattning av den längsta rimligen förväntade tiden som målprogrammet kräver för att tolka en indatafil. Ju mer exakt uppskattningen är, desto effektivare körs den luddiga appen.
       - **Testdrivrutinen kan köras upprepade gånger:** Markera den här kryssrutan om testdrivrutinen kan köras upprepade gånger utan att vara beroende av ett beständigt eller delat globalt tillstånd.
       - **Testdrivrutinen kan byta namn:** Markera den här kryssrutan om körbar testdrivrutinsfil kan byta namn och fortfarande fungera korrekt.
       - **Fuzzing-programmet körs som en enda OS-process:** Markera den här kryssrutan om testdrivrutinen körs under en enda OS-process. Rensa den om testföraren ger upphov till ytterligare processer.

Information om YAML-konfiguration för den här uppgiften finns i våra [YAML-alternativ](yaml-configuration.md#microsoft-security-risk-detection-task) för identifiering av säkerhet

## <a name="roslyn-analyzers-task"></a>Roslyn Analyzers uppgift

> [!NOTE]
> Innan du kan köra aktiviteten Roslyn Analyzers måste din version uppfylla följande villkor:
>
> - Din build-definition innehåller den inbyggda MSBuild- eller VSBuild-bygguppgiften för att kompilera C# eller Visual Basic-kod. Analysatoruppgiften är beroende av indata och utdata från den inbyggda aktiviteten för att köra MSBuild-kompileringen med Roslyn-analysatorer aktiverade.
> - Byggagenten som kör den här bygguppgiften har Visual Studio 2017 version 15.5 eller senare installerad, så att kompilatorversion 2.6 eller senare används.

Information om aktivitetskonfiguration visas i följande lista och anmärkning.

Tillgängliga alternativ inkluderar:

- **Regeluppsättning**: Värden är **SDL-obligatoriska,** **SDL Rekommenderas**eller din egen anpassade regeluppsättning.
- **Analyzers Version:** Vi rekommenderar att du väljer **Senaste**.
- **Dämpning av kompilatorvarningar Fil:** En textfil med en lista över varningar ID:er som undertrycks.
- **Kontrollalternativ** > **Kör den här aktiviteten:** Anger när aktiviteten ska köras. Välj **Anpassade villkor** för att ange mer komplexa villkor.

> [!NOTE]
>
> - Roslyn Analyzers är integrerade med kompilatorn och kan endast köras som en del av csc.exe-kompileringen. Därför kräver den här uppgiften kompilatorkommandot som kördes tidigare i bygget som ska spelas upp eller köras igen. Den här uppspelningen eller körningen görs genom att fråga Visual Studio Team Services (VSTS) för MSBuild build-aktivitetsloggarna.
>
>   Det finns inget annat sätt för uppgiften att på ett tillförlitligt sätt hämta MSBuild-kompileringskommandoraden från build-definitionen. Vi övervägde att lägga till en frihandstextruta så att användarna kan ange sina kommandorader. Men då skulle det vara svårt att hålla dessa kommando linjer up-to-date och i synk med de viktigaste bygga.
>
>   Anpassade versioner kräver att hela uppsättningen kommandon spelas upp, inte bara kompilatorkommandon. I dessa fall är det inte trivialt eller tillförlitligt att aktivera Roslyn Analyzers.
>
> - Roslyn Analyzers är integrerade med kompilatorn. För att anropas kräver Roslyn Analyzers kompilering.
>
>   Den här nybyggningsuppgiften implementeras genom att C#-projekt som redan har byggts om. Den nya aktiviteten använder endast MSBuild- och VSBuild-byggaktiviteterna i samma bygg- eller byggdefinition som den ursprungliga uppgiften. I det här fallet använder den nya aktiviteten dem med Roslyn Analyzers aktiverade.
>
>   Om den nya aktiviteten körs på samma agent som den ursprungliga aktiviteten skriver den nya aktivitetens utdata över den ursprungliga aktivitetens utdata i *källmappen.* Även om build-utdata är densamma rekommenderar vi att du kör MSBuild, kopierar utdata till den artefakter som mellanlagringskatalogen och sedan kör Roslyn Analyzers.

Om du vill ha ytterligare resurser för aktiviteten Roslyn Analyzers finns i [Roslyn-baserade analysatorer](https://docs.microsoft.com/dotnet/standard/analyzers/) på Microsoft Dokument.

Du hittar analysatorpaketet som är installerat och används av den här bygguppgiften på NuGet-sidan [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Mer information om YAML-konfiguration för den här uppgiften finns i våra [Roslyn Analyzers YAML-alternativ](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint-uppgift

Mer information om TSLint finns i [repo- för TSLint GitHub](https://github.com/palantir/tslint).

>[!NOTE] 
>Som du kanske vet säger [TSLint GitHub repo](https://github.com/palantir/tslint) hemsida att TSLint kommer att vara föråldrad någon gång under 2019. Microsoft undersöker [ESLint](https://github.com/eslint/eslint) som en alternativ uppgift.

Mer information om YAML-konfiguration för den här uppgiften finns i våra [TSLint YAML-alternativ](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Uppgift publicera säkerhetsanalysloggar

Information om aktivitetskonfiguration visas i följande skärmdump och lista.

![Konfigurera byggaktiviteten för publiceringssäkerhetsanalysloggar](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Artefaktnamn**: Alla strängidentifierare.
- **Artefakttyp:** Beroende på ditt val kan du publicera loggar till din Azure DevOps Server eller till en delad fil som är tillgänglig för byggagenten.
- **Verktyg**: Du kan välja att bevara loggar för specifika verktyg, eller så kan du välja **Alla verktyg** för att bevara alla loggar.

Mer information om YAML-konfiguration för den här uppgiften finns i [yaml-alternativen för publicera säkerhetsloggar](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Uppgift om säkerhetsrapport

Information om konfigurationen av säkerhetsrapporten visas i följande skärmbild och lista.

![Konfigurera aktiviteten För att skapa säkerhetsrapport](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Rapporter**: Välj något av **formaten Pipeline Console,** **TSV File**och **Html-fil.** En rapportfil skapas för varje markerat format.
- **Verktyg**: Välj de verktyg i byggdefinitionen som du vill ha en sammanfattning av upptäckta problem för. För varje valt verktyg kan det finnas ett alternativ för att välja om du bara ser fel eller ser både fel och varningar i sammanfattningsrapporten.
- **Avancerade alternativ:** Om det inte finns några loggar för något av de valda verktygen kan du välja att logga en varning eller ett fel. Om du loggar ett fel misslyckas aktiviteten.
- **Basloggar Mapp:** Du kan anpassa basen loggar mappen där loggar finns. Men det här alternativet används vanligtvis inte.

Information om YAML-konfiguration för den här uppgiften finns i [yaml-alternativen för säkerhetsrapporten](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Uppgift efter analys

Information om aktivitetskonfiguration visas i följande skärmdump och lista.

![Konfigurera aktiviteten för bygge efter analys](./media/security-tools/a-post-analysis600.png)

- **Verktyg:** Välj de verktyg i din byggdefinition som du vill använda villkorligt för att injicera en byggpaus. För varje valt verktyg kan det finnas ett alternativ för att välja om du vill bryta med fel eller på både fel och varningar.
- **Rapport**: Du kan också skriva de resultat som orsakar byggbrytningen. Resultaten skrivs till Azure DevOps-konsolfönstret och loggfilen.
- **Avancerade alternativ:** Om det inte finns några loggar för något av de valda verktygen kan du välja att logga en varning eller ett fel. Om du loggar ett fel misslyckas aktiviteten.

Mer information om YAML-konfiguration för den här uppgiften finns i våra [yaml-alternativ för postanalys](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Nästa steg

Information om YAML-baserad konfiguration finns i vår [YAML-konfigurationsguide](yaml-configuration.md).

Om du har ytterligare frågor om tillägget Security Code Analysis och de verktyg som erbjuds, kolla in [vår FAQ-sida.](security-code-analysis-faq.md)
