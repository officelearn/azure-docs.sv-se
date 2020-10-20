---
title: Säkerhets alternativ för Hive i Azure HDInsight
description: Säkerhets alternativ för Hive i standard-och ESP-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 14a41365640439ff99861bbb22cc04a40f35da5e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222973"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Säkerhets alternativ för Hive i Azure HDInsight

Det här dokumentet beskriver rekommenderade säkerhets alternativ för Hive i HDInsight. Dessa alternativ kan konfigureras via Ambari.

![' Säkerhets alternativ för Hive '](./media/hdinsight-security-options-for-hive/security-options-hive.png "Säkerhets alternativ för Hive")

## <a name="hiveserver2-authentication"></a>HiveServer2-autentisering

För standard kluster är den rekommenderade inställningen för HiveServer2-autentisering standardvärdet som är ingen. Om du vill aktivera autentisering rekommenderar vi att du uppgraderar till ett [ESP](https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview) -kluster (Enterprise Security Package). 

För ESP-kluster är [Kerberos](https://web.mit.edu/Kerberos/) -autentisering aktiverat som standard. PAM (Pluggable Authentication modules) och anpassade autentiseringsscheman stöds inte.

## <a name="hiveserver2-authorization"></a>HiveServer2-auktorisering

Standardvärdet är ingen för standard kluster. [SqlStdAuth (SQL-standardbaserad auktorisering)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) kan aktive ras. Auktorisering via [Apache Ranger](https://ranger.apache.org/) stöds inte för standard kluster. Vi rekommenderar att du uppgraderar till ett ESP-kluster för Ranger-auktorisering. 

För ESP-kluster är auktorisering via Ranger aktiverat som standard. 


## <a name="ssl-encryption-for-hiveserver2"></a>SSL-kryptering för HiveServer2

Aktivering av Hiveserver2-SSL rekommenderas inte för antingen standard-eller ESP-kluster. SSL är aktiverat på gatewayen i stället. [Kryptering i överföring](https://docs.microsoft.com/azure/hdinsight/domain-joined/encryption-in-transit) kan aktive ras för kryptering av kommunikation mellan klusternoderna med [Internet Protocol säkerhet (IPSec)](https://en.wikipedia.org/wiki/IPsec).


## <a name="next-steps"></a>Nästa steg
* [Översikt över HiveServer2-autentisering](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Översikt över HiveServer2-auktorisering](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [Aktivera SQL-standarden baserad på Hive-auktorisering](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger med Hive](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive#:~:text=Create%20Hive%20ODBC%20data%20source%20%20%20,Enter%20hiveuser1%40contoso158.onmicrosoft.c%20...%20%205%20more%20rows%20)
