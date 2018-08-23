---
title: Skydda PaaS-distributioner | Microsoft Docs
description: " Förstå fördelarna med PaaS jämfört med andra tjänstmodeller i molnet och lär dig rekommenderade metoder för att skydda din Azure PaaS-distribution. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: da5d59aaaea8e6186609eb5f3419fba5e67d4279
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42061508"
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

## <a name="identity-as-the-primary-security-perimeter"></a>Identitet som primär säkerhetsperimeter
En av fem grundläggande egenskaper för molnbaserad databehandling är bred nätverksåtkomst, vilket gör nätverks-centric tänker mindre relevanta. Målet med mycket av molnbaserad databehandling är att ge användare åtkomst till resurser, oavsett plats. För de flesta användare ska var vara någonstans på Internet.

Följande bild visar hur säkerhetsperimeter har utvecklats från en nätverksperimeter en perimeter-identitet. Säkerheten blir mindre om försvar ditt nätverk och mer information om försvar dina data, samt hanterar säkerheten för dina appar och användare. Den viktigaste skillnaden är att du vill skicka security närmare till viktig information för ditt företag.

![Identitet som nya säkerhetsperimeter][4]

Azure PaaS-tjänster (till exempel web-roller och Azure SQL) anges först har lite eller ingen traditionellt nätverk perimeter försvar. Det har förstått att elementets syfte var att exponeras för Internet (web-roll) och att autentisering ger den nya perimetern (till exempel BLOB eller Azure SQL).

Moderna säkerhetsrutiner förutsätter att angriparen har brutit mot perimeternätverket. Därför har moderna defense praxis flyttat till identitet. Organisationer måste upprätta en identitetsbaserad säkerhetsperimeter med stark autentisering och auktorisering hygien (metodtips).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Rekommendationer för att hantera identitet perimeternätverket

Principer och mönster för perimeternätverket har varit tillgängliga för flera decennier. Däremot har branschen relativt mindre erfarenhet av att använda identitet som primär säkerhetsperimeter. Därmed SA har vi ackumulerade så mycket erfarenhet för att tillhandahålla vissa allmänna rekommendationer som har varit i fältet och gäller för nästan alla PaaS-tjänster.

Följande sammanfattar en allmän praxis för att hantera din identitet perimeternätverket.

- **Förlora inte dina nycklar eller autentiseringsuppgifter** att skydda nycklar och autentiseringsuppgifter är mycket viktigt att skydda PaaS-distributioner. Att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. En bra lösning är att använda en centraliserad lösning där nycklar och hemligheter kan lagras i maskinvarusäkerhetsmoduler (HSM). Azure ger dig en HSM i molnet med [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Placera inte autentiseringsuppgifter och andra hemligheter i källkoden eller GitHub** enda värre än att förlora dina nycklar och autentiseringsuppgifter har obehöriga personer få tillgång till dem. Angripare kan utnyttja fördelarna med bot tekniker för att hitta nycklar och hemligheter som lagras i koddatabaser, till exempel GitHub. Placera inte nyckeln och hemligheter i de här offentliga källkodslager.
- **Skydda dina VM-hanteringsgränssnitt på hybrid PaaS och IaaS** IaaS och PaaS-tjänster som körs på virtuella datorer (VM). Beroende på vilken typ av tjänst flera hanteringsgränssnitt är tillgängliga att aktivera du remote hantera dessa virtuella datorer direkt. Fjärrhantering protokoll som [SSH (Secure Shell Protocol)](https://en.wikipedia.org/wiki/Secure_Shell), [Remote Desktop Protocol (RDP)](https://support.microsoft.com/kb/186607), och [fjärr-PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) kan användas. I allmänhet rekommenderar vi att du inte aktiverar direkt fjärråtkomst till virtuella datorer från Internet. Om det är tillgängligt, bör du använda alternativa metoder, till exempel med ett virtuellt privat nätverk till ett Azure-nätverk. Om alternativa metoder är inte tillgängliga och sedan kontrollera att du använder komplexa lösenfraser och i förekommande fall, tvåfaktorsautentisering (till exempel [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).
- **Använda stark autentisering och auktorisering plattformar**

  - Använda federerade identiteter i Azure AD i stället för anpassade användarlager. När du använder federerade identiteter kan du dra nytta av en plattformbaserade metod och du delegera hanteringen av auktoriserade identiteter till dina partner. En metod för federerad identitet är särskilt viktigt i fall när anställda avslutas och information behöver återspeglas via flera system för identitet och auktorisering.
  - Använd plattformslista mekanismer för autentisering och auktorisering istället för anpassad kod. Anledningen är att utveckla anpassade Autentiseringskod kan vara felbenägna. De flesta av dina utvecklare är inte säkerhetsexperter och är inte troligt att känna till nyanser och den senaste utvecklingen i autentisering och auktorisering. Kommersiella kod (till exempel från Microsoft) är ofta omfattande säkerhet granskas.
  - Använd Multi-Factor authentication. Multi-Factor authentication är den aktuella standarden för autentisering och auktorisering eftersom de undviker de security svagheterna i användarnamnet och lösenordet typer av autentisering. Åtkomst till både Azure-hantering (portal/fjärråtkomst PowerShell) gränssnitten och kundorienterade tjänster bör utformade och konfigurerade för att använda [Azure Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md).
  - Använd standard autentiseringsprotokoll, till exempel OAuth2- och Kerberos. Dessa protokoll har stor utsträckning peer granskas och förmodligen implementeras som en del av din plattformsbibliotek för autentisering och auktorisering.

## <a name="next-steps"></a>Nästa steg
I den här artikeln fokuserar vi på fördelarna med en Azure PaaS-distribution. Lär dig sedan rekommenderade metoder för att skydda dina PaaS webb- och mobila lösningar. Vi börjar med Azure App Service, Azure SQL Database och Azure SQL Data Warehouse. När artiklar om rekommenderade metoder för andra Azure-tjänster blir tillgängliga, ges länkar i listan nedan:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database och Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- [Azure Storage](security-paas-applications-using-storage.md)
- Azure REDIS Cache
- Azure Service Bus
- Brandväggar för webbprogram

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
