---
title: Felsök vanliga anslutningsproblem till Azure SQL Database
description: Steg för att identifiera och lösa vanliga fel för Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: daleche
ms.reviewer: jrasnik
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: d278fd6ed06b58db052154e632e565de36853e77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331442"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Felsöka problem med anslutningen till Azure SQL Database

När anslutningen till Azure SQL Database misslyckas kan du få [felmeddelanden](sql-database-develop-error-messages.md). Den här artikeln är en centraliserad artikel som hjälper dig att felsöka anslutningsproblem för Azure SQL Database. Det introducerar [vanliga orsaker](#cause) av problem med anslutningen rekommenderar [ett verktyg för felsökning](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) som hjälper dig att identitet problemet och ger felsökningssteg för att lösa [tillfälligt fel](#troubleshoot-transient-errors) och [permanenta eller icke tillfälliga fel](#troubleshoot-persistent-errors). 

Om du stöter på problem med anslutningen kan du prova Felsök steg som beskrivs i den här artikeln.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Orsak

Anslutningsproblem kan orsakas av något av följande:

* Det gick inte att tillämpa bästa metoder och designriktlinjer under designprocessen för programmet.  Se [översikt över SQL Database Development](sql-database-develop-overview.md) att komma igång.
* Azure SQL Database omkonfiguration
* Brandväggsinställningar
* Anslutnings-timeout
* Felaktig inloggningsinformation
* Maxgränsen har nåtts för vissa Azure SQL Database-resurser

Problem med anslutningen till Azure SQL Database kan i allmänhet bör klassificeras på följande sätt:

* [Tillfälliga fel (tillfällig eller återkommande)](#troubleshoot-transient-errors)
* [Permanenta eller icke tillfälliga fel (fel regelbundet återkommande)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Försök med felsökaren för Azure SQL Database-anslutningsproblem

Om du får ett särskilt projekt-fel, försök [det här verktyget](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), som kommer få hjälp att du snabbt identifiera och lösa problemet.

## <a name="troubleshoot-transient-errors"></a>Felsök tillfälliga fel

När ett program som ansluter till en Azure SQL database, visas följande felmeddelande visas:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Det här felmeddelandet är vanligen övergående (tillfällig).
> 
> 

Det här felet uppstår när databasen flyttas (eller omkonfigureras) och programmet förlorar anslutningen till databasen. Omkonfiguration databashändelser på grund av en planerad händelse (till exempel programvaruuppgradering) eller en oplanerad händelse (till exempel en processkrasch eller Utjämning av nätverksbelastning). De flesta omkonfiguration händelser är vanligtvis kortlivade och högst bör slutföras i mindre än 60 sekunder. Dessa händelser kan dock ibland ta längre tid att slutföra, till exempel när en stor transaktion gör en tidskrävande återställning.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Steg för att lösa tillfälliga anslutningsproblem

1. Kontrollera den [Microsoft Azure-instrumentpanelen](https://azure.microsoft.com/status) för alla kända avbrott som inträffat under den tid då fel rapporterades av programmet.
2. Program som ansluter till en molntjänst som Azure SQL Database bör förvänta dig periodiska omkonfiguration händelser och implementera logik för omprövning för att hantera de här felen i stället för att visa dessa som programfel för användare. Granska den [tillfälliga fel](sql-database-connectivity-issues.md) avsnittet och bästa metoder och designriktlinjer på [översikt över SQL Database Development](sql-database-develop-overview.md) för mer information och allmänna återförsöksstrategier. Kontrollera i kodexemplen på [anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md) ge specifik information.
3. När en databas närmar sig sin resursbegränsningar, kan det verka vara ett tillfälligt anslutningsproblem. Se [resursgränser](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Om problem med nätverksanslutningen fortsätta, eller om den varaktighet som ditt program påträffar felet överstiger 60 sekunder eller om du ser upprepade förekomster av felet i en viss dag fil en supportförfrågan för Azure genom att välja **få stöd för**på den [Azure-supporten](https://azure.microsoft.com/support/options) plats.

## <a name="troubleshoot-persistent-errors"></a>Felsöka permanenta fel
Om programmet misslyckas med ett beständigt sätt att ansluta till Azure SQL Database, tyder den vanligtvis på ett problem med något av följande:

* Brandväggskonfigurationen. Azure SQL-databas eller klientside-brandväggen blockerar anslutningar till Azure SQL Database.
* Nätverksomkonfigurering på klientsidan: till exempel en ny IP-adress eller en proxyserver.
* Användarfel: till exempel felstavad anslutningsparametrar, som servernamnet i anslutningssträngen.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Steg för att lösa beständiga anslutningsproblem
1. Konfigurera [brandväggsregler](sql-database-configure-firewall-settings.md) som tillåter klientens IP-adress. Konfigurera en brandväggsregel med 0.0.0.0 som det första IP-adressintervallet och använda 255.255.255.255 som det sista IP-adressintervallet för tillfälliga testning. Servern för att alla IP-adresser öppnas. Om det löser problem med nätverksanslutningen, ta bort den här regeln och skapa en brandväggsregel för en korrekt begränsad IP-adressen eller adressintervallet. 
2. Kontrollera att port 1433 är öppen för utgående anslutningar på alla brandväggar mellan klienten och Internet. Granska [konfigurera Windows-brandväggen för att tillåta åtkomst för SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) och [Hybrid Identity portar och protokoll krävs](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) för ytterligare pekare som rör ytterligare portar som du behöver öppna för Azure Active Directory-autentisering.
3. Kontrollera anslutningssträngen och andra anslutningsinställningar. Se avsnittet anslutningssträngen i den [anslutning problem avsnittet](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Kontrollera hälsan hos tjänster i instrumentpanelen. Om du tror att det finns ett regionalt strömavbrott kan du se [återställa från ett avbrott](sql-database-disaster-recovery.md) anvisningar för hur du återställer till en ny region.

## <a name="next-steps"></a>Nästa steg
* [Sök i dokumentationen på Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Visa de senaste uppdateringarna för Azure SQL Database-tjänsten](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Ytterligare resurser
* [Översikt över SQL Database-utveckling](sql-database-develop-overview.md)
* [Allmänna riktlinjer för tillfälliga hantering av fel](../best-practices-retry-general.md)
* [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)

