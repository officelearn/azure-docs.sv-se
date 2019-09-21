---
title: Azure Database for MariaDB lagrade procedurer
description: Den här artikeln beskriver lagrade procedurer som är speciella för Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: d9daaf619a19c0f4e4a591d4bbb4925679fd1fcb
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174908"
---
# <a name="azure-database-for-mariadb-stored-procedures"></a>Azure Database for MariaDB lagrade procedurer

Lagrade procedurer finns på Azure Database for MariaDB-servrar för att hantera din MariaDB-Server. Detta omfattar hantering av serverns anslutningar, frågor och konfiguration av Datareplikering.  

## <a name="data-in-replication-stored-procedures"></a>Datareplikering lagrade procedurer

Med datareplikering kan du synkronisera data från en MariaDB-server som körs lokalt, på virtuella datorer eller i databastjänster som hanteras av andra molnleverantörer i tjänsten Azure Database for MariaDB-tjänsten.

Följande lagrade procedurer används för att ställa in eller ta bort Datareplikering mellan en huvud server och replik.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Användnings notering**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Gäller inte|Överför data med SSL-läge genom att skicka certifikat utfärdarens certifikats kontext till master_ssl_ca-parametern. </br><br>Om du vill överföra data utan SSL skickar du en tom sträng till parametern master_ssl_ca.|
|*mysql.az_replication _start*|Gäller inte|Gäller inte|Startar replikering.|
|*mysql.az_replication _stop*|Gäller inte|Gäller inte|Stoppar replikering.|
|*mysql.az_replication _remove_master*|Gäller inte|Gäller inte|Tar bort replikeringsrelationen mellan huvud servern och repliken.|
|*mysql.az_replication_skip_counter*|Gäller inte|Gäller inte|Hoppar över ett replikeringsfel.|

Information om hur du konfigurerar Datareplikering mellan en huvud server och en replik i Azure Database for MariaDB finns i så här [konfigurerar du datareplikering](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

Följande lagrade procedurer är tillgängliga i Azure Database for MariaDB att hantera servern.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Användnings notering**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|Gäller inte|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Motsvarande kommando. Avslutar anslutningen som är associerad med den angivna processlist_id när du har avslutat en instruktion som anslutningen körs.|
|*MySQL. AZ _kill_query*|processlist_id|Gäller inte|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Motsvarande kommando. Avslutar instruktionen som anslutningen körs för tillfället. Lämnar själva anslutningen.|
|*MySQL. AZ _load_timezone*|Gäller inte|Gäller inte|Läser in tids zons tabeller så `time_zone` att parametern kan anges till namngivna värden (t. ex. "USA/Stilla havs området").|

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du konfigurerar [datareplikering](howto-data-in-replication.md)
- Lär dig hur du använder [tids zons tabellerna](howto-server-parameters.md#working-with-the-time-zone-parameter)