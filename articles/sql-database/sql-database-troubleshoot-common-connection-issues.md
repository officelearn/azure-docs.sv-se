---
title: Felsök vanliga anslutningsproblem till Azure SQL Database
description: Steg för att identifiera och lösa vanliga anslutningsfel för Azure SQL Database.
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: daleche
ms.openlocfilehash: 2737b641559b04d661db6ede0e487af30b36737a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Felsökning av problem med anslutningen till Azure SQL Database
När det inte går att ansluta till Azure SQL Database, visas [felmeddelanden](sql-database-develop-error-messages.md). Den här artikeln är en centraliserad artikel som hjälper dig att felsöka anslutningsproblem för Azure SQL Database. Det inför [vanliga orsaker](#cause) av anslutningsproblem, rekommenderar [ett verktyg för felsökning](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) som hjälper dig att identiteten problemet och åtgärder för felsökning som löser [tillfälligt fel](#troubleshoot-transient-errors) och [beständiga eller inte är tillfällig fel](#troubleshoot-persistent-errors). 

Om du stöter på problem med anslutningen kan försöka felsöka stegen som beskrivs i den här artikeln.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Orsak
Anslutningsproblem kan orsakas av något av följande:

* Underlåtenhet att tillämpa metodtips och riktlinjer för utformning under designprocessen för programmet.  Se [Utvecklingsöversikt för SQL-databasen](sql-database-develop-overview.md) att komma igång.
* Azure SQL Database omkonfiguration
* Brandväggsinställningar
* Timeout-värde
* Felaktig inloggningsinformation
* Högsta gränsen på vissa Azure SQL Database-resurser

I allmänhet kan problem med anslutningen till Azure SQL Database klassificeras enligt följande:

* [Tillfälligt fel (tillfällig eller återkommande)](#troubleshoot-transient-errors)
* [Beständiga eller inte är tillfällig fel (fel regelbundet återkommande)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Försök med felsökaren för Azure SQL Database-anslutningsproblem
Om du stöter på en specifik anslutningsfel, försök [verktyget](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), som kommer att du snabbt identitet och lösa problemet.

## <a name="troubleshoot-transient-errors"></a>Felsök tillfälliga fel

När ett program ansluter till en Azure SQL database, visas följande felmeddelande:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Det här felmeddelandet är vanligen övergående (tillfällig).
> 
> 

Felet uppstår när Azure-databas håller på att flytta (eller omkonfigureras) och ditt program förlorar sin anslutning till SQL-databasen. SQL-databas omkonfiguration händelser inträffar på grund av en planerad åtgärd (till exempel programuppdatering) eller en oväntad händelse (till exempel en process krasch eller belastningsutjämning). De flesta omkonfiguration händelser är vanligtvis tillfällig och högst ska slutföras på mindre än 60 sekunder. Dessa händelser kan emellertid ibland ta längre tid att slutföra, till exempel när en stor transaktion medför en tidskrävande återställning.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Åtgärder för att lösa tillfälligt anslutningsproblem

1. Kontrollera den [Microsoft Azure Service instrumentpanelen](https://azure.microsoft.com/status) för alla kända avbrott som har inträffat under den tid under vilken fel rapporterades av programmet.
2. Program som ansluter till en molntjänst som Azure SQL Database ska förvänta sig periodiska omkonfiguration händelser och implementera försök logik för att hantera de här felen i stället för att visa dessa som programfel för användare. Granska de [tillfälliga fel](sql-database-connectivity-issues.md) avsnittet och metodtips och riktlinjer för utformning på [Utvecklingsöversikt för SQL-databasen](sql-database-develop-overview.md) försök strategier för mer information och allmänna. Kontrollera kodexempel på [anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md) för specifik information.
3. När en databas närmar sig gränsen resurs, kan det verka vara ett tillfälligt anslutningsproblem. Se [felsökning av prestandaproblem med](sql-database-troubleshoot-performance.md).
4. Om problem med nätverksanslutningen fortsätta, eller om den varaktighet som ditt program lider av felet är längre än 60 sekunder eller om du ser flera förekomster av felet under en viss dag, fil en Azure-supportbegäran genom att välja **få stöd för**på den [Azure-supporten](https://azure.microsoft.com/support/options) plats.

## <a name="troubleshoot-persistent-errors"></a>Felsöka beständiga
Om programmet beständigt misslyckas med att ansluta till Azure SQL Database anger vanligtvis ett problem med något av följande:

* Brandväggskonfigurationen. Azure SQL database eller klientsidan brandväggen blockerar anslutningar till Azure SQL Database.
* Nätverk omkonfiguration på klientsidan: till exempel en ny IP-adress eller en proxyserver.
* Fel: till exempel skrev du fel anslutningsparametrar, till exempel servernamnet i anslutningssträngen.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Åtgärder för att lösa problem med nätverksanslutningen beständiga
1. Ställ in [regler i brandväggen](sql-database-configure-firewall-settings.md) som tillåter klientens IP-adress. För tillfällig testning, ställa in en brandväggsregel med 0.0.0.0 som det första IP-adressintervallet och använda 255.255.255.255 som det sista IP-adressintervallet. Då öppnas server till alla IP-adresser. Om det löser problemet med anslutningen, ta bort den här regeln och skapa en brandväggsregel för en korrekt begränsade IP-adressen eller adressintervallet. 
2. Kontrollera att port 1433 är öppen för utgående anslutningar på alla brandväggar mellan klienten och Internet. Granska [konfigurera Windows-brandväggen att tillåta åtkomst för SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) och [Hybrid Identity krävs portar och protokoll](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) för ytterligare Pekare rör ytterligare portar som behövs för att öppna för Azure Active Directory-autentisering.
3. Kontrollera anslutningssträngen och andra inställningar. Se avsnittet anslutningssträngen i den [anslutning problem avsnittet](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Kontrollera tjänstens hälsa i instrumentpanelen. Om du tror att det finns ett regionalt strömavbrott, se [återställa från avbrott](sql-database-disaster-recovery.md) steg för att återställa till en ny region.

## <a name="next-steps"></a>Nästa steg
* [Felsökning av problem med Azure SQL Database prestanda](sql-database-troubleshoot-performance.md)
* [Sök i dokumentationen på Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Visa de senaste uppdateringarna till Azure SQL Database-tjänsten](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Ytterligare resurser
* [Översikt över SQL Database-utveckling](sql-database-develop-overview.md)
* [Allmänna riktlinjer för tillfälliga fel-hantering](../best-practices-retry-general.md)
* [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)

