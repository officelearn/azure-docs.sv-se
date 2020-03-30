---
title: Metodtips för säkra PaaS-distributioner – Microsoft Azure
description: Lär dig metodtips för att utforma, skapa och hantera säkra molnprogram på Azure och förstå säkerhetsfördelarna med PaaS jämfört med andra molntjänstmodeller.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 8fd5a063683d09cb94b45205426871d880119cc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138022"
---
# <a name="securing-paas-deployments"></a>Skydda PaaS-distributioner

Den här artikeln innehåller information som hjälper dig:

- Förstå säkerhetsfördelarna med att vara värd för program i molnet
- Utvärdera säkerhetsfördelarna med plattformen som en tjänst (PaaS) jämfört med andra molntjänstmodeller
- Ändra ditt säkerhetsfokus från en nätverkscentrerad till en identitetscentrerad perimetersäkerhetsmetod
- Implementera allmänna rekommendationer om bästa praxis för PaaS-säkerhet

[Att utveckla säkra program på Azure](abstract-develop-secure-apps.md) är en allmän guide till de säkerhetsfrågor och kontroller som du bör tänka på i varje fas av livscykeln för programvaruutveckling när du utvecklar program för molnet.

## <a name="cloud-security-advantages"></a>Fördelar med molnsäkerhet
Det är viktigt att förstå [ansvarsfördelningen](shared-responsibility.md) mellan dig och Microsoft. Lokalt äger du hela stacken, men när du flyttar till molnet överförs vissa ansvarsområden till Microsoft.

Det finns [säkerhetsfördelar med att vara i molnet](shared-responsibility.md#cloud-security-advantages). I en lokal miljö har organisationer sannolikt ouppfyllda ansvarsområden och begränsade resurser tillgängliga för att investera i säkerhet, vilket skapar en miljö där angripare kan utnyttja sårbarheter på alla lager.

Organisationer kan förbättra sina hotidentifierings- och svarstider med hjälp av en leverantörs molnbaserade säkerhetsfunktioner och molninformation.  Genom att flytta ansvar till molnleverantören kan organisationer få mer säkerhetstäckning, vilket gör att de kan omfördela säkerhetsresurser och budget till andra affärsprioriteringar.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Säkerhetsfördelar med en PaaS-molntjänstmodell
Låt oss titta på säkerhetsfördelarna med en Azure PaaS-distribution jämfört med lokala.

![Säkerhetsfördelar med PaaS](./media/paas-deployments/advantages-of-paas.png)

Från botten av stapeln, den fysiska infrastrukturen, Microsoft minskar vanliga risker och ansvar. Eftersom Microsoft-molnet kontinuerligt övervakas av Microsoft är det svårt att attackera. Det är inte meningsfullt för en angripare att driva Microsoft-molnet som ett mål. Om inte angriparen har massor av pengar och resurser, är angriparen sannolikt att gå vidare till ett annat mål.  

I mitten av stapeln finns det ingen skillnad mellan en PaaS-distribution och lokalt. På programlagret och konto- och åtkomsthanteringslagret har du liknande risker. I nästa steg i den här artikeln guidar vi dig till bästa praxis för att eliminera eller minimera dessa risker.

Högst upp i stacken, datastyrning och rättighetshantering tar du en risk som kan minskas av nyckelhantering. (Nyckelhantering omfattas av bästa praxis.) Nyckelhantering är ett extra ansvar, men du har områden i en PaaS-distribution som du inte längre behöver hantera så att du kan flytta resurser till nyckelhantering.

Azure-plattformen ger dig också ett starkt DDoS-skydd med hjälp av olika nätverksbaserade tekniker. Alla typer av nätverksbaserade DDoS-skyddsmetoder har dock sina gränser per länk och per datacenter. För att undvika effekten av stora DDoS-attacker kan du dra nytta av Azures grundläggande molnkapacitet för att du snabbt och automatiskt ska kunna skala ut för att försvara dig mot DDoS-attacker. Vi kommer att gå in mer i detalj på hur du kan göra detta i de rekommenderade praxis artiklarna.

## <a name="modernizing-the-defenders-mindset"></a>Modernisera försvararens tänkesätt
Med PaaS-distributioner kommer en förändring i din övergripande inställning till säkerhet. Du övergår från att behöva styra allt själv till att dela ansvaret med Microsoft.

En annan viktig skillnad mellan PaaS och traditionella lokala distributioner är en ny vy över vad som definierar den primära säkerhetsperimetern. Historiskt sett var den primära lokala säkerhetsperimetern ditt nätverk och de flesta lokala säkerhetsdesigner använder nätverket som sin primära säkerhets pivot. För PaaS-distributioner är du bättre betjänt av att betrakta identitet som den primära säkerhetsperimetern.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Anta en identitetsprincip som den primära säkerhetsperimetern
En av de fem viktigaste egenskaperna hos cloud computing är bred nätverksåtkomst, vilket gör nätverkscentrerat tänkande mindre relevant. Målet med mycket av cloud computing är att tillåta användare att komma åt resurser oavsett plats. För de flesta användare, deras plats kommer att vara någonstans på Internet.

Följande bild visar hur säkerhetsperimetern har utvecklats från en nätverksperimeter till en identitetsperimeter. Säkerheten blir mindre om att försvara ditt nätverk och mer om att försvara dina data, samt hantera säkerheten för dina appar och användare. Den viktigaste skillnaden är att du vill driva säkerheten närmare det som är viktigt för ditt företag.

![Identitet som ny säkerhetsperimeter](./media/paas-deployments/identity-perimeter.png)

Azure PaaS-tjänster (till exempel webbroller och Azure SQL) gav till en början lite eller inget traditionellt nätverksperimeterskydd. Det var underförstått att elementets syfte var att exponeras för Internet (webbroll) och att autentisering ger den nya perimetern (till exempel BLOB eller Azure SQL).

Moderna säkerhetsrutiner förutsätter att motståndaren har brutit mot nätverksperimetern. Därför har moderna försvarsmetoder flyttat till identitet. Organisationer måste upprätta en identitetsbaserad säkerhetsperimeter med stark autentiserings- och auktoriseringshygien (bästa praxis).

Principer och mönster för nätverksperimetern har funnits i årtionden. Däremot har branschen relativt mindre erfarenhet av att använda identitet som den primära säkerhetsperimetern. Med det sagt har vi samlat tillräckligt med erfarenhet för att ge några allmänna rekommendationer som är beprövade på området och gäller för nästan alla PaaS-tjänster.

Följande är metodtips för hantering av identitetsper perimeter.

**Bästa praxis:** Skydda dina nycklar och autentiseringsuppgifter för att skydda din PaaS-distribution.   
**Detalj:** Att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. Du kan använda en centraliserad lösning där nycklar och hemligheter kan lagras i maskinvarusäkerhetsmoduler (HSM). [Azure Key Vault](../../key-vault/key-vault-overview.md) skyddar dina nycklar och hemligheter genom att kryptera autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar, PFX-filer och lösenord med hjälp av nycklar som skyddas av HSM-moduler.

**Bästa praxis**: Lägg inte autentiseringsuppgifter och andra hemligheter i källkoden eller GitHub.   
**Detalj:** Det enda som är värre än att förlora dina nycklar och referenser är att ha en obehörig part få tillgång till dem. Angripare kan dra nytta av bot-teknik för att hitta nycklar och hemligheter som lagras i koddatabaser som GitHub. Placera inte nyckel- och hemligheter i dessa offentliga kodarkiv.

**Bästa praxis:** Skydda dina VM-hanteringsgränssnitt på hybrid PaaS- och IaaS-tjänster med hjälp av ett hanteringsgränssnitt som gör att du kan fjärrhantera dessa virtuella datorer direkt.   
**Detalj:** Fjärrhanteringsprotokoll som [SSH,](https://en.wikipedia.org/wiki/Secure_Shell) [RDP](https://support.microsoft.com/kb/186607)och [PowerShell-ommotning](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) kan användas. I allmänhet rekommenderar vi att du inte aktiverar direkt fjärråtkomst till virtuella datorer från Internet.

Använd om möjligt alternativa metoder som att använda virtuella privata nätverk i ett virtuellt Azure-nätverk. Om alternativa metoder inte är tillgängliga, se till att du använder komplexa lösenfraser och tvåfaktorsautentisering (till exempel [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)).

**Bästa praxis**: Använd starka autentiserings- och auktoriseringsplattformar.   
**Detalj**: Använd federerade identiteter i Azure AD i stället för anpassade användararkiv. När du använder federerade identiteter utnyttjar du en plattformsbaserad metod och delegerar hanteringen av auktoriserade identiteter till dina partner. En federerad identitetsmetod är särskilt viktig när medarbetare sägs upp och den informationen måste återspeglas genom flera identitets- och auktoriseringssystem.

Använd autentiserings- och auktoriseringsmekanismer som tillhandahålls av plattformen i stället för anpassad kod. Anledningen är att utveckla anpassad autentiseringskod kan vara felbenägna. De flesta av dina utvecklare är inte säkerhetsexperter och är osannolikt att vara medvetna om nyanser och den senaste utvecklingen inom autentisering och auktorisering. Kommersiell kod (till exempel från Microsoft) granskas ofta i stor utsträckning.

Använd tvåfaktorsautentisering. Tvåfaktorsautentisering är den aktuella standarden för autentisering och auktorisering eftersom den undviker de säkerhetsbrister som finns i användarnamn och lösenordstyper för autentisering. Åtkomst till både Azure-hanteringsgränssnitt (portal/fjärr powershell) och kundinriktade tjänster bör utformas och konfigureras för att använda [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication).

Använd standardautentiseringsprotokoll, till exempel OAuth2 och Kerberos. Dessa protokoll har granskats utförligt och implementeras troligen som en del av dina plattformsbibliotek för autentisering och auktorisering.

## <a name="use-threat-modeling-during-application-design"></a>Använda hotmodellering under programdesign
Microsoft [Security Development Lifecycle](https://www.microsoft.com/en-us/sdl) anger att team ska delta i en process som kallas hotmodellering under designfasen. För att underlätta den här processen har Microsoft skapat [SDL Threat Modeling Tool](/azure/security/azure-security-threat-modeling-tool). Modellering av programdesignen och uppräkning [av STRIDE-hot](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) över alla förtroendegränser kan fånga designfel tidigt.

I följande tabell visas STRIDE-hoten och några exempel på mildrande åtgärder som använder Azure-funktioner. Dessa mildrande åtgärder fungerar inte i alla situationer.

| Hot | Egenskapen Security | Potentiella Azure-plattformsbegränsningar |
| --- | --- | --- |
| Förfalskning | Autentisering | Kräv HTTPS-anslutningar. |
| Manipulering | Integritet | Verifiera SSL-certifikat. |
| Avståndstagande | Icke-avståndstagande | Aktivera [Azure-övervakning och diagnostik](/azure/architecture/best-practices/monitoring). |
| Utlämnande av information | Sekretess | Kryptera känsliga data i vila med hjälp av [tjänstcertifikat](/rest/api/appservice/certificates). |
| Denial of Service (nekad tjänst) | Tillgänglighet | Övervaka prestandamått för potentiella dos-villkor. Implementera anslutningsfilter. |
| Behörighetshöjning | Auktorisering | Använd [Privilegierad identitetshantering](/azure/active-directory/privileged-identity-management/subscription-requirements). |

## <a name="develop-on-azure-app-service"></a>Utveckla på Azure App Service
[Azure App Service](/azure/app-service/overview) är ett PaaS-erbjudande som låter dig skapa webb- och mobilappar för alla plattformar eller enheter och ansluta till data var som helst, i molnet eller lokalt. App-tjänsten innehåller de webb- och mobilfunktioner som tidigare levererades separat som Azure-webbplatser och Azure Mobile Services. Det finns nya funktioner för att automatisera affärsprocesser och hantera moln-API:er. Som en enda integrerad tjänst ger App Service en omfattande uppsättning funktioner till webb-, mobil- och integrationsscenarier.

Här följer metodtips för hur du använder App Service.

**Bästa praxis:** [Autentisera via Azure Active Directory](/azure/app-service/overview-authentication-authorization).   
**Detalj:** App Service tillhandahåller en OAuth 2.0-tjänst för din identitetsleverantör. OAuth 2.0 fokuserar på enkelhet för klientutvecklare samtidigt som specifika auktoriseringsflöden för webbapplikationer, skrivbordsprogram och mobiltelefoner. Azure AD använder OAuth 2.0 för att du ska kunna auktorisera åtkomst till mobila program och webbprogram.

**Bästa praxis**: Begränsa åtkomsten baserat på behovet av att känna till och minst privilegier säkerhetsprinciper.   
**Detalj:** Det är absolut nödvändigt att begränsa åtkomsten för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Du kan använda RBAC för att tilldela behörigheter till användare, grupper och program i ett visst omfång. Mer information om hur du ger användare åtkomst till program finns i [Komma igång med åtkomsthantering](/azure/role-based-access-control/overview).

**Bästa praxis:** Skydda dina nycklar.   
**Detalj:** Azure Key Vault hjälper till att skydda kryptografiska nycklar och hemligheter som molnprogram och tjänster använder. Med Key Vault kan du kryptera nycklar och hemligheter (t.ex. autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar. PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler. Mer information finns i [Azure Key Vault.](/azure/key-vault/key-vault-overview) Du kan också använda Key Vault för att hantera dina TLS-certifikat med automatisk förnyelse.

**Bästa praxis:** Begränsa inkommande käll-IP-adresser.   
**Detalj:** [App Service Environment](/azure/app-service/environment/intro) har en virtuell nätverksintegreringsfunktion som hjälper dig att begränsa inkommande käll-IP-adresser via nätverkssäkerhetsgrupper. Virtuella nätverk kan du placera Azure-resurser i ett icke-internet, dirigerbart nätverk som du styr åtkomsten till. Mer information finns i [Integrera din app med ett virtuellt Azure-nätverk](/azure/app-service/web-sites-integrate-with-vnet).

**Bästa praxis**: Övervaka säkerhetstillståndet för dina App Service-miljöer.   
**Detalj:** Använd Azure Security Center för att övervaka dina App Service-miljöer. När Security Center identifierar potentiella säkerhetsproblem skapas [rekommendationer](../../security-center/security-center-virtual-machine-protection.md) som hjälper dig att konfigurera de kontroller som behövs.

> [!NOTE]
> Övervakningsapptjänsten är i förhandsversion och endast tillgänglig på [standardnivån](/azure/security-center/security-center-pricing) i Security Center.
>
>

## <a name="install-a-web-application-firewall"></a>Installera en brandvägg för webbprogram
Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

[Brandvägg för webbprogram (WAF)](/azure/frontdoor/waf-overview) är en funktion i Application Gateway som ger centraliserat skydd av dina webbprogram från vanliga kryphål och sårbarheter. WAF baseras på regler från [OWASP-kärnregeluppsättningarna](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Övervaka prestanda för dina program
Övervakning är handlingen att samla in och analysera data för att fastställa prestanda, hälsa och tillgänglighet för ditt program. En effektiv övervakningsstrategi hjälper dig att förstå den detaljerade driften av komponenterna i ditt program. Det hjälper dig att öka din drifttid genom att meddela dig om kritiska problem så att du kan lösa dem innan de blir problem. Det hjälper dig också att upptäcka avvikelser som kan vara säkerhetsrelaterade.

Använd [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights) för att övervaka tillgänglighet, prestanda och användning av ditt program, oavsett om det finns i molnet eller lokalt. Genom att använda Application Insights kan du snabbt identifiera och diagnostisera fel i ditt program utan att vänta på att en användare ska rapportera dem. Du kan göra välgrundade val om underhåll och förbättringar för ditt program med den information som du samlar in.

Application Insights har omfattande verktyg för att interagera med de data som samlas in. Application Insights lagrar data i en gemensam databas. Den kan dra nytta av delade funktioner som aviseringar, instrumentpaneler och djupanalys med Kusto-frågespråket.

## <a name="perform-security-penetration-testing"></a>Utför säkerhetspenetrationstester
Att validera säkerhetsförsvar är lika viktigt som att testa andra funktioner. Gör [penetrationstestning](pen-testing.md) till en standarddel av din bygg- och distributionsprocess. Schemalägg regelbundna säkerhetstester och sårbarhetsskanning på distribuerade program och övervaka för öppna portar, slutpunkter och attacker.

Fuzz-testning är en metod för att hitta programfel (kodfel) genom att tillhandahålla felaktiga indata till programgränssnitt (startpunkter) som tolkar och använder dessa data. [Microsoft Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/) är ett molnbaserat verktyg som du kan använda för att leta efter buggar och andra säkerhetsproblem i programvaran innan du distribuerar den till Azure. Verktyget är utformat för att fånga upp sårbarheter innan du distribuerar programvara så att du inte behöver korrigera en bugg, hantera krascher eller svara på en attack efter att programvaran har släppts.


## <a name="next-steps"></a>Nästa steg
I den här artikeln fokuserade vi på säkerhetsfördelar med en Azure PaaS-distribution och bästa säkerhetspraxis för molnprogram. Lär dig sedan rekommenderade metoder för att skydda dina PaaS-webb- och mobillösningar med hjälp av specifika Azure-tjänster. Vi börjar med Azure App Service, Azure SQL Database och Azure SQL Data Warehouse och Azure Storage. När artiklar om rekommenderade metoder för andra Azure-tjänster blir tillgängliga finns länkar i följande lista:

- [Azure App-tjänst](paas-applications-using-app-services.md)
- [Azure SQL Database och Azure SQL Data Warehouse](paas-applications-using-sql.md)
- [Azure-lagring](paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- Brandväggar för webbprogram

Se [Utveckla säkra program på Azure](abstract-develop-secure-apps.md) för säkerhetsfrågor och kontroller som du bör tänka på i varje fas av livscykeln för programvaruutveckling när du utvecklar program för molnet.

Se metodtips och mönster för [Azure-säkerhet](best-practices-and-patterns.md) för fler metodtips för säkerhet som ska användas när du utformar, distribuerar och hanterar dina molnlösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Blogg för Azure Security Team](https://blogs.msdn.microsoft.com/azuresecurity/) – för aktuell information om det senaste inom Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där Microsofts säkerhetsproblem, inklusive problem med Azure, kan rapporteras eller via e-post tillsecure@microsoft.com
