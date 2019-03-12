---
title: Skapa ett virtuellt nätverk för Azure SQL Database Managed Instance | Microsoft Docs
description: Den här artikeln beskriver hur du skapar ett virtuellt nätverk där du kan distribuera Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 238be49c29b80e308a81ed34e9eba937c60fe018
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769131"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Skapa ett virtuellt nätverk för Azure SQL Database Managed Instance

Den här artikeln beskriver hur du skapar ett giltigt virtuellt nätverk och undernät där du kan distribuera Azure SQL Database Managed Instance.

Azure SQL Database Managed Instance måste distribueras i en Azure [virtuellt nätverk](../virtual-network/virtual-networks-overview.md). Den här distributionen gör det möjligt för följande scenarier:

- Skydda privata IP-adress
- Ansluta till en hanterad instans direkt från ett lokalt nätverk
- Ansluta en hanterad instans till länkad server eller en annan lokala datalager
- Ansluta en hanterad instans till Azure-resurser  

> [!Note]
> Du bör [avgör storleken på undernätet för hanterad instans](sql-database-managed-instance-determine-size-vnet-subnet.md) innan du distribuerar den första instansen. Du kan inte ändra storlek på undernätet när du lägger till resurser i.
>
> Om du planerar att använda ett befintligt virtuellt nätverk måste du ändra den nätverkskonfigurationen för att hantera din hanterade instans. Mer information finns i [ändra ett befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Det enklaste sättet att skapa och konfigurera ett virtuellt nätverk är att använda en Distributionsmall av Azure Resource Manager.

1. Logga in på Azure Portal.

2. Välj den **distribuera till Azure** knappen:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Den här knappen öppnar ett formulär som du kan använda för att konfigurera nätverket där du kan distribuera Managed Instance.

   > [!Note]
   > Azure Resource Manager-mallen distribuerar ett virtuellt nätverk med två undernät. Ett undernät som kallas **ManagedInstances**, är reserverad för hanterad instans och har en förkonfigurerad routningstabell. Den andra undernät som kallas **standard**, används för andra resurser som ska få åtkomst till Managed Instance (till exempel Azure virtuella datorer).

3. Konfigurera nätverket. Du kan konfigurera parametrar för din nätverksmiljö på följande format:

   ![Resource Manager-mall för att konfigurera Azure-nätverket](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Du kan ändra namn på virtuellt nätverk och undernät och justera de IP-intervall som är associerade med dina nätverksresurser. När du har valt den **köp** knapp, det här formuläret skapar och konfigurerar din miljö. Om du inte behöver två undernät, kan du ta bort standardvärdet.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans?](sql-database-managed-instance.md).
- Lär dig mer om [anslutningsarkitektur i Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Lär dig hur du [ändra ett befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen [skapa en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md).
