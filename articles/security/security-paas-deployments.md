---
title: Skydda PaaS-distributioner | Microsoft Docs
description: " Förstå fördelarna med PaaS jämfört med andra molnet tjänstmodeller och Läs rekommenderade metoder för att säkra din Azure PaaS-distribution. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
ms.openlocfilehash: f218fe7e59e46683b544fd83bfea505b7cbe2d59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-deployments"></a>Att säkra PaaS-distributioner

Den här artikeln innehåller information som hjälper dig att:

- Förstå fördelarna med värd för program i molnet
- Utvärdera fördelarna med plattform som en tjänst (PaaS) jämfört med andra molntjänster tjänstmodeller
- Ändra din säkerhet fokus från ett nätverk till Central till en identitet till Central perimeter säkerhet metod
- Implementera Allmänt PaaS säkerhetsrekommendationer om bästa praxis

## <a name="cloud-security-advantages"></a>Fördelarna med molnet
Det finns säkerhetsfördelar med att den finns i molnet. I en lokal miljö organisationer troligen har unmet ansvar och begränsade resurser som är tillgängliga att investera i säkerhet, som skapar en miljö där angripare kan utnyttja sårbarheter i alla skikt.

![Fördelarna med molnet era][1]

Organisationer kan förbättra sina hotidentifiering och svarstider med hjälp av en provider molnbaserade säkerhetsfunktioner och moln för tillgångsinformation.  Genom att ändra ansvarsområden till molnleverantören kan organisationer få mer säkerhet täckning, vilket gör det enkelt att allokera säkerhetsresurser och budget till andra företag prioriteringar.

## <a name="division-of-responsibility"></a>Uppdelning av ansvar
Det är viktigt att förstå divisionen av ansvar mellan dig och Microsoft. Lokal du äger hela stacken men när du flyttar till molnet några ansvarsområden Överför till Microsoft. Följande ansvar matrisen visar områden för stacken i en SaaS-PaaS och IaaS-distribution som du är ansvarig för och Microsoft ansvarar för.

![Ansvar zoner][2]

För alla moln distributionstyper äger du dina data och identiteter. Du ansvarar för att skydda dina data och identiteter, lokala resurser och moln-komponenter du kontrollen (varierar beroende på typ av tjänst).

Ansvarsområden som behålls alltid av du, oavsett vilken typ av distribution är:

- Data
- Slutpunkter
- Konto
- Åtkomsthantering

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Fördelarna med en PaaS molnet tjänstmodell
Med samma ansvar matris ska vi titta på av fördelarna med en Azure PaaS-distribution jämfört med lokalt.

![Fördelarna med PaaS][3]

Starta längst ned i stacken, den fysiska infrastrukturen minskar Microsoft vanliga risker och ansvarsområden. Eftersom Microsoft cloud övervakas kontinuerligt av Microsoft, är det svårt för angrepp. Den passar inte för en angripare att driva Microsoft-molntjänster som mål. Om inte angriparen har många pengar och resurser, angripare kommer troligen att gå vidare till ett annat mål.  

Det finns ingen skillnad mellan en PaaS-distribution och lokala mitt i stacken. När programlager och lagringshanteringslager konto och komma åt har liknande risker. Under nästa steg i den här artikeln hjälper vi dig till bästa praxis för att eliminera eller minska riskerna.

Överst i stacken, datastyrning och rights management ta på en risk att kan begränsas med nyckelhantering. (Key management ingår i metodtips.) Hantering av nycklar är en ytterligare ansvar, har områden i en PaaS-distribution som du inte längre behöver hantera så att du kan ändra resurser till nyckelhantering.

Azure-plattformen ger dig också starkt DDoS-skydd genom att använda olika nätverksbaserade tekniker. Men har alla typer av nätverksbaserade DDoS skyddsmetod sina begränsningar på grundval av per länk och per datacenter. För att undvika påverkan av den stora DDoS-attacker kan dra du nytta av Azures core molnkapacitet för att aktivera du att snabbt och automatiskt skala ut för att skydda mot DDoS-attacker. Vi ska gå in mer i detalj på hur du kan göra detta i rekommendationer artiklar.

## <a name="modernizing-the-defenders-mindset"></a>Modernizing i defender tänkesätt
Distributioner medför en SKIFT i din övergripande metoden PaaS säkerhet. Du växlar från behöva styra allt själv för att dela ansvar med Microsoft.

En annan betydande skillnaden mellan PaaS och traditionella lokala distributioner är en ny vy över vad definierar säkerhetsinformation perimeternätverket. Tidigare primär lokal säkerhet perimeternätverket var ditt nätverk och de flesta lokal säkerhet Designer använder nätverket som dess primära säkerhet pivot. För PaaS-distributioner Service du bättre genom att beakta identitet ska säkerhetsinformation perimeternätverket.

## <a name="identity-as-the-primary-security-perimeter"></a>Identitet som säkerhetsinformation perimeternätverket
En av fem grundläggande egenskaper för cloud computing-lösningar är bred nätverksåtkomst, vilket gör nätverket till Central tänker mindre relevant. Målet för mycket av cloud computing-lösningar är att ge användare åtkomst till nätverksresurser oavsett plats. För de flesta användare kommer deras plats att vara någonstans på Internet.

Följande bild visar hur säkerhet perimeternätverket har utvecklats från ett perimeternätverk i nätverket till en identitet perimeternätverk. Säkerheten blir mer eller mindre om försvara nätverket om samtidigt skyddar dina data, samt hantera säkerheten för dina appar och användare. Den viktigaste skillnaden är att du vill skicka säkerhet närmare till vad som är viktigt för ditt företag.

![Identitet som den nya säkerhet perimeternätverk][4]

Azure PaaS-tjänster (till exempel webbroller och Azure SQL) anges inledningsvis lite eller ingen traditionellt nätverk perimeter försvar. Det gick att tolka att elementets syftet var att exponeras mot Internet (webbroll) och att autentisering ger nya perimeternätverk (till exempel BLOB eller Azure SQL).

Moderna säkerhetspraxis förutsätter att angriparen har utsatts för intrång nätverket perimeternätverket. Därför har moderna defense praxis flyttats till identitet. Organisationer måste upprätta en identitetsbaserade säkerhet perimeternätverket med stark autentisering och auktorisering hygien (bästa metoder).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Rekommendationer för att hantera identitet perimeternätverket

Principer och mönster för nätverket perimeternätverket har funnits i åren. Däremot har bransch relativt mindre erfarenhet av att använda identitet som säkerhetsinformation perimeternätverket. Med som säger har vi ackumulerade så mycket erfarenhet för att tillhandahålla vissa allmänna rekommendationer som visat i fältet och gäller för nästan alla PaaS-tjänster.

Följande sammanfattar en allmän praxis att hantera din identitet perimeternätverk.

- **Förlora dina nycklar eller autentiseringsuppgifter** säkra nycklar och autentiseringsuppgifter är mycket viktigt att skydda PaaS-distributioner. Att förlora nycklar och autentiseringsuppgifter är ett vanligt problem. En bra lösning är att använda en central lösning där nycklar och hemligheter kan lagras i maskinvarusäkerhetsmoduler (HSM). Azure tillhandahåller en HSM i molnet med [Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Placera inte autentiseringsuppgifter och andra hemligheter i källkoden eller GitHub** enda värre än att förlora dina nycklar och autentiseringsuppgifter har obehöriga personer få tillgång till dem. Angripare kan utnyttja bot tekniker för att hitta nycklar och hemligheter som lagras i koden databaser, till exempel GitHub. Placera inte nyckeln och hemligheter i dessa offentliga källkodslager.
- **Skydda din VM hanteringsgränssnitt på hybrid PaaS- och IaaS** IaaS och PaaS-tjänster körs på virtuella datorer (VM). Beroende på vilken typ av tjänst flera hanteringsgränssnitt är tillgängliga att aktivera du remote hantera dessa virtuella datorer direkt. Protokoll för fjärrhantering som [protokollet SSH (Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell), [Remote Desktop Protocol (RDP)](https://support.microsoft.com/kb/186607), och [fjärr-PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) kan användas. I allmänhet rekommenderar vi att du inte aktiverar direkt fjärråtkomst till virtuella datorer från Internet. Om de är tillgängliga, bör du använda alternativa metoder, till exempel med ett virtuellt privat nätverk till Azure-nätverk. Om alternativa metoder är inte tillgängliga, och sedan kontrollera att du använder komplexa lösenfraser och i förekommande fall, tvåfaktorsautentisering (exempelvis [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)).
- **Använd stark autentisering och auktorisering plattformar**

  - Använd federerade identiteter i Azure AD i stället för anpassad användare lagrar. När du använder federerade identiteter kan du dra nytta av en synsätt, plattform och du delegera hanteringen av auktoriserade identiteter till dina partners. En metod för federerad identitet är särskilt viktigt i situationer när anställda avslutas och information behöver återspeglas via flera identitet och auktorisering system.
  - Använd mekanismer för autentisering och auktorisering av plattform som anges i stället för anpassad kod. Anledningen är att utveckla anpassade Autentiseringskod kan vara tillförlitligt. De flesta av utvecklarna inte säkerhetsexperter och kommer troligen inte att vara medveten om de detaljerad och nyansrik och den senaste utvecklingen inom autentisering och auktorisering. Extern kod (till exempel från Microsoft) är ofta mycket säkerhet ses över.
  - Använda multifaktorautentisering. Multifaktorautentisering är aktuell standard för autentisering och auktorisering eftersom den förhindrar security-svagheter med användarnamn och lösenord typer av autentisering. Åtkomst till både i Azure (portal/fjärråtkomst PowerShell) hanteringsgränssnitt och kundinriktade tjänster ska utformas och konfigurerats för att använda [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md).
  - Använd standard autentiseringsprotokoll, till exempel OAuth2 och Kerberos. Dessa protokoll har omfattande peer granskat och troligtvis implementeras som en del av din plattformsbibliotek för autentisering och auktorisering.

## <a name="next-steps"></a>Nästa steg
I den här artikeln fokuserar vi på fördelarna med en Azure PaaS-distribution. Lär dig sedan rekommenderade metoder för att säkra din PaaS webb- och mobile-lösningar. Vi börjar med Azure App Service, Azure SQL Database och Azure SQL Data Warehouse. Artiklar om rekommenderade metoder för andra Azure-tjänster blir tillgängliga ges länkar i följande lista:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database och Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- Azure Storage
- Azure REDIS-Cache
- Azure Service Bus
- Web Application brandväggar

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
