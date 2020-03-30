---
title: Konfigurera ett befintligt virtuellt nätverk för hanterad instans
description: I den här artikeln beskrivs hur du konfigurerar ett befintligt virtuellt nätverk och undernät där du kan distribuera Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476957"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurera ett befintligt virtuellt nätverk för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance måste distribueras i ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) och undernätet som endast är dedikerat för hanterade instanser. Du kan använda det befintliga virtuella nätverket och undernätet om det är konfigurerat enligt [kraven för virtuellt nätverk för hanterad instans](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Om något av följande fall gäller dig kan du validera och ändra nätverket med hjälp av skriptet som beskrivs i den här artikeln:

- Du har ett nytt undernät som fortfarande inte är konfigurerat.
- Du är inte säker på att undernätet är i linje med [kraven](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Du vill kontrollera att undernätet fortfarande uppfyller [nätverkskraven](sql-database-managed-instance-connectivity-architecture.md#network-requirements) när du har gjort ändringar.

> [!Note]
> Du kan bara skapa en hanterad instans i virtuella nätverk som skapats via Distributionsmodellen för Azure Resource Manager. Virtuella Azure-nätverk som skapats via den klassiska distributionsmodellen stöds inte. Beräkna undernätsstorleken genom att följa riktlinjerna i artikeln [Bestäm storleken på undernätet för hanterade instanser.](sql-database-managed-instance-determine-size-vnet-subnet.md) Du kan inte ändra storlek på undernätet när du har distribuerat resurserna inuti.
>
> När en hanterad instans har skapats stöds inte att flytta den hanterade instansen eller det virtuella nätverket till en annan resursgrupp eller prenumeration.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validera och ändra ett befintligt virtuellt nätverk

Om du vill skapa en hanterad instans i ett befintligt undernät rekommenderar vi följande PowerShell-skript för att förbereda undernätet:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Skriptet förbereder undernätet i tre steg:

1. Validera: Den validerar det valda virtuella nätverket och undernätet för nätverkskrav för hanterad instans.
2. Bekräfta: Den visar användaren en uppsättning ändringar som måste göras för att förbereda undernätet för distribution av hanterade instanser. Den ber också om samtycke.
3. Förbered: Det konfigurerar det virtuella nätverket och undernätet på rätt sätt.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- En självstudiekurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en databassäkerhetskopiering finns i [Skapa en hanterad Azure SQL-databas-hanterad instans](sql-database-managed-instance-get-started.md).
- För DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
