---
title: Identifiera IP-adress för hanterings slut punkt
titleSuffix: Azure SQL Managed Instance
description: Lär dig hur du hämtar den offentliga IP-adressen för hanterings slut punkten för Azure SQL Managed instance Management och kontrollerar dess inbyggda brand Väggs skydd
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 88965c25702917f17a226cfa51de662703136aae
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045083"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Ta reda på hanterings slut punktens IP-adress – Azure SQL-hanterad instans 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Det virtuella Azure SQL Managed instance-klustret innehåller en hanterings slut punkt som Azure använder för hanterings åtgärder. Hanterings slut punkten skyddas med en inbyggd brand vägg på nätverks nivå och ömsesidig certifikat verifiering på program nivå. Du kan fastställa hanterings slut punktens IP-adress, men du kan inte komma åt den här slut punkten.

Kontrol lera IP-adressen för hantering genom att göra en [DNS-sökning](/windows-server/administration/windows-commands/nslookup) på din SQL Managed instance-FQDN: `mi-name.zone_id.database.windows.net` . Detta kommer att returnera en DNS-post som är likadan `trx.region-a.worker.vnet.database.windows.net` . Du kan sedan göra en DNS-sökning på detta FQDN med ". VNet" borttaget. Detta kommer att returnera hanterings-IP-adressen. 

Den här PowerShell gör allt åt dig om du ersätter \<MI FQDN\> med DNS-posten för SQL-hanterad instans: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Mer information om SQL-hanterade instanser och anslutningar finns i [anslutnings arkitektur för Azure SQL Managed instance](connectivity-architecture-overview.md).
