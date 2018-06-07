---
title: Azure SQL-hanterade databasinstans ansluta program | Microsoft Docs
description: Den här artikeln beskrivs hur du ansluter tillämpningsprogrammet till hanterade Azure SQL Database-instans.
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: bea1dc88d66717717cdeacbc8504f5df7e37ba04
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647841"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Anslut ditt program till Azure SQL Database Managed Instance

Idag finns det flera alternativ när du bestämmer hur och var du är värd för ditt program. 
 
Du kan välja att vara värd för programmet i molnet antingen med hjälp av Azure App Service eller vissa av Azures virtuella nätverk (VNet) integrerad alternativ som Skalningsuppsättning i Azure Apptjänst-miljön, virtuell dator, virtuell dator. Du kan också ta hybrid cloud-metoden och hålla ditt program lokalt. 
 
Det alternativ som du valde ansluta du den till en hanterad-instans (förhandsversion).  

![Hög tillgänglighet](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Ansluta ett program i samma virtuella nätverk 

Det här scenariot är den enklaste. Virtuella datorer i det virtuella nätverket kan ansluta till varandra direkt även om de inte finns i olika undernät. Det innebär att allt du behöver ansluta program i en miljö för Azure-program eller en virtuell dator är att ange anslutningssträngen på lämpligt sätt.  
 
Om du inte kan upprätta anslutningen kan du kontrollera att du har en Nätverkssäkerhetsgrupp som angetts i programmet undernät. I det här fallet måste du öppna utgående anslutning på SQL-port 1433 som 11000 12000 portintervall för omdirigering. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Ansluta ett program i ett annat virtuellt nätverk 

Det här scenariot är lite mer komplexa eftersom hanteras instansen har en privat IP-adress i sin egen virtuella nätverk. För att ansluta, behöver ett program tillgång till VNet där hanteras instans distribueras. Så först måste du upprätta en anslutning mellan programmet och VNet hanteras instans. Vnet inte finnas i samma prenumeration för det här scenariot ska fungera. 
 
Det finns två alternativ för att ansluta Vnet: 
- [Azure Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md) 
- VNet-till-VNet VPN-gateway ([Azure-portalen](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
Alternativet peering är det bättre eftersom peering använder Microsoft stamnät network så ur anslutning finns det någon märkbar skillnad i fördröjning mellan virtuella datorer i peerkoppla VNet och i samma virtuella nätverk. VNet-peering är begränsad till nätverk i samma region.  
 
> [!IMPORTANT]
> VNet-peering scenario för hanterade instansen är begränsad till nätverk i samma region på grund av att [begränsningarna för globala virtuella nätverk peering](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). 

## <a name="connect-an-on-premises-application"></a>Ansluta ett lokalt program 

Hanterade instans kan endast nås via en privat IP-adress. För att komma åt den från lokala måste du skapa en plats-till-plats-anslutning mellan programmet och VNet hanteras instans. 
 
Det finns två alternativ ansluta lokalt till Azure VNet: 
- Plats-till-plats VPN-anslutning ([Azure-portalen](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- [ExpressRoute](../expressroute/expressroute-introduction.md) anslutning  
 
Om du har skapat lokalt på Azure-anslutning har du inte kan upprätta anslutningen till instansen hanteras, måste du kontrollera brandväggen har öppen utgående anslutning för SQL-port 1433 som 11000 12000 portintervall för omdirigering. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Ansluta ett program i Azure App Service finns 

Hanterad instans kan nås endast via en privat IP-adress så för att komma åt den från Azure App Service måste du först upprätta en anslutning mellan programmet och VNet hanteras instans. Se [integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).  
 
Vid felsökning av [felsökning Vnet och program](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Om inte det går att upprätta en anslutning, försök [synkroniserar nätverkskonfigurationen](sql-database-managed-instance-sync-network-configuration.md). 
 
Ett specialfall av Azure App Service som ansluter till hanterade instans är när du har integrerat Azure App Service till ett nätverk som är peerkopplat till hanterade instans virtuellt nätverk. Ärendet kräver att ställa in följande konfiguration: 

- Hanterade instans virtuellt nätverk får inte ha gateway  
- Hanterade instans virtuella nätverk måste ha Använd remote gateways alternativuppsättning 
- Peerkoppla virtuella nätverk måste ha Tillåt gateway överföring alternativuppsättning 
 
Det här scenariot illustreras i följande diagram:

![integrerad app peering](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Ansluta ett program på rutan för utvecklare 

Hanterad instans kan nås endast via en privat IP-adress så för att komma åt den från developer-rutan, måste du först att upprätta en anslutning mellan developer-rutan och VNet hanteras instans.  
 
Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av inbyggda Azure certifikat autentisering artiklar ([Azure-portalen](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) visar i detalj hur Det kan göras.  

## <a name="next-steps"></a>Nästa steg

- Information om hanterade instansen finns [vad är en hanterad instans](sql-database-managed-instance.md).
- En självstudiekurs visar hur du skapar en ny instans av hanterade finns [skapa en instans för hanterade](sql-database-managed-instance-create-tutorial-portal.md).
