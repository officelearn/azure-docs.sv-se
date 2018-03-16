---
title: "DNS-alias för Azure SQL Database | Microsoft Docs"
description: "Dina program kan ansluta till ett alias för namnet på din Azure SQL Database-server. Under tiden kan du ändra SQL-databasen aliaset pekar till när som helst, för att underlätta testning och så vidare."
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: DNS alias
ms.topic: article
ms.date: 02/05/2018
ms.reviewer: genemi;ayolubek
ms.author: dmalik
ms.openlocfilehash: c2a2bce3b385bd5700bf327f283289cf4d94b057
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="dns-alias-for-azure-sql-database"></a>DNS-alias för Azure SQL Database

Azure SQL-databas har en Domain Name System (DNS)-server. Använda PowerShell eller REST API: er [anrop för att skapa och hantera DNS-alias](#anchor-powershell-code-62x) för namnet på SQL Database-servern.

En *DNS-alias* kan användas i stället för Azure SQL Database-servernamn. Klientprogram kan använda detta alias i sina anslutningssträngar. DNS innebär alias en översättning som kan dirigera dina klientprogram till olika servrar. Det här lagret besparar problem att söka efter och redigera alla klienter och deras anslutningssträngar.

Vanliga användningsområden för ett DNS-alias är följande fall:

- Skapa en lätt att komma ihåg namnet för en Azure SQL Server.
- Under inledande utvecklingsarbete kan ditt alias referera till ett test SQL Database-server. När programmet lanseras, kan du ändra alias att referera till produktionsservern. Övergången från test till produktion kräver inte några andra ändringar i konfigurationen för flera klienter som ansluter till databasservern.
- Anta att databasen i ditt program har flyttats till en annan SQL Database-server. Här kan du ändra alias utan att behöva ändra konfigurationer av flera klienter.

#### <a name="domain-name-system-dns-of-the-internet"></a>Domain Name System (DNS) på Internet

Internet är beroende av DNS. DNS översätter dina egna namn till namnet på din Azure SQL Database-server.

## <a name="scenarios-with-one-dns-alias"></a>Scenarier med en DNS-alias

Anta att du måste växla systemet till en ny Azure SQL Database-server. I förflutna behov att hitta och uppdatera varje anslutningssträngen i varje klientprogram. Men nu om anslutningssträngar för att använda en DNS-alias, bara en alias-egenskap måste uppdateras.

Funktionen DNS-alias i Azure SQL Database hjälper i följande scenarier:

#### <a name="test-to-production"></a>Test till produktion

När du börjar utveckla program för klienter, har de använder en DNS-alias i sina anslutningssträngar. Du egenskaperna för punkten som alias för en testversion av din Azure SQL Database-server.

Senare när det nya systemet lanseras i produktion, du kan uppdatera egenskaperna för alias att peka till SQL Database produktionsservern. Ingen ändring på klientprogram är nödvändig.

#### <a name="cross-region-support"></a>Stöd för Cross-region

En katastrofåterställning kan flytta SQL Database-server till en annan geografisk region. För ett system än använde ett DNS-alias kan behovet av att hitta och uppdatera alla anslutningssträngar för alla klienter undvikas. I stället kan du uppdatera ett alias att referera till den nya SQL Database-server som värd för din databas.




## <a name="properties-of-a-dns-alias"></a>Egenskaper för ett DNS-alias

Följande egenskaper gäller för varje DNS-alias för SQL Database-server:

- *Unikt namn:* varje aliasnamn som du skapar är unikt över alla Azure SQL Database-servrar, precis som server namn är.

- *Server krävs:* A DNS alias kan inte skapas om den refererar till exakt en server och servern måste redan finnas. Uppdaterade alias måste alltid referera till exakt en befintlig server.
    - När du släpper en SQL-databasserver, släpper Azure systemet även alla DNS-alias som refererar till servern.

- *Inte har bundits till en region:* DNS-alias är inte bunden till en region. DNS-alias kan uppdateras för att referera till en Azure SQL Database-server som finns i en geografisk region.
    - Men när du uppdaterar ett alias att referera till en annan server, båda servrarna måste finnas i samma Azure *prenumeration*.

- *Behörigheter:* för att hantera ett DNS-alias, måste användaren ha *Server deltagare* behörigheter, eller högre. Mer information finns i [Kom igång med rollbaserad åtkomstkontroll i Azure portal](../active-directory/role-based-access-control-what-is.md).





## <a name="manage-your-dns-aliases"></a>Hantera DNS-alias

Både REST API: er och PowerShell-cmdlets är tillgängliga så att du kan hantera programmässigt DNS-alias.

#### <a name="rest-apis-for-managing-your-dns-aliases"></a>REST API: er för att hantera dina DNS-alias

<!-- TODO
??2 "soon" in the following live sentence, is not the best situation.
TODO update this subsection very soon after REST API docu goes live.
Dev = Magda Bojarska
Comment as of:  2018-01-26
-->

I dokumentationen för REST-API: er är nära följande webbplats:
- [Azure SQL Database REST-API](https://docs.microsoft.com/rest/api/sql/)

REST-API: er kan också ses i GitHub på:
- [Azure SQL Database-server, DNS-alias REST API: er](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell för att hantera dina DNS-alias

PowerShell-cmdlets är tillgängliga som anropa REST-API: er.

Ett exempel på PowerShell-cmdlets som används för att hantera DNS-alias dokumenteras på:
- [PowerShell för DNS-Alias till Azure SQL-databas](dns-alias-powershell.md)


De cmdletar som används i kodexempel är följande:
- [Nya AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/New-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): skapar en ny DNS-alias i systemet för Azure SQL Database-tjänsten. Aliaset som refererar till Azure SQL Database server 1.
- [Get-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): hämta och visa en lista över alla DNS-alias som är tilldelade till SQL DB-server 1.
- [Set-AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Set-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): ändrar namnet på servern som alias har konfigurerats för att referera till, från server 1 till SQL DB-server 2.
- [Ta bort AzureRMSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Remove-AzureRmSqlServerDnsAlias?view=azurermps-5.1.1): ta bort DNS-alias från SQL DB-server 2, med hjälp av namnet på aliaset.


Tidigare cmdletar har lagts till i den **AzureRM.Sql** modulen startar med Modulversion 5.1.1.




## <a name="limitations-during-preview"></a>Begränsningar under förhandsgranskning

För närvarande kan har en DNS alias följande begränsningar:

- *Fördröjning på upp till 2 minuter:* tar upp till 2 minuter för en DNS-alias uppdateras eller tas bort.
    - Oavsett en kort fördröjning stoppar alias omedelbart hänvisar klientanslutningar till den gamla servern.

- *DNS-sökning:* för närvarande endast auktoritära sätt att kontrollera vilken server som en given DNS alias refererar till är genom att utföra en [DNS-sökning](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).

- *[Tabell granskning stöds inte](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md):* du kan inte använda en DNS-alias på en Azure SQL Database-server som har *tabell granskning* aktiverad på en databas.
    - Tabell granskning är föråldrad.
    - Vi rekommenderar att du flyttar till [Blobbgranskning](sql-database-auditing.md).




## <a name="related-resources"></a>Relaterade resurser

- [Översikt över verksamhetskontinuitet med Azure SQL Database](sql-database-business-continuity.md), inklusive katastrofåterställning.



## <a name="next-steps"></a>Nästa steg

- [PowerShell för DNS-Alias till Azure SQL-databas](dns-alias-powershell.md)

