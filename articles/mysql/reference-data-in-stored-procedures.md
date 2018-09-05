---
title: Azure Database for MySQL-Data i replikering lagrade procedurer
description: Den här artikeln beskrivs alla lagrade procedurer som används för Data i replikering.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: fb1a1b31d90df0022e5973de3ae2f55fb4c36701
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665954"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL-Data i replikering lagrade procedurer

Data i replikering kan du synkronisera data från en MySQL-server som körs lokalt, i virtuella datorer eller databastjänster med andra molnleverantörer till Azure Database for MySQL-tjänsten.

Följande lagrade procedurer används för att konfigurera eller ta bort Data i replikeringen mellan huvud- och repliken.

|**Namn på lagrad procedur**|**Indataparametrar**|**Utdataparametrar**|**Obs! användning**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Gäller inte|Skicka i kontexten för CA-certifikatet till master_ssl_ca-parametern för att överföra data med SSL-läge. </br><br>Skicka en tom sträng till master_ssl_ca-parametern för att överföra data utan SSL.|
|*_starta MySQL.az_replication*|Gäller inte|Gäller inte|Startar replikering.|
|*MySQL.az_replication _stop*|Gäller inte|Gäller inte|Stoppar replikeringen.|
|*MySQL.az_replication _remove_master*|Gäller inte|Gäller inte|Tar bort replikeringsrelationen mellan huvud- och repliken.|
|*MySQL.az_replication_skip_counter*|Gäller inte|Gäller inte|Hoppar över en replikeringsfel.|

Om du vill ställa in data i replikering mellan en och en replik i Azure Database för MySQL, referera till [hur du konfigurerar data i replikering](howto-data-in-replication.md).
