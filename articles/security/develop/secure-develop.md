---
title: Utveckla säkra program på Microsoft Azure
description: I den här artikeln beskrivs metodtips som ska beaktas under implementerings- och verifieringsfaserna av webbprogrammet.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 9d98660230e0ab9f4edcd9a7af8a3797106dd17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255650"
---
# <a name="develop-secure-applications-on-azure"></a>Utveckla säkra program på Azure
I den här artikeln presenterar vi säkerhetsaktiviteter och kontroller att tänka på när du utvecklar program för molnet. Säkerhetsfrågor och begrepp att tänka på under implementerings- och verifieringsfaserna av Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) behandlas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att utveckla ett säkrare program.

Följande SDL-faser behandlas i den här artikeln:

- Implementering
- Verifiering

## <a name="implementation"></a>Implementering
Fokus för genomförandefasen är att fastställa bästa praxis för tidigt förebyggande och att upptäcka och ta bort säkerhetsproblem från koden.
Anta att ditt program kommer att användas på ett sätt som du inte hade för avsikt att använda. Detta hjälper dig att skydda dig mot oavsiktligt eller avsiktligt missbruk av ditt program.

### <a name="perform-code-reviews"></a>Utföra kodgranskningar

Innan du checkar in kod, genomföra [kod recensioner](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) för att öka den totala koden kvalitet och minska risken för att skapa buggar. Du kan använda [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) för att hantera kodgranskningsprocessen.

### <a name="perform-static-code-analysis"></a>Utföra analys av statisk kod

[Statisk kodanalys](https://www.owasp.org/index.php/Static_Code_Analysis) (kallas även *källkodsanalys)* utförs vanligtvis som en del av en kodgranskning. Statisk kodanalys refererar ofta till att köra statiska kodanalysverktyg för att hitta potentiella sårbarheter i kod som inte körs med hjälp av tekniker som [färgkontroll](https://en.wikipedia.org/wiki/Taint_checking) och [dataflödesanalys](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace erbjuder [utvecklarverktyg](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) som utför statisk kodanalys och hjälper till med kodgranskningar.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Validera och sanera alla indata för ditt program

Behandla alla indata som ej betrodda för att skydda ditt program från de vanligaste sårbarheterna för webbprogram. Ej betrodda data är ett fordon för injektionsattacker. Indata för ditt program innehåller parametrar i URL: en, indata från användaren, data från databasen eller från ett API, och allt som skickas i att en användare potentiellt kan manipulera. Ett program bör [validera](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) att data är syntaktiskt och semantiskt giltiga innan programmet använder data på något sätt (inklusive att visa dem tillbaka för användaren).

Validera indata tidigt i dataflödet för att säkerställa att endast korrekt utformade data kommer in i arbetsflödet. Du vill inte att felaktiga data ska finnas kvar i databasen eller utlösa ett fel i en underordnad komponent.

Svartlistning och vitlistning är två allmänna metoder för att utföra validering av indatasyntax:

  - Svartlistning försöker kontrollera att en viss användarinmatning inte innehåller "känt för att vara skadligt" innehåll.

  - Vitlistning försöker kontrollera att en viss användarindata matchar en uppsättning "kända bra" ingångar. Teckenbaserad vitlistning är en form av vitlistning där ett program kontrollerar att användarindata endast innehåller "kända bra" tecken eller att indata matchar ett känt format.
    Det kan till exempel handla om att kontrollera att ett användarnamn bara innehåller alfanumeriska tecken eller att det innehåller exakt två nummer.

Vitlistning är det bästa sättet att bygga säker programvara.
Svartlistning är benägna att fel eftersom det är omöjligt att tänka sig en komplett lista över potentiellt dålig input.

Gör detta arbete på servern, inte på klientsidan (eller på servern och på klientsidan).

### <a name="verify-your-applications-outputs"></a>Verifiera programmets utdata

Alla utdata som du presenterar antingen visuellt eller i ett dokument ska alltid kodas och komma ifråns. [Att fly](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), även känd som *utdatakodning,* används för att säkerställa att ej betrodda data inte är ett fordon för en injektionsattack. Att fly, i kombination med datavalidering, ger skiktat försvar för att öka säkerheten för systemet som helhet.

Om du flyr ser du till att allt visas som *utdata.* Om du flyr kan tolken också veta att data inte är avsedda att köras, vilket förhindrar att attacker fungerar. Detta är en annan vanlig attack teknik som kallas *cross-site scripting* (XSS).

Om du använder en webbramverk från en tredje part, kan du kontrollera dina alternativ för utdata kodning på webbplatser med hjälp av [OWASP XSS förebyggande lathund](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Använda parameteriserade frågor när du kontaktar databasen

Skapa aldrig en infogad databasfråga "i farten" i koden och skicka den direkt till databasen. Skadlig kod som infogas i programmet kan leda till att databasen blir stulen, rensad eller ändrad. Programmet kan också användas för att köra skadliga operativsystemkommandon i operativsystemet som är värd för databasen.

Använd i stället parameteriserade frågor eller lagrade procedurer. När du använder parameteriserade frågor kan du anropa proceduren från koden på ett säkert sätt och skicka den till en sträng utan att oroa dig för att den ska behandlas som en del av frågesatsen.

### <a name="remove-standard-server-headers"></a>Ta bort standardserverhuvuden

Rubriker som Server, X-Powered-By och X-AspNet-Version avslöjar information om servern och underliggande teknik. Vi rekommenderar att du undertrycker dessa rubriker för att undvika att programmet tas fingeravtryck.
Se [Ta bort standardserverhuvuden på Azure-webbplatser](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Segregera dina produktionsdata

Dina produktionsdata, eller "riktiga" data, bör inte användas för utveckling, testning eller något annat syfte än vad företaget avsett. En maskerad ([anonymiserad](https://en.wikipedia.org/wiki/Data_anonymization)) datauppsättning bör användas för all utveckling och testning.

Det innebär att färre personer har åtkomst till dina verkliga data, vilket minskar angreppsytan. Det innebär också färre anställda ser personuppgifter, vilket eliminerar en potentiell överträdelse av sekretessen.

### <a name="implement-a-strong-password-policy"></a>Implementera en stark lösenordsprincip

Om du vill försvara dig mot brute-force och ordlistebaserad gissning måste du implementera en stark lösenordsprincip för att säkerställa att användarna skapar ett komplext lösenord (till exempel 12 tecken minsta längd och kräver alfanumeriska och specialtecken).

Du kan använda ett identitetsramverk för att skapa och framtvinga lösenordsprinciper. Azure AD B2C hjälper dig med lösenordshantering genom att tillhandahålla [inbyggda principer,](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow) [återställning av lösenord med självbetjäning](../../active-directory-b2c/user-flow-self-service-password-reset.md)med mera.

Om du vill skydda dig mot angrepp på standardkonton kontrollerar du att alla nycklar och lösenord är utbytbara och att de genereras eller ersätts när du har installerat resurser.

Om programmet måste generera lösenord automatiskt, se till att de genererade lösenorden är slumpmässiga och att de har hög entropy.

### <a name="validate-file-uploads"></a>Validera filuppladdningar

Om ditt program tillåter [filuppladdningar](https://www.owasp.org/index.php/Unrestricted_File_Upload)bör du överväga försiktighetsåtgärder som du kan vidta för den här riskfyllda aktiviteten. Det första steget i många attacker är att få lite skadlig kod i ett system som är under attack. Med hjälp av en filöverföring hjälper angriparen att åstadkomma detta. OWASP erbjuder lösningar för att validera en fil för att säkerställa att filen du laddar upp är säker.

Skydd mot skadlig kod hjälper till att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Du kan installera [Microsoft Antimalware](../fundamentals/antimalware.md) eller en Microsoft-partners slutpunktsskyddslösning ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)och [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](../fundamentals/antimalware.md) innehåller funktioner som realtidsskydd, schemalagd skanning, reparation av skadlig kod, signaturuppdateringar, motoruppdateringar, exempelrapportering och händelseinsamling för uteslutning. Du kan integrera Microsoft Antimalware och partnerlösningar med [Azure Security Center](../../security-center/security-center-partner-integration.md) för enkel distribution och inbyggda identifieringar (aviseringar och incidenter).

### <a name="dont-cache-sensitive-content"></a>Cachelagra inte känsligt innehåll

Cachelagra inte känsligt innehåll i webbläsaren. Webbläsare kan lagra information för cachelagring och historik. Cachelagrade filer lagras i en mapp som mappen Temporära Internet-filer, när det gäller Internet Explorer. När dessa sidor refereras till igen visar webbläsaren sidorna från cacheminnet. Om känslig information (adress, kreditkortsuppgifter, personnummer, användarnamn) visas för användaren, kan informationen lagras i webbläsarens cacheminne och hämtas genom att undersöka webbläsarens cache eller genom att helt enkelt trycka på webbläsarens **bakåtknapp.**

## <a name="verification"></a>Verifiering
Verifieringsfasen innebär en omfattande insats för att se till att koden uppfyller de grundsatser för säkerhet och integritet som fastställdes i de föregående faserna.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Hitta och åtgärda säkerhetsproblem i dina programberoenden

Du söker igenom ditt program och dess beroende bibliotek för att identifiera kända sårbara komponenter. Produkter som är tillgängliga för att utföra den här genomsökningen inkluderar [OWASP Dependency Check,](https://www.owasp.org/index.php/OWASP_Dependency_Check)[Snyk](https://snyk.io/)och [Black Duck](https://www.blackducksoftware.com/).

Sårbarhetsskanning som drivs av [Tinfoil Security](https://www.tinfoilsecurity.com/) är tillgängligt för Azure App Service Web Apps. [Tinfoil Security scanning through App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) erbjuder utvecklare och administratörer ett snabbt, integrerat och ekonomiskt sätt att upptäcka och åtgärda sårbarheter innan en skadlig aktör kan dra nytta av dem.

> [!NOTE]
> Du kan också [integrera Aluminiumfoliesäkerhet med Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). Genom att integrera Aluminiumfoliesäkerhet med Azure AD får du följande fördelar:
>  - I Azure AD kan du styra vem som har åtkomst till Aluminiumfoliesäkerhet.
>  - Dina användare kan automatiskt loggas in på Tinfoil Security (enkel inloggning) med hjälp av sina Azure AD-konton.
>  - Du kan hantera dina konton på en enda, central plats, Azure-portalen.

### <a name="test-your-application-in-an-operating-state"></a>Testa ditt program i drifttillstånd

Dynamisk programsäkerhetstestning (DAST) är en process för att testa ett program i drifttillstånd för att hitta säkerhetsproblem. DAST-verktyg analyserar program medan de körs för att hitta säkerhetsproblem som minnesskador, osäker serverkonfiguration, skript över flera webbplatser, problem med användarbehörighet, SQL-injektion och andra kritiska säkerhetsproblem.

DAST skiljer sig från statisk applikationssäkerhetstestning (SAST). SAST-verktyg analyserar källkoden eller kompilerade versioner av kod när koden inte körs för att hitta säkerhetsbrister.

Utför DAST, gärna med hjälp av en säkerhetspersonal (en [penetrationstestare](../fundamentals/pen-testing.md) eller sårbarhetsbedömare). Om en säkerhetsproffs inte är tillgänglig kan du utföra DAST själv med en webbproxyskan och viss utbildning. Koppla in en DAST-skanner tidigt för att se till att du inte introducerar uppenbara säkerhetsproblem i koden. Se [OWASP-webbplatsen](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) för en lista över sårbarhetsskannrar för webbprogram.

### <a name="perform-fuzz-testing"></a>Utföra fuzz-testning

I [fuzz-testning](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)inducerar du programfel genom att avsiktligt införa felaktiga eller slumpmässiga data till ett program. Inducera programfel hjälper till att avslöja potentiella säkerhetsproblem innan programmet släpps.

[Security Risk Detection](https://docs.microsoft.com/security-risk-detection/) är Microsofts unika fuzz-testtjänst för att hitta säkerhetskritiska fel i programvara.

### <a name="conduct-attack-surface-review"></a>Genomföra granskning av angreppsytan

Genom att granska angreppsytan efter att koden har slutförts säkerställs att alla ändringar av design eller implementering av ett program eller system har övervägts. Det bidrar till att säkerställa att alla nya attackvektorer som har skapats till följd av ändringarna, inklusive hotmodeller, har granskats och mildrats.

Du kan skapa en bild av attackytan genom att skanna programmet. Microsoft erbjuder en attack yta analysverktyg som kallas [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Du kan välja mellan många kommersiella dynamiska test- och sårbarhetsskanningsverktyg eller -tjänster, inklusive [OWASP Zed Attack Proxy Project,](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project) [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/)och [w3af](http://w3af.sourceforge.net/). Dessa skanningsverktyg genomsöker din app och kartlägger de delar av programmet som är tillgängliga via webben. Du kan också söka på Azure Marketplace efter liknande [utvecklarverktyg](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Utför säkerhetspenetrationstester

Att se till att ditt program är säkert är lika viktigt som att testa andra funktioner. Gör [penetrationstestning](../fundamentals/pen-testing.md) till en standarddel av bygg- och distributionsprocessen. Schemalägg regelbundna säkerhetstester och sårbarhetsskanning på distribuerade program och övervaka för öppna portar, slutpunkter och attacker.

### <a name="run-security-verification-tests"></a>Kör säkerhetsverifieringstester

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) innehåller SVT för flera tjänster på Azure-plattformen. Du kör dessa SVT-kort regelbundet för att säkerställa att din Azure-prenumeration och de olika resurser som ingår i ditt program är i ett säkert tillstånd. Du kan också automatisera dessa tester genom att använda funktionen för kontinuerlig integrering/kontinuerlig distribution (CI/CD) i AzSK, vilket gör SVTs tillgängliga som ett Visual Studio-tillägg.

## <a name="next-steps"></a>Nästa steg
I följande artiklar rekommenderar vi säkerhetskontroller och aktiviteter som kan hjälpa dig att utforma och distribuera säkra program.

- [Utforma säkra program](secure-design.md)
- [Distribuera säkra program](secure-deploy.md)
