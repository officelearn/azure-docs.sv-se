---
title: Vanliga frågor och svar om dokumentation om säkerhets kod analys Microsoft Azure
description: Den här artikeln innehåller vanliga frågor och svar om tillägg för säkerhets kod analys
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
ms.openlocfilehash: 8038b7bd60ac771c798a1a8645022b0bf9e142a9
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934852"
---
# <a name="frequently-asked-questions"></a>Vanliga frågor och svar
Har du frågor? Mer information finns i vanliga frågor och svar nedan.

## <a name="general-faqs"></a>Vanliga frågor och svar

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>Kan jag installera tillägget på min TFS-server (inte Azure DevOps)? 

Nej, tillägget är inte tillgängligt för nedladdning och installation av TFS.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Måste jag köra Microsofts säkerhets kod analys med min version? 

Ja och nej. Beroende på typen av analys verktyg kan själva käll koden vara det enda som krävs eller så kan resultatet av skapandet krävas. Till exempel, eftersom säkerhets kopiering av autentiseringsuppgifter analyserar filer i mappstrukturen i kod lagrings platsen, kan du köra säkerhets kopieringen av autentiseringsuppgifter och publicera säkerhets analys loggar för att skapa uppgifter i en fristående version för att hämta resultat.
För andra verktyg som analyserar build-artefakter, till exempel BinSkim, krävs versionen först.

### <a name="can-i-break-my-build-when-results-are-found"></a>Kan jag avbryta min version när det finns resultat? 
Ja, du kan införa en Bygg rast när ett verktyg rapporterar ett problem, en sökning i logg filen. Lägg bara till bygg aktiviteten efter analys och markera kryss rutan för alla verktyg som du vill bryta skapandet för. Du kan välja att avbryta versionen när ett verktyg rapporterar fel eller varningar och fel i användar gränssnittet för aktiviteten efter analys.

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>Hur skiljer sig kommando rads argumenten åt i Azure DevOps än i de fristående Skriv bords verktygen? 

För det mesta är Azure DevOps build-uppgifter direkt omslutna omslutningar i kommando rads argumenten för säkerhets verktygen. Allt som du normalt skickar till verktyget på kommando raden från Skriv bordet kan du skicka till argumenten för build-uppgiften.
Här är en lista över märkbara skillnader:
 - Verktyget kommer att köras från källmappen i agenten $ (build. SourcesDirectory) eller% BUILD_SOURCESDIRECTORY%. Exempel: C:\Agent\_work\1\s 
 - Sökvägar i argumenten kan vara relativa till roten i käll katalogen som anges ovan eller absolut antingen genom att köra en lokal agent med kända distributions platser för lokala resurser eller med hjälp av Azure DevOps build-variabler
 - Verktyg tillhandahåller automatiskt en sökväg till utdatafilen eller mappen om en sökväg för utdata anges, tas den bort och ersätts med en sökväg till vår välkända inloggnings plats på Build-agenten
 - Vissa ytterligare kommando rads parametrar är sanerade och tas bort i vissa verktyg, till exempel tillägg eller borttagning av alternativ för att säkerställa att inga användar gränssnitt startas.

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Kan jag köra en build-uppgift (t. ex. en certifierings skanner) över flera databaser i en Azure DevOps-version? 

Nej, det finns för närvarande inte stöd för att köra säkra utvecklings verktyg mot flera databaser i en enda pipeline.

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>Den utdatafil som jag har angett har inte skapats/jag kan inte hitta den utdatafil som jag har angett 

Bygg aktiviteterna är för närvarande sanerade användarindata och uppdaterar platsen för utdatafilen som genereras till en gemensam plats på Build-agenten. Mer information om den här platsen finns i följande frågor.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Var sparas utdatafilerna som genereras av verktygen? 

Bygg uppgifter lägger automatiskt till sökvägar till följande välkända plats i versions agenten $ (agent. BuildDirectory)\_sdt\logs. Genom att standardisera den här platsen kan vi garantera att andra team som skapar kod analys loggar eller använder dem kommer att ha åtkomst.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Kan jag köa en build för att köra dessa uppgifter på en värdbaserad build-agent? 

Ja, alla aktiviteter och verktyg i tillägget kan köras på en värdbaserad versions agent.

>[!NOTE]
> En version av program mot skadlig kod kräver en build-agent med Windows Defender aktiverat, vilket är sant på "värdbaserade VS2017" eller senare build-agenter. (Den kommer inte att köras på den äldre/VS2015 "värdbaserade agenten".) Det går inte att uppdatera signaturer för de här agenterna, men signaturen ska alltid vara relativt aktuell, mindre än tre timmar gamla.
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Kan jag köra dessa build-uppgifter som en del av en versions pipeline (i stället för en build-pipeline)? 
I de flesta fall är ja. Aktiviteter som publicerar artefakter stöds dock inte av Azure-DevOps för att köras inifrån release-pipelines: "Den enda aktivitets kategori som inte förväntas arbeta med versionen är de som publicerar artefakter. Det beror på att vi inte har stöd för att publicera artefakter inom versionen, från och med nu.
Detta förhindrar att aktiviteten "publicera säkerhets analys loggar" körs från en versions pipeline. Det kommer inte att fungera med ett beskrivande fel meddelande.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Varifrån kan du hämta verktygen med Bygg aktiviteterna? 
Build tasks a) Hämta NuGet-paket för verktygen från följande [paket hanterings flöde i Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json) eller med hjälp av Node Pack Manager, som måste vara förinstallerat på Build-agenten (exempel: "NPM install tslint").

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>Vilken funktion kommer att installera tillägget på min Azure DevOps-organisation? 

Vid installationen blir säkerhets build-uppgifter som tillhandahålls av tillägget tillgängliga för användning av alla användare i din organisation. När du skapar eller redigerar en Azure-pipeline är dessa uppgifter tillgängliga för att läggas till från listan skapa uppgifts samling. Annars påverkas inte installationen av tillägget i din Azure DevOps-organisation. Det ändrar inte några konto-eller projekt inställningar eller pipeliner.

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>Kommer installationen av tillägget att ändra mina befintliga Azure-pipeliner? 

Nej. Om du installerar tillägget blir säkerhets versions uppgifterna tillgängliga för att läggas till i dina Azure-pipeliner. Användare krävs fortfarande för att lägga till eller uppdatera Bygg definitioner för att integrera verktygen i bygg processen.

## <a name="task-specific-faqs"></a>Vanliga frågor och svar

Vanliga frågor och svar som är specifika för att bygga uppgifter visas i det här avsnittet.

### <a name="credential-scanner-faqs"></a>Vanliga frågor och svar om autentiseringsuppgifter

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>Vad är vanliga undervisnings scenarier och exempel? 
Två av de vanligaste undervisnings scenarierna beskrivs nedan:
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Ignorera alla förekomster av en viss hemlighet inom den angivna sökvägen 
Hash-nyckeln för hemligheten från utdatafilen för autentiseringsuppgifter måste anges i exemplet nedan
   
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
> Hash-nyckeln genereras av en del av matchning svärdet eller fil innehållet. All käll kods revision kan ändra hash-nyckeln och inaktivera regeln för att utelämna. 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>För att utelämna alla hemligheter i en angiven fil (eller för att förhindra själva hemligheter filen) 
Fil uttrycket kan vara ett fil namn eller en postfix-del av filens fullständiga sökväg/namn. Jokertecken stöds inte. 

**Exempel** 

Fil som ska undertryckas: [InputPath] \src\JS\lib\angular.js 

Giltiga undertrycks regler: 
- [InputPath] \src\JS\lib\angular.js--undertrycker inte filen på den angivna sökvägen
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- vinkel. js--ignorera alla filer med samma namn

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
> Alla framtida hemligheter som läggs till i filen ignoreras också automatiskt. 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>Vad är rekommendationer för hemligheter för hemligheter? 
Vid identifiering av hårdkodade hemligheter inom rimlig tid och för att minska riskerna är det till och med bättre om en kan hindra hemligheter från att bli incheckat helt och hållet. I detta hänseende har Microsoft släppt CredScan Code Analyzer som en del av [Microsoft DevLabs-tillägget](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) för Visual Studio. I tidiga förhands granskning ger utvecklare en infogad upplevelse för att identifiera potentiella hemligheter i sin kod, vilket ger dem möjlighet att åtgärda problemen i real tid. Mer information finns i [den här](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/) bloggen om hur du hanterar hemligheter på ett säkert sätt i molnet. Nedan finns ytterligare resurser som hjälper dig att hantera hemligheter och komma åt känslig information i dina program på ett säkert sätt: 
 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD-Hanterad tjänstidentitet](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Hanterad tjänstidentitet (MSI) för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure-Hanterad tjänstidentitet](../../app-service/overview-managed-identity.md)
 - [AppAuthentication-bibliotek](../../key-vault/service-to-service-authentication.md)

#### <a name="can-i-write-my-own-custom-searchers"></a>Kan jag skriva egna anpassade sökre?

Genomsökning av autentiseringsuppgifter förlitar sig på en uppsättning innehålls Sök vägar som är definierade i filen **buildsearchers. XML** . Filen innehåller en matris med serialiserade XML-objekt som representerar ett ContentSearcher-objekt. Programmet distribueras med en uppsättning sökverktyg som har testats väl, men du kan även implementera dina egna anpassade sökverktyg. 

En innehålls sökre definieras enligt följande: 

- **Namn** – namnet på den beskrivande sökaren som ska användas i utdatafilen för en fil för autentiseringsuppgifter. Vi rekommenderar att du använder kamel notation ärende namn konvention för Sök namn. 
- **RuleID** – det stabila täckande ID: t för Sök funktionen. 
    - Standard sökrna för autentiseringsuppgifter för autentiseringsuppgifter tilldelas till RuleIds som CSCAN0010, CSCAN0020, CSCAN0030 osv. Den sista siffran är reserverad för potentiella sökmotors regex grupp sammanfogning eller division.
    - RuleId för anpassade sökprogram måste ha ett eget namn område i formatet: CSCAN-{namespace} 0010, CSCAN-{namnrymd} 0020, CSCAN-{namespace} 0030 osv.
    - Det fullständigt kvalificerade Sök förnamnet är kombinationen av RuleId och Sök funktionens namn. Exempel CSCAN0010. KeyStoreFiles, CSCAN0020. Base64EncodedCertificate osv.
- **ResourceMatchPattern** – regex av fil namns tillägg för att kontrol lera mot Sök funktionen
- **ContentSearchPatterns** – matris med strängar som innehåller regex-uttryck som ska matchas. Om inga Sök mönster har definierats returneras alla filer som matchar resurs matchnings mönstret.
- **ContentSearchFilters** – matris med strängar som innehåller regex-uttryck för att filtrera sökverktyget som är beroende av en speciell falsk identifiering.
- **Matchdetails** – ett beskrivande meddelande och/eller minsknings instruktioner som ska läggas till för varje matchning av Sök funktionen.
- **Rekommendation** – ger förslag på fält innehåll för en matchning med hjälp av ett för snabb rapport format.
- **Allvarlighets grad** – ett heltal som återspeglar problemets allvarlighets grad (högst 1).
![Installations program för autentiseringsuppgifter](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Vanliga frågor och svar om Roslyn-analys

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Vilka är de vanligaste felen när du använder Roslyn för analys av uppgifter?

**Fel: Projektet har återställts med Microsoft. NetCore. app version x. x, men med de aktuella inställningarna används version y. y. y i stället. Lös problemet genom att kontrol lera att samma inställningar används för återställning och för efterföljande åtgärder, till exempel build eller Publish. Det här problemet kan vanligt vis uppstå om egenskapen RuntimeIdentifier anges under build eller Publish men inte under återställningen:**

Roslyn-analyserare körs som en del av kompileringen, så käll trädet på Build-datorn måste vara i ett build-tillstånd. Ett steg (troligen "dotNet. exe Publishing") mellan dina huvudsakliga build-och Roslyn-analyser kan ha gjort käll trädet i ett tillstånd där det inte går att skapa. Kanske att duplicera steget som gör en NuGet återställning, precis innan steget Roslyn analys verktyg, kommer käll trädet att återställas i ett buildable-tillstånd.

**"CSC. exe" avslutades med felkod 1 – det går inte att skapa en instans av Analyzer AAAA från C:\BBBB.dll: Det gick inte att läsa in filen eller sammansättningen "Microsoft. CodeAnalysis, version = X. x. x, Culture = neutral, PublicKeyToken = 31bf3856ad364e35" eller något av dess beroenden. Det går inte att hitta den angivna filen i systemet.**

Se till att din kompilator stöder Roslyn-analyser. "CSC. exe/version" ska rapportera minst v 2.6. x. I vissa fall kan enskilda. CSPROJ-filer åsidosätta build-datorns Visual Studio-installation genom att referera till ett paket från Microsoft.Net. compilers. Om du inte avsåg att använda en angiven version av kompileraren, tar du bort referenser till Microsoft.Net. compilers. Annars kontrollerar du att det refererade paketet också är minst v 2.6. x. Försök att hämta fel loggen, som du hittar i parametern/Errorlog: från CSC. exe-kommandoraden (finns i Roslyn-build-uppgiftens logg). Det kan se ut ungefär så här:/Errorlog:\_f:\ts-Services-123 work\456\s\Some\Project\Code\Code.CSPROJ.sarif

**C# Kompilatorn är inte tillräckligt aktuell (den måste vara > = 2,6)**

De senaste versionerna av C# kompilatorn publiceras här:. https://www.nuget.org/packages/Microsoft.Net.Compilers För att hämta den installerade versionen använder du kör `C:\>csc.exe /version` från kommando tolken. Se till att du inte har någon referens till ett Microsoft.Net. compilers NuGet-paket som är < v 2.6.

**MSBuild/VSBuild-loggar hittades inte**

På grund av hur uppgiften fungerar måste den här uppgiften fråga Azure-DevOps efter MSBuild-loggen från MSBuild-build-aktiviteten. Om den här aktiviteten körs omedelbart efter MSBuild-build-aktiviteten, kommer loggen ännu inte att vara tillgänglig. Placera andra build-uppgifter, inklusive SecDevTools build-uppgifter, t. ex. Binskim, virus kontroll av skadlig kod och andra), mellan MSBuild-build-aktiviteten och Roslyn för analys verktyg. 

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare hjälp finns Microsoft Security code Analysis support måndag till fredag från 9:00 AM-5:00 PM Stilla havs tid, normal tid

  - Onboarding – kontakta teknik konto ansvariga för att komma igång. 
  >[!NOTE] 
  >Om du inte redan har en avgiftsbelagd support-relation med Microsoft eller om du har ett support erbjudande som inte gör det möjligt att köpa tjänster från Phoenix-katalogen går du till [Start sidan för Support tjänster](https://www.microsoft.com/enterprise/services/support) för mer information.

  - Support – e-posta vårt team på [Microsoft Security code Analysis support](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)