---
title: Skapa virtuellt nätverk för Azure SQL Database Managed Instance | Microsoft Docs
description: Det här avsnittet beskriver hur du skapar ett virtuellt nätverk (VNet) där du kan distribuera en Azure SQL Database Managed Instance.
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
ms.openlocfilehash: a8000fb26ce5496a9c62ba475b862f8f80adf6b7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041772"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>Konfigurera ett virtuellt nätverk för Azure SQL Database Managed Instance

Det här avsnittet beskriver hur du skapar ett giltigt virtuellt nätverk och undernät där du kan distribuera hanterade Azure SQL Database-instanser.

Azure SQL Database Managed Instance måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Den här distributionen gör det möjligt för följande scenarier:

- Skydda privata IP-adressen.
- Ansluta till en hanterad instans direkt från ett lokalt nätverk
- Ansluta en hanterad instans till länkad server eller en annan lokala datalager
- Ansluta en hanterad instans till Azure-resurser  

  > [!Note]
  > Du bör [avgör storleken på undernätet för hanterad instans](sql-database-managed-instance-determine-size-vnet-subnet.md) innan du distribuerar första instansen eftersom sunet inte kan ändras när du lägger till resurser i.
  > Om du planerar att använda ett befintligt virtuellt nätverk måste du ändra den nätverkskonfigurationen för att hantera din hanterade instans. Mer information finns i [ändra befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="create-a-new-virtual-network"></a>Skapa ett nytt virtuellt nätverk

Det enklaste sättet att skapa och konfigurera ett virtuellt nätverk är att använda Azure Resource Manager-mall för distribution.

1. Logga in på Azure Portal.

2. Använd **distribuera till Azure** knappen för att distribuera virtuella nätverk i Azure-molnet:

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Den här knappen öppnas ett formulär som du kan använda för att konfigurera nätverket där du kan distribuera Managed Instance.

   > [!Note]
   > Azure Resource Manager-mallen distribuerar virtuellt nätverk med två undernät. Ett undernät som kallas **ManagedInstances** är reserverad för hanterade instanser och har förkonfigurerade routningstabellen, medan andra undernätet kallas **standard** används för andra resurser som ska få åtkomst till hanterad Instans (till exempel Azure virtuella datorer). Du kan ta bort **standard** undernät om du inte behöver den.

3. Konfigurera nätverksmiljön. Du kan konfigurera parametrar för din nätverksmiljö på följande format:

![Konfigurera azure-nätverk](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

Du kan ändra namnen på VNet och undernät och justera IP-adressintervall som är kopplade till dina nätverksresurser. När du trycker på ”Köp”-knappen, kommer det här formuläret Skapa och konfigurera din miljö. Om du inte behöver två undernät, kan du ta bort standardvärdet.

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md).
- Lär dig mer om [anslutningsarkitektur i Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Rensa så [ändra befintligt virtuellt nätverk för hanterad instans](sql-database-managed-instance-configure-vnet-subnet.md)
- Se en självstudie som visar hur du skapar ett virtuellt nätverk, skapar en hanterad instans och återställa en databas från en säkerhetskopia av databasen, [skapar en Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- DNS-problem, se [konfigurera en anpassad DNS](sql-database-managed-instance-custom-dns.md)
