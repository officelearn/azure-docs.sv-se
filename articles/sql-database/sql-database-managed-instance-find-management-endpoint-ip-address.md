---
title: Identifiera Azure SQL Database Hanterad instans hanterings slut punkt
description: Lär dig hur du hämtar en offentlig IP-adress för Azure SQL Database Hanterad instans hantering och kontrollerar dess inbyggda brand Väggs skydd
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 0cc3ca3a2b8130e52d8c7fc670c0ee7997e4aa47
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688043"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Fastställa IP-adressen för hanterings slut punkten

Den Azure SQL Database hanterade instansens virtuella kluster innehåller en hanterings slut punkt som Microsoft använder för hanterings åtgärder. Hanterings slut punkten skyddas med en inbyggd brand vägg på nätverks nivå och ömsesidig certifikat verifiering på program nivå. Du kan fastställa hanterings slut punktens IP-adress, men du kan inte komma åt den här slut punkten.

För att fastställa IP-adressen för hantering gör du en DNS-sökning på den hanterade instansen med fullständigt domän namn: `mi-name.zone_id.database.windows.net`. Detta kommer att returnera en DNS-post som liknar `trx.region-a.worker.vnet.database.windows.net`. Du kan sedan göra en DNS-sökning på detta FQDN med ". VNet" borttaget. Detta kommer att returnera hanterings-IP-adressen. 

Den här PowerShell gör allt åt dig om du ersätter \<MI FQDN-\> med DNS-posten för din hanterade instans: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Mer information om hanterade instanser och anslutningar finns [Azure SQL Database arkitektur för hanterad instans anslutning](sql-database-managed-instance-connectivity-architecture.md).
