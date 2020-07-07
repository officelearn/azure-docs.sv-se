---
title: Lagrade procedurer för hantering – Azure Database for MySQL
description: Lär dig vilka lagrade procedurer i Azure Database for MySQL är användbara för att hjälpa dig att konfigurera datareplikering, ställa in timezone-och Kill-frågor.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 6a3fa40eaae174d3616fd0318f81576b7c59eac7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80067702"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Lagrade procedurer för Azure Database for MySQL hantering

Lagrade procedurer är tillgängliga på Azure Database for MySQL servrar som hjälper dig att hantera MySQL-servern. Detta omfattar hantering av serverns anslutningar, frågor och konfiguration av Datareplikering.  

## <a name="data-in-replication-stored-procedures"></a>Datareplikering lagrade procedurer

Med datareplikering kan du synkronisera data från en MySQL-server som körs lokalt, på virtuella datorer eller i databastjänster som hanteras av andra molnleverantörer i tjänsten Azure Database for MySQL.

Följande lagrade procedurer används för att ställa in eller ta bort Datareplikering mellan en huvud server och replik.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Användnings notering**|
|-----|-----|-----|-----|
|*MySQL. az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Saknas|Överför data med SSL-läge genom att skicka certifikat utfärdarens certifikats kontext till master_ssl_ca-parametern. </br><br>Om du vill överföra data utan SSL skickar du en tom sträng till master_ssl_ca-parametern.|
|*MySQL. az_replication _start*|Saknas|Saknas|Startar replikering.|
|*MySQL. az_replication _stop*|Saknas|Saknas|Stoppar replikering.|
|*MySQL. az_replication _remove_master*|Saknas|Saknas|Tar bort replikeringsrelationen mellan huvud servern och repliken.|
|*MySQL. az_replication_skip_counter*|Saknas|Saknas|Hoppar över ett replikeringsfel.|

Information om hur du konfigurerar Datareplikering mellan en huvud server och en replik i Azure Database for MySQL finns i så här [konfigurerar du datareplikering](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

Följande lagrade procedurer är tillgängliga i Azure Database for MySQL att hantera servern.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Användnings notering**|
|-----|-----|-----|-----|
|*MySQL. az_kill*|processlist_id|Saknas|Motsvarande [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) kommando. Avslutar anslutningen som är associerad med den angivna processlist_id när du har avslutat en instruktion som anslutningen körs.|
|*MySQL. az_kill_query*|processlist_id|Saknas|Motsvarande [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) kommando. Avslutar instruktionen som anslutningen körs för tillfället. Lämnar själva anslutningen.|
|*MySQL. az_load_timezone*|Saknas|Saknas|Läser in tids zons tabeller så att `time_zone` parametern kan anges till namngivna värden (t. ex. "USA/Stilla havs området").|

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du konfigurerar [datareplikering](howto-data-in-replication.md)
- Lär dig hur du använder [tids zons tabellerna](howto-server-parameters.md#working-with-the-time-zone-parameter)
