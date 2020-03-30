---
title: Hitta slutpunkt för hantering av hanterade instanser
description: Lär dig hur du skaffar azure SQL Database Managed Instance management endpoint public IP-adress och verifierar det inbyggda brandväggsskyddet
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
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825721"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Ta reda på IP-adressen för hanteringsslutpunkten

Virtuella kluster för hanterad instans i Azure SQL Database innehåller en hanteringsslutpunkt som Microsoft använder för hanteringsåtgärder. Hanteringsslutpunkten är skyddad med en inbyggd brandvägg på nätverksnivå och ömsesidig certifikatverifiering på programnivå. Du kan bestämma IP-adressen för hanteringsslutpunkten, men du kan inte komma åt den här slutpunkten.

För att fastställa hanterings-IP-adressen gör du en DNS-sökning på din hanterade instans FQDN: `mi-name.zone_id.database.windows.net`. Detta returnerar en DNS-post `trx.region-a.worker.vnet.database.windows.net`som är som . Du kan sedan göra en DNS-sökning på denna FQDN med ".vnet" bort. Detta returnerar hanterings-IP-adressen. 

Detta PowerShell kommer att göra allt \<åt dig\> om du ersätter MI `mi-name.zone_id.database.windows.net`FQDN med DNS-posten för din hanterade instans: :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Mer information om hanterade instanser och anslutning finns i [Azure SQL Database Managed Instance Connectivity Architecture](sql-database-managed-instance-connectivity-architecture.md).
