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
ms.openlocfilehash: d5bb2f2f4b79c4b03e631fc844a712f76fc69109
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258175"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurera en anpassad DNS för Azure SQL Database Managed Instance

En Azure SQL Database Managed Instance (förhandsversion) måste distribueras i en Azure [virtuellt nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Det finns ett par scenarier, länkade servrar till andra SQL-instanser i miljön moln eller hybrid, som kräver privata värdnamn som matchas från den hanterade instansen. I så fall måste du konfigurera en anpassad DNS i Azure. Eftersom Managed Instance använder samma DNS för dess informationsresurser, måste DNS-konfiguration för virtuellt nätverk vara kompatibel med Managed Instance. 

Om du vill göra en anpassad DNS-konfiguration som är kompatibla med Managed Instance, måste du utföra följande steg: 
- Konfigurera anpassad DNS för att vidarebefordra begäranden till Azure DNS 
- Ställa in anpassad DNS som primär och Azure DNS som sekundär för det virtuella nätverket 
- Registrera anpassad DNS som primär och Azure DNS som sekundär

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Konfigurera anpassad DNS för att vidarebefordra begäranden till Azure DNS 

Följ dessa steg för att konfigurera vidarebefordran av DNS på Windows Server 2016: 

1. I **Serverhanteraren**, klickar du på **verktyg**, och klicka sedan på **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Dubbelklicka på **vidarebefordrare**.

   ![Vidarebefordrare](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Klicka på **Redigera**. 

   ![Över vidarebefordrare](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Ange Azures rekursiva matchare IP-adress, till exempel 168.63.129.16.

   ![Rekursiva matchare ip-adress](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Konfigurera anpassad DNS som primär och Azure DNS som sekundär 
 
DNS-konfigurationen på ett virtuellt Azure nätverk kräver att du ange IP-adresser, så konfigurerar Azure VM som är värd för DNS-servern med en statisk IP-adress med hjälp av följande steg: 

1. Öppna det anpassade DNS VM-nätverksgränssnittet i Azure-portalen.

   ![nätverksskrivare](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. I avsnittet IP-konfigurationer. Välj IP-konfiguration 

   ![IP-konfiguration](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Ange privata IP-adressen som statisk. Anteckna IP-adress (10.0.1.5 på den här skärmbilden) 

   ![statisk](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Registrera anpassad DNS som primär och Azure DNS som sekundär 

1. Hitta den anpassade DNS-alternativet för ditt virtuella nätverk i Azure-portalen.

   ![anpassad dns-alternativ](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Växla till anpassade och ange din anpassade DNS-server IP-adress som Azures rekursiva matchare IP-adress, till exempel 168.63.129.16. 

   ![anpassad dns-alternativ](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Ange inte Azures rekursiva matchare i DNS orsakar listan den hanterade instansen anger felaktigt tillstånd. Återställa från att tillståndet kan kräva att du vill skapa en ny instans i ett virtuellt nätverk med efterlevnadsprinciperna för nätverk, skapa nivå instansdata och återställa databaserna. Se [konfiguration av virtuellt nätverk](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Nästa steg

- En översikt finns i [vad är en hanterad instans](sql-database-managed-instance.md)
- En självstudiekurs som visar hur du skapar en ny hanterad instans finns i [skapar en hanterad instans](sql-database-managed-instance-create-tutorial-portal.md).
- Information om hur du konfigurerar ett virtuellt nätverk för en hanterad instans finns i [konfiguration av virtuellt nätverk för hanterade instanser](sql-database-managed-instance-vnet-configuration.md)
