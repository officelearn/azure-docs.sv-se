---
title: Azure SQL Database hanteras instans anpassad DNS | Microsoft Docs
description: "Det här avsnittet beskriver konfigurationsalternativ för en anpassad DNS med en Azure SQL Database hanteras-instans."
services: sql-database
author: srdjan-bozovic
manager: cguyer
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: e660174038aa46ef496f5de67ac616bcd41b1f81
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Konfigurera en anpassad DNS för Azure SQL Database hanteras instans

En Azure SQL-hanterade databasinstans (förhandsgranskning) måste distribueras i en Azure [virtuella nätverk (VNet)](../virtual-network/virtual-networks-overview.md). Det finns några scenarier, länkade servrar till andra SQL-instanser i moln eller hybrid miljön, som kräver privata värdnamn som matchas från den hanterade instansen. I det här fallet måste du konfigurera en anpassad DNS i Azure. Eftersom hanteras instans använder samma DNS för dess proxyklassen, måste DNS-konfiguration för virtuellt nätverk vara kompatibel med hanterade instans. 

Om du vill göra en anpassad konfiguration av DNS-kompatibel med hanterade instans måste du utföra följande steg: 
- Konfigurera anpassade DNS för att vidarebefordra begäran till Azure DNS 
- Ställ in anpassad DNS som primär och Azure DNS som sekundär för VNet 
- Registrera anpassad DNS som primär och Azure DNS som sekundär

## <a name="configure-custom-dns-to-forward-requests-to-azure-dns"></a>Konfigurera anpassade DNS för att vidarebefordra begäran till Azure DNS 

Följ dessa steg om du vill konfigurera DNS-vidarebefordran på Windows Server 2016: 

1. I **Serverhanteraren**, klickar du på **verktyg**, och klicka sedan på **DNS**. 

   ![DNS](./media/sql-database-managed-instance-custom-dns/dns.png) 

2. Dubbelklicka på **vidarebefordrare**.

   ![Vidarebefordrare](./media/sql-database-managed-instance-custom-dns/forwarders.png) 

3. Klicka på **Redigera**. 

   ![Över vidarebefordrare](./media/sql-database-managed-instance-custom-dns/forwarders-list.png) 

4. Ange Azures rekursiv matchare IP-adress, till exempel 168.63.129.16.

   ![Rekursiva matchare IP-adress](./media/sql-database-managed-instance-custom-dns/recursive-resolvers-ip-address.png) 
 
## <a name="set-up-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Ställa in anpassad DNS som primär och Azure DNS som sekundär 
 
DNS-konfigurationen på ett virtuellt Azure-nätverk måste du ange IP-adresser, så konfigurerar det virtuella Azure-datorn som är värd för DNS-servern med en statisk IP-adress med hjälp av följande steg: 

1. Öppna nätverksgränssnittet anpassade DNS VM i Azure-portalen.

   ![network-interface](./media/sql-database-managed-instance-custom-dns/network-interface.png) 

2. I avsnittet för IP-konfigurationer. Välj IP-konfiguration 

   ![IP-konfiguration](./media/sql-database-managed-instance-custom-dns/ip-configuration.png) 


3. Ange privat IP-adress som statisk. Skriv ner IP-adress (10.0.1.5 på den här skärmbilden) 

   ![statisk](./media/sql-database-managed-instance-custom-dns/static.png) 


## <a name="register-custom-dns-as-primary-and-azure-dns-as-secondary"></a>Registrera anpassad DNS som primär och Azure DNS som sekundär 

1. Hitta den anpassade DNS-alternativet för din VNet i Azure-portalen.

   ![anpassade dns-alternativet](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Växla till anpassad och ange din anpassade DNS-serverns IP-adress som Azures rekursiv matchare IP-adress, till exempel 168.63.129.16. 

   ![anpassade dns-alternativet](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Listan leder inte ställa in Azures rekursiv matcharen i DNS hanteras-instans att ange felaktigt tillstånd. Återställa från att kräva att skapa en ny instans i ett VNet med kompatibla nätverk principer, skapa nivån instansdata och återställa databaserna. Se [konfiguration av virtuellt nätverk](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Nästa steg

- En översikt finns [vad är en hanterad instans](sql-database-managed-instance.md)
- Ett exempel för att skapa ett VNet och sedan skapa en instans för hanterade finns [att skapa en instans för hanterade](sql-database-managed-instance-tutorial-portal.md).
- För att konfigurera VNet finns [konfiguration av virtuellt nätverk för hanterade instanser](sql-database-managed-instance-vnet-configuration.md)
