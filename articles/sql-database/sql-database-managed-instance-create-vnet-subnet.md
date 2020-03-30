---
title: Skapa ett virtuellt nätverk för hanterad instans
description: I den här artikeln beskrivs hur du skapar ett virtuellt nätverk där du kan distribuera Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 9f115d51657993562642391a235de79420aa434a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823364"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Skapa ett virtuellt nätverk för Hanterad instans för Azure SQL Database

I den här artikeln beskrivs hur du skapar ett giltigt virtuellt nätverk och undernät där du kan distribuera Azure SQL Database Managed Instance.

Azure SQL Database Managed Instance måste distribueras i ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). Den här distributionen möjliggör följande scenarier:

- Säker privat IP-adress
- Ansluta till en hanterad instans direkt från ett lokalt nätverk
- Ansluta en hanterad instans till länkad server eller ett annat lokalt datalager
- Ansluta en hanterad instans till Azure-resurser  

> [!Note]
> Du bör [bestämma storleken på undernätet för hanterad instans](sql-database-managed-instance-determine-size-vnet-subnet.md) innan du distribuerar den första instansen. Du kan inte ändra storlek på undernätet när du har placerat resurserna inuti.
>
> Om du planerar att använda ett befintligt virtuellt nätverk måste du ändra nätverkskonfigurationen så att den passar din hanterade instans. Mer information finns i [Ändra ett befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
>
> När en hanterad instans har skapats stöds inte att flytta den hanterade instansen eller det virtuella nätverket till en annan resursgrupp eller prenumeration.


## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Det enklaste sättet att skapa och konfigurera ett virtuellt nätverk är att använda en Distributionsmall för Azure Resource Manager.

1. Logga in på Azure Portal.

2. Välj knappen **Distribuera till Azure:**

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Den här knappen öppnar ett formulär som du kan använda för att konfigurera nätverksmiljön där du kan distribuera hanterad instans.

   > [!Note]
   > Den här Azure Resource Manager-mallen distribuerar ett virtuellt nätverk med två undernät. Ett undernät, kallat **ManagedInstances,** är reserverat för hanterad instans och har en förkonfigurerad flödestabell. Det andra undernätet, som kallas **Standard,** används för andra resurser som ska komma åt hanterad instans (till exempel Virtuella Azure-datorer).

3. Konfigurera nätverksmiljön. I följande formulär kan du konfigurera parametrar för nätverksmiljön:

   ![Resource Manager-mall för att konfigurera Azure-nätverket](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Du kan ändra namnen på det virtuella nätverket och undernäten och justera IP-intervallen som är associerade med nätverksresurserna. När du har valt knappen **Köp** skapas och konfigureras miljön i det här formuläret. Om du inte behöver två undernät kan du ta bort standardnätet.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [Vad är en hanterad instans?](sql-database-managed-instance.md).
- Lär dig mer om [anslutningsarkitektur i Hanterad instans](sql-database-managed-instance-connectivity-architecture.md).
- Lär dig hur du [ändrar ett befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
- En självstudiekurs som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställer en databas från en databassäkerhetskopiering finns i [Skapa en hanterad Azure SQL-databas-hanterad instans](sql-database-managed-instance-get-started.md).
- För DNS-problem finns i [Konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
