---
title: IP-adresser i Azure Integration Runtime
description: Lär dig vilka IP-adresser du måste tillåta inkommande trafik från, för att konfigurera brand väggar korrekt för att skydda nätverks åtkomsten till data lager.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: b0ba47ff28208bce1a6fa6ec300a261d788167de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415604"
---
# <a name="azure-integration-runtime-ip-addresses"></a>IP-adresser i Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vilka IP-adresser som Azure Integration Runtime använder beror på den region där din Azure integration runtime finns. *Alla* Azures integrerings körningar som finns i samma region använder samma IP-adressintervall.

> [!IMPORTANT]  
> Data flöden använder inte de här IP-adresserna för närvarande. 
>
> Du kan använda dessa IP-intervall för data förflyttning, pipeline och externa aktiviteter. De här IP-intervallen kan användas för vit listning i data lager/nätverks säkerhets grupper (NSG)/brand väggar för inkommande åtkomst från Azure integration Runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP-adresser: vissa regioner

Tillåt trafik från IP-adresserna som anges för Azure integration runtime i den aktuella Azure-region där dina resurser finns:

|                | Region              | IP-adresser                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Asien           | Asien, östra           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Sydostasien      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Australien      | Australien, östra      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Australien, sydöstra | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brasilien         | Brasilien, södra        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Kanada         | Kanada, centrala      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| Kina          | Kina, östra 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| Europa         | Europa, norra        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europa, västra         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| Frankrike         | Frankrike, centrala      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| Indien          | Indien, centrala       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japan          | Japan, östra          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Korea          | Sydkorea, centrala       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Sydafrika   | Sydafrika, norra  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Storbritannien | Storbritannien, södra            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| USA  | USA, centrala          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | USA, östra             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | USA, östra 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | USA, östra 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | USA, norra centrala    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | USA, södra centrala    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | USA, västra centrala     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | USA, västra             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | USA, västra 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | US Gov, Virginia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Känt problem med Azure Storage

* När du ansluter till Azure Storage-kontot har IP-regler ingen påverkan på begär Anden som kommer från Azure integration runtime i samma region som lagrings kontot. Mer information [finns i den här artikeln](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  I stället rekommenderar vi [att du använder betrodda tjänster när du ansluter till Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Nästa steg

* [Säkerhets överväganden för data förflyttning i Azure Data Factory](data-movement-security-considerations.md)
