---
title: Skapa ett virtuellt nätverk för Azure SQL Database Hanterad instans | Microsoft Docs
description: Den här artikeln beskriver hur du skapar ett virtuellt nätverk där du kan distribuera Azure SQL Database Hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 01/15/2019
ms.openlocfilehash: 832f43d1f25393a29df4b836c353611a19e3a417
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567575"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Skapa ett virtuellt nätverk för Azure SQL Database Hanterad instans

Den här artikeln beskriver hur du skapar ett giltigt virtuellt nätverk och undernät där du kan distribuera Azure SQL Database Hanterad instans.

Azure SQL Database hanterade instansen måste distribueras i ett virtuellt Azure- [nätverk](../virtual-network/virtual-networks-overview.md). Den här distributionen möjliggör följande scenarier:

- Säker privat IP-adress
- Ansluta till en hanterad instans direkt från ett lokalt nätverk
- Ansluta en hanterad instans till en länkad server eller till ett annat lokalt data lager
- Ansluta en hanterad instans till Azure-resurser  

> [!Note]
> Du bör [fastställa storleken på under nätet för hanterade instanser](sql-database-managed-instance-determine-size-vnet-subnet.md) innan du distribuerar den första instansen. Du kan inte ändra storlek på under nätet när du har lagt till resurserna i.
>
> Om du planerar att använda ett befintligt virtuellt nätverk måste du ändra nätverks konfigurationen så att den passar din hanterade instans. Mer information finns i [ändra ett befintligt virtuellt nätverk för en hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
>
> När en hanterad instans har skapats går det inte att flytta den hanterade instansen eller det virtuella nätverket till en annan resurs grupp eller prenumeration.


## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Det enklaste sättet att skapa och konfigurera ett virtuellt nätverk är att använda en mall för Azure Resource Manager distribution.

1. Logga in på Azure Portal.

2. Välj knappen **distribuera till Azure** :

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Den här knappen öppnar ett formulär som du kan använda för att konfigurera nätverks miljön där du kan distribuera den hanterade instansen.

   > [!Note]
   > Den här Azure Resource Manager mallen distribuerar ett virtuellt nätverk med två undernät. Ett undernät, som kallas **ManagedInstances**, är reserverat för hanterad instans och har en förkonfigurerad routningstabell. Det andra under nätet som kallas **standard**används för andra resurser som ska komma åt hanterade instanser (till exempel Azure Virtual Machines).

3. Konfigurera nätverks miljön. I följande formulär kan du konfigurera parametrar för din nätverks miljö:

   ![Resource Manager-mall för att konfigurera Azure-nätverket](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Du kan ändra namnen på det virtuella nätverket och undernät och justera IP-intervallen som är kopplade till dina nätverks resurser. När du har valt knappen **köp** skapas och konfigureras din miljö med det här formuläret. Om du inte behöver två undernät kan du ta bort standard ett.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- Lär dig mer om [anslutnings arkitekturen i en hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- Lär dig hur du [ändrar ett befintligt virtuellt nätverk för en hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
- En själv studie kurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en säkerhets kopia av databasen finns i [skapa en Azure SQL Database Hanterad instans](sql-database-managed-instance-get-started.md).
- Information om DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
