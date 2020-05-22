---
title: Identifiera slut punkt för hantering av hanterade instanser
description: Lär dig hur du hämtar en offentlig IP-adress för Azure SQL Database Hanterad instans hantering och kontrollerar dess inbyggda brand Väggs skydd
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: ac4675be25cb5f2f65cc444e65bd25d65d3c3856
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770048"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Fastställa IP-adressen för hanterings slut punkten

Den Azure SQL Database hanterade instansens virtuella kluster innehåller en hanterings slut punkt som Microsoft använder för hanterings åtgärder. Hanterings slut punkten skyddas med en inbyggd brand vägg på nätverks nivå och ömsesidig certifikat verifiering på program nivå. Du kan fastställa hanterings slut punktens IP-adress, men du kan inte komma åt den här slut punkten.

För att fastställa IP-adressen för hantering gör du en DNS-sökning på din hanterade instans-FQDN: `mi-name.zone_id.database.windows.net` . Detta kommer att returnera en DNS-post som är likadan `trx.region-a.worker.vnet.database.windows.net` . Du kan sedan göra en DNS-sökning på detta FQDN med ". VNet" borttaget. Detta kommer att returnera hanterings-IP-adressen. 

Den här PowerShell gör allt åt dig om du ersätter \< mi FQDN \> med DNS-posten för den hanterade instansen: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Mer information om hanterade instanser och anslutningar finns [Azure SQL Database arkitektur för hanterad instans anslutning](sql-database-managed-instance-connectivity-architecture.md).
