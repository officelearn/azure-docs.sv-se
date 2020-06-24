---
title: Identifiera IP-adress för hanterings slut punkt
titleSuffix: Azure SQL Managed Instance
description: Lär dig hur du hämtar den offentliga IP-adressen för hanterings slut punkten för Azure SQL Managed instance Management och kontrollerar dess inbyggda brand Väggs skydd
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 40a44fe46cf38c633380c4c353960cc4e11f2f3d
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708730"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Ta reda på hanterings slut punktens IP-adress – Azure SQL-hanterad instans 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Det virtuella Azure SQL Managed instance-klustret innehåller en hanterings slut punkt som Azure använder för hanterings åtgärder. Hanterings slut punkten skyddas med en inbyggd brand vägg på nätverks nivå och ömsesidig certifikat verifiering på program nivå. Du kan fastställa hanterings slut punktens IP-adress, men du kan inte komma åt den här slut punkten.

Kontrol lera IP-adressen för hantering genom att göra en [DNS-sökning](/windows-server/administration/windows-commands/nslookup) på din SQL Managed instance-FQDN: `mi-name.zone_id.database.windows.net` . Detta kommer att returnera en DNS-post som är likadan `trx.region-a.worker.vnet.database.windows.net` . Du kan sedan göra en DNS-sökning på detta FQDN med ". VNet" borttaget. Detta kommer att returnera hanterings-IP-adressen. 

Den här PowerShell-koden gör allt åt dig om du ersätter \<MI FQDN\> med DNS-posten för SQL-hanterad instans: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Mer information om SQL-hanterad instans och anslutningar finns i [anslutnings arkitektur för Azure SQL Managed instance](connectivity-architecture-overview.md).
