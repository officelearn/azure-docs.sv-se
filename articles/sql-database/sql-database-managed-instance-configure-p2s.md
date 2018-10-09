---
title: Konfigurera P2S - Azure SQL Database Managed Instance | Microsoft Docs
description: Anslut till en Azure SQL Database Managed Instance med med en punkt-till-plats-anslutning från en klientdator för en lokal SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova, jovanpop
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: 370df2f13ddf9a2cf6613da95bd845ebfd0f253a
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868204"
---
# <a name="configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Konfigurera en punkt-till-plats-anslutning till en Azure SQL Database Managed Instance från en lokal plats

Den här snabbstarten visar hur du ansluter till en Azure SQL Database Managed Instance med hjälp av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) från en lokal klientdator över en punkt-till-plats-anslutning. Mer information om punkt-till-plats-anslutningar finns i [om punkt-till-plats-VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten:

- Använder de resurser som har skapats i följande snabbstart som utgångspunkt: [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- Kräver PowerShell 5.1 och Azure PowerShell 5.4.2 eller högre den lokala klientdatorn.
- Kräver den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) på den lokala klientdatorn

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Bifoga en VPN-gateway till det virtuella nätverket för hanterad instans

1. Öppna Powershell på den lokala klientdatorn.
2. Kopiera och klistra in det här PowerShell-skriptet. Det här skriptet bifogar en VPN-Gateway till det virtuella nätverket för hanterad instans som du skapade i den [skapar en hanterad instans](sql-database-managed-instance-get-started.md) Snabbstart. Det här skriptet utför följande tre steg:

   - Skapa och installera certifikat på klientdatorn
   - Beräknar framtida VPN-Gateway undernätets IP-intervall
   - Skapar GatewaySubnet
   - Distribuerar Azure Resource Manager-mallen som kopplar VPN-gatewayen till VPN-undernät

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. Ange de begärda parametrarna i PowerShell-skript. Värdena för `<subscriptionId>`, `<resourceGroup>` och `<virtualNetworkName>` måste matcha de som används i [skapa Managed Instance](sql-database-managed-instance-get-started.md) Snabbstart. Värdet för `<certificateNamePrefix>` kan vara en sträng med ditt val.

4. Kör PowerShell-skript.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Skapa en VPN-anslutning till din hanterade instans

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna resursgruppen där du skapade den virtuella nätverksgatewayen och öppna sedan den virtuella nätverksresursen på en gateway.

    ![gateway-resurs för virtuella nätverk](./media/sql-database-managed-instance-configure-p2s/vnet-gateway.png)  

3. Klicka på **punkt-till-plats-konfiguration** och klicka sedan på **hämta VPN-klient**.

    ![Ladda ned VPN-klienten](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Extrahera filerna från zip-filen och öppna den extrahera mappen.
5. Navigera till mappen WindowsAmd64 och öppna den **VpnClientSetupAmd64.exe** fil.
6. Om du får en **Windows skyddade datorn** klickar du på **mer info** och klicka sedan på **kör ändå**.

    ![Installera VPN-klienten](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Klicka på **Ja** i dialogrutan User Account Control för att fortsätta.
8. I dialogrutan MyNewVNet klickar du på **Ja** att installera en Vpn-klient för MyNewVNet.

## <a name="connect-to-the-vpn-connection"></a>Ansluta till VPN-anslutningen

1. Gå till VPN-anslutningar på din klientdator och klicka på **MyNewVNet** att upprätta en anslutning till det här virtuella nätverket.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Klicka på **Anslut**.
3. I dialogrutan MyNewVNet klickar du på **Connect**.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. När du uppmanas att Anslutningshanteraren behov förhöjda privilegier för att uppdatera din routningstabell, klickar du på **Fortsätt**.
5. Klicka på **Ja** i dialogrutan User Account Control för att fortsätta.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

   Du har skapat en VPN-anslutning till din hanterade instans i virtuellt nätverk.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Använd SSMS för att ansluta till den hanterade instansen

1. Öppna SQL Server Management Studio (SSMS) på den lokala datorn.
2. I den **Anslut till Server** dialogrutan anger du det fullständiga **värdnamn** för din hanterade instans i den **servernamn** väljer **SQL Server Autentisering**, ange ditt inloggningsnamn och lösenord och klicka sedan på **Connect**.

    ![ssms anslut](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

När du ansluter kan du visa system- och användardatabaserna i noden för databaser och olika objekt i noderna för säkerhet, server-objekt, replikering, hantering, SQL Server Agent och XEvent Profiler.

## <a name="next-steps"></a>Nästa steg

- En Snabbstart som visar hur du ansluter från en Azure virtuell dator, se [konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md)
- En översikt över anslutningsalternativen för program finns i [Ansluta dina program till hanterad instans](sql-database-managed-instance-connect-app.md).
- Om du vill återställa en lokal befintlig SQL Server-databas till en hanterad instans kan du använda [Azure Database Migration Service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) till att återställa från en databassäkerhetskopia eller kommandot [T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) till att återställa från en databassäkerhetskopia.
