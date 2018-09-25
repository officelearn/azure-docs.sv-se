---
title: Azure Database for MariaDB-Data i replikering lagrade procedurer
description: Den här artikeln beskrivs alla lagrade procedurer som används för Data i replikering.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 87c6fa783964c019841810ec38f342a5a44c0ee3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959095"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Azure Database for MariaDB-Data i replikering lagrade procedurer

Data i replikering kan du synkronisera data från en MariaDB-server som körs lokalt, i virtuella datorer eller databastjänster med andra molnleverantörer till Azure Database for MariaDB-tjänsten.

Följande lagrade procedurer används för att konfigurera eller ta bort Data i replikeringen mellan huvud- och repliken.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Obs! användning**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Gäller inte|Skicka i kontexten för CA-certifikatet till master_ssl_ca-parametern för att överföra data med SSL-läge. </br><br>Skicka en tom sträng till master_ssl_ca-parametern för att överföra data utan SSL.|
|*_starta MySQL.az_replication*|Gäller inte|Gäller inte|Startar replikering.|
|*MySQL.az_replication _stop*|Gäller inte|Gäller inte|Stoppar replikeringen.|
|*MySQL.az_replication _remove_master*|Gäller inte|Gäller inte|Tar bort replikeringsrelationen mellan huvud- och repliken.|
|*MySQL.az_replication_skip_counter*|Gäller inte|Gäller inte|Hoppar över en replikeringsfel.|

Om du vill ställa in data i replikering mellan en och en replik i Azure Database for MariaDB, referera till [hur du konfigurerar data i replikering](howto-data-in-replication.md).