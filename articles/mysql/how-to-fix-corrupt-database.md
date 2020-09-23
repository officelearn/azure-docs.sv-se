---
title: Lös skadad databas – Azure Database for MySQL
description: Läs mer om hur du åtgärdar problem med databas skada för Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941488"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Felsöka databas fel på Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Skadade databaser kan orsaka drift stopp för ditt program och det är också viktigt att lösa problemet i tid för att undvika data förlust. När databasen skadas visas det här felet InnoDB i servern: fel **på databas sidan på disken eller en misslyckad**.

I den här guiden får du lära dig hur du åtgärdar om databasen eller tabellen är skadad. Azure Database for MySQL använder InnoDB-motorn och den automatiserar fel söknings-och reparations åtgärder. InnoDB söker efter skadade sidor genom att utföra kontroll summor på varje sida den läser, och om den hittar en avvikelse i kontroll summan stoppas automatiskt MySQL-servern.

Prova något av de här alternativen nedan för att hjälpa dig att snabbt minimera problem med databas fel.

## <a name="restart-your-mysql-server"></a>Starta om MySQL-servern

Du ser vanligt vis att en databas eller tabell är skadad när programmet har åtkomst till tabellen ro-databas. Eftersom InnoDB innehåller en funktion för krasch återställning som kan lösa de flesta problem när servern startas om. Omstart av servern bör därför hjälpa servern att återställas från en krasch som gjorde att databasen är i ett felaktigt tillstånd.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Lösa datafel med dump och Restore-metod

Vi rekommenderar att du löser problemet med en dump- **och Restore-metod**. Detta innebär att få åtkomst till den skadade tabellen med hjälp av **mysqldump** -verktyget för att skapa en logisk säkerhets kopia av tabellen, vilket behåller tabell strukturen och data som finns i den, och sedan laddar om tabellen till databasen igen.

### <a name="backup-your-database-or-tables"></a>Säkerhetskopiera databasen eller tabellerna

> [!Important]
> - Gör så att du har konfigurerat en brand Väggs regel för att få åtkomst till servern från klient datorn. Se Konfigurera [brand Väggs regel på en enskild server](howto-manage-firewall-using-portal.md) och [brand Väggs regel på en flexibel Server](flexible-server/how-to-connect-tls-ssl.md).
> - Använd SSL-alternativet ```--ssl-cert``` för **MYSQLDUMP** om SSL är aktiverat

Skapa en säkerhets kopierings fil från kommando raden med mysqldump med det här kommandot

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

De parametrar som ska tillhandahållas är:
- [ssl-cert =/path/to/PEM] Ladda ned SSL-certifikatet på klient datorn och ange sökvägen i kommandot. Använd inte det här är SSL har inaktiverats.
- [värd] är din Azure Database for MySQL-Server
- [uname] är ditt användar namn för Server administratör
- [pass] är lösen ordet för din administratörs användare
- [dbname] är namnet på din databas
- [BackupFile. SQL] om fil namnet för säkerhets kopian av databasen

> [!Important]
> - Använd det format ```admin-user@servername``` som ska ersättas ```myserveradmin``` i kommandona nedan för en enskild server.
> - För flexibel Server använder du det format ```admin-user``` som ska ersättas ```myserveradmin``` i kommandona nedan.

Om en enskild tabell är skadad väljer du sedan vissa tabeller i databasen för att säkerhetskopiera med hjälp av det här exemplet
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Om du vill säkerhetskopiera en eller flera databaser använder du växeln--Database och listar databas namnen avgränsade med blank steg.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Återställa databasen eller tabellerna

I följande steg visas hur du återställer databasen eller tabellerna i TP. När säkerhets kopierings filen har skapats kan du återställa tabellen eller databaserna med hjälp av ***MySQL** -verktyget. Kör kommandot på det sätt som visas nedan:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Här är ett exempel på återställnings ```testdb``` filen från säkerhets kopian som skapats med **mysqldump**. 

> [!Important]
> - Använd det format ```admin-user@servername``` som ska ersättas i kommandot nedan för en enskild server ```myserveradmin``` .
> - För flexibel Server använder du det format ```admin-user``` som ska ersättas ```myserveradmin``` i kommandot nedan. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Efterföljande moment
Om stegen ovan inte hjälper att lösa problemet kan du alltid återställa hela servern.
- [Återställa en Azure Database for MySQL enskild server](howto-restore-server-portal.md)
- [Återställa en Azure Database for MySQL flexibel Server](flexible-server/how-to-restore-server-portal.md)



