---
title: Anslutningstyper för hanterade instanser
description: Lär dig mer om anslutningstyper för hanterade instanser
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819459"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL Database-anslutningstyper för hanterade instanser

I den här artikeln beskrivs hur klienter ansluter till Azure SQL Database-hanterad instans beroende på anslutningstypen. Skriptexempel för att ändra anslutningstyper finns nedan, tillsammans med överväganden som är relaterade till att ändra standardinställningarna för anslutning.

## <a name="connection-types"></a>Anslutningstyper

Azure SQL Database-hanterad instans stöder följande två anslutningstyper:

- **Omdirigering (rekommenderas):** Klienter upprättar anslutningar direkt till den nod som är värd för databasen. Om du vill aktivera anslutning med omdirigering måste du öppna brandväggar och NSG (Network Security Groups) för att tillåta åtkomst på portarna 1433 och 11000-11999. Paket går direkt till databasen, och därför finns det svarstid och dataflöde prestandaförbättringar med Omdirigera över Proxy.
- **Proxy (standard):** I det här läget använder alla anslutningar en proxygatewaykomponent. För att aktivera anslutning behöver endast port 1433 för privata nätverk och port 3342 för offentlig anslutning öppnas. Om du väljer det här läget kan det leda till högre svarstid och lägre dataflöde, beroende på arbetsbelastningens art. Vi rekommenderar starkt principen om omdirigeringsanslutning över proxyanslutningsprincipen för den lägsta svarstiden och det högsta dataflödet.

## <a name="redirect-connection-type"></a>Omdirigeringsanslutningstyp

Omdirigeringsanslutningstyp innebär att när TCP-sessionen har upprättats till SQL-motorn, hämtar klientsessionen målvirklig IP för den virtuella klusternoden från belastningsutjämnaren. Efterföljande paket flödar direkt till den virtuella klusternoden och kringgår gatewayen. Följande diagram illustrerar detta trafikflöde.

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> Omdirigeringsanslutningstyp fungerar för närvarande endast för privat slutpunkt. Oavsett inställningen för anslutningstypen skulle anslutningar som kommer via den offentliga slutpunkten vara via en proxy.

## <a name="proxy-connection-type"></a>Proxyanslutningstyp

Proxyanslutningstyp innebär att TCP-sessionen upprättas med hjälp av gatewayen och alla efterföljande paket flödar genom den. Följande diagram illustrerar detta trafikflöde.

![proxy.png (på andra)](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>Skript för att ändra inställningar för anslutningstyp med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande PowerShell-skript visar hur du ändrar anslutningstypen för en hanterad instans till Omdirigera.

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>Nästa steg

- [Återställa en databas till en hanterad instans](sql-database-managed-instance-get-started-restore.md)
- Lär dig hur du [konfigurerar en offentlig slutpunkt på hanterad instans](sql-database-managed-instance-public-endpoint-configure.md)
- Lär dig mer om [anslutningsarkitektur för hanterade instanser](sql-database-managed-instance-connectivity-architecture.md)