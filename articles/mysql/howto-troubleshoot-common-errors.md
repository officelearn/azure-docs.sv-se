---
title: Felsök vanliga fel – Azure Database for MySQL
description: Lär dig hur du felsöker vanliga fel vid migrering av användare som är nya i den Azure Database for MySQL tjänsten
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: ebe9f936e3d0dfafec23842fcdbfd225995d546b
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720255"
---
# <a name="common-errors"></a>Vanliga fel

Azure Database for MySQL är en fullständigt hanterad tjänst som drivs av community-versionen av MySQL. MySQL-upplevelsen i en hanterad tjänst miljö kan skilja sig från att köra MySQL i din egen miljö. I den här artikeln visas några vanliga fel som användarna kan stöta på när de migrerar till eller utvecklar Azure Database for MySQL tjänsten för första gången.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Fel på grund av brist på SUPER-privilegium och DBA-roll

Rollen SUPER Privilege och DBA stöds inte för tjänsten. Därför kan det uppstå några vanliga fel som anges nedan:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>FEL 1419: du har inte behörigheten SUPER och binär loggning har Aktiver ATS (du *kanske* vill använda den mindre säkra log_bin_trust_function_creators-variabeln)

Ovanstående fel kan inträffa när du skapar en funktion, utlöses enligt nedan eller importerar ett schema. DDL-instruktionerna som skapa-funktion eller skapa utlösare skrivs till den binära loggen så att den sekundära repliken kan köra dem. SQL-tråden för replikering har fullständig behörighet, vilket kan utnyttjas för att höja privilegierna. För att skydda mot den här risken för att servrar som har binär loggning aktive rad kräver MySQL-motorn lagrad funktion skapare måste ha behörigheten SUPER, förutom den vanliga behörigheten Skapa rutin som krävs. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Lösning**: Lös problemet genom att ange log_bin_trust_function_creators till 1 från [Server parametrar](howto-server-parameters.md) -bladet i portalen, köra DDL-instruktionerna eller importera schemat för att skapa önskade objekt och återställa log_bin_trust_function_creators-parametern till det tidigare värdet efter att det har skapats.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>FEL 1227 (42000) på rad 101: åtkomst nekad; du behöver (minst en av) superprivilegierna för den här åtgärden. Åtgärden misslyckades med ExitCode 1

Ovanstående fel kan uppstå när du importerar en dumpfil eller skapar en procedur som innehåller [avfinare](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Lösning**: för att lösa det här felet kan administratörs användaren bevilja behörighet att skapa eller köra procedurer genom att köra kommandot Grant som i följande exempel:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Alternativt kan du ersätta avfinarna med namnet på den administratörs användare som kör import processen som visas nedan.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```

## <a name="next-steps"></a>Nästa steg
Om du inte har hittat det svar du letade efter kan du tänka på följande:
- Publicera din fråga på [Microsoft Q&en fråge sida](https://docs.microsoft.com/answers/topics/azure-database-mysql.html) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Skicka ett e-postmeddelande till Azure Database for MySQLs gruppen [ @Ask Azure dB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Den här e-postadressen är inte ett alias för teknisk support.
- Kontakta Azure-supporten, [File a Ticket from the Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
