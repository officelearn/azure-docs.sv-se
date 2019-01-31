---
title: Anslut program för Azure SQL Database Managed Instance | Microsoft Docs
description: Den här artikeln beskrivs hur du ansluter ditt program till Azure SQL Database Managed Instance.
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
ms.date: 09/14/2018
ms.openlocfilehash: ac0513e2298877c63bb25c26de32834c07a55474
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55294156"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Anslut ditt program till Azure SQL Database Managed Instance

Idag har du flera alternativ när du bestämmer hur och var värd för ditt program. 
 
Du kan välja att vara värd för program i molnet antingen med hjälp av Azure App Service eller vissa av Azures virtuella nätverk (VNet) integrerade alternativ som Azure App Service Environment, virtuell dator, Virtual Machine Scale Sets. Du kan också ta hybridmolnlösning och behålla ditt program lokalt. 
 
Vilka alternativ som du valde ansluta du den till en hanterad instans.  

![hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)  
## <a name="connect-an-application-inside-the-same-vnet"></a>Ansluta ett program i samma virtuella nätverk 

Det här scenariot är enkel. Virtuella datorer i det virtuella nätverket kan ansluta till varandra direkt även om de finns i olika undernät. Det innebär att allt du behöver för att ansluta programmet i en miljö för Azure-program eller en virtuell dator är att ange anslutningssträngen på rätt sätt.  
 
## <a name="connect-an-application-inside-a-different-vnet"></a>Ansluta ett program i ett annat virtuellt nätverk 

Det här scenariot är lite mer komplexa eftersom hanterad instans har privat IP-adress i ett eget virtuellt nätverk. För att ansluta, behöver åtkomst till det virtuella nätverket där hanterad instans har distribuerats i ett program. Så först måste du upprätta en anslutning mellan programmet och VNet hanterade instans. De virtuella nätverken behöver inte vara i samma prenumeration för det här scenariot ska fungera. 
 
Det finns två alternativ för att ansluta virtuella nätverk: 
- [Azure Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md) 
- VNet-till-VNet VPN-gateway ([Azure-portalen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
Peering alternativet är det bättre eftersom peering använder Microsoft-stamnätverk så ur anslutningen finns det någon märkbar skillnad i fördröjning mellan virtuella datorer i peerkopplade virtuella nätverket och i samma virtuella nätverk. VNet-peering är begränsad till nätverk i samma region.  
 
> [!IMPORTANT]
> VNet-peering scenario för Managed Instance är begränsad till nätverk i samma region på grund av [begränsningarna för Global Vnet-peering](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). 

## <a name="connect-an-on-premises-application"></a>Ansluta ett lokalt program 

Hanterad instans kan endast nås via en privat IP-adress. För att komma åt den från en lokal plats måste du upprätta en plats-till-plats-anslutning mellan programmet och VNet hanterad instans. 
 
Det finns två alternativ för hur du ansluter en lokal till Azure VNet: 
- Plats-till-plats VPN-anslutning ([Azure-portalen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) anslutning  
 
Om du har skapat en lokal plats till Azure-anslutningen har och du kan inte upprätta anslutning till hanterad instans kan du kontrollera om din brandvägg har öppna utgående anslutningar på SQL-port 1433 samt 11000 12000 portintervall för omdirigering. 

## <a name="connect-an-application-on-the-developers-box"></a>Ansluta ett program på rutan utvecklare

Hanterad instans kan bara nås via en privat IP-adress så för att komma åt den från developer-box, måste du först att upprätta en anslutning mellan developer-rutan och VNet hanterade instans. Du gör detta genom att konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med Azures interna certifikatautentisering. Mer information finns i [konfigurera en punkt-till-plats-anslutning för att ansluta till en Azure SQL Database Managed Instance från den lokala datorn](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Ansluta från en lokal plats med VNet-peering
Ett annat scenario som implementeras av kunder är där VPN-gateway är installerad i ett separat virtuellt nätverk och en prenumeration från en värd Managed Instance. De två virtuella nätverken är sedan peer-kopplade. Arkitekturdiagram för följande exempel visar hur detta kan implementeras.

![VNET-peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

När du har grundläggande infrastruktur ställa in kan behöva du ändra vissa inställningen så att VPN-gatewayen kan se IP-adresser i det virtuella nätverket som är värd för den hanterade instansen. Du gör detta genom att göra följande mycket specifik ändringar under den **Peering inställningar**.
1.  I det virtuella nätverket som är värd för VPN-gateway, går du till **Peerings**, sedan till den hanterade instansen peer-kopplat VNet-anslutning och klicka sedan på **Tillåt Gatewayöverföring**.
2.  I det virtuella nätverket som är värd för den hanterade instansen, går du till **Peerings**sedan peer-kopplat VNet-anslutning till VPN-Gateway och klicka sedan på **Använd fjärrgateway**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Ansluta ett program i Azure App Service som värd 

Hanterad instans kan nås endast via en privat IP-adress så för att kunna komma åt den från Azure App Service måste du först att upprätta en anslutning mellan programmet och VNet hanterade instans. Se [integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).  
 
Felsökning, finns i [felsökning av virtuella nätverk och program](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Om det går inte att upprätta en anslutning, försök [synkroniserar nätverkskonfigurationen](sql-database-managed-instance-sync-network-configuration.md). 
 
Specialfall för att ansluta Azure App Service till Managed Instance är när du integrerade Azure App Service till ett nätverk peer-kopplas till hanterad instans VNet. Det här fallet kräver följande konfiguration konfigureras: 

- Hanterad instans virtuellt nätverk får inte ha någon gateway  
- Hanterad instans virtuellt nätverk måste ha Använd fjärrgateway alternativuppsättning 
- Peerkopplade virtuella nätverket måste ha Tillåt gateway överföring alternativuppsättning 
 
Det här scenariot illustreras i följande diagram:

![integrerad app peering](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>VNet-integrationsfunktionen integreras inte en app med ett virtuellt nätverk som har en ExpressRoute-Gateway. Även om ExpressRoute-gatewayen har konfigurerats i samexistens läge fungerar inte VNet-integrering. Om du vill komma åt resurser via en ExpressRoute-anslutning kan du använda en App Service Environment som körs i ditt virtuella nätverk.
>
 
## <a name="troubleshooting-connectivity-issues"></a>Felsökning av problem med nätverksanslutningen

För att felsöka problem med nätverksanslutningen, kontrollerar du följande:
- Om det inte går att ansluta till Managed Instance från en Azure virtuell dator i samma virtuella nätverk men olika undernät kan du kontrollera om du har en Nätverkssäkerhetsgrupp som angetts på VM-undernät som blockerar åtkomst. Dessutom Observera att du behöver öppna utgående anslutning på SQL-port 1433 samt portar i intervallet 11000 12000 eftersom de behövs för att ansluta via omdirigering inom gränsen för Azure. 
- Kontrollera att BGP-spridning är inställd på **aktiverad** för routningstabellen som är associerade med det virtuella nätverket.
- Om du använder P2S VPN, kontrollera konfigurationen i Azure portal för att se om du ser **ingående/utgående trafik** siffror. Inte är noll talen anger att Azure är dirigera trafiken till och från den lokala.

   ![ingående/utgående trafik siffror](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Kontrollera att klientdatorn (som kör den VPN-klienten) har routningsposterna för alla virtuella nätverk som du behöver komma åt. Vägarna lagras i `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.


   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   I den här bilden visas det finns två poster för varje virtuellt nätverk som ingår och en tredje post för den VPN-slutpunkt som har konfigurerats i portalen.

   Ett annat sätt att kontrollera vägar är via följande kommando. Utdata visar vägarna till de olika undernäten: 

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

- Om du använder VNet-peering, se till att du har följt anvisningarna för inställningen [Tillåt Gatewayöverföring och Använd fjärrgateway](#connect-from-on-premises-with-vnet-peering). 

## <a name="required-versions-of-drivers-and-tools"></a>Nödvändiga versionerna av drivrutiner och verktyg

Följande minsta versioner av verktyg och drivrutiner rekommenderas om du vill ansluta till hanterad instans:

| Drivrutinen/verktyget | Version |
| --- | --- |
|.NET Framework | 4.6.1 (eller .NET Core) | 
|ODBC-drivrutin    | v17 |
|PHP-drivrutinen | 5.2.0 |
|JDBC-drivrutinen    | 6.4.0 |
|Node.js-drivrutinen | 2.1.1 |
|OLEDB-drivrutin   | 18.0.2.0 |
|SSMS   | 17.8.1 eller [högre](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) |

## <a name="next-steps"></a>Nästa steg

- Information om hanterade instanser finns i avsnittet [Vad är en hanterad instans?](sql-database-managed-instance.md).
- En självstudiekurs som visar hur du skapar en ny hanterad instans finns i [skapar en hanterad instans](sql-database-managed-instance-get-started.md).
