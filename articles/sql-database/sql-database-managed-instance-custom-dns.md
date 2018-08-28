---
title: Azure SQL Database Managed Instance anpassad DNS | Microsoft Docs
description: Det här avsnittet beskriver konfigurationsalternativ för en anpassad DNS med en Azure SQL Database Managed Instance.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: af9afcbf97df5f3d7fa82f6ea0163c714fa4f582
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051749"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurera en anpassad DNS för Azure SQL Database Managed Instance

En Azure SQL Database Managed Instance (förhandsversion) måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Det finns ett par scenarier (dvs. länkade servrar till andra SQL-instanser i miljön moln eller hybrid) som kräver privata värdnamn som matchas från den hanterade instansen. I så fall måste du konfigurera en anpassad DNS i Azure. Eftersom Managed Instance använder samma DNS för dess informationsresurser, måste DNS-konfiguration för virtuellt nätverk vara kompatibel med Managed Instance. 

Om du vill göra en anpassad DNS-konfiguration är kompatibel med den hanterade instansen måste du: 
- Konfigurera anpassad DNS-server så att den är kan matcha offentliga domännamn 
- Placera Azures rekursiva matchare DNS-IP-adressen 168.63.129.16 i slutet av listan över DNS-virtuella nätverk 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>Anpassad konfiguration av DNS-servrar

1. Hitta den anpassade DNS-alternativet för ditt virtuella nätverk i Azure-portalen.

   ![anpassad dns-alternativ](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Växla till anpassade och ange din anpassade DNS-server IP-adress som Azures rekursiva matchare IP-adressen 168.63.129.16. 

   ![anpassad dns-alternativ](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Ange inte Azures rekursiva matchare i DNS orsakar listan den hanterade instansen anger felaktigt tillstånd. Återställa från att tillståndet kan kräva att du vill skapa en ny instans i ett virtuellt nätverk med efterlevnadsprinciperna för nätverk, skapa nivå instansdata och återställa databaserna. Se [konfiguration av virtuellt nätverk](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- En självstudiekurs som visar hur du skapar en ny hanterad instans finns i [skapar en hanterad instans](sql-database-managed-instance-create-tutorial-portal.md).
- Information om hur du konfigurerar ett virtuellt nätverk för en hanterad instans finns i [konfiguration av virtuellt nätverk för hanterade instanser](sql-database-managed-instance-vnet-configuration.md)
