---
title: Anslutningsprogram för hanterade instanser
description: I den här artikeln beskrivs hur du ansluter ditt program till Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 9f592c345b7cfcf5f21d816fde1fae6b8e6b98c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823387"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Anslut ditt program till Azure SQL Database Managed Instance

Idag har du flera val när du bestämmer hur och var du är värd för ditt program.

Du kan välja att vara värd för program i molnet antingen med hjälp av Azure App Service eller några av Azures virtuella nätverk (VNet) integrerade alternativ som Azure App Service Environment, Virtual Machine, Virtual Machine Scale Set. Du kan också ta hybridmolnsmetod och hålla dina program lokalt.

Oavsett vilket val du har gjort kan du ansluta det till en hanterad instans.  

![hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Ansluta ett program i samma virtuella nätverk

Det här scenariot är det enklaste. Virtuella datorer inuti det virtuella nätverket kan ansluta till varandra direkt även om de finns i olika undernät. Det innebär att allt du behöver för att ansluta program i en Azure Application Environment eller Virtual Machine är att ställa in anslutningssträngen på rätt sätt.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Ansluta ett program i ett annat virtuella nätverk

Det här scenariot är lite mer komplext eftersom hanterad instans har privat IP-adress i sitt eget virtuella nätverk. För att ansluta behöver ett program åtkomst till det virtuella nätverk där hanterad instans distribueras. Så först måste du göra en anslutning mellan programmet och den hanterade instansen VNet. Virtuella nätverk behöver inte vara i samma prenumeration för att det här scenariot ska fungera.

Det finns två alternativ för att ansluta virtuella nätverk:

- [Azure Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md)
- VPN-gateway för VNet till VNet ([Azure-portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Peering-alternativet är att föredra eftersom peering använder Microsofts stamnätsnätverk, så ur anslutningsperspektivet finns det ingen märkbar skillnad i svarstid mellan virtuella datorer i peered VNet och i samma virtuella nätverk. VNet-peering är begränsad till nätverken i samma region.  

> [!IMPORTANT]
> VNet peering scenario för hanterad instans är begränsad till nätverk i samma region på grund [av begränsningar för global virtuellt nätverk peering](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Mer information finns i avsnittet Om [Azure Virtual Networks Frequently Asked Questions.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 

## <a name="connect-an-on-premises-application"></a>Ansluta ett lokalt program

Hanterad instans kan endast nås via en privat IP-adress. För att komma åt den från lokala, måste du göra en plats-till-plats-anslutning mellan programmet och den hanterade instansen VNet.

Det finns två alternativ för hur du ansluter lokalt till Azure VNet:

- Plats-till-plats VPN-anslutning ([Azure-portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute-anslutning](../expressroute/expressroute-introduction.md)  

Om du har etablerat lokal till Azure-anslutning och du inte kan upprätta anslutning till hanterad instans kontrollerar du om brandväggen har öppen utgående anslutning på SQL-port 1433 samt 11000-11999-intervall av portar för omdirigering.

## <a name="connect-an-application-on-the-developers-box"></a>Ansluta ett program med utvecklarverktyg

Hanterad instans kan endast nås via en privat IP-adress, så för att komma åt den från din utvecklarruta måste du först upprätta en anslutning mellan utvecklarrutan och det hanterade instansens virtuella nätverk. Det gör du genom att konfigurera en point-to-site-anslutning till ett virtuella nätverk med inbyggd Azure-certifikatautentisering. Mer information finns i [Konfigurera en point-to-site-anslutning för att ansluta till en Hanterad Azure SQL-databas-instans från den lokala datorn](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Ansluta lokalt med VNet-peering

Ett annat scenario som implementeras av kunder är där VPN-gateway installeras i ett separat virtuellt nätverk och en prenumeration från en värd hanterad instans. De två virtuella nätverken peer-eras sedan. Följande exempelarkitekturdiagram visar hur detta kan implementeras.

![VNet-peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

När du har konfigurerat den grundläggande infrastrukturen måste du ändra vissa inställningar så att VPN-gatewayen kan se IP-adresserna i det virtuella nätverket som är värd för den hanterade instansen. Det gör du genom att göra följande mycket specifika ändringar under **peering-inställningarna**.

1. I det virtuella nätverk som är värd för VPN-gatewayen går du till **Peerings**, går sedan till den hanterade instansens peer-peer-VNet-anslutning och klickar sedan på **Tillåt gatewaytransitring**.
2. I det virtuella nätverk som är värd för den hanterade instansen går du till **Peerings**, går sedan till VPN Gateway-peer-vnet-anslutningen och klickar sedan på **Använd fjärrgateways**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Ansluta ett värdprogram för Azure App-tjänsten

Hanterad instans kan endast nås via en privat IP-adress så för att komma åt den från Azure App Service måste du först upprätta en anslutning mellan programmet och det hanterade instansenS virtuella nätverk. Se [Integrera din app med ett Virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).  

Felsökning finns i [Felsöka virtuella nätverk och program](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Om en anslutning inte kan upprättas kan du prova [att synkronisera nätverkskonfigurationen](sql-database-managed-instance-sync-network-configuration.md).

Ett specialfall att ansluta Azure App Service till hanterad instans är när du integrerar Azure App Service till ett nätverk som peered till hanterad instans VNet. Det fallet kräver att följande konfiguration ställs in:

- Hanterad instans VNet får INTE ha gateway  
- Hanterad instans VNet måste ha alternativet Använd fjärrgateways inställd
- Peered VNet måste ha alternativet Tillåt gateway transit inställt

Det här scenariot illustreras i följande diagram:

![integrerad app peering](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Funktionen VNet-integrering integrerar inte en app med ett virtuella nätverk som har en ExpressRoute Gateway. Även om ExpressRoute Gateway är konfigurerad i samexistensläge fungerar inte VNet-integreringen. Om du behöver komma åt resurser via en ExpressRoute-anslutning kan du använda en apptjänstmiljö som körs i ditt virtuella nätverk.

## <a name="troubleshooting-connectivity-issues"></a>Felsökning av anslutningsproblem

Om du vill felsöka anslutningsproblem läser du följande:

- Om du inte kan ansluta till hanterad instans från en virtuell Azure-dator i samma virtuella nätverk men ett annat undernät kontrollerar du om du har en nätverkssäkerhetsgrupp inställd på vm-undernät som kan blockera åtkomst. Observera dessutom att du måste öppna utgående anslutning på SQL-port 1433 samt portar i intervallet 11000-11999 eftersom de behövs för anslutning via omdirigering inom Azure-gränsen.
- Kontrollera att BGP-spridning är inställd på Aktiverad för den **flödestabell** som är associerad med det virtuella nätverket.
- Om du använder P2S VPN kontrollerar du konfigurationen i Azure-portalen för att se om du ser **Ingående/Egress-nummer.** Icke-noll-tal anger att Azure dirigerar trafik till/från lokalt.

   ![ingående/utgående tal](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Kontrollera att klientdatorn (som kör VPN-klienten) har vägposter för alla virtuella nätverk som du behöver komma åt. Rutterna lagras i `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Som visas i den här bilden finns det två poster för varje involverat VNet och en tredje post för VPN-slutpunkten som är konfigurerad i portalen.

   Ett annat sätt att kontrollera rutterna är via följande kommando. Utdata visar flödena till de olika undernäten:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Om du använder VNet-peering, se till att du har följt instruktionerna för att ställa in [Tillåt gateway transit och använda fjärrgateways](#connect-from-on-premises-with-vnet-peering).

## <a name="required-versions-of-drivers-and-tools"></a>Nödvändiga versioner av drivrutiner och verktyg

Följande minimala versioner av verktyg och drivrutiner rekommenderas om du vill ansluta till hanterad instans:

| Drivrutin/verktyg | Version |
| --- | --- |
|.NET Framework | 4.6.1 (eller .NET Core) |
|ODBC-drivrutin| v17 |
|PHP-drivrutin| 5.2.0 |
|JDBC-drivrutin| 6.4.0 |
|Node.js-drivrutin| 2.1.1 |
|OLEDB-drivrutin| 18.0.2.0 |
|SSMS| 18,0 eller [senare](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) eller högre |

## <a name="next-steps"></a>Nästa steg

- Information om hanterad instans finns i [Vad är en hanterad instans](sql-database-managed-instance.md).
- En självstudiekurs som visar hur du skapar en ny hanterad instans finns i [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).
