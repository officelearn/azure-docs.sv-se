---
title: Konfigurera P2S - Hanterad instans
description: Anslut till en Hanterad Azure SQL-databas-instans med SQL Server Management Studio med en point-to-site-anslutning från en lokal klientdator.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268891"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Snabbstart: Konfigurera en point-to-site-anslutning till en Hanterad Azure SQL-databasinstans från lokala

Den här snabbstarten visar hur du ansluter till en Azure SQL Database Managed Instance med [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) från en lokal klientdator via en point-to-site-anslutning. Information om point-to-site-anslutningar finns i [Om Punkt-till-plats-VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Krav

Den här snabbstarten:

- Använder de resurser som skapas [Skapa en hanterad instans](sql-database-managed-instance-get-started.md) som utgångspunkt.
- Kräver PowerShell 5.1 och AZ PowerShell 1.4.0 eller senare på den lokala klientdatorn. Om det behövs läser du instruktionerna för [installation av Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module).
- Kräver den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) på din lokala klientdator.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>Koppla en VPN-gateway till det virtuella nätverket Hanterad instans

1. Öppna PowerShell på den lokala klientdatorn.

2. Kopiera det här PowerShell-skriptet. Det här skriptet kopplar en VPN-gateway till det virtuella nätverk för hanterad instans som du skapade i [snabbstarten Skapa en hanterad instans.](sql-database-managed-instance-get-started.md) Det här skriptet använder Azure PowerShell Az Module och gör följande för antingen Windows- eller Linux-baserade värdar:

   - Skapar och installerar certifikat på klientdator
   - Beräknar det framtida IP-intervallet för VPN Gateway-undernät
   - Skapar GatewaySubnet
   - Distribuerar Azure Resource Manager-mallen som kopplar VPN-gatewayen till VPN-undernätet

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

3. Klistra in skriptet i PowerShell-fönstret och ange de parametrar som krävs. Värdena `<subscriptionId>`för `<resourceGroup>`, `<virtualNetworkName>` och bör matcha de som du använde för [snabbstarten Skapa hanterad instans.](sql-database-managed-instance-get-started.md) Värdet för `<certificateNamePrefix>` kan vara en sträng som du väljer.

4. Kör PowerShell-skriptet.

> [!IMPORTANT]
> Fortsätt inte förrän PowerShell-skriptet är klart.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>Skapa en VPN-anslutning till din hanterade instans

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Öppna resursgruppen där du skapade den virtuella nätverksgatewayen och öppna sedan den virtuella nätverksgatewayresursen.
3. Välj Konfiguration mellan punkt och plats och välj sedan **Hämta VPN-klient**. **Point-to-site configuration**

    ![Ladda ner VPN-klient](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. På den lokala klientdatorn extraherar du filerna från zip-filen och öppnar sedan mappen med de extraherade filerna.
5. Öppna mappen**WindowsAmd64** och öppna filen **VpnClientSetupAmd64.exe.**
6. Om du får ett **Windows-skyddat datormeddelande** klickar du på **Mer information** och sedan på **Kör ändå**.

    ![Installera VPN-klient](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Klicka på **Ja** i dialogrutan User Account Control om du vill fortsätta.
8. Välj **Ja** för att installera VPN-klienten för det virtuella nätverket i dialogrutan som refererar till det virtuella nätverket.

## <a name="connect-to-the-vpn-connection"></a>Ansluta till VPN-anslutningen

1. Gå till **VPN** i **Network & Internet** på din lokala klientdator och välj det virtuella nätverket Hanterad instans för att upprätta en anslutning till det här virtuella nätverket. I följande bild heter VNet **MyNewVNet**.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Välj **Anslut**.
3. Välj **Anslut**i dialogrutan .

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. När du uppmanas att Anslutningshanteraren behöver förhöjd behörighet för att uppdatera flödestabellen väljer du **Fortsätt**.
5. Välj **Ja** i dialogrutan Kontroll av användarkonton för att fortsätta.

   Du har upprättat en VPN-anslutning till ditt virtuella nätverk för hanterad instans.

    ![VPN-anslutning](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Använda SSMS för att ansluta till den hanterade instansen

1. Öppna SQL Server Management Studio (SSMS) på den lokala klientdatorn.
2. I dialogrutan **Anslut till server** anger du det fullständigt kvalificerade **värdnamnet** för den hanterade instansen i rutan **Servernamn.**
3. Välj **SQL Server-autentisering**, ange ditt användarnamn och lösenord och välj sedan **Anslut**.

    ![ssms anslut](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

När du har anslutit kan du visa system- och användardatabaserna i noden Databaser. Du kan också visa olika objekt i noderna Säkerhet, Serverobjekt, Replikering, Hantering, SQL Server Agent och XEvent Profiler.

## <a name="next-steps"></a>Nästa steg

- En snabbstart som visar hur du ansluter från en virtuell Azure-dator finns i [Konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md).
- En översikt över anslutningsalternativen för program finns i [Ansluta dina program till hanterad instans](sql-database-managed-instance-connect-app.md).
- Om du vill återställa en befintlig SQL Server-databas från lokalt till en hanterad instans kan du använda [DMS (Azure Database Migration Service) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) eller [kommandot T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) för att återställa från en säkerhetskopia av databasen.
