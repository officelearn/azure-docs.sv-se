---
title: Azure SQL Database Hanterad instans Connect-program | Microsoft Docs
description: Den här artikeln beskriver hur du ansluter ditt program till Azure SQL Database Hanterad instans.
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
ms.openlocfilehash: 133110d015ac7a26f18f14f6ff957729a4f079b5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060651"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Anslut ditt program till Azure SQL Database Hanterad instans

Idag har du flera val när du bestämmer hur och var du är värd för ditt program.

Du kan välja att vara värd för programmet i molnet antingen genom att använda Azure App Service eller vissa av Azures virtuella nätverk (VNet) integrerade alternativ som Azure App Service-miljön, virtuell dator, skalnings uppsättning för virtuella datorer. Du kan också ta hybrid moln och se till att dina program är lokalt.

Vilket val du gjort kan du ansluta det till en hanterad instans.  

![hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Ansluta ett program inuti samma VNet

Det här scenariot är det enklaste. Virtuella datorer i VNet kan ansluta till varandra direkt även om de finns i olika undernät. Det innebär att allt du behöver för att ansluta program i en Azure Application-miljö eller virtuell dator är att ange anslutnings strängen på lämpligt sätt.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Ansluta ett program i ett annat virtuellt nätverk

Det här scenariot är lite mer komplicerat eftersom den hanterade instansen har en privat IP-adress i sitt eget VNet. För att du ska kunna ansluta behöver ett program åtkomst till det VNet där en hanterad instans distribueras. Så först måste du upprätta en anslutning mellan programmet och den hanterade instansen VNet. Virtuella nätverk behöver inte finnas i samma prenumeration för att det här scenariot ska fungera.

Det finns två alternativ för att ansluta virtuella nätverk:

- [Azure Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md)
- VNet-till-VNet VPN-gateway ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [POWERSHELL](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Peering-alternativet är det som föredras eftersom peering använder Microsofts stamnät nätverk, så att det inte finns någon märkbar skillnad i svars tiden mellan virtuella datorer i peer-kopplat VNet och i samma VNet. VNet-peering är begränsad till nätverken i samma region.  

> [!IMPORTANT]
> VNet-peering-scenariot för hanterade instanser är begränsat till nätverken i samma region på grund av [begränsningar i den globala Virtual Network](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)-peeringen. Se även det relevanta avsnittet i artikeln [vanliga frågor och svar om Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) för mer information. 

## <a name="connect-an-on-premises-application"></a>Ansluta ett lokalt program

Den hanterade instansen kan bara nås via en privat IP-adress. För att kunna komma åt den från en lokal plats måste du skapa en plats-till-plats-anslutning mellan programmet och den hanterade instansen VNet.

Det finns två alternativ för att ansluta lokalt till Azure VNet:

- Plats-till-plats-VPN-anslutning ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [POWERSHELL](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../expressroute/expressroute-introduction.md) -anslutning  

Om du har upprättat lokal anslutning till Azure-anslutningen och du inte kan upprätta en anslutning till en hanterad instans kontrollerar du om brand väggen har öppen utgående anslutning på SQL-port 1433 samt 11000-11999 port intervall för omdirigering.

## <a name="connect-an-application-on-the-developers-box"></a>Ansluta ett program i rutan utvecklare

Den hanterade instansen kan bara nås via en privat IP-adress, så för att få åtkomst till den från din utvecklare måste du först skapa en anslutning mellan din utvecklings ruta och det hanterade instans-VNet. Det gör du genom att konfigurera en punkt-till-plats-anslutning till ett VNet med intern Azure-certifikatautentisering. Mer information finns i [Konfigurera en punkt-till-plats-anslutning för att ansluta till en Azure SQL Database Hanterad instans från den lokala datorn](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Ansluta lokalt med VNet-peering

Ett annat scenario som implementeras av kunder är var VPN-gatewayen är installerad i ett separat virtuellt nätverk och en prenumeration från den hanterade instansen som är värd. De två virtuella nätverken är sedan peer-kopplade. Följande exempel arkitektur diagram visar hur detta kan implementeras.

![VNET-peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

När du har konfigurerat den grundläggande infrastrukturen måste du ändra vissa inställningar så att VPN Gateway kan se IP-adresserna i det virtuella nätverket som är värd för den hanterade instansen. Det gör du genom att göra följande mycket speciella ändringar under peering- **inställningarna**.

1. I det VNet som är värd för VPN-gatewayengår du till peering, sedan till den hanterade instansen med peer-anslutning och klickar sedan på **Tillåt Gateway-överföring**.
2. I det virtuella nätverket som är värd för den hanteradeinstansen går du till peering, sedan till VPN gateway peer-ansluten VNET-anslutning och klickar sedan på **Använd**fjärrgatewayer.

## <a name="connect-an-azure-app-service-hosted-application"></a>Ansluta ett Azure App Service värdbaserade program

Hanterad instans kan endast nås via en privat IP-adress, så för att få åtkomst till den från Azure App Service måste du först upprätta en anslutning mellan programmet och den hanterade instansen VNet. Se [integrera din app med en Azure-Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  

Fel sökning finns i [Felsöka virtuella nätverk och program](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Försök att [Synkronisera nätverks konfigurationen](sql-database-managed-instance-sync-network-configuration.md)om det inte går att upprätta en anslutning.

Ett specialfall av att ansluta Azure App Service till hanterade instanser är när du integrerar Azure App Service till ett nätverk som är peer-kopplat till hanterad instans-VNet. Det innebär att följande konfiguration måste konfigureras:

- Den hanterade instansens VNet får inte ha någon Gateway  
- Den hanterade instansens VNet måste ha alternativet Använd fjärran sluten Gateway
- Peer-kopplat VNet måste ha alternativ uppsättningen Tillåt Gateway-överföring

Det här scenariot illustreras i följande diagram:

![integrerad app-peering](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>Funktionen för VNet-integrering integrerar inte en app med ett VNet som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har kon figurer ATS i läget för samexistens fungerar inte VNet-integreringen. Om du behöver åtkomst till resurser via en ExpressRoute-anslutning kan du använda en App Service-miljön som körs i ditt VNet.

## <a name="troubleshooting-connectivity-issues"></a>Felsöka anslutnings problem

För fel sökning av anslutnings problem, se följande:

- Om du inte kan ansluta till den hanterade instansen från en virtuell Azure-dator inom samma VNet men till ett annat undernät, kontrollerar du om du har en nätverks säkerhets grupp som är inställd på VM-undernät som kan blockera åtkomst. Observera också att du måste öppna utgående anslutningar på SQL-port 1433 samt portar i intervallet 11000-11999 eftersom de behövs för att ansluta via omdirigering i Azure-gräns.
- Se till att BGP-spridningen är **aktive rad** för den routningstabell som är associerad med det virtuella nätverket.
- Om du använder P2S VPN kontrollerar du konfigurationen i Azure Portal för att se om du ser ingångs **-/** utgångs nummer. Siffror som inte är noll anger att Azure dirigerar trafik till/från lokalt.

   ![inkommande/utgående nummer](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Kontrol lera att klient datorn (som kör VPN-klienten) har väg poster för alla virtuella nätverk som du behöver komma åt. Vägarna lagras i `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![Route. txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Som det visas i den här bilden finns det två poster för varje virtuellt nätverk och en tredje post för VPN-slutpunkten som har kon figurer ATS i portalen.

   Ett annat sätt att kontrol lera vägarna är via följande kommando. Utdata visar vägarna till olika undernät:

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

- Om du använder VNet-peering måste du kontrol lera att du har följt anvisningarna för att ställa in [Tillåt Gateway-överföring och använda](#connect-from-on-premises-with-vnet-peering)fjärrgatewayer.

## <a name="required-versions-of-drivers-and-tools"></a>Nödvändiga versioner av driv rutiner och verktyg

Följande minimala versioner av verktygen och driv rutinerna rekommenderas om du vill ansluta till en hanterad instans:

| Driv rutin/verktyg | Version |
| --- | --- |
|.NET Framework | 4.6.1 (eller .NET Core) |
|ODBC-drivrutin| v17 |
|PHP-drivrutin| 5.2.0 |
|JDBC-drivrutin| 6.4.0 |
|Node.js-drivrutin| punkt |
|OLEDB-drivrutin| 18.0.2.0 |
|SSMS| 18,0 eller [högre](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) eller högre |

## <a name="next-steps"></a>Nästa steg

- Information om hanterade instanser finns i avsnittet [Vad är en hanterad instans?](sql-database-managed-instance.md).
- En själv studie kurs som visar hur du skapar en ny hanterad instans finns i [skapa en hanterad instans](sql-database-managed-instance-get-started.md).
