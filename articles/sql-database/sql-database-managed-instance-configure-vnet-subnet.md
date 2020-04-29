---
title: Konfigurera ett befintligt virtuellt nätverk för en hanterad instans
description: Den här artikeln beskriver hur du konfigurerar ett befintligt virtuellt nätverk och undernät där du kan distribuera Azure SQL Database Hanterad instans.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476957"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Konfigurera ett befintligt virtuellt nätverk för Azure SQL Database Managed Instance

Azure SQL Database hanterade instansen måste distribueras i ett virtuellt Azure- [nätverk](../virtual-network/virtual-networks-overview.md) och under nätet dedikerat endast för hanterade instanser. Du kan använda det befintliga virtuella nätverket och under nätet om det har kon figurer ATS enligt kraven i den [hanterade instansens virtuella nätverk](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Om något av följande gäller för dig kan du validera och ändra nätverket med hjälp av skriptet som beskrivs i den här artikeln:

- Du har ett nytt undernät som fortfarande inte har kon figurer ATS.
- Du är inte säker på att under nätet är justerat mot [kraven](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Du vill kontrol lera att under nätet fortfarande uppfyller [nätverks kraven](sql-database-managed-instance-connectivity-architecture.md#network-requirements) när du har gjort ändringar.

> [!Note]
> Du kan bara skapa en hanterad instans i virtuella nätverk som skapats via Azure Resource Manager distributions modell. Virtuella Azure-nätverk som skapats via den klassiska distributions modellen stöds inte. Beräkna under näts storlek genom att följa rikt linjerna i artikeln [Bestäm storlek på undernät för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md) . Du kan inte ändra storlek på under nätet när du har distribuerat resurserna i.
>
> När en hanterad instans har skapats går det inte att flytta den hanterade instansen eller det virtuella nätverket till en annan resurs grupp eller prenumeration.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Verifiera och ändra ett befintligt virtuellt nätverk

Om du vill skapa en hanterad instans i ett befintligt undernät rekommenderar vi följande PowerShell-skript för att förbereda under nätet:

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

Skriptet förbereder under nätet i tre steg:

1. Verifiera: den verifierar det valda virtuella nätverket och under nätet för nätverks krav för hanterade instanser.
2. Bekräfta: det visar användaren en uppsättning ändringar som behöver göras för att förbereda under nätet för distribution av hanterade instanser. Den ber även om medgivande.
3. Förbered: det virtuella nätverket och under nätet konfigureras på rätt sätt.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- En själv studie kurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en säkerhets kopia av databasen finns i [skapa en Azure SQL Database Hanterad instans](sql-database-managed-instance-get-started.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
