---
title: Konfigurera P2S-hanterad instans
description: Anslut till en Azure SQL Database Hanterad instans med SQL Server Management Studio hjälp av en punkt-till-plats-anslutning från en lokal klient dator.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 30b2ba92174996ea2bae34e7553a3258d8ebee27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268891"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Snabb start: Konfigurera en punkt-till-plats-anslutning till en Azure SQL Database Hanterad instans från den lokala platsen

Den här snabb starten visar hur du ansluter till en Azure SQL Database Hanterad instans med [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) från en lokal klient dator över en punkt-till-plats-anslutning. Information om punkt-till-plats-anslutningar finns i [om punkt-till-plats-VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Krav

Den här snabbstarten:

- Använder de resurser som skapats [skapa en hanterad instans](sql-database-managed-instance-get-started.md) som start punkt.
- Kräver PowerShell 5,1 och AZ PowerShell-1.4.0 eller senare på din lokala klient dator. Om det behövs kan du läsa anvisningarna för [att installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Kräver den nyaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) på den lokala klient datorn.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Koppla en VPN-gateway till ditt virtuella nätverk med hanterad instans

1. Öppna PowerShell på den lokala klient datorn.

2. Kopiera det här PowerShell-skriptet. Det här skriptet bifogar en VPN Gateway till det virtuella nätverket med den hanterade instansen som du skapade i snabb starten för att [skapa en hanterad instans](sql-database-managed-instance-get-started.md) . Det här skriptet använder modulen Azure PowerShell AZ och gör följande för antingen Windows-eller Linux-baserade värdar:

   - Skapar och installerar certifikat på klient datorn
   - Beräknar IP-intervall för framtida VPN Gateway undernät
   - Skapar GatewaySubnet
   - Distribuerar Azure Resource Manager-mallen som bifogar VPN Gateway till VPN-undernätet

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

3. Klistra in skriptet i PowerShell-fönstret och ange de parametrar som krävs. Värdena för `<subscriptionId>`, `<resourceGroup>`och `<virtualNetworkName>` ska matcha de som du använde för snabb starten [skapa hanterad instans](sql-database-managed-instance-get-started.md) . Värdet för `<certificateNamePrefix>` kan vara en valfri sträng.

4. Kör PowerShell-skriptet.

> [!IMPORTANT]
> Fortsätt inte förrän PowerShell-skriptet har slutförts.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Skapa en VPN-anslutning till din hanterade instans

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna resurs gruppen där du skapade den virtuella Nätverksgatewayen och öppna sedan resursen för den virtuella Nätverksgatewayen.
3. Välj **punkt-till-plats-konfiguration** och välj sedan **Ladda ned VPN-klient**.

    ![Ladda ned VPN-klient](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Extrahera filerna från zip-filen på den lokala klient datorn och öppna sedan mappen med de extraherade filerna.
5. Öppna mappen**WindowsAmd64** och öppna filen **VpnClientSetupAmd64. exe** .
6. Om du får ett **Windows-skyddat dator** meddelande klickar du på **mer info** och sedan på **kör ändå**.

    ![Installera VPN-klient](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Klicka på **Ja** i dialogrutan User Account Control om du vill fortsätta.
8. I dialog rutan som refererar till det virtuella nätverket väljer du **Ja** för att installera VPN-klienten för det virtuella nätverket.

## <a name="connect-to-the-vpn-connection"></a>Anslut till VPN-anslutningen

1. Gå till **VPN** i **nätverk & Internet** på din lokala klient dator och välj ditt virtuella nätverk för hanterade instanser för att upprätta en anslutning till det här virtuella nätverket. I följande bild heter VNet **MyNewVNet**.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Välj **Anslut**.
3. I dialog rutan väljer du **Anslut**.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. När du uppmanas att anslutnings hanteraren behöver förhöjd behörighet för att uppdatera routningstabellen väljer du **Fortsätt**.
5. Välj **Ja** i dialog rutan User Account Control för att fortsätta.

   Du har upprättat en VPN-anslutning till din hanterade instans-VNet.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Använd SSMS för att ansluta till den hanterade instansen

1. Öppna SQL Server Management Studio (SSMS) på den lokala klient datorn.
2. I dialog rutan **Anslut till Server** anger du det fullständigt kvalificerade **värd namnet** för din hanterade instans i rutan **Server namn** .
3. Välj **SQL Server autentisering**, ange ditt användar namn och lösen ord och välj sedan **Anslut**.

    ![ssms anslut](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

När du har anslutit kan du Visa system-och användar databaser i noden databaser. Du kan också visa olika objekt i noderna säkerhet, Server objekt, replikering, hantering, SQL Server Agent och XEvent profiler.

## <a name="next-steps"></a>Nästa steg

- En snabb start som visar hur du ansluter från en virtuell Azure-dator finns i [Konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md).
- En översikt över anslutningsalternativen för program finns i [Ansluta dina program till hanterad instans](sql-database-managed-instance-connect-app.md).
- Om du vill återställa en befintlig SQL Server databas från en lokal plats till en hanterad instans kan du använda [Azure Database migration service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) eller [kommandot T-SQL Restore](sql-database-managed-instance-get-started-restore.md) för att återställa från en databas säkerhets kopia.
