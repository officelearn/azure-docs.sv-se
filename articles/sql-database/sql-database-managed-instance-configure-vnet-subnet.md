---
title: Konfigurera ett befintligt virtuellt nätverk för Azure SQL Database Managed Instance | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar ett befintligt virtuellt nätverk och undernät där du kan distribuera Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 16684619ccf542783e425852a075eaa74e96e592
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332191"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurera ett befintligt virtuellt nätverk för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance måste distribueras i en Azure [virtuellt nätverk](../virtual-network/virtual-networks-overview.md) och undernätet dedikerade endast för hanterade instanser. Du kan använda det befintliga virtuella nätverk och undernät om den har konfigurerats enligt den [Managed Instance virtuella nätverkskrav](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Om något av följande fall till dig, kan du validera och ändra ditt nätverk med hjälp av skript som beskrivs i den här artikeln:

* Du har ett nytt undernät som fortfarande inte har konfigurerats.
* Du inte vet att undernätet stämmer överens med den [krav](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
* Du vill kontrollera att undernätet fortfarande uppfyller de [krav på](sql-database-managed-instance-connectivity-architecture.md#network-requirements) när du har gjort ändringar.


> [!Note]
> Du kan skapa en hanterad instans bara i virtuella nätverk som skapats via Azure Resource Manager-distributionsmodellen. Azure-nätverk som skapats via den klassiska distributionsmodellen stöds inte. Beräkna undernätets storlek genom att följa riktlinjerna i den [avgör storleken på undernätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md) artikeln. Du kan inte ändra storlek på undernätet när du har distribuerat resurser i.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Validera och ändra ett befintligt virtuellt nätverk

Om du vill skapa en hanterad instans i ett befintligt undernät, rekommenderar vi följande PowerShell-skript för att förbereda undernätet:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

Skriptet förbereder undernät i tre steg:

1. Verifiera: Den verifierar det valda virtuella nätverket och undernätet för hanterad instans nätverkskrav.
2. Bekräfta: Den visar användaren en uppsättning ändringar som behöver göras för att förbereda undernätet för hanterad instans-distribution. Uppmanas även om samtycke.
3. Förbered: Den konfigurerar det virtuella nätverk och undernät.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen [skapa en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
