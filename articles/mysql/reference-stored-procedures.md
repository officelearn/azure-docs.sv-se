---
title: Lagrade procedurer för hantering – Azure Database for MySQL
description: Lär dig vilka lagrade procedurer i Azure Database for MySQL är användbara för att hjälpa dig att konfigurera datareplikering, ställa in timezone-och Kill-frågor.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 7ab77f822ace61ccb023dffe6d79fb1d08278d11
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774948"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Lagrade procedurer för Azure Database for MySQL hantering

Lagrade procedurer är tillgängliga på Azure Database for MySQL servrar som hjälper dig att hantera MySQL-servern. Detta omfattar hantering av serverns anslutningar, frågor och konfiguration av Datareplikering.  

## <a name="data-in-replication-stored-procedures"></a>Datareplikering lagrade procedurer

Med datareplikering kan du synkronisera data från en MySQL-server som körs lokalt, på virtuella datorer eller i databastjänster som hanteras av andra molnleverantörer i tjänsten Azure Database for MySQL.

Följande lagrade procedurer används för att ställa in eller ta bort Datareplikering mellan en huvud server och replik.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Användnings notering**|
|-----|-----|-----|-----|
|*MySQL. az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Gäller inte|Överför data med SSL-läge genom att skicka certifikat utfärdarens certifikats kontext till master_ssl_ca-parametern. </br><br>Om du vill överföra data utan SSL skickar du en tom sträng till master_ssl_ca-parametern.|
|*MySQL. az_replication _start*|Gäller inte|Gäller inte|Startar replikering.|
|*MySQL. az_replication _stop*|Gäller inte|Gäller inte|Stoppar replikering.|
|*MySQL. az_replication _remove_master*|Gäller inte|Gäller inte|Tar bort replikeringsrelationen mellan huvud servern och repliken.|
|*MySQL. az_replication_skip_counter*|Gäller inte|Gäller inte|Hoppar över ett replikeringsfel.|

Information om hur du konfigurerar Datareplikering mellan en huvud server och en replik i Azure Database for MySQL finns i så här [konfigurerar du datareplikering](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

Följande lagrade procedurer är tillgängliga i Azure Database for MySQL att hantera servern.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Användnings notering**|
|-----|-----|-----|-----|
|*MySQL. az_kill*|processlist_id|Gäller inte|Motsvarar [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) kommandot. Avslutar anslutningen som är associerad med den angivna processlist_id när du har avslutat en instruktion som anslutningen körs.|
|*MySQL. az_kill_query*|processlist_id|Gäller inte|Motsvarar [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) kommandot. Avslutar instruktionen som anslutningen körs för tillfället. Lämnar själva anslutningen.|
|*MySQL. az_load_timezone*|Gäller inte|Gäller inte|Läser in tids zons tabeller så att parametern `time_zone` kan anges till namngivna värden (t. ex. "USA/Stilla havs området").|

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du konfigurerar [datareplikering](howto-data-in-replication.md)
- Lär dig hur du använder [tids zons tabellerna](howto-server-parameters.md#working-with-the-time-zone-parameter)
