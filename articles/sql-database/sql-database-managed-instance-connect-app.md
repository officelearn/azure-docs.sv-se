---
title: Anslut program för Azure SQL Database Managed Instance | Microsoft Docs
description: Den här artikeln beskrivs hur du ansluter ditt program till Azure SQL Database Managed Instance.
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 82e8836892b033ccbb3c3ad9806257348afe3702
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818410"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Anslut ditt program till Azure SQL Database Managed Instance

Idag har du flera alternativ när du bestämmer hur och var värd för ditt program. 
 
Du kan välja att vara värd för program i molnet antingen med hjälp av Azure App Service eller vissa av Azures virtuella nätverk (VNet) integrerade alternativ som Azure App Service Environment, virtuell dator, Virtual Machine Scale Sets. Du kan också ta hybridmolnlösning och behålla ditt program lokalt. 
 
Vilka alternativ som du valde ansluta du den till en hanterad instans (förhandsversion).  

![hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Ansluta ett program i samma virtuella nätverk 

Det här scenariot är enkel. Virtuella datorer i det virtuella nätverket kan ansluta till varandra direkt även om de finns i olika undernät. Det innebär att allt du behöver för att ansluta programmet i en miljö för Azure-program eller en virtuell dator är att ange anslutningssträngen på rätt sätt.  
 
Om du inte kan upprätta anslutningen, kan du kontrollera om du har en Nätverkssäkerhetsgrupp som angetts i programmet undernät. I så fall måste du öppna utgående anslutning på SQL-port 1433 samt 11000 12000 portintervall för omdirigering. 

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

## <a name="connect-an-azure-app-service-hosted-application"></a>Ansluta ett program i Azure App Service som värd 

Hanterad instans kan nås endast via en privat IP-adress så för att kunna komma åt den från Azure App Service måste du först att upprätta en anslutning mellan programmet och VNet hanterade instans. Se [integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).  
 
Felsökning, finns i [felsökning av virtuella nätverk och program](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Om det går inte att upprätta en anslutning, försök [synkroniserar nätverkskonfigurationen](sql-database-managed-instance-sync-network-configuration.md). 
 
Specialfall för att ansluta Azure App Service till Managed Instance är när du integrerade Azure App Service till ett nätverk peer-kopplas till hanterad instans VNet. Det här fallet kräver följande konfiguration konfigureras: 

- Hanterad instans virtuellt nätverk får inte ha någon gateway  
- Hanterad instans virtuellt nätverk måste ha Använd fjärrgateway alternativuppsättning 
- Peerkopplade virtuella nätverket måste ha Tillåt gateway överföring alternativuppsättning 
 
Det här scenariot illustreras i följande diagram:

![integrerad app peering](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Ansluta ett program på rutan utvecklare 

Hanterad instans kan bara nås via en privat IP-adress så för att komma åt den från developer-box, måste du först att upprätta en anslutning mellan developer-rutan och VNet hanterade instans.  
 
Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med intern Azure-certifikat autentisering artiklar ([Azure-portalen](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) innehåller detaljerad information om hur Det kan göras. 

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
|SSMS   | 17.8.1 eller [högre](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) |

## <a name="next-steps"></a>Nästa steg

- Information om Managed Instance finns i [vad är en hanterad instans](sql-database-managed-instance.md).
- En självstudiekurs som visar hur du skapar en ny hanterad instans finns i [skapar en hanterad instans](sql-database-managed-instance-get-started.md).
