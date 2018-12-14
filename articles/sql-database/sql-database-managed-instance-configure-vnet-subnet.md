---
title: Azure SQL Database Managed Instance konfigurera befintliga virtuella nätverk/undernät | Microsoft Docs
description: Det här avsnittet beskriver hur du konfigurerar ett befintligt virtuellt nätverk (VNet) och undernät där du kan distribuera Azure SQL Database Managed Instance.
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
ms.date: 09/20/2018
ms.openlocfilehash: 53aba5192ddf57598965fcfe0db5f2b18423c7e9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346607"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Konfigurera ett befintligt virtuellt nätverk för Azure SQL Database Managed Instance

Azure SQL Database Managed Instance måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md) och undernätet dedikerade endast för hanterade instanser. Du kan använda det befintliga virtuella nätverket och undernätet om den har konfigurerats enligt den [hanterad instans-VNet-krav](sql-database-managed-instance-connectivity-architecture.md#network-requirements). 

Om du har ett nytt undernät som fortfarande inte har konfigurerats kan du inte är säker på att justeras undernätet med den [krav](sql-database-managed-instance-connectivity-architecture.md#network-requirements), eller om du vill kontrollera är undernätet fortfarande kompatibel med den [krav på](sql-database-managed-instance-connectivity-architecture.md#network-requirements) när vissa ändringar som du gjort, som du kan validera och ändra ditt nätverk med hjälp av skript som beskrivs i det här avsnittet. 

  > [!Note]
  > Du kan bara skapa en hanterad instans i Resource Manager-nätverk. Azure Vnet som distribuerats med klassisk distributionsmodell är inte suported. Se till att du beräkna undernätets storlek genom att följa riktlinjerna i den [avgör storleken på undernätet för hanterade instanser](#determine-the-size-of-subnet-for-managed-instances) avsnittet eftersom undernätet inte kan ändras när du har distribuerat resurser i.

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
Förberedelse av undernät görs i tre enkla steg:

1. Verifiera – valda virtuella nätverket och undernätet verifieras för hanterad instans nätverkskraven.
2. Bekräfta - användare visas en uppsättning ändringar som behöver göras att förbereda undernät för hanterad instans-distribution och tillfrågas om samtycke.
3. Förbereda – virtuellt nätverk och undernät är korrekt konfigurerade.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen, [skapar en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
