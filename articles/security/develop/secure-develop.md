---
title: Utveckla säkra program i Microsoft Azure
description: Den här artikeln beskrivs bästa praxis att överväga under implementering och verifiering faserna i webbprojektet för programmet.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: bcd66d1a8077b4cc87c184f34b43cc5846a83f2f
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144430"
---
# <a name="develop-secure-applications-on-azure"></a>Utveckla säkra program i Azure
I den här artikeln visar vi säkerhetsaktiviteter och kontroller att tänka på när du utvecklar program för molnet. Säkerhetsfrågor och begrepp att överväga under faser för implementering och verifiering av Microsofts [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att utveckla en säkrare program.

Följande faser för SDL-processen beskrivs i den här artikeln:

- Implementering
- Verifiering

## <a name="implementation"></a>Implementering
Fokus i implementeringsfasen är att upprätta Metodtips för tidig skydd och för att identifiera och ta bort säkerhetsproblem från koden.
Anta att ditt program kommer att användas på ett sätt att du inte hade för avsikt att kunna användas. Detta hjälper dig att skydda mot oavsiktlig eller avsiktlig missbruk av ditt program.

### <a name="perform-code-reviews"></a>Utföra kodgranskningar

Innan du checkar in koden utför [code granskningar](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) att öka övergripande kodkvaliteten och minska risken för att skapa buggar. Du kan använda [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) att hantera granskningsprocessen kod.

### <a name="perform-static-code-analysis"></a>Utföra statiska kodanalys

[Statiska kodanalys](https://www.owasp.org/index.php/Static_Code_Analysis) (även kallat *source kodanalys*) utförs vanligtvis som en del av en Kodgranskning av. Statiska kodanalys avser vanligtvis köra statisk kod analysverktyg för att hitta potentiella säkerhetsproblem i icke-igång-kod med hjälp av teknik som [förorena kontrollerar](https://en.wikipedia.org/wiki/Taint_checking) och [dataflöde analysis](https://en.wikipedia.org/wiki/Data-flow_analysis).

Azure Marketplace erbjuder [utvecklarverktyg](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) som utför statiska kodanalys och hjälpa till med kodgranskningar.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Kontrollera och rensa alla indata för ditt program

Hantera alla indata som inte är betrodd att skydda dina program mot de vanligaste säkerhetsrisker för programmet. Ej betrodda data är ett fordon för-inmatningsattacker. Indata för ditt program innehåller parametrar i Webbadressen indata från användare, data från databasen eller från ett API, och allt som skickas som en användare kan potentiellt manipulera. Ett program bör [Validera](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) att data är syntaktiskt och semantiskt giltig innan programmet använder data på något sätt (inklusive visas för användaren).

Verifiera indata tidigt i dataflödet att se till att endast korrekt strukturerad data kommer in i arbetsflödet. Vill du inte vissa data består i din databas eller utlösa ett fel i en underordnad komponent.

Blacklisting och listan över tillåtna program finns två sätt att utföra syntaxverifiering av indata:

  - Godkänna försöker kontrollera att en viss användarindata inte innehåller ”kända är skadliga” innehåll.

  - Listan över tillåtna program försöker att kontrollera att en viss användarindata matchar en uppsättning ”kända bra” indata. Teckenbaserade listan över tillåtna program är en typ av listan över tillåtna program där ett program kontrollerar att indata från användaren innehåller endast ”kända bra” tecken eller att indata matchar ett känt format.
    Detta kan till exempel handla om att ett användarnamn innehåller endast alfanumeriska tecken och att den innehåller exakt två tal.

Listan över tillåtna program är det primära tillvägagångssättet för att skapa säkra program.
Godkänna är felbenägna eftersom det är omöjligt att tänka på en fullständig lista över potentiellt felaktiga indata.

Utför den här arbete på servern, inte på klientsidan (eller på servern och på klientsidan).

### <a name="verify-your-applications-outputs"></a>Kontrollera programmets utdata

Inga utdata som du presenterar visuellt eller i ett dokument ska alltid kodade och undantaget. [Undantagstecken](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), även kallat *utdatakodning*, används för att säkerställa att obetrodda data inte är ett fordon för en-angrepp. Undantagstecken, i kombination med dataverifiering, ger skydd i flera lager för att öka säkerheten för systemet som helhet.

Undantagstecken gör till att allt visas som *utdata.* Undantagstecken kan även tolk veta att data inte är avsedd att köras och detta gör att attacker inte fungerar. Det här är en annan vanlig attack metod som kallas *cross site scripting* (XSS).

Om du använder ett webbramverk från en tredje part, kan du kontrollera dina alternativ för utdata encoding på webbplatser med hjälp av den [OWASP XSS dataförlustskydd facit](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Använda frågor med parametrar när du kontaktar databasen

Skapa aldrig en intern databasfråga ”i farten” i din kod och skicka den direkt till databasen. Skadlig kod som infogats i ditt program kan eventuellt medföra att din databas blir stulen, rensas eller ändras. Ditt program kan också användas för att köra skadliga operativsystemkommandon på det operativsystem som är värd för databasen.

Använd i stället parameteriserade frågor eller lagrade procedurer. När du använder frågor med parametrar, kan du anropa proceduren från din kod på ett säkert sätt och skicka dem till en sträng utan att det kommer att behandlas som en del av frågeuttrycket.

### <a name="remove-standard-server-headers"></a>Ta bort standardserver rubriker

Rubriker som Server X-drivna-av, och X-AspNet-Version avslöja information om server och underliggande teknikerna. Vi rekommenderar att du ignorera dessa rubriker för att undvika fingeravtryck programmet.
Se [tar bort standardserver huvuden på Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Särskilja produktionsdata

Din produktion eller ”verklig” data bör inte användas för utveckling, testning eller något annat syfte än vad företaget avsett. Ett maskerat ([anonymiserad](https://en.wikipedia.org/wiki/Data_anonymization)) datauppsättningen ska användas för alla utveckling och testning.

Det innebär att färre personer har tillgång till dina verkliga data, vilket minskar risken för angrepp. Det innebär också färre anställda finns i personliga data, vilket eliminerar potentiella intrång i sekretess.

### <a name="implement-a-strong-password-policy"></a>Implementera en princip för starkt lösenord

För att skydda mot brute force- och ordlista-baserade gissa, måste du implementera en princip för starkt lösenord för att säkerställa att användarna skapar ett komplext lösenord (till exempel 12 tecken minimilängd och kräver alfanumeriska tecken och specialtecken).

Du kan använda ett ramverk för identitet för att skapa och genomdriva lösenordsprinciper. Azure AD B2C hjälper dig med lösenordshantering genom att tillhandahålla [inbyggda principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-password-reset-policy), [lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr), med mera.

Kontrollera att alla nycklar och lösenord är utbytbara och att de skapats eller ersättas när du har installerat resurser för att skydda mot attacker på standardkonton.

Om programmet måste Autogenerera lösenord, kontrollera att de genererade lösenord är slumpmässig och att de har hög entropi.

### <a name="validate-file-uploads"></a>Verifiera filöverföringar

Om programmet medger [filen uppladdningar](https://www.owasp.org/index.php/Unrestricted_File_Upload), Överväg att vissa åtgärder som du kan vidta för den här riskfyllda aktiviteten. Det första steget i många attacker är att få skadlig kod i ett system som är utsatt för en attack. Med hjälp av en uppladdning kan angriparen åstadkomma detta. OWASP erbjuder lösningar för att verifiera en fil för att säkerställa att den fil som du laddar upp är säkra.

Skydd mot skadlig kod kan du identifiera och ta bort virus, spionprogram och annan skadlig programvara. Du kan installera [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) eller en Microsoft-partner endpoint protection-lösning ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10), och [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)).

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) innehåller funktioner som realtidsskydd, schemalagd genomsökning, korrigering av skadlig kod, Signaturuppdateringar, uppdateringar, exempel reporting och insamling av undantag. Du kan integrera lösningar från Microsoft Antimalware och partner med [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) för enkel distribution och inbyggda identifieringar (aviseringar och incidenter).

### <a name="dont-cache-sensitive-content"></a>Inte cachelagra känsligt innehåll

Inte cachelagra känsligt innehåll i webbläsare. Webbläsare kan lagra information för cachelagring och historik. Cachelagrade filer lagras i en mapp som mappen Tillfälliga Internetfiler för Internet Explorer. När dessa sidor kallas igen och webbläsaren visar sidor från sitt cacheminne. Om känslig information (adress, kreditkort, personnummer, användarnamn) visas för användaren, informationen kan lagras i webbläsarens cacheminne och längre hämtningsbart genom att undersöka webbläsarens cache eller bara trycka på webbläsarens  **Tillbaka** knappen.

## <a name="verification"></a>Verifiering
Verifiering fasen för migreringsplanering innebär en omfattande arbete för att se till att koden uppfyller säkerhet och sekretess tenets som fastställdes i föregående faser.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Hitta och åtgärda säkerhetsrisker i dina programberoenden

Du skanna ditt program och dess beroende bibliotek för att identifiera några kända sårbara komponenter. Produkter som är tillgängliga för att utföra den här genomsökningen inkluderar [OWASP beroende Kontrollera](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/), och [svart ankor](https://www.blackducksoftware.com/).

Säkerhetsriskssökning drivs av [Tinfoil Security](https://www.tinfoilsecurity.com/) är tillgänglig för Azure App Service Web Apps. [Tinfoil Security scanning via App Service](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) erbjuder utvecklare och administratörer en snabb, integrerad och ekonomiska möjlighet att upptäcka och åtgärda säkerhetsrisker innan en skadlig aktören kan dra nytta av dem.

> [!NOTE]
> Du kan också [integrera Tinfoil Security med Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial). Integrera Tinfoil Security med Azure AD ger dig följande fördelar:
>  - Du kan styra vem som har åtkomst till Tinfoil Security i Azure AD.
>  - Användarna kan vara loggas in automatiskt till Tinfoil Security (enkel inloggning) med hjälp av sina Azure AD-konton.
>  - Du kan hantera dina konton på en gemensam, central plats, Azure-portalen.

### <a name="test-your-application-in-an-operating-state"></a>Testa ditt program med tillståndet operativsystem

Dynamisk säkerhet Programtestning (DAST) är en process som testar ett program i ett tillstånd som operativsystem för att hitta säkerhetsproblem. DAST verktyg analysera program medan de körs för att hitta säkerhetsproblem, till exempel minnet skadas, osäkert serverkonfiguration, cross site scripting, användaren behörighet problem, SQL-inmatning och andra viktiga säkerhetsproblem.

DAST skiljer sig från statisk säkerhet för testning (SAST). SAST verktyg analysera källkoden eller kompilerade versioner av kod när koden inte körs för att identifiera säkerhetsbrister.

Utföra DAST, helst med hjälp av en säkerhetsproffs (en [penetrationstester tester](https://docs.microsoft.com/azure/security/azure-security-pen-testing) eller säkerhetsproblem assessor). Om en säkerhetsproffs inte är tillgänglig, kan du utföra DAST själv med en web proxy skanner och utbildning. Anslut en DAST skanner tidigt för att se till att du inte orsakar uppenbara säkerhetsproblem i din kod. Se den [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) platsen för en lista över web application sårbarhet för skannrar.

### <a name="perform-fuzz-testing"></a>Testa fuzz

I [fuzz testning](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), du orsaka fel i programmet genom att introducera avsiktligt fel format eller slumpmässiga data till ett program. Att programmet upphör att fungera hjälper till att avslöja potentiella säkerhetsproblem innan programmet har släppts.

[Identifiering av säkerhetsrisk](https://docs.microsoft.com/security-risk-detection/) är den unika Microsoft-fuzz testa tjänsten för att hitta säkerhetskritiska buggar i program.

### <a name="conduct-attack-surface-review"></a>Genomför attack surface granskning

Granska risken för angrepp när kodifyllning hjälper till att säkerställa att alla design eller implementering ändras till ett program eller system har bedömts vara. Det säkerställer att nya attackvektorer som skapades på grund av ändringar, inklusive hotmodeller, har granskat och undvikas.

Du kan skapa en bild av risken för angrepp genom att skanna programmet. Microsoft erbjuder en attack surface analysverktyget kallas [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Du kan välja bland många kommersiella dynamisk testning och granskar säkerhetsrisker verktyg och tjänster, inklusive [OWASP Okaliserad Attack Proxy-projekt](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](http://code.google.com/p/skipfish/), och [w3af](http://w3af.sourceforge.net/). Dessa genomsökningsverktyg crawla din app och mappa de delar av programmet som är tillgängliga via webben. Du kan också söka på Azure Marketplace efter liknande [utvecklarverktyg](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Utföra security penetrationstester

Se till att ditt program är säker är lika viktigt som testa några andra funktioner. Kontrollera [penetrationstester](https://docs.microsoft.com/azure/security/azure-security-pen-testing) en del av bygg- och distributionsprocessen. Schemalägga regelbundna säkerhetsuppdatering tester och granskar säkerhetsrisker på distribuerade program och övervaka för att öppna portar, slutpunkter och attacker.

### <a name="run-security-verification-tests"></a>Kör verifieringstest för säkerhet

[Secure DevOps Kit för Azure](https://azsk.azurewebsites.net/index.html) (AzSK) innehåller SVTs för flera tjänster i Azure-plattformen. Du kan köra dessa SVTs regelbundet för att se till att din Azure-prenumeration och de olika resurserna som utgör ditt program är i ett säkert tillstånd. Du kan även automatisera dessa tester med hjälp av funktionen tillägg kontinuerlig integrering/kontinuerlig distribution (CI/CD) för av AzSK, vilket gör SVTs tillgänglig som ett tillägg för Visual Studio.

## <a name="next-steps"></a>Nästa steg
I följande artiklar, rekommenderar vi säkerhetskontroller och aktiviteter som kan hjälpa dig att utforma och distribuera säkra program.

- [Utforma säkra program](secure-design.md)
- [Distribuera säkra program](secure-deploy.md)
