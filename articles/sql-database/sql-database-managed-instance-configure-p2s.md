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
ms.date: 12/14/2018
ms.openlocfilehash: e8d6d48461e41353057bd554b9e898d118e68ab0
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545305"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Snabbstart: Konfigurera en punkt-till-plats-anslutning till en Azure SQL Database Managed Instance från en lokal plats

Den här snabbstarten visar hur du ansluter till en Azure SQL Database Managed Instance med hjälp av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) från en lokal klientdator över en punkt-till-plats-anslutning. Mer information om punkt-till-plats-anslutningar finns i [om punkt-till-plats-VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten:

- Använder resurserna som du skapade [skapar en hanterad instans](sql-database-managed-instance-get-started.md) som startpunkt.
- Kräver PowerShell 5.1 och Azure PowerShell 5.4.2 eller senare på den lokala klientdatorn. Om det behövs, se anvisningarna för [installerar Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0#install-the-azure-powershell-module).
- Kräver den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) på den lokala klientdatorn.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Bifoga en VPN-gateway till det virtuella nätverket för hanterad instans

1. Öppna Powershell på den lokala klientdatorn.
2. Kopiera följande PowerShell-skript. Det här skriptet bifogar en VPN-Gateway till det virtuella nätverket för hanterad instans som du skapade i den [skapar en hanterad instans](sql-database-managed-instance-get-started.md) Snabbstart. Skriptet gör följande:

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

3. Klistra in skriptet i PowerShell-fönster och ange de obligatoriska parametrarna. Värdena för `<subscriptionId>`, `<resourceGroup>`, och `<virtualNetworkName>` måste matcha de som du använde för den [skapa Managed Instance](sql-database-managed-instance-get-started.md) Snabbstart. Värdet för `<certificateNamePrefix>` kan vara en sträng med ditt val.

4. Kör PowerShell-skript.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Skapa en VPN-anslutning till din hanterade instans

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Öppna resursgruppen där du skapade den virtuella nätverksgatewayen och öppna sedan den virtuella nätverksresursen på en gateway.
3. Välj **punkt-till-plats-konfiguration** och välj sedan **hämta VPN-klient**.

    ![Ladda ned VPN-klienten](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Extrahera filerna från zip-filen på din lokala dator och sedan öppna den extrahera mappen.
5. Öppna mappen WindowsAmd64 och öppna den **VpnClientSetupAmd64.exe** fil.
6. Om du får en **Windows skyddade datorn** meddelandet markerar **mer info** och välj sedan **kör ändå**.

    ![Installera VPN-klienten](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Välj **Ja** i dialogrutan User Account Control för att fortsätta.
8. I dialogrutan som refererar till det virtuella nätverket väljer **Ja** att installera VPN-klienten.

## <a name="connect-to-the-vpn-connection"></a>Ansluta till VPN-anslutningen

1. Gå till VPN-anslutningar på din lokala dator och välj ditt virtuella nätverk för hanterad instans att upprätta en anslutning till det här virtuella nätverket. I följande bild, det virtuella nätverket namnet **MyNewVNet**.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Välj **Anslut**.
3. I dialogrutan Välj **Connect**.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. När du uppmanas att Connection Manager behöver förhöjda privilegier för att uppdatera din routningstabell, Välj **Fortsätt**.
5. Välj **Ja** i dialogrutan User Account Control för att fortsätta.

   Du har skapat en VPN-anslutning till din hanterade instans i virtuellt nätverk.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  


## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Använd SSMS för att ansluta till den hanterade instansen

1. Öppna SQL Server Management Studio (SSMS) på den lokala datorn.
2. I den **Anslut till Server** dialogrutan anger du det fullständiga **värdnamn** för din hanterade instans i den **servernamn** box. 
1. Välj **SQL Server-autentisering**, ange ditt användarnamn och lösenord och välj sedan **Connect**.

    ![ssms anslut](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

När du ansluter kan kan du visa din system- och användardatabaserna i noden databaser. Du kan också visa olika objekt i noderna säkerhet, Server-objekt, replikering, hantering, SQL Server Agent och XEvent Profiler.

## <a name="next-steps"></a>Nästa steg

- En Snabbstart som visar hur du ansluter från en Azure virtuell dator, se [konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md).
- En översikt över anslutningsalternativen för olika program finns i [Ansluta dina program till Managed Instance](sql-database-managed-instance-connect-app.md).
- Om du vill återställa en befintlig SQL Server-databas från en lokal plats till en hanterad instans, kan du använda den [Azure Database Migration Service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) eller [T-SQL RESTORE-kommandot](sql-database-managed-instance-get-started-restore.md) att återställa från en säkerhetskopian av databasfilen.
