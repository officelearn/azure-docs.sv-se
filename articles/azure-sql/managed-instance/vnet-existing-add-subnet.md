---
title: Konfigurera ett befintligt virtuellt nätverk
titleSuffix: Azure SQL Managed Instance
description: Den här artikeln beskriver hur du konfigurerar ett befintligt virtuellt nätverk och undernät där du kan distribuera en hanterad Azure SQL-instans.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: bce5acd6ce51092efccd1e09f7436ff78fd420a8
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219336"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Konfigurera ett befintligt virtuellt nätverk för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance måste distribueras i ett virtuellt Azure- [nätverk](../../virtual-network/virtual-networks-overview.md) och under nätet dedikerat endast för hanterade instanser. Du kan använda det befintliga virtuella nätverket och under nätet om de har kon figurer ATS enligt [kraven i SQL Managed instance Virtual Network](connectivity-architecture-overview.md#network-requirements).

Om något av följande gäller för dig kan du validera och ändra nätverket med hjälp av skriptet som beskrivs i den här artikeln:

- Du har ett nytt undernät som fortfarande inte har kon figurer ATS.
- Du är inte säker på att under nätet är justerat mot [kraven](connectivity-architecture-overview.md#network-requirements).
- Du vill kontrol lera att under nätet fortfarande uppfyller [nätverks kraven](connectivity-architecture-overview.md#network-requirements) när du har gjort ändringar.

> [!Note]
> Du kan bara skapa en hanterad instans i virtuella nätverk som skapats via Azure Resource Manager distributions modell. Virtuella Azure-nätverk som skapats via den klassiska distributions modellen stöds inte. Beräkna under näts storlek genom att följa rikt linjerna i artikeln [Bestäm storleken på undernät för SQL-hanterad instans](vnet-subnet-determine-size.md) . Du kan inte ändra storlek på under nätet när du har distribuerat resurserna i.
>
> När den hanterade instansen har skapats går det inte att flytta instansen eller VNet till en annan resurs grupp eller prenumeration.

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

Skriptet förbereder undernätet i tre steg:

1. Verifiera: den verifierar det valda virtuella nätverket och under nätet för nätverks krav för SQL-hanterade instanser.
2. Bekräfta: det visar användaren en uppsättning ändringar som behöver göras för att förbereda under nätet för distribution av SQL-hanterade instanser. Den ber även om medgivande.
3. Förbered: det virtuella nätverket och under nätet konfigureras på rätt sätt.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- En själv studie kurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en säkerhets kopia av databasen finns i [skapa en hanterad instans](instance-create-quickstart.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](custom-dns-configure.md).
