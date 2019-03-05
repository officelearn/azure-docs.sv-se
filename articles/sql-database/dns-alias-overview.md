---
title: DNS-alias för Azure SQL Database | Microsoft Docs
description: Dina program kan ansluta till ett alias för namnet på din Azure SQL Database-server. Under tiden kan ändra du SQL-databasen aliaset pekar på när som helst, för att underlätta testning och så vidare.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: genemi,ayolubek, jrasnick
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: dff0f9eae28e38b7049a51b1cb873991cc41999f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310224"
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-alias för Azure SQL Database

Azure SQL Database har en Domain Name System (DNS)-server. PowerShell och REST API: er acceptera [anrop för att skapa och hantera DNS-alias](#anchor-powershell-code-62x) för din SQL Database-servernamn.

En *DNS-alias* kan användas i stället för Azure SQL Database-servernamn. Klientprogram kan använda detta alias i sina anslutningssträngar. DNS innebär alias en översättning som kan dirigera dina klientprogram till olika servrar. Det här lagret besparar problem att hitta och redigera alla klienter och deras anslutningssträngar.

Vanliga användningsområden för en DNS-alias är följande fall:

- Skapa ett enkelt sätt att komma ihåg namn för en Azure SQL Server.
- Under inledande utvecklingsarbete kan ditt alias referera till ett test SQL Database-server. När programmet lanseras, kan du ändra alias att referera till produktionsservern. Övergången från test till produktion kräver inte någon ändring av konfigurationer flera klienter som ansluter till databasservern.
- Anta att databasen i ditt program har flyttats till en annan SQL Database-server. Här kan du ändra alias utan att behöva ändra konfigurationerna för flera klienter.

## <a name="domain-name-system-dns-of-the-internet"></a>Domain Name System (DNS) över Internet

Internet är beroende av DNS. DNS omvandlar dina egna namn till namnet på din Azure SQL Database-server.

## <a name="scenarios-with-one-dns-alias"></a>Scenarier med en DNS-alias

Anta att du behöver växla systemet till en ny Azure SQL Database-server. Tidigare behövde du hitta och uppdatera varje anslutningssträngen i varje klientprogram. Men nu om anslutningssträngar för att använda en DNS-alias, endast en alias-egenskap måste uppdateras.

DNS-alias-funktionen i Azure SQL Database hjälper i följande scenarier:

### <a name="test-to-production"></a>Testa i produktion

När du börja utveckla program för klienter, har du dem använda en DNS-alias i sina anslutningssträngar. Du gör egenskaperna punktens alias i en testversion av Azure SQL Database-servern.

Senare när det nya systemet slutar live i produktionen kan du uppdatera egenskaperna för ett alias så att den pekar till produktion SQL Database-server. Ingen förändring av klientprogrammen är nödvändigt.

### <a name="cross-region-support"></a>Stöd för över flera regioner

En katastrofåterställning kan flytta din SQL Database-server till en annan geografisk region. För ett system med hjälp av ett DNS-alias kan behovet av att hitta och uppdatera alla anslutningssträngarna för alla klienter undvikas. I stället kan du uppdatera ett alias för att referera till den nya SQL Database-server som nu är värd för databasen.

## <a name="properties-of-a-dns-alias"></a>Egenskaperna för en DNS-alias

Följande egenskaper gäller för varje DNS-alias för din SQL Database-server:

- *Unikt namn:* Varje aliasnamn som du skapar är unikt för alla Azure SQL Database-servrar, precis som servernamn.
- *Server krävs:* En DNS kan alias inte skapas, såvida inte den refererar till exakt en server och servern måste redan finnas. Uppdaterade alias måste alltid referera till exakt en befintlig server.
  - När du släpper en SQL-databasserver, utelämnar Azure systemet även alla DNS-alias som refererar till servern.
- *Inte har bundits till alla regioner:* DNS-alias är inte bunden till en region. Andra DNS-alias kan uppdateras för att referera till en Azure SQL Database-server som finns i alla geografiska regioner.
  - Men när du uppdaterar ett alias för att referera till en annan server måste båda servrarna måste finnas i samma Azure *prenumeration*.
- *Behörigheter:* För att hantera ett DNS-alias, måste användaren ha *Server-deltagare* behörigheter, eller högre. Mer information finns i [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Hantera dina DNS-alias

Både REST API: er och PowerShell-cmdlets är tillgängliga för att programmässigt hantera dina DNS-alias.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API: er för att hantera dina DNS-alias

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

Dokumentation för REST-API: er finns nära följande webbplats:

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/)

REST-API: er kan också ses i GitHub vid:

- [Azure SQL Database-server, DNS-alias REST API: er](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell för att hantera dina DNS-alias

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell-cmdlets är tillgängliga som anropar REST-API: er.

Ett exempel på PowerShell-cmdletar som används för att hantera DNS-alias är dokumenterade på:

- [PowerShell för DNS-Alias till Azure SQL Database](dns-alias-powershell.md)

De cmdletar som används i kodexempel är följande:

- [New-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias?view=azurermps-5.1.1): Skapar en ny DNS-alias i systemet för Azure SQL Database-tjänsten. Aliaset som refererar till Azure SQL Database-server 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias?view=azurermps-5.1.1): Hämta och visa alla DNS-alias som är kopplade till SQL DB server 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias?view=azurermps-5.1.1): Ändrar servernamnet som alias har konfigurerats för att referera till, från 1 till SQL DB server 2-servern.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias?view=azurermps-5.1.1): Ta bort DNS-alias från SQL DB server 2, med hjälp av namnet på aliaset.

## <a name="limitations-during-preview"></a>Begränsningar i förhandsversionen

För närvarande kan har en DNS alias följande begränsningar:

- *Fördröjning på upp till 2 minuter:* Det tar upp till 2 minuter för en DNS-alias uppdateras eller tas bort.
  - Oavsett eventuell fördröjning av kort stoppar aliaset omedelbart refererar klientanslutningar till den gamla servern.
- *DNS-sökning:* För tillfället endast auktoritativa sätt att kontrollera vilken server som en viss DNS alias refererar till är genom att utföra en [DNS-sökning](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- *[Tabellgranskning stöds inte](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* Du kan inte använda en DNS-alias på en Azure SQL Database-server som har *tabellgranskning* aktiverats för en databas.
  - Tabellgranskning är inaktuell.
  - Vi rekommenderar att du flyttar till [Blobbgranskning](sql-database-auditing.md).

## <a name="related-resources"></a>Relaterade resurser

- [Översikt över affärskontinuitet med Azure SQL Database](sql-database-business-continuity.md), inklusive haveriberedskap.

## <a name="next-steps"></a>Nästa steg

- [PowerShell för DNS-Alias till Azure SQL Database](dns-alias-powershell.md)
