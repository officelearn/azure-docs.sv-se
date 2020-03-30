---
title: DNS-alias
description: Dina program kan ansluta till ett alias för namnet på din Azure SQL Database-server. Under tiden kan du ändra SQL Database alias pekar på när som helst, för att underlätta testning och så vidare.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820616"
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-alias för Azure SQL Database

Azure SQL Database har en DNS-server (Domain Name System). PowerShell- och REST-API:er accepterar [anrop för att skapa och hantera DNS-alias](#anchor-powershell-code-62x) för sql database-servernamnet.

Ett *DNS-alias* kan användas i stället för Azure SQL Database-servernamnet. Klientprogram kan använda aliaset i sina anslutningssträngar. DNS-aliaset tillhandahåller ett översättningslager som kan omdirigera klientprogrammen till olika servrar. Detta lager sparar du svårigheterna med att behöva hitta och redigera alla klienter och deras anslutningssträngar.

Vanliga användningsområden för ett DNS-alias är följande fall:

- Skapa ett namn som är lätt att komma ihåg för en Azure SQL Server.
- Under den första utvecklingen kan ditt alias referera till en test-SQL Database-server. När programmet visas kan du ändra aliaset så att det refererar till produktionsservern. Övergången från test till produktion kräver inga ändringar av konfigurationerna flera klienter som ansluter till databasservern.
- Anta att den enda databasen i programmet flyttas till en annan SQL Database-server. Här kan du ändra aliaset utan att behöva ändra konfigurationerna för flera klienter.
- Under ett regionalt avbrott använder du geoåterställning för att återställa databasen i en annan server och region. Du kan ändra ditt befintliga alias så att det pekar på den nya servern så att det befintliga klientprogrammet kan ansluta till det igen. 

## <a name="domain-name-system-dns-of-the-internet"></a>DNS (Domain Name System) på Internet

Internet är beroende av DNS. DNS översätter dina egna namn till namnet på din Azure SQL Database-server.

## <a name="scenarios-with-one-dns-alias"></a>Scenarier med ett DNS-alias

Anta att du behöver byta system till en ny Azure SQL Database-server. Tidigare har du behövt hitta och uppdatera varje anslutningssträng i varje klientprogram. Men nu, om anslutningssträngarna använder ett DNS-alias, måste endast en aliasegenskap uppdateras.

Dns-aliasfunktionen i Azure SQL Database kan hjälpa till i följande scenarier:

### <a name="test-to-production"></a>Testa till produktion

När du börjar utveckla klientprogrammen ber du dem använda ett DNS-alias i sina anslutningssträngar. Du gör egenskaperna för aliaset pekar på en testversion av din Azure SQL Database-server.

Senare när det nya systemet går live i produktion kan du uppdatera egenskaperna för aliaset för att peka på produktions-SQL Database-servern. Ingen ändring av klientprogrammen är nödvändig.

### <a name="cross-region-support"></a>Stöd mellan regioner

En haveriberedskap kan flytta SQL Database-servern till ett annat geografiskt område. För ett system som använde ett DNS-alias kan behovet av att hitta och uppdatera alla anslutningssträngar för alla klienter undvikas. I stället kan du uppdatera ett alias för att referera till den nya SQL Database-servern som nu är värd för databasen.

## <a name="properties-of-a-dns-alias"></a>Egenskaper för ett DNS-alias

Följande egenskaper gäller för varje DNS-alias för SQL Database-servern:

- *Unikt namn:* Varje aliasnamn som du skapar är unikt på alla Azure SQL Database-servrar, precis som servernamn är.
- *Servern krävs:* Det går inte att skapa ett DNS-alias om det inte refererar till exakt en server och servern måste redan finnas. Ett uppdaterat alias måste alltid referera till exakt en befintlig server.
  - När du släpper en SQL Database-server släpper Azure-systemet också alla DNS-alias som refererar till servern.
- *Inte bunden till någon region:* DNS-alias är inte bundna till en region. Alla DNS-alias kan uppdateras för att referera till en Azure SQL Database-server som finns i alla geografiska regioner.
  - Men när du uppdaterar ett alias för att referera till en annan server måste båda servrarna finnas i samma *Azure-prenumeration*.
- *Behörigheter:* Om du vill hantera ett DNS-alias måste användaren ha behörighet *för Serverdeltagare* eller högre. Mer information finns [i Komma igång med rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Hantera DNS-alias

Både REST API:er och PowerShell-cmdletar är tillgängliga så att du kan hantera DNS-aliasen programmässigt.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API:er för hantering av DNS-alias

Dokumentationen för REST-API:erna är tillgänglig nära följande webbplats:

- [REST-API för Azure SQL-databas](https://docs.microsoft.com/rest/api/sql/)

Rest-API:erna kan också ses i GitHub på:

- [Azure SQL Database server, DNS alias REST API:er](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell för hantering av DNS-alias

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

PowerShell-cmdletar är tillgängliga som anropar REST-API:erna.

Ett kodexempel på PowerShell-cmdletar som används för att hantera DNS-alias dokumenteras på:

- [PowerShell för DNS-alias till Azure SQL Database](dns-alias-powershell.md)

De cmdlets som används i kodexemplet är följande:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Skapar ett nytt DNS-alias i Azure SQL Database-tjänstsystemet. Aliaset refererar till Azure SQL Database server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Hämta och lista alla DNS-alias som har tilldelats SQL DB server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Ändrar servernamnet som aliaset är konfigurerat för att referera till, från server 1 till SQL DB server 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Ta bort DNS-aliaset från SQL DB server 2 med hjälp av namnet på aliaset.

## <a name="limitations-during-preview"></a>Begränsningar under förhandsgranskningen

För närvarande har ett DNS-alias följande begränsningar:

- *Fördröjning på upp till 2 minuter:* Det tar upp till 2 minuter innan ett DNS-alias uppdateras eller tas bort.
  - Oavsett en kort fördröjning slutar aliaset omedelbart att hänvisa klientanslutningar till den äldre servern.
- *DNS-sökning:* För tillfället är det enda auktoritära sättet att kontrollera vilken server ett givet DNS-alias refererar till genom att utföra en [DNS-sökning](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _Tabellgranskning stöds inte:_ Du kan inte använda ett DNS-alias på en Azure SQL Database-server som har *tabellgranskning* aktiverad i en databas.
  - Tabellgranskning är inaktuell.
  - Vi rekommenderar att du går vidare till [Blob Auditing](sql-database-auditing.md).

## <a name="related-resources"></a>Relaterade resurser

- [Översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md), inklusive haveriberedskap.

## <a name="next-steps"></a>Nästa steg

- [PowerShell för DNS-alias till Azure SQL Database](dns-alias-powershell.md)
