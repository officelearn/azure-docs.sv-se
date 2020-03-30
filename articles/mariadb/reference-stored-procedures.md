---
title: Hantera lagrade procedurer - Azure Database för MariaDB
description: Lär dig vilka lagrade procedurer i Azure Database för MariaDB som är användbara för att hjälpa dig att konfigurera data-in-replikering, ange tidszon och döda frågor.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2f6d1e20db64cb0c2a64771ea26b971b22031fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529998"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Azure Database för MariaDB-hanterings lagrade procedurer

Lagrade procedurer är tillgängliga på Azure Database för MariaDB-servrar för att hantera din MariaDB-server. Detta inkluderar hantering av serverns anslutningar, frågor och inställning av data-in Replication.  

## <a name="data-in-replication-stored-procedures"></a>Lagrade datainsamlingsbaserade procedurer

Med datareplikering kan du synkronisera data från en MariaDB-server som körs lokalt, på virtuella datorer eller i databastjänster som hanteras av andra molnleverantörer i tjänsten Azure Database for MariaDB-tjänsten.

Följande lagrade procedurer används för att ställa in eller ta bort data-in Replication mellan en huvud- och replik.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Anm.-**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Ej tillämpligt|Om du vill överföra data med SSL-läge skickar du in ca-certifikatets kontext till parametern master_ssl_ca. </br><br>Om du vill överföra data utan SSL skickar du in en tom sträng till parametern master_ssl_ca.|
|*mysql.az_replication _start*|Ej tillämpligt|Ej tillämpligt|Startar replikeringen.|
|*mysql.az_replication _stop*|Ej tillämpligt|Ej tillämpligt|Stoppar replikeringen.|
|*mysql.az_replication _remove_master*|Ej tillämpligt|Ej tillämpligt|Tar bort replikeringsrelationen mellan huvud- och repliken.|
|*mysql.az_replication_skip_counter*|Ej tillämpligt|Ej tillämpligt|Hoppar över ett replikeringsfel.|

Information om hur du konfigurerar data-in Replication mellan en huvudhanterare och en replik i Azure Database för MariaDB finns [i hur du konfigurerar data-in Replication](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

Följande lagrade procedurer är tillgängliga i Azure Database för MariaDB för att hantera servern.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Anm.-**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|Ej tillämpligt|Motsvarar [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) kommandot. Kommer att avsluta anslutningen som är associerad med den medföljande processlist_id efter att ha avslutat ett uttalande som anslutningen utför.|
|*mysql.az_kill_query*|processlist_id|Ej tillämpligt|Motsvarar [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) kommandot. Kommer att avsluta satsen anslutningen körs för närvarande. Lämnar själva anslutningen vid liv.|
|*mysql.az_load_timezone*|Ej tillämpligt|Ej tillämpligt|Läser in tidszonstabeller så att parametern `time_zone` kan ställas in på namngivna värden (t.ex. "USA/Stilla havet").|

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du konfigurerar [data-in Replication](howto-data-in-replication.md)
- Läs om hur du använder [tidszonstabellerna](howto-server-parameters.md#working-with-the-time-zone-parameter)