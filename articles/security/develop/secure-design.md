---
title: Utforma säkra program på Microsoft Azure
description: I den här artikeln beskrivs metodtips som ska beaktas under krav- och designfaserna i webbprogrammets projekt.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 889897cfd4dc8714ae3aea556f0924c9dbcd7825
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299422"
---
# <a name="design-secure-applications-on-azure"></a>Utforma säkra program på Azure
I den här artikeln presenterar vi säkerhetsaktiviteter och kontroller att tänka på när du utformar program för molnet. Utbildningsresurser tillsammans med säkerhetsfrågor och koncept som ska beaktas under kraven och designfaserna i [Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att utforma ett säkrare program.

Följande SDL-faser behandlas i den här artikeln:

- Utbildning
- Krav
- Design

## <a name="training"></a>Utbildning
Innan du börjar utveckla ditt molnprogram, ta dig tid att förstå säkerhet och sekretess på Azure. Genom att ta det här steget kan du minska antalet och allvarlighetsgraden för exploateringsbara sårbarheter i ditt program. Du kommer att vara mer beredd att reagera på lämpligt sätt på det ständigt föränderliga hotlandskapet.

Använd följande resurser under utbildningsfasen för att bekanta dig med De Azure-tjänster som är tillgängliga för utvecklare och med metodtips för säkerhet på Azure:

  - [Utvecklarguiden till Azure](https://azure.microsoft.com/campaigns/developer-guide/) visar hur du kommer igång med Azure. Guiden visar vilka tjänster du kan använda för att köra dina program, lagra dina data, införliva intelligens, skapa IoT-appar och distribuera dina lösningar på ett mer effektivt och säkert sätt.

  - [Komma igång-guide för Azure-utvecklare](../../guides/developer/azure-developer-guide.md) ger viktig information för utvecklare som vill komma igång med Azure-plattformen för sina utvecklingsbehov.

  - [SDK:er och verktyg](https://docs.microsoft.com/azure/index?pivot=sdkstools) beskriver de verktyg som är tillgängliga på Azure.

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) tillhandahåller verktyg för utvecklingssamarbete. Verktygen omfattar högpresterande pipelines, kostnadsfria Git-databaser, konfigurerbara Kanban-anslagstavlor och omfattande automatiserade och molnbaserade belastningstester.
    [DevOps Resource Center](https://docs.microsoft.com/azure/devops/learn/) kombinerar våra resurser för att lära sig DevOps-metoder, Git-versionskontroll, agila metoder, hur vi arbetar med DevOps på Microsoft och hur du kan bedöma din egen DevOps-utveckling.

  - [De fem viktigaste säkerhetsobjekten som du bör tänka på innan du går vidare till produktion](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca) visar hur du skyddar dina webbprogram på Azure och skyddar dina appar mot de vanligaste och farligaste webbprogramattackerna.

  - [Secure DevOps Kit för Azure](https://azsk.azurewebsites.net/index.html) är en samling skript, verktyg, tillägg och automatiseringar som tillgodoser de omfattande Azure-prenumerations- och resurssäkerhetsbehoven hos DevOps-team som använder omfattande automatisering. Secure DevOps Kit för Azure kan visa hur du smidigt kan integrera säkerheten i dina inbyggda DevOps-arbetsflöden. Satsen behandlar verktyg som säkerhetsverifieringstester (SVTs), som kan hjälpa utvecklare att skriva säker kod och testa den säkra konfigurationen av sina molnprogram i kodnings- och tidiga utvecklingsstadier.

  - [Metodtips för säkerhet för Azure-lösningar](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) ger en samling metodtips för säkerhet som du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.

## <a name="requirements"></a>Krav
Definitionsfasen för krav är ett avgörande steg för att definiera vad ditt program är och vad det ska göra när det släpps. Kravfasen är också en tid att tänka på de säkerhetskontroller som du ska bygga in i ditt program. Under den här fasen börjar du också de steg som du kommer att vidta i hela SDL för att säkerställa att du släpper och distribuerar ett säkert program.

### <a name="consider-security-and-privacy-issues"></a>Överväg säkerhets- och sekretessfrågor
Den här fasen är den bästa tiden att överväga grundläggande säkerhets- och integritetsfrågor. Genom att definiera godtagbara säkerhets- och sekretessnivåer i början av ett projekt får ett team:

- Förstå risker i samband med säkerhetsproblem.
- Identifiera och åtgärda säkerhetsfel under utveckling.
- Tillämpa etablerade nivåer av säkerhet och integritet under hela projektet.

När du skriver kraven för ditt program bör du tänka på säkerhetskontroller som kan skydda ditt program och dina data.

### <a name="ask-security-questions"></a>Ställ säkerhetsfrågor
Ställ säkerhetsfrågor som:

  - Innehåller mitt program känsliga data?

  - Har min ansökan samla in eller lagra data som kräver att jag följer branschstandarder och program efterlevnad som [Federal Financial Institution Examination Council (FFIEC)](../blueprints/ffiec-analytics-overview.md) eller Betalkort Industry Data Security Standards [(PCI DSS)](../blueprints/pcidss-analytics-overview.md)?

  - Samlar min ansökan in eller innehåller känsliga personuppgifter eller kunddata som kan användas, antingen på egen hand eller med annan information, för att identifiera, kontakta eller hitta en enda person?

  - Samlar min ansökan in eller innehåller data som kan användas för att få tillgång till en persons medicinska, utbildnings-, ekonomi- eller anställningsinformation? Genom att identifiera dina datas känslighet under behovsfasen kan du klassificera dina data och identifiera den dataskyddsmetod som du ska använda för ditt program.

  - Var och hur lagras mina data? Fundera över hur du ska övervaka de lagringstjänster som programmet använder för oväntade ändringar (till exempel långsammare svarstider). Kommer du att kunna påverka loggning för att samla in mer detaljerade data och analysera ett problem på djupet?

  - Kommer min ansökan att vara tillgänglig för allmänheten (endast på internet) eller internt? Om din ansökan är tillgänglig för allmänheten, hur skyddar du de data som kan samlas in från att användas på fel sätt? Om ditt program endast är tillgängligt internt bör du tänka på vem i organisationen som ska ha åtkomst till programmet och hur länge de ska ha åtkomst.

  - Förstår du din identitetsmodell innan du börjar utforma ditt program? Hur kommer du att avgöra att användarna är de de säger att de är och vad en användare är behörig att göra?

  - Utför min ansökan känsliga eller viktiga uppgifter (som att överföra pengar, låsa upp dörrar eller leverera medicin)?
    Fundera över hur du validerar att användaren som utför en känslig uppgift har behörighet att utföra uppgiften och hur du ska autentisera att personen är den personen är den personen säger att de är. Authz (AuthorizationZ) är handlingen att bevilja ett autentiserat säkerhetstillstånd för att göra något. Autentisering (AuthN) är handlingen att utmana en part för legitima autentiseringsuppgifter.

  - Utför mitt program några riskfyllda programvaruaktiviteter, som att tillåta användare att ladda upp eller ladda ned filer eller andra data? Om ditt program utför riskfyllda aktiviteter bör du överväga hur programmet skyddar användare från att hantera skadliga filer eller data.

### <a name="review-owasp-top-10"></a>Granska OWASP topp 10
Överväg att granska [<span class="underline">OWASP Top 10 Application Security Risks</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).
OWASP Top 10 tar itu med kritiska säkerhetsrisker för webbprogram.
Medvetenhet om dessa säkerhetsrisker kan hjälpa dig att fatta krav och designbeslut som minimerar dessa risker i ditt program.

Det är viktigt att tänka på säkerhetskontroller för att förhindra överträdelser.
Men du vill också anta att [ett brott](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops) kommer att inträffa. Förutsatt att ett brott hjälper till att svara på några viktiga frågor om säkerhet i förväg, så att de inte behöver besvaras i en nödsituation:

  - Hur kommer jag att upptäcka en attack?

  - Vad ska jag göra om det sker en attack eller ett brott?

  - Hur ska jag kunna återhämta mig från attacken som data som läcker eller manipulerar?

## <a name="design"></a>Design

Designfasen är avgörande för att fastställa bästa praxis för design och funktionella specifikationer. Det är också viktigt för att utföra riskanalys som hjälper till att minska säkerhets- och integritetsfrågor i hela ett projekt.

När du har säkerhetskrav på plats och använder säkra designkoncept kan du undvika eller minimera möjligheterna till ett säkerhetsfel. Ett säkerhetsfel är ett förbiseende i utformningen av programmet som kan tillåta en användare att utföra skadliga eller oväntade åtgärder när ditt program har släppts.

Under designfasen, också tänka på hur du kan tillämpa säkerhet i lager; en nivå av försvar är inte nödvändigtvis tillräckligt. Vad händer om en angripare kommer förbi brandväggen för webbprogrammet (WAF)? Du vill ha en annan säkerhetskontroll på plats för att försvara dig mot attacken.

Med detta i åtanke diskuterar vi följande säkra designkoncept och de säkerhetskontroller du bör ta itu med när du utformar säkra program:

- Använd ett säkert kodningsbibliotek och ett programvaruramverk.
- Sök efter sårbara komponenter.
- Använd hotmodellering under programdesign.
- Minska din attack yta.
- Anta en identitetsprincip som den primära säkerhetsperimetern.
- Kräv omautentisering för viktiga transaktioner.
- Använd en nyckelhanteringslösning för att skydda nycklar, autentiseringsuppgifter och andra hemligheter.
- Skydda känsliga data.
- Genomföra felsäkra åtgärder.
- Dra nytta av fel- och undantagshantering.
- Använd loggning och avisering.

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>Använda ett säkert kodningsbibliotek och ett programvaruramverk

För utveckling, använd ett säkert kodningsbibliotek och ett programvaruramverk som har inbyggd säkerhet. Utvecklare kan använda befintliga, beprövade funktioner (kryptering, indata sanitet, utdatakodning, nycklar eller anslutningssträngar, och allt annat som skulle betraktas som en säkerhetskontroll) istället för att utveckla säkerhetskontroller från grunden. Detta hjälper till att skydda mot säkerhetsrelaterade konstruktions- och implementeringsfel.

Se till att du använder den senaste versionen av ramverket och alla säkerhetsfunktioner som är tillgängliga i ramverket. Microsoft erbjuder en omfattande [uppsättning utvecklingsverktyg](https://azure.microsoft.com/product-categories/developer-tools/) för alla utvecklare, som arbetar på alla plattformar eller språk, för att leverera molnprogram. Du kan koda med det språk du väljer genom att välja mellan olika [SDK: er](https://azure.microsoft.com/downloads/).
Du kan dra nytta av fullständiga integrerade utvecklingsmiljöer (IDEs) och redigerare som har avancerade felsökningsfunktioner och inbyggt Azure-stöd.

Microsoft erbjuder en mängd olika [språk, ramverk och verktyg](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all) som du kan använda för att utveckla program på Azure. Ett exempel är [Azure för .NET- och .NET Core-utvecklare](https://docs.microsoft.com/dotnet/azure/). För varje språk och ramverk som vi erbjuder hittar du snabbstarter, självstudier och API-referenser som hjälper dig att komma igång snabbt.

Azure erbjuder en mängd olika tjänster som du kan använda för att vara värd för webbplatser och webbprogram. Med dessa tjänster kan du utveckla på ditt favoritspråk, oavsett om det är .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python.
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) är en av dessa tjänster.

Web Apps lägger till kraften i Microsoft Azure i ditt program. Den innehåller säkerhet, belastningsutjämning, automatisk skalning och automatiserad hantering. Du kan också dra nytta av DevOps-funktioner i Webbappar, till exempel pakethantering, mellanlagringsmiljöer, anpassade domäner, SSL/TLS-certifikat och kontinuerlig distribution från Azure DevOps, GitHub, Docker Hub och andra källor.

Azure erbjuder andra tjänster som du kan använda för att vara värd för webbplatser och webbprogram. I de flesta fall är Web Apps det bästa alternativet. Överväg Azure Service [Fabric](https://azure.microsoft.com/documentation/services/service-fabric)för en mikrotjänstarkitektur .
Om du behöver större kontroll över de virtuella datorer koden körs på kan du använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
Mer information om hur du väljer mellan dessa Azure-tjänster finns i en [jämförelse mellan Azure App Service, Virtuella datorer, Service Fabric och Cloud Services](/azure/architecture/guide/technology-choices/compute-decision-tree).

### <a name="apply-updates-to-components"></a>Tillämpa uppdateringar på komponenter

För att förhindra säkerhetsproblem bör du kontinuerligt inventera både komponenter på klientsidan och serversidan (till exempel ramverk och bibliotek) och deras beroenden för uppdateringar. Nya sårbarheter och uppdaterade programvaruversioner släpps kontinuerligt. Se till att du har en pågående plan för att övervaka, triage, och tillämpa uppdateringar eller konfigurationsändringar på bibliotek och komponenter som du använder.

Se [OWASP (Open Web Application Security Project) på sidan Öppna webbprogram Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) om [hur du använder komponenter med kända säkerhetsproblem](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) för verktygsförslag. Du kan också prenumerera på e-postaviseringar för säkerhetsproblem som är relaterade till komponenter som du använder.

### <a name="use-threat-modeling-during-application-design"></a>Använda hotmodellering under programdesign

Hotmodellering är processen att identifiera potentiella säkerhetshot mot ditt företag och program och sedan se till att lämpliga åtgärder finns på plats. SDL anger att team bör engagera sig i hotmodellering under designfasen, när det är relativt enkelt och kostnadseffektivt att lösa potentiella problem. Att använda hotmodellering i designfasen kan avsevärt minska din totala utvecklingskostnad.

För att underlätta hotmodelleringsprocessen har vi utformat [SDL Threat Modeling Tool](threat-modeling-tool.md) med icke-säkerhetsexperter i åtanke. Det här verktyget gör hotmodellering enklare för alla utvecklare genom att ge tydlig vägledning om hur du skapar och analyserar hotmodeller.

Att modellera programdesignen och räkna upp [STRIDE-hot](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) – Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service och Elevation of Privilege – över alla förtroendegränser har visat sig vara ett effektivt sätt att fånga designfel tidigt. I följande tabell visas STRIDE-hoten och några exempel på begränsningar som använder funktioner som tillhandahålls av Azure. Dessa mildrande åtgärder fungerar inte i alla situationer.

| Hot | Egenskapen Security | Potentiella azure-plattformsbegränsningar |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Förfalskning               | Autentisering        | [Kräv HTTPS-anslutningar](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio). |
| Manipulering              | Integritet             | Verifiera SSL/TLS-certifikat. Program som använder SSL/TLS måste verifiera X.509-certifikaten för de entiteter som de ansluter till. Använd Azure Key Vault-certifikat för att [hantera dina x509-certifikat](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates). |
| Avståndstagande            | Icke-avståndstagande       | Aktivera [Azure-övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).|
| Utlämnande av information | Sekretess       | Kryptera känsliga data [i](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) [vila](../fundamentals/encryption-atrest.md) och under transport . |
| Överbelastning      | Tillgänglighet          | Övervaka prestandamått för potentiella dosfördåelsevillkor. Implementera anslutningsfilter. [Azure DDoS-skydd](../../virtual-network/ddos-protection-overview.md#next-steps), kombinerat med metodtips för programdesign, ger skydd mot DDoS-attacker.|
| Behörighetshöjning | Auktorisering         | Använd Azure <span class="underline"> </span> Active Directory [Privilegierad identitetshantering](../../active-directory/privileged-identity-management/pim-configure.md).|

### <a name="reduce-your-attack-surface"></a>Minska angreppsytan

En attackyta är den totala summan av var potentiella sårbarheter kan uppstå. I det här dokumentet fokuserar vi på en applikations attackyta.
Fokus ligger på att skydda ett program från angrepp. Ett enkelt och snabbt sätt att minimera angreppsytan är att ta bort oanvända resurser och kod från ditt program. Ju mindre ditt program, desto mindre attackyta. Ta till exempel bort:

- Kod för funktioner som du inte har släppt ännu.
- Felsökning av supportkod.
- Nätverksgränssnitt och protokoll som inte används eller som har inaktuella.
- Virtuella datorer och andra resurser som du inte använder.

Att regelbundet rensa dina resurser och se till att du tar bort oanvänd kod är bra sätt att se till att det finns färre möjligheter för skadliga aktörer att attackera.

Ett mer detaljerat och djupgående sätt att minska din attackyta är att slutföra en analys av angreppsytan. En analys av angreppsytan hjälper dig att mappa de delar av ett system som måste granskas och testas för säkerhetsproblem.

Syftet med en attack yta analys är att förstå riskområden i ett program så utvecklare och specialister säkerhet är medvetna om vilka delar av programmet är öppna för angrepp. Sedan kan du hitta sätt att minimera denna potential, spåra när och hur attackytan förändras, och vad detta innebär ur ett riskperspektiv.

En analys av angreppsytan hjälper dig att identifiera:

- Funktioner och delar av systemet som du behöver granska och testa för säkerhetsproblem.
- Högriskområden med kod som kräver skydd på djupet (delar av systemet som du behöver försvara).
- När du ändrar angreppsytan och behöver uppdatera en hotbedömning.

För att minska möjligheterna för angripare att utnyttja en potentiell svag punkt eller sårbarhet måste du noggrant analysera programmets totala angreppsyta. Det omfattar också att inaktivera eller begränsa tillgången till systemtjänster, tillämpa principen om lägsta privilegium och använda skiktade försvar där det är möjligt.

Vi diskuterar [att genomföra en översyn attack yta](secure-develop.md#conduct-attack-surface-review) under verifieringsfasen av SDL.

> [!NOTE]
> **Vad är skillnaden mellan hotmodellering och analys av angreppsytan?**
Hotmodellering är processen att identifiera potentiella säkerhetshot mot ditt program och se till att lämpliga mildrande åtgärder mot hoten finns på plats. Attack yta analys identifierar högriskområden av kod som är öppna för angrepp. Det handlar om att hitta sätt att försvara högriskområden i ditt program och granska och testa dessa kodområden innan du distribuerar programmet.

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Anta en identitetsprincip som den primära säkerhetsperimetern

När du utformar molnprogram är det viktigt att utöka fokus på säkerhetsperimetern från en nätverkscentrerad metod till en identitetscentrerad metod. Historiskt sett var den primära lokala säkerhetsperimetern en organisations nätverk. De flesta lokala säkerhetsdesigner använder nätverket som den primära säkerhets pivoten. För molnprogram är du bättre betjänt av att betrakta identitet som den primära säkerhetsperimetern.

Saker du kan göra för att utveckla en identitetscentrerad metod för att utveckla webbapplikationer:

- Framtvinga multifaktorautentisering för användare.
- Använd starka autentiserings- och auktoriseringsplattformar.
- Tillämpa principen om lägsta behörighet.
- Implementera just-in-time-åtkomst.

#### <a name="enforce-multi-factor-authentication-for-users"></a>Framtvinga multifaktorautentisering för användare

Använd tvåfaktorsautentisering. Tvåfaktorsautentisering är den aktuella standarden för autentisering och auktorisering eftersom den undviker de säkerhetsbrister som är inneboende i användarnamn och lösenordstyper för autentisering. Åtkomst till Azure-hanteringsgränssnitt (Azure portal/remote PowerShell) och till kundinriktade tjänster bör utformas och konfigureras för att använda [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

#### <a name="use-strong-authentication-and-authorization-platforms"></a>Använda starka autentiserings- och auktoriseringsplattformar

Använd autentiserings- och auktoriseringsmekanismer som tillhandahålls av plattformen i stället för anpassad kod. Detta beror på att utveckla anpassad autentiseringskod kan vara benägna att fel. Kommersiell kod (till exempel från Microsoft) granskas ofta utförligt för säkerhet. [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) är Azure-lösningen för identitets- och åtkomsthantering. Dessa Azure AD-verktyg och -tjänster hjälper till med säker utveckling:

- [Azure AD-identitetsplattform (Azure AD för utvecklare)](../../active-directory/develop/about-microsoft-identity-platform.md) är en molnidentitetstjänst som utvecklare använder för att skapa appar som loggar in användare på ett säkert sätt. Azure AD hjälper utvecklare som bygger LOB-appar (single-tenant, line-of-business) och utvecklare som vill utveckla appar med flera innehavare. Förutom grundläggande inloggning kan appar som skapas med Hjälp av Azure AD anropa Microsoft-API:er och anpassade API:er som är byggda på Azure AD-plattformen. Azure AD-identitetsplattformen stöder protokoll av branschstandard som OAuth 2.0 och OpenID Connect.

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) är en identitetshanteringstjänst som du kan använda för att anpassa och styra hur kunder registrerar sig, loggar in och hanterar sina profiler när de använder dina program. Detta inkluderar program som har utvecklats för bland annat iOS, Android och .NET. Azure AD B2C aktiverar dessa åtgärder samtidigt som kundidentiteter skyddas.

#### <a name="apply-the-principle-of-least-privilege"></a>Tillämpa principen om lägsta behörighet

Begreppet [lägsta privilegium](https://en.wikipedia.org/wiki/Principle_of_least_privilege) innebär att ge användarna exakt åtkomst och kontroll de behöver för att göra sitt jobb och inget mer.

Skulle en programutvecklare behöver domänadministratörsrättigheter? Skulle en administrativ assistent behöva tillgång till administrativa kontroller på sin dator? Att utvärdera åtkomsten till programvara är inte annorlunda. Om du använder [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) för att ge användarna olika förmågor och befogenheter i ditt program, skulle du inte ge alla tillgång till allt. Genom att begränsa åtkomsten till vad som krävs för varje roll begränsar du risken för att ett säkerhetsproblem uppstår.

Se till att ditt program ger [lägsta behörighet](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) under hela åtkomstmönstren.

> [!NOTE]
>  De regler som minst privilegium måste gälla för programvaran och för de människor som skapar programvaran. Mjukvaruutvecklare kan vara en stor risk för IT-säkerhet om de får för mycket tillgång. Konsekvenserna kan bli allvarliga om en utvecklare har ont uppsåt eller får för mycket åtkomst. Vi rekommenderar att de minst förmånsreglerna tillämpas på utvecklare under hela utvecklingslivscykeln.

#### <a name="implement-just-in-time-access"></a>Implementera just-in-time-åtkomst

Implementera *just-in-time* (JIT) åtkomst för att ytterligare sänka exponeringstiden för privilegier. Använd [Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity) för att:

- Ge användarna de behörigheter de behöver bara JIT.
- Tilldela roller för en förkortad varaktighet med tillförsikt om att privilegierna återkallas automatiskt.

### <a name="require-re-authentication-for-important-transactions"></a>Kräv omautentisering för viktiga transaktioner

[Förfalskning av begäran på flera webbplatser](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (kallas även *XSRF* eller *CSRF)* är en attack mot webbbaserade appar där en skadlig webbapp påverkar interaktionen mellan en klientwebbläsare och en webbapp som litar på den webbläsaren. Förfalskningsattacker över flera webbplatser är möjliga eftersom webbläsare skickar vissa typer av autentiseringstoken automatiskt med varje begäran till en webbplats.
Denna form av utnyttjande kallas också en attack eller *sessionskörning* *med ett klick* eftersom attacken utnyttjar användarens tidigare autentiserade session.

Det bästa sättet att försvara sig mot denna typ av attack är att be användaren om något som bara användaren kan tillhandahålla innan varje viktig transaktion, till exempel ett köp, kontoinaktivering eller en lösenordsändring. Du kan be användaren att ange sitt lösenord igen, fylla i en captcha eller skicka in en hemlig token som bara användaren skulle ha. Den vanligaste metoden är den hemliga token.

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>Använd en nyckelhanteringslösning för att skydda nycklar, autentiseringsuppgifter och andra hemligheter

Att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. Det enda som är värre än att förlora dina nycklar och referenser är att ha en obehörig part få tillgång till dem. Angripare kan dra nytta av automatiserade och manuella tekniker för att hitta nycklar och hemligheter som lagras i kodarkiv som GitHub. Placera inte nycklar och hemligheter i dessa offentliga koddatabaser eller på någon annan server.

Placera alltid nycklar, certifikat, hemligheter och anslutningssträngar i en viktig hanteringslösning. Du kan använda en centraliserad lösning där nycklar och hemligheter lagras i maskinvarusäkerhetsmoduler (HSM). Azure ger dig en HSM i molnet med [Azure Key Vault](../../key-vault/key-vault-overview.md).

Key Vault är en *hemlig butik:* det är en centraliserad molntjänst för lagring av programhemligheter. Key Vault skyddar dina konfidentiella data genom att hålla programhemligheter på en enda central plats och ge säker åtkomst, behörighetskontroll och åtkomstloggning.

Hemligheter lagras i enskilda *valv*. Varje valv har sina egna konfigurations- och säkerhetsprinciper för att styra åtkomsten. Du kommer till dina data via ett REST API eller via en klient SDK som är tillgänglig för de flesta programmeringsspråk.

> [!IMPORTANT]
> Azure Key Vault är utformat för att lagra konfigurationshemligheter för serverprogram. Den är inte avsedd för lagring av data som tillhör appanvändare. Det återspeglas i dess prestandaegenskaper, API och kostnadsmodell.
>
> Användardata bör lagras någon annanstans, till exempel i en Azure SQL Database-instans som har Transparent Data Encryption (TDE) eller i ett lagringskonto som använder Azure Storage Service Encryption. Hemligheter som används av ditt program för att komma åt dessa datalager kan sparas i Azure Key Vault.

### <a name="protect-sensitive-data"></a>Skydda känsliga data

Att skydda data är en viktig del av din säkerhetsstrategi.
Genom att klassificera dina data och identifiera dina dataskyddsbehov kan du utforma din app med datasäkerhet i åtanke. Klassificering (kategorisering) lagrade data efter känslighet och affärspåverkan hjälper utvecklare att avgöra de risker som är associerade med data.

Märk alla tillämpliga data som känsliga när du utformar dina dataformat. Se till att programmet behandlar tillämpliga data som känsliga. De här metoderna kan hjälpa dig att skydda känsliga data:

- Använd kryptering.
- Undvik hårdkodning hemligheter som nycklar och lösenord.
- Se till att åtkomstkontroller och granskning finns på plats.

#### <a name="use-encryption"></a>Använd kryptering

Att skydda data bör vara en viktig del av din säkerhetsstrategi.
Om dina data lagras i en databas eller om de flyttas fram och tillbaka mellan platser, använd kryptering av data i [vila](../fundamentals/encryption-atrest.md) (medan de är i databasen) och kryptering av [data under överföring](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (på väg till och från användaren, databasen, ett API eller tjänstslutpunkt). Vi rekommenderar att du alltid använder SSL/TLS-protokoll för att utbyta data. Se till att du använder den senaste versionen av TLS för kryptering (för närvarande är detta version 1.2).

#### <a name="avoid-hard-coding"></a>Undvik hårdkodning

Vissa saker bör aldrig vara hårdkodade i din programvara. Några exempel är värdnamn eller IP-adresser, webbadresser, e-postadresser, användarnamn, lösenord, lagringskontonycklar och andra kryptografiska nycklar. Överväg att implementera krav kring vad som kan eller inte kan hårdkodas i koden, inklusive i kommentarsavsnitten i koden.

När du lägger in kommentarer i koden ska du se till att du inte sparar känslig information. Detta inkluderar din e-postadress, lösenord, anslutningssträngar, information om ditt program som bara skulle vara känd av någon i din organisation, och allt annat som kan ge en angripare en fördel i att attackera ditt program eller din organisation .

I grund och botten, anta att allt i ditt utvecklingsprojekt kommer att vara allmänt känt när det distribueras. Undvik att inkludera känsliga data av något slag i projektet.

Tidigare diskuterade vi [Azure Key Vault](../../key-vault/key-vault-overview.md). Du kan använda Key Vault för att lagra hemligheter som nycklar och lösenord i stället för hårdkodning av dem. När du använder Key Vault i kombination med hanterade identiteter för Azure-resurser kan din Azure-webbapp komma åt hemliga konfigurationsvärden enkelt och säkert utan att lagra några hemligheter i källkontrollen eller konfigurationen. Mer information finns [i Hantera hemligheter i serverapparna med Azure Key Vault](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/).

### <a name="implement-fail-safe-measures"></a>Genomföra felsäkra åtgärder

Ditt program måste kunna hantera [fel](https://docs.microsoft.com/dotnet/standard/exceptions/) som uppstår under körningen på ett konsekvent sätt. Programmet bör fånga alla fel och antingen misslyckas säker eller stängd.

Du bör också se till att fel loggas med tillräcklig användarkontext för att identifiera misstänkt eller skadlig aktivitet. Loggarna bör behållas tillräckligt med tid för att möjliggöra fördröjd kriminalteknisk analys. Loggar bör vara i ett format som lätt kan förbrukas av en logghanteringslösning. Kontrollera att aviseringar för fel som är relaterade till säkerhet utlöses. Otillräcklig loggning och övervakning gör det möjligt för angripare att ytterligare attackera system och upprätthålla uthållighet.

### <a name="take-advantage-of-error-and-exception-handling"></a>Dra nytta av fel- och undantagshantering

Att implementera korrekt fel- och [undantagshantering](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions) är en viktig del av defensiv kodning. Fel- och undantagshantering är avgörande för att göra ett system tillförlitligt och säkert. Misstag i felhantering kan leda till olika typer av säkerhetsproblem, till exempel läcka information till angripare och hjälpa angripare att förstå mer om din plattform och design.

Se till att:

- Du hanterar undantag på ett centraliserat sätt för att undvika duplicerade [try/catch-block](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) i koden.

- Alla oväntade beteenden hanteras i programmet.

- Meddelanden som visas för användare läcker inte kritiska data men ger tillräckligt med information för att förklara problemet.

- Undantag loggas och att de ger tillräckligt med information för kriminalteknik eller incidentteam för att undersöka.

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) ger en förstklassig upplevelse för [hantering av fel och undantag](../../logic-apps/logic-apps-exception-handling.md) som orsakas av beroende system. Du kan använda Logic Apps för att skapa arbetsflöden för att automatisera uppgifter och processer som integrerar appar, data, system och tjänster i företag och organisationer.

### <a name="use-logging-and-alerting"></a>Använda loggning och avisering

[Logga](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) dina säkerhetsproblem för säkerhetsutredningar och utlösa aviseringar om problem för att säkerställa att människor känner till problem i tid. Aktivera granskning och loggning på alla komponenter. Granskningsloggar bör fånga användarkontexten och identifiera alla viktiga händelser.

Kontrollera att du inte loggar några känsliga data som en användare skickar till din webbplats. Exempel på känsliga data är:

- Användaruppgifter
- Personnummer eller annan identifierande information
- Kreditkortsnummer eller annan finansiell information
- Hälsoinformation
- Privata nycklar eller andra data som kan användas för att dekryptera krypterad information
- System- eller applikationsinformation som kan användas för att mer effektivt angripa

Se till att programmet övervakar användarhanteringshändelser som lyckade och misslyckade användarinloggningar, återställning av lösenord, lösenordsändringar, kontoutelåsning och användarregistrering. Genom att logga för dessa händelser kan du identifiera och reagera på potentiellt misstänkt beteende. Det låter dig också samla in verksamhetsdata, som vem som har åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
I följande artiklar rekommenderar vi säkerhetskontroller och aktiviteter som kan hjälpa dig att utveckla och distribuera säkra program.

- [Utveckla säkra program](secure-develop.md)
- [Distribuera säkra program](secure-deploy.md)
