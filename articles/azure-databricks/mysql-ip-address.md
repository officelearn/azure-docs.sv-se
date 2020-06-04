---
title: Uppdatera dina Azure Databricks väg tabeller och brand väggar med nya MySQL IP-adresser
description: Lär dig hur du uppdaterar Azure Databricks routningstabeller och brand väggar med nya MySQL-IP-adresser.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 5bd4822c21e6d9dbe8f3534dd0171a6f785bd5ac
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325917"
---
# <a name="update-your-azure-databricks-route-tables-and-firewalls-with-new-mysql-ips"></a>Uppdatera dina Azure Databricks väg tabeller och brand väggar med nya MySQL IP-adresser

Alla IP-adresser för dina Azure Database for MySQL, som används som Azure Databricks metaarkiv, ändras. Uppdatera dina Azure Databricks väg tabeller eller brand väggar med nya MySQL-IP: er före den 30 juni 2020 för att undvika avbrott.

En Azure Databricks arbets yta som distribueras i ditt eget virtuella nätverk har en bifogad [routningstabell](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/udr#--metastore-artifact-blob-storage-log-blob-storage-and-event-hub-endpoint-ip-addresses) . Routningstabellen kan direkt innehålla en Azure Databricks metaarkiv IP-adress eller väg till en brand vägg eller proxyserver som kan vara vit listning adressen.

## <a name="recommended-actions"></a>Rekommenderade åtgärder

Om du vill undvika avbrott i tjänsten granskar du och tillämpar dessa åtgärder före den 30 juni 2020.

* Kontrol lera om routningstabellen påverkas av en ändring i uppdateringen av Azure MySQL IP-adressen.

* Använd tabellen i följande avsnitt för att leta upp de nya IP-adresserna. För varje region kan det finnas flera IP-adresser som används som primära och sekundära IP-adresser för Azure Database for MySQL gatewayer. Den primära IP-adressen är den aktuella IP-adressen för gatewayen och de andra IP-adresserna är IP-adresser för redundans vid fel på den primära. För att säkerställa tjänstens hälsa bör du tillåta utgående till alla IP-adresser. Om du använder en [extern metaarkiv](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore), se till att en giltig IP-adress som per Azure MySQL-aviseringar vidarebefordras eller vit listas.

* Uppdatera routningstabellen, brand väggen eller proxyservern med de nya IP-adresserna.

## <a name="updated-mysql-ip-addresses"></a>Uppdaterade MySQL IP-adresser

Följande tabell innehåller alla IP-adresser som måste vara vit listas. IP-adresser i **fetstil** är de nya IP-adresserna. 

| Region namn          | IP-adresser för gateway                                                                                       |
| -------------------- | ---------------------------------------------------------------------------------------------------------- |
| Australien, centrala    | 20.36.105.0                                                                                                |
| Australien, centrala 2  | 20.36.113.0                                                                                                |
| Australien, östra       | 13.75.149.87<br><br>40.79.161.1                                                                            |
| Sydöstra Australien | 191.239.192.109<br><br>13.73.109.251                                                                       |
| Brasilien, södra         | 104.41.11.5 <br><br> 191.233.201.8 <br><br> **191.233.200.16**                                             |
| Kanada, centrala       | 40.85.224.249                                                                                              |
| Kanada, östra          | 40.86.226.166                                                                                              |
| USA, centrala           | 23.99.160.139<br><br>13.67.215.62<br><br>**52.182.136.37**<br><br>**52.182.136.38**                        |
| Kina, östra           | 139.219.130.35                                                                                             |
| Kina, östra 2         | 40.73.82.1                                                                                                 |
| Kina, norra          | 139.219.15.17                                                                                              |
| Kina, norra 2        | 40.73.50.0                                                                                                 |
| Asien, östra            | 191.234.2.139<br><br>52.175.33.150<br><br>**13.75.33.20**<br><br>**13.75.33.21**                           |
| USA, östra              | 40.121.158.30<br>191.238.6.43                                                                              |
| USA, östra 2            | 40.79.84.180<br><br>191.239.224.107<br><br>52.177.185.181<br><br>**40.70.144.38**<br><br>**52.167.105.38** |
| Frankrike, centrala       | 40.79.137.0<br><br>40.79.129.1                                                                             |
| Tyskland, centrala      | 51.4.144.100                                                                                               |
| Tyskland, norra öst   | 51.5.144.179                                                                                               |
| Indien, centrala        | 104.211.96.159                                                                                             |
| Södra Indien          | 104.211.224.146                                                                                            |
| Indien, västra           | 104.211.160.80                                                                                             |
| Japan, östra           | 13.78.61.196<br><br>191.237.240.43                                                                         |
| Japan, västra           | 104.214.148.156<br><br>191.238.68.11<br><br>**40.74.96.6**<br><br>**40.74.96.7**                           |
| Sydkorea, centrala        | 52.231.32.42                                                                                               |
| Sydkorea, södra          | 52.231.200.86                                                                                              |
| USA, norra centrala     | 23.96.178.199<br><br>23.98.55.75<br><br>**52.162.104.35**<br><br>**52.162.104.36**                         |
| Europa, norra         | 40.113.93.91<br><br>191.235.193.75<br><br>**52.138.224.6**<br><br>**52.138.224.7**                         |
| Sydafrika, norra   | 102.133.152.0                                                                                              |
| Sydafrika, västra    | 102.133.24.0                                                                                               |
| USA, södra centrala     | 13.66.62.124<br><br>23.98.162.75<br><br>**104.214.16.39**<br><br>**20.45.120.0**                           |
| Sydostasien      | 104.43.15.0<br><br>23.100.117.95<br><br>**40.78.233.2**<br><br>**23.98.80.12**                             |
| Förenade Arabemiraten Central          | 20.37.72.64                                                                                                |
| Förenade Arabemiraten, norra            | 65.52.248.0                                                                                                |
| Storbritannien, södra             | 51.140.184.11                                                                                              |
| Storbritannien, västra              | 51.141.8.11                                                                                                |
| USA, västra centrala      | 13.78.145.25                                                                                               |
| Europa, västra          | 40.68.37.158<br><br>191.237.232.75<br><br>**13.69.105.208**                                                |
| USA, västra              | 104.42.238.205<br><br>23.99.34.75                                                                          |
| USA, västra 2            | 13.66.226.202                                                                                              |

## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Databricks i ditt virtuella Azure-nätverk (VNet-inmatning)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)
* [Externa Apache Hive metaarkiv](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)
