---
title: Skydda PaaS-distributioner | Microsoft Docs
description: " Förstå fördelarna med PaaS jämfört med andra tjänstmodeller i molnet och lär dig rekommenderade metoder för att skydda din Azure PaaS-distribution. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: terrylan
ms.openlocfilehash: e833317fa16576fa0006a774226d12974fd93ed8
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404815"
---
# <a name="securing-paas-deployments"></a>Skydda PaaS-distributioner

Den här artikeln innehåller information som hjälper dig att:

- Förstå av fördelarna med som är värd för program i molnet
- Utvärdera fördelarna med plattform som en tjänst (PaaS) jämfört med andra cloud service-modeller
- Ändra fokus säkerhet från en nätverks-centric till en identity-centric perimeter security-metod
- Implementera allmänna PaaS security rekommendationer om bästa praxis

## <a name="cloud-security-advantages"></a>Cloud security fördelar
Det finns säkerhetsfördelar med att vara i molnet. I en lokal miljö organisationer sannolikt har motsvarar hittills ouppfyllda ansvarsområden och begränsade resurser som är tillgängliga att investera i säkerhet, vilket skapar en miljö där angripare kan utnyttja sårbarheter på alla nivåer.

![Fördelarna med molnet][1]

Organisationer kan förbättra sin hotidentifiering och svarstider med hjälp av en provider molnbaserade säkerhetsfunktioner och intelligenta molntjänster.  Organisationer kan få mer säkerhetstäckning, vilket gör det möjligt för dem att allokera säkerhetsresurser och budget med andra affärsbehov genom att ändra ansvarsområden till molnleverantören.

## <a name="division-of-responsibility"></a>Divisionen av ansvar
Det är viktigt att förstå divisionen av ansvar mellan dig och Microsoft. Lokalt, du äger i hela stacken men när du migrerar till molnet några ansvarsområden Överför till Microsoft. Följande ansvar matrisen visar områdena av stacken i en SaaS, PaaS och IaaS-distribution som du är ansvarig för och Microsoft ansvarar för.

![Ansvar zoner][2]

För alla moln-distributionstyper som du äger dina data och identiteter. Du ansvarar för att skydda säkerheten för dina data och identiteter, lokala resurser och molnbaserade komponenterna du kontrollen (som varierar beroende på typ av tjänst).

Ansvarsområden som behålls alltid av dig, oavsett vilken typ av distribution, är:

- Data
- Slutpunkter
- Konto
- Åtkomsthantering

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Fördelarna med en PaaS modell i molnet
Med samma ansvar matris kan vi titta på fördelarna med en Azure PaaS-distribution eller lokalt.

![Fördelarna med PaaS][3]

Startar längst ned i stacken, den fysiska infrastrukturen Microsoft minskar risken för vanliga risker och ansvarsområden. Eftersom Microsoft-molnet övervakas kontinuerligt av Microsoft, är det svårt att angrepp. Det meningsfullt inte för en angripare att nå Microsoft-molnet som mål. Om angriparen har mycket pengar och resurser, angriparen är troligt att gå vidare till ett annat mål.  

Mitt stacken finns det ingen skillnad mellan en PaaS-distribution och lokalt. När programnivån och lagringshanteringslager konton och komma åt har liknande risker. Under nästa steg i den här artikeln vägleder vi dig till bästa praxis för att ta bort eller minimera riskerna.

Överst i stacken, datastyrning och rights management ta på en risk för angrepp som kan undvikas genom nyckelhantering. (Hantering av nycklar beskrivs bästa praxis.) Nyckelhantering är ett ytterligare ansvar, har du områden i en PaaS-distribution som du behöver inte längre hantera så att du kan ändra resurser till nyckelhantering.

Azure-plattformen ger också starkt DDoS-skydd genom att använda olika nätverksbaserade tekniker. Men har alla typer av nätverksbaserade DDoS protection metoder sina begränsningar på basis av per länk och per datacenter. För att undvika effekten av stora DDoS-attacker kan dra du nytta av Azures grundläggande molnet funktion av så att du snabbt och automatiskt skala ut för att skydda mot DDoS-attacker. Vi ska gå in mer i detalj på hur du kan göra detta i artiklar rekommenderade metoder.

## <a name="modernizing-the-defenders-mindset"></a>Modernisera i defender tänkesätt
Med PaaS kommer distributioner en förändring i din övergripande metoden säkerhet. Du vill ändra inte behöver att styra allt själv om du vill dela ansvar med Microsoft.

En annan viktig skillnad mellan PaaS och traditionella lokala distributioner, är en ny vy av primär säkerhetsperimeter definieras. Historiskt sett säkerhetsperimeter primära lokala var ditt nätverk och de flesta lokala security-Designer använder nätverket som sin primära security pivot. För PaaS-distributioner betjänas du bättre av överväger identitet för att vara primär säkerhetsperimeter.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Anta en princip av identitet som primär säkerhetsperimeter
En av fem grundläggande egenskaper för molnbaserad databehandling är bred nätverksåtkomst, vilket gör nätverks-centric tänker mindre relevanta. Målet med mycket av molnbaserad databehandling är att ge användare åtkomst till resurser, oavsett plats. För de flesta användare ska var vara någonstans på Internet.

Följande bild visar hur säkerhetsperimeter har utvecklats från en nätverksperimeter en perimeter-identitet. Säkerheten blir mindre om försvar ditt nätverk och mer information om försvar dina data, samt hanterar säkerheten för dina appar och användare. Den viktigaste skillnaden är att du vill skicka security närmare till viktig information för ditt företag.

![Identitet som nya säkerhetsperimeter][4]

Azure PaaS-tjänster (till exempel web-roller och Azure SQL) anges först har lite eller ingen traditionellt nätverk perimeter försvar. Det har förstått att elementets syfte var att exponeras för Internet (web-roll) och att autentisering ger den nya perimetern (till exempel BLOB eller Azure SQL).

Moderna säkerhetsrutiner förutsätter att angriparen har brutit mot perimeternätverket. Därför har moderna defense praxis flyttat till identitet. Organisationer måste upprätta en identitetsbaserad säkerhetsperimeter med stark autentisering och auktorisering hygien (metodtips).

Principer och mönster för perimeternätverket har varit tillgängliga för flera decennier. Däremot har branschen relativt mindre erfarenhet av att använda identitet som primär säkerhetsperimeter. Därmed SA har vi ackumulerade så mycket erfarenhet för att tillhandahålla vissa allmänna rekommendationer som har varit i fältet och gäller för nästan alla PaaS-tjänster.

Här följer bästa praxis för att hantera identitet perimeternätverket.

**Bästa praxis**: Skydda dina nycklar och autentiseringsuppgifter för att skydda PaaS-distribution.   
**Information om**: Att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. Du kan använda en centraliserad lösning där nycklar och hemligheter kan lagras i maskinvarusäkerhetsmoduler (HSM). [Azure Key Vault](../key-vault/key-vault-whatis.md) skyddar dina nycklar och hemligheter genom att kryptera autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar, PFX-filer och lösenord med nycklar som skyddas av HSM: er.

**Bästa praxis**: Placera inte autentiseringsuppgifter och andra hemligheter i källkoden eller GitHub.   
**Information om**: Enda sämre resultat än att förlora dina nycklar och autentiseringsuppgifter som har en obehörig part få åtkomst till dem. Angripare kan dra nytta av bot tekniker för att hitta nycklar och hemligheter som lagras i koddatabaser, till exempel GitHub. Placera inte nyckeln och hemligheter i de här offentliga lagringsplatser.

**Bästa praxis**: Skydda dina VM-hanteringsgränssnitt på hybrid PaaS och IaaS-tjänster med hjälp av ett hanteringsgränssnitt som gör det möjligt för fjärranslutna hantera dessa virtuella datorer direkt.   
**Information om**: Fjärrhantering protokoll som [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607), och [PowerShell-fjärrkommunikation](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) kan användas. I allmänhet rekommenderar vi att du inte aktiverar direkt fjärråtkomst till virtuella datorer från internet.

Använd om möjligt alternativa metoder som att använda virtuella privata nätverk i Azure-nätverk. Om alternativa metoder inte är tillgängliga, kontrollera att du använder komplexa lösenfraser och tvåfaktorsautentisering (till exempel [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).

**Bästa praxis**: Använd stark autentisering och auktorisering plattformar.   
**Information om**: Använda federerade identiteter i Azure AD i stället för anpassade användarlager. När du använder federerade identiteter kan du dra nytta av en plattformbaserade metod och du delegera hanteringen av auktoriserade identiteter till dina partner. En federerad identitet-metoden är särskilt viktigt när anställda avslutas och att information behöver återspeglas via flera identiteter och auktorisering system.

Använd plattform anger mekanismer för autentisering och auktorisering istället för anpassad kod. Anledningen är att utveckla anpassade Autentiseringskod kan vara felbenägna. De flesta av dina utvecklare är inte säkerhetsexperter och är inte troligt att känna till nyanser och den senaste utvecklingen i autentisering och auktorisering. Kommersiella kod (till exempel från Microsoft) är ofta omfattande säkerhet granskas.

Använda tvåfaktorsautentisering. Tvåfaktorsautentisering är den aktuella standarden för autentisering och auktorisering eftersom de undviker de security svagheterna i användarnamnet och lösenordet typer av autentisering. Åtkomst till både Azure hanteringsgränssnitt (portal/fjärråtkomst PowerShell) och kundinriktad tjänster bör utformade och konfigurerade för att använda [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).

Använd standard autentiseringsprotokoll, till exempel OAuth2- och Kerberos. Dessa protokoll har stor utsträckning peer granskas och förmodligen implementeras som en del av din plattformsbibliotek för autentisering och auktorisering.

## <a name="use-threat-modeling-during-application-design"></a>Använda threat modeling under programmets design
Microsofts [livscykler för säkerhetsutveckling](https://www.microsoft.com/en-us/sdl) anger att grupper ska delta i en process som kallas threat modeling under designfasen. För att den här processen förenklas, Microsoft har skapat den [SDL Threat Modeling Tool](../security/azure-security-threat-modeling-tool.md). Modellering programmets design och räkna upp [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) hot över alla förtroende gränser kan fånga upp utforma fel tidigt.

I följande tabell visar en lista över STRIDE hot och ger vissa exempel åtgärder som använder Azure-funktioner. Dessa åtgärder fungerar inte i alla situationer.

| Hot | Egenskapen för säkerhet | Möjliga lösningar i Azure-plattformen |
| --- | --- | --- |
| Förfalskning | Authentication | Kräv HTTPS-anslutningar. |
| Tampering | Integritet | Verifiera SSL-certifikat. |
| Repudiation | Oavvislighet | Aktivera Azure [övervakning och diagnostik](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). |
| Avslöjande av information | Sekretess | Kryptera känsliga data i vila med hjälp av [tjänsten certifikat](https://docs.microsoft.com/rest/api/appservice/certificates). |
| Denial of Service | Tillgänglighet | Övervaka prestandamått för potentiell denial of service-villkor. Implementera anslutningsfilter. |
| Upphöjning av rättigheter | Auktorisering | Använd [Privileged Identity Management](../active-directory/privileged-identity-management/subscription-requirements.md). |

## <a name="develop-on-azure-app-service"></a>Utveckla på Azure App Service
[Azure App Service](../app-service/overview.md) är en PaaS-erbjudande som låter dig skapa webb- och mobilappar för valfri plattform eller enhet och ansluta till data överallt, i molnet eller lokalt. Apptjänst innehåller webb- och mobilfunktioner som tidigare har levererat separat som Azure Websites och Azure Mobile Services. Det finns nya funktioner för att automatisera affärsprocesser och hantera moln-API:er. En enda integrerad tjänst innehåller App Service en omfattande uppsättning funktioner för webb, mobil- och integrationsscenarier.

Nedan följer Metodtips för App Service.

**Bästa praxis**: [Autentisera via Azure Active Directory](../app-service/overview-authentication-authorization.md).   
**Information om**: App Service tillhandahåller en OAuth 2.0-tjänst för din identitetsprovider. OAuth 2.0 fokuserar på klienten utvecklare enkelt få tillgång till specifika auktoriseringsflöden för webbprogram, program och mobiltelefoner. Azure AD använder OAuth 2.0 att auktorisera åtkomst till mobila och webbprogram.

**Bästa praxis**: Begränsa åtkomsten baserat på att känna och minsta privilegium säkerhetsprinciper.   
**Information om**: Begränsa åtkomst är absolut nödvändigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Du kan använda RBAC för att tilldela behörigheter till användare, grupper och program för ett visst omfång. Läs mer om att bevilja användare åtkomst till program i [Kom igång med åtkomsthantering](../role-based-access-control/overview.md).

**Bästa praxis**: Skydda dina nycklar.   
**Information om**: Azure Key Vault skyddar kryptografiska nycklar och hemligheter som program och tjänster i molnet använder. Med Key Vault kan kryptera du nycklar och hemligheter (till exempel autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar. PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler. Se [Azure Key Vault](../key-vault/key-vault-whatis.md) vill veta mer. Du kan också använda Key Vault för att hantera din TLS-certifikat med automatisk förnyelse.

**Bästa praxis**: Begränsa inkommande IP-källadresser.   
**Information om**: [App Service Environment](../app-service/environment/intro.md) har en funktion för integrering av virtuellt nätverk som hjälper dig att begränsa inkommande källans IP-adresser via nätverkssäkerhetsgrupper. Virtuella nätverk kan du placera Azure-resurser i ett icke-internet, dirigerbara nätverk som du styr åtkomst till. Mer information finns i [integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).

**Bästa praxis**: Övervaka säkerhetstillståndet för dina App Service-miljöer.   
**Information om**: Använd Azure Security Center för att övervaka din App Service-miljöer. När Security Center identifierar potentiella säkerhetsproblem skapas [rekommendationer](../security-center/security-center-virtual-machine-recommendations.md) som vägleder dig genom processen att konfigurera nödvändiga kontroller.

> [!NOTE]
> Övervaka App Service är förhandsversion och bara på den [standardnivån](../security-center/security-center-pricing.md) i Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Installera en brandvägg för webbaserade program
Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

[Brandvägg för webbaserade program (WAF)](../application-gateway/waf-overview.md) är en funktion i Application Gateway som ger ett centraliserat skydd för dina webbprogram mot vanliga kryphål och säkerhetsproblem. WAF är baserat på regler från den [OWASP Open Web Application Security Project ()-kärnregeluppsättningar](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Övervaka prestanda för dina program
Övervakning är syftar till insamling och analys av data för att avgöra prestanda, hälsotillstånd och tillgänglighet för ditt program. En effektiv övervakningsstrategi hjälper dig att förstå den detaljerade driften av komponenterna i ditt program. Det hjälper dig att förbättra din drifttid genom att meddela dig om kritiska tillstånd så att du kan lösa dem innan de hunnit bli problem. Du kan dessutom identifiera avvikelser som kan vara säkerhetsrelaterade.

Använd [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) att övervaka tillgänglighet, prestanda och användning av ditt program, oavsett om den finns i molnet eller lokalt. Genom att använda Application Insights kan du snabbt identifiera och diagnostisera fel i ditt program utan att behöva vänta på att en användare rapporterar dem. Du kan göra välgrundade val om underhåll och förbättringar för ditt program med den information som du samlar in.

Application Insights har omfattande verktyg för att interagera med de data som samlas in. Application Insights lagrar data i en gemensam databas. Det kan dra nytta av delade funktioner, till exempel aviseringar, instrumentpaneler och detaljerad analys med Kusto-frågespråket.



## <a name="next-steps"></a>Nästa steg
I den här artikeln fokuserar vi på fördelarna med en Azure PaaS-distribution och rekommenderade säkerhetsmetoder för molnprogram. Lär dig sedan rekommenderade metoder för att skydda dina PaaS webb- och mobillösningar med specifika Azure-tjänster. Vi börjar med Azure App Service, Azure SQL Database och Azure SQL Data Warehouse och Azure Storage. När artiklar om rekommenderade metoder för andra Azure-tjänster blir tillgängliga, ges länkar i listan nedan:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database och Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- [Azure Storage](security-paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Brandväggar för webbprogram

Se [säkerhet i Azure-metodtips och mönster](security-best-practices-and-patterns.md) för flera beprövade metoder för att använda när du utforma, distribuera och hantera dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att tillhandahålla mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure-Säkerhetsteamets blogg](https://blogs.msdn.microsoft.com/azuresecurity/) – uppdaterad information på senast inom Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där kan du rapportera säkerhetsproblem i Microsoft, inklusive problem med Azure, eller mejla till secure@microsoft.com

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
