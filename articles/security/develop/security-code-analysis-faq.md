---
title: Vanliga frågor och svar om Microsoft Security Code Analysis-dokumentation
description: Den här artikeln innehåller en vanliga frågor och svar om tillägget Microsoft Security Code Analysis
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
ms.openlocfilehash: de76467cc741a65a851e5f3a7ec424d0326aebb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851527"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar
Har du frågor? Läs följande vanliga frågor och svar för mer information.

## <a name="general-faq"></a>Vanliga frågor och svar

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Kan jag installera tillägget på min Visual Studio Team Foundation Server-instans i stället för på en Azure DevOps-instans?

Nej. Tillägget är inte tillgängligt för nedladdning och installation för Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Måste jag köra Microsoft Security Code Analysis med min version? 

Kanske. Det beror på vilken typ av analysverktyg. Källkoden kan vara det enda som krävs, eller byggutdata kan krävas.

CredScan (Credential Scanner) analyserar till exempel filer i koddatabasens mappstruktur. På grund av den här analysen kan du köra credscan- och publicera säkerhetsanalysloggar skapa uppgifter i en fristående version för att få resultat.

För andra verktyg som BinSkim som analyserar post-build artefakter, bygget krävs först.

### <a name="can-i-break-my-build-when-results-are-found"></a>Kan jag bryta min version när resultaten hittas?

Ja. Du kan introducera en byggbrytning när ett verktyg rapporterar ett problem eller problem i loggfilen. Lägg bara till aktiviteten Efter analys och markera kryssrutan för alla verktyg som du vill bryta bygget för.

I användargränssnittet för aktiviteten Efter analys kan du välja att bryta versionen när ett verktyg rapporterar antingen fel eller både fel och varningar.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Hur skiljer sig kommandoradsargumenten i Azure DevOps från argumenten i de fristående skrivbordsverktygen? 

För det mesta är Azure DevOps-bygguppgifterna direkta omslag runt kommandoradsargumenten i säkerhetsverktygen. Du kan skicka som argument till en bygguppgift allt som du normalt skickar till ett kommandoradsverktyg.

Märkbara skillnader:

- Verktyg körs från källmappen för agenten $(Build.SourcesDirectory) eller från %BUILD_SOURCESDIRECTORY%. Ett exempel är C:\agent\_work\1\s.
- Sökvägar i argumenten kan vara relativa till roten i den källkatalog som tidigare listats. Banor kan också vara absoluta. Du får absoluta sökvägar antingen genom att använda Azure DevOps Build Variables eller genom att köra en lokal agent med kända distributionsplatser för lokala resurser.
- Verktygen tillhandahåller automatiskt en sökväg eller mapp för utdatafilen. Om du anger en utdataplats för en bygguppgift ersätts den platsen med en sökväg till vår välkända plats för loggar på byggagenten
- Vissa ytterligare kommandoradsargument ändras för vissa verktyg. Ett exempel är tillägg eller borttagning av alternativ som säkerställer att inget grafiskt gränssnitt startas.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Kan jag köra en bygguppgift som Autentiseringsskanner över flera databaser i en Azure DevOps Build?

Nej. Det går inte att köra de säkra utvecklingsverktygen i flera databaser i en enda pipeline.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Utdatafilen som jag angav skapas inte eller så hittar jag inte utdatafilen som jag angav

Bygguppgifterna filtrerar vissa användarindata. För den här frågan specifikt uppdaterar de platsen för den genererade utdatafilen för att vara en gemensam plats på byggagenten. Mer information om den här platsen finns i följande frågor.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Var sparas utdatafilerna av verktygen? 

Byggaktiviteterna lägger automatiskt till utdatasökvägar till den här välkända platsen på\_byggagenten: $(Agent.BuildDirectory) sdt\logs. Eftersom vi standardiserar på den här platsen har alla team som producerar eller använder kodanalysloggar åtkomst till utdata.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Kan jag köa en version för att köra dessa uppgifter på en värdbyggagent? 

Ja. Alla uppgifter och verktyg i tillägget kan köras på en värdbyggd byggagent.

>[!NOTE]
> Build-uppgiften Mot Skadlig Kod kannning kräver en byggagent med Windows Defender aktiverat. Hosted Visual Studio 2017 och senare tillhandahålla en sådan agent. Bygguppgiften körs inte på den värdbaserade Visual Studio 2015-agenten.
>
> Även om signaturer inte kan uppdateras på dessa agenter, bör signaturer alltid vara mindre än tre timmar gamla.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Kan jag köra dessa bygguppgifter som en del av en versionspipeline i motsats till en byggpipeline?

I de flesta fall, ja.

Azure DevOps stöder dock inte att utföra uppgifter i versionspipelines när dessa uppgifter publicerar artefakter. Den här bristen på stöd förhindrar att aktiviteten Publicera säkerhetsanalysloggar körs i en versionspipeline. Uppgiften misslyckas i stället med ett beskrivande felmeddelande.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Varifrån hämtar bygguppgifterna verktygen?

Skapa uppgifter kan hämta verktygens NuGet-paket från [Azure DevOps Package Management feed](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Bygguppgifter kan också använda Nod Package Manager, som måste förinstalleras på byggagenten. Ett exempel på en sådan installation är kommandot **npm installera tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Vilken effekt har installationen av tillägget på min Azure DevOps-organisation? 

Vid installationen blir säkerhetsbyggen som tillhandahålls av tillägget tillgängliga för alla användare i organisationen. När du skapar eller redigerar en Azure Pipeline är dessa uppgifter tillgängliga från samlingslistan för build-task. Annars har det ingen effekt att installera tillägget i din Azure DevOps-organisation. Installationen ändrar inga kontoinställningar, projektinställningar eller pipelines.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Ändrar installationen av tillägget mina befintliga Azure Pipelines? 

Nej. Om du installerar tillägget blir säkerhetsversionsuppgifterna tillgängliga för tillägg till dina pipelines. Du måste fortfarande lägga till eller uppdatera byggdefinitioner, så att verktygen kan fungera med byggprocessen.

## <a name="task-specific-faq"></a>Uppgiftsspecifika vanliga frågor och svar

Frågor som är specifika för att skapa uppgifter visas i det här avsnittet.

### <a name="credential-scanner"></a>Skanner för autentiseringsuppgifter

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Vad är vanliga undertryckande scenarier och exempel?

Här är information om två av de vanligaste undertryckande scenarier.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Så här undertrycker du alla förekomster av en viss hemlighet i den angivna sökvägen

Hash-nyckeln för hemligheten från CredScan-utdatafilen krävs enligt följande exempel.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> Hash-nyckeln genereras av en del av det matchande värdet eller filinnehållet. Alla källkodsändringar kan ändra hash-tangenten och inaktivera dämpningsregeln.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Så här undertrycker du alla hemligheter i en angiven fil eller för att undertrycka själva hemlighetsfilen

Filuttrycket kan vara ett filnamn. Det kan också vara basnamnet del av en fullständig filsökväg eller ett filnamn. Jokertecken stöds inte.

I följande exempel visas hur \<du undertrycker indatapath-filen>\src\JS\lib\angular.js

Exempel på giltiga undertryckande regler:

- \<InputPath>\src\JS\lib\angular.js - undertrycker filen i den angivna sökvägen
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - undertrycker alla filer med samma namn

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Alla framtida hemligheter som läggs till i filen kommer också att undertryckas automatiskt.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Vilka är rekommenderade riktlinjer för hantering av hemligheter?

Följande resurser hjälper dig att hantera hemligheter på ett säkert sätt och få tillgång till känslig information inifrån dina program:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD-hanterad tjänstidentitet (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Hanterade identiteter i Azure App Service och Azure Functions](../../app-service/overview-managed-identity.md)
 - [AppAuthentication bibliotek](../../key-vault/service-to-service-authentication.md)


Mer information finns i blogginlägget [Hantera hemligheter säkert i molnet](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Kan jag skriva mina egna anpassade sökare?

Autentiseringsläsare förlitar sig på en uppsättning innehållssökare som vanligen definieras i filen buildsearchers.xml. Filen innehåller en matris med serialiserade XML-objekt som representerar ett **ContentSearcher-objekt.** Programmet distribueras med en uppsättning väl testade sökare. Men du kan implementera dina egna anpassade sökare också.

En innehållssökare definieras på följande sätt:

- **Namn**: Det beskrivande söknamnet som ska användas i Autentiseringsläsares utdatafiler. Vi rekommenderar att du använder namngivningskonventionen för att söka eftersöksjägaren.
- **RuleId**: Sökarens stabila ogenomskinliga ID:
    - En standardsökare för autentiseringsuppgifter tilldelas ett **RuleId-värde** som CSCAN0010, CSCAN0020 eller CSCAN0030. Den sista siffran är reserverad för att eventuellt slå samman eller dela sökgrupper via reguljära uttryck (regex).
    - **RuleId-värdet** för en anpassad sökare ska ha ett eget namnområde. Exempel är\<CSCAN-\>Namespace 0010, CSCAN-\<Namespace\>0020 och CSCAN-\<Namespace\>0030.
    - Ett fullständigt kvalificerat sökarnamn är kombinationen av ett **RuleId-värde** och ett sökarnamn. Exempel är CSCAN0010. KeyStoreFiles och CSCAN0020. Base64Kodacertificate.
- **ResourceMatchPattern**: Regex av filnamnstillägg för att kontrollera mot sökaren.
- **ContentSearchPatterns**: En matris med strängar som innehåller regex-satser som ska matchas. Om inga sökmönster har definierats returneras alla filer som matchar **ResourceMatchPattern-värdet.**
- **ContentSearchFilters**: En matris med strängar som innehåller regex-satser för att filtrera sökspecifika falska positiva identifieringar.
- **MatchDetails**: Ett beskrivande meddelande, begränsningsinstruktioner eller båda som ska läggas till för varje matchning av sökaren.
- **Rekommendation**: Innehåll för förslag-fält för en matchning med hjälp av PREfast rapportformat.
- **Allvarlighetsgrad**: Ett heltal som återspeglar allvarlighetsgraden för ett problem. Den högsta allvarlighetsgraden har värdet 1.

  ![XML som visar inställningar för autentiseringsläsare](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn-analysverktyg

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Vilka är vanliga fel när du använder aktiviteten Roslyn Analyzers?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Projektet återställdes med en felaktig Microsoft.NETCore.App-version

Det fullständiga felmeddelandet:

"Fel: Projektet återställdes med Microsoft.NETCore.App version *x.x.x.x*, men med aktuella inställningar skulle version *y.y.y* användas i stället. LÃ¶s problemet genom att kontrollera att samma inställningar används fÃ¶r återställning och fÃ¶r efterföljande åtgärder som att skapa eller publicera. Det här problemet kan vanligtvis uppstå om egenskapen RuntimeIdentifier anges under bygg eller publicera men inte under återställningen."

Eftersom Roslyn Analyzers-uppgifter körs som en del av kompileringen måste källträdet på byggda datorn vara i ett byggbart tillstånd.

Ett steg mellan huvudbygget och Roslyn Analyzers-stegen kan ha försatt källträdet i ett tillstånd som förhindrar att byggnaden byggs. Det här extra steget är förmodligen **dotnet.exe publicera**. Prova att duplicera steget som gör en NuGet-återställning strax före Roslyn Analyzers-steget. Det här duplicerade steget kan försätta källträdet i ett byggbart tillstånd igen.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe kan inte skapa en analysatorinstans

Det fullständiga felmeddelandet:

"'csc.exe' exited with error code 1 -- An instance of analyzer *AAAA* cannot be created from C:\\*BBBB*.dll : Could not load file or assembly 'Microsoft.CodeAnalysis, Version=*X.X.X.X*, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. Det går inte att hitta den angivna filen."

Se till att kompilatorn stöder Roslyn Analyzers. Om du kör kommandot **csc.exe /version** ska ett versionsvärde på 2.6 eller senare rapporteras.

Ibland kan en CSproj-fil åsidosätta byggmaskinens Visual Studio-installation genom att referera till ett paket från Microsoft.Net.Compilers. Om du inte tänker använda en viss version av kompilatorn tar du bort referenser till Microsoft.Net.Compilers. Annars, se till att versionen av det refererade paketet är också 2,6 eller senare.

Försök att hämta sökvägen för felloggen, som anges i alternativet **csc.exe /errorlog.** Alternativet och sökvägen visas i loggen för roslynanalysatorernas bygguppgift. De kan se ut ungefär **som /errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C# kompilator versionen är inte tillräckligt nyligen

Om du vill hämta de senaste versionerna av C#-kompilatorn går du till [Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Om du vill hämta den installerade versionen kör du **csc.exe /version** i en kommandotolk. Kontrollera att du refererar till ett Microsoft.Net.Compilers NuGet-paket som är version 2.6 eller senare.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>MSBuild- och VSBuild-loggar hittades inte

Roslyn Analyzers-bygguppgiften måste fråga Azure DevOps för MSBuild-loggen från MSBuild-bygguppgiften. Om analysatoraktiviteten körs direkt efter MSBuild-aktiviteten är loggen ännu inte tillgänglig. Placera andra aktiviteter mellan MSBuild-aktiviteten och aktiviteten Roslyn Analyzers. Exempel på andra uppgifter är BinSkim och Anti-Malware Scanner.

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare hjälp är Microsoft Security Code Analysis Support tillgängligt måndag till fredag från 09:00 till 17:00 Pacific Standard Time.

- Onboarding: Se vår [onboarding-dokumentation](security-code-analysis-onboard.md)
  
- Support: Skicka e-post till vårt team på [Microsoft Security Code Analysis Support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)