---
title: Felsök vanliga anslutningsproblem till Azure SQL Database
description: Steg för att identifiera och lösa vanliga anslutnings fel för Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
manager: dcscontentpm
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 01/25/2019
ms.openlocfilehash: cd0ab6d89d88c594d283dc0718c0f58ebb98bf43
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090802"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Felsöka anslutnings problem till Azure SQL Database

När anslutningen till Azure SQL Database Miss lyckas får du [fel meddelanden](sql-database-develop-error-messages.md). Den här artikeln är ett centraliserat avsnitt som hjälper dig att felsöka problem med Azure SQL Database-anslutningen. Den introducerar [vanliga orsaker](#cause) till anslutnings problem, rekommenderar [ett fel söknings verktyg](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) som hjälper dig att identifiera problemet och ger fel söknings steg för att lösa [tillfälliga fel](#troubleshoot-transient-errors) och [beständiga eller icke-tillfälliga fel ](#troubleshoot-persistent-errors). 

Om du stöter på anslutnings problem kan du prova med fel söknings stegen som beskrivs i den här artikeln.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Orsak

Anslutnings problem kan ha orsakats av något av följande:

* Det gick inte att tillämpa metod tips och design rikt linjer under programmets design process.  Kom igång genom att se [SQL Database utvecklings översikt](sql-database-develop-overview.md) .
* Azure SQL Database omkonfiguration
* Brandväggsinställningar
* Anslutnings-timeout
* Felaktig inloggnings information
* Max gränsen har nåtts för vissa Azure SQL Database resurser

I allmänhet kan anslutnings problem till Azure SQL Database klassificeras på följande sätt:

* [Tillfälliga fel (kort livs längd eller intermittent)](#troubleshoot-transient-errors)
* [Beständiga eller icke-tillfälliga fel (fel som upprepas regelbundet)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Prova fel sökaren för Azure SQL Database anslutnings problem

Om du stöter på ett särskilt anslutnings fel kan du försöka med [verktyget](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), som hjälper dig att snabbt identifiera och lösa ditt problem.

## <a name="troubleshoot-transient-errors"></a>Felsök tillfälliga fel

När ett program ansluter till en Azure SQL-databas visas följande fel meddelande:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Det här fel meddelandet är vanligt vis tillfälligt (kort livs längd).
> 
> 

Felet uppstår när databasen flyttas (eller konfigureras om) och programmet förlorar anslutningen till databasen. Händelser vid databas konfiguration inträffar på grund av en planerad händelse (till exempel en program uppgradering) eller en oplanerad händelse (till exempel en process krasch eller belastnings utjämning). De flesta omkonfigurations händelser är vanligt vis korta och bör slutföras på mindre än 60 sekunder. Dessa händelser kan dock ibland ta längre tid att slutföra, till exempel när en stor transaktion orsakar en tids krävande återställning.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Steg för att lösa problem med tillfälliga anslutningar

1. Kontrol lera [instrument panelen för Microsoft Azures tjänsten](https://azure.microsoft.com/status) för eventuella kända avbrott som inträffat under den tid då felen rapporterades av programmet.
2. Program som ansluter till en moln tjänst som Azure SQL Database bör förvänta sig regelbunden omkonfiguration av händelser och implementera omprövnings logik för att hantera dessa fel i stället för att visa dem som program fel till användare. Läs avsnittet om [tillfälliga fel](sql-database-connectivity-issues.md) och de bästa metoderna och design rikt linjerna i [SQL Database utvecklings översikt](sql-database-develop-overview.md) för mer information och allmänna metoder för återförsök. Se sedan kod exempel i [anslutnings bibliotek för SQL Database och SQL Server](sql-database-libraries.md) för information.
3. När en databas närmar sig resurs gränserna kan det verka som ett tillfälligt anslutnings problem. Se [resurs begränsningar](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Om problem med anslutningen fortsätter, eller om varaktigheten för programmet stöter på felet överskrider 60 sekunder eller om du ser flera förekomster av felet under en specifik dag, kan du skicka en support förfrågan till Azure genom att välja **få support** på [Azure Support](https://azure.microsoft.com/support/options) webbplats.

## <a name="troubleshoot-persistent-errors"></a>Felsöka beständiga fel
Om programmet inte kan ansluta till Azure SQL Database, indikerar det vanligt vis ett problem med något av följande:

* Brand Väggs konfiguration. Azure SQL Database eller klients IDE brand väggen blockerar anslutningar till Azure SQL Database.
* Omkonfiguration av nätverk på klient sidan: till exempel en ny IP-adress eller proxyserver.
* Användar fel: till exempel felskrivna anslutnings parametrar, t. ex. Server namnet i anslutnings strängen.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Steg för att lösa problem med beständiga anslutningar
1. Konfigurera [brand Väggs regler](sql-database-configure-firewall-settings.md) som tillåter KLIENTens IP-adress. För tillfälliga test ändamål ställer du in en brand Väggs regel med 0.0.0.0 som första IP-adressintervall och använder 255.255.255.255 som sista IP-adressintervall. Då öppnas servern med alla IP-adresser. Om detta löser problemet med anslutningen tar du bort den här regeln och skapar en brand Väggs regel för en lämplig, begränsad IP-adress eller ett IP-adressintervall. 
2. Kontrol lera att port 1433 är öppen för utgående anslutningar på alla brand väggar mellan klienten och Internet. Granska [Konfigurera Windows-brandväggen för att tillåta SQL Server åtkomst](https://msdn.microsoft.com/library/cc646023.aspx) och [hybrid identitet krävs portar och protokoll](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) för ytterligare pekare som är relaterade till ytterligare portar som du måste öppna för Azure Active Directory autentisering.
3. Kontrol lera anslutnings strängen och andra anslutnings inställningar. Se avsnittet anslutnings sträng i [avsnittet anslutnings problem](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Kontrol lera tjänstens hälso tillstånd på instrument panelen. Om du tror att det finns ett regionalt avbrott kan du läsa mer i [återställa från ett avbrott](sql-database-disaster-recovery.md) för att återställa till en ny region.

## <a name="next-steps"></a>Nästa steg
* [Sök i dokumentationen om Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Visa de senaste uppdateringarna för tjänsten Azure SQL Database](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Ytterligare resurser
* [Översikt över SQL Database utveckling](sql-database-develop-overview.md)
* [Allmän vägledning om tillfälligt fel hantering](../best-practices-retry-general.md)
* [Anslutnings bibliotek för SQL Database och SQL Server](sql-database-libraries.md)

