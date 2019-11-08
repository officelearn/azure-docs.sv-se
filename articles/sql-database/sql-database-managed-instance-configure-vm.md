---
title: Anslut klientens VM-hanterade instans
description: Anslut till en Azure SQL Database Hanterad instans med SQL Server Management Studio från en virtuell Azure-dator.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a455607b1459ebc9e37b1df70b454feea76d1f2f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822782"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Snabb start: Konfigurera virtuell Azure-dator för att ansluta till en Azure SQL Database Hanterad instans

Den här snabb starten visar hur du konfigurerar en virtuell Azure-dator för att ansluta till en Azure SQL Database Hanterad instans med SQL Server Management Studio (SSMS). En snabb start som visar hur du ansluter från en lokal klient dator med en punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

I den här snabb starten används de resurser som skapats i [skapa en hanterad instans](sql-database-managed-instance-get-started.md) som start punkt.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Skapa ett nytt undernät i det hanterade instans-VNet

Följande steg skapar ett nytt undernät i den hanterade instansens VNet så att en virtuell Azure-dator kan ansluta till den hanterade instansen. Under nätet för hanterade instanser är dedikerat till hanterade instanser. Du kan inte skapa några andra resurser, t. ex. Azure Virtual Machines, i det under nätet.

1. Öppna resurs gruppen för den hanterade instans som du skapade i snabb starten för att [skapa en hanterad instans](sql-database-managed-instance-get-started.md) . Välj det virtuella nätverket för din hanterade instans.

   ![Hanterade instansresurser](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Välj **undernät** och välj sedan **+ undernät** för att skapa ett nytt undernät.

   ![Under nät för hanterade instanser](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Fyll i formuläret med hjälp av informationen i den här tabellen:

   | Inställning| Föreslaget värde | Beskrivning |
   | ---------------- | ----------------- | ----------- |
   | **Namn** | Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).|
   | **Adressintervall (CIDR-block)** | Ett giltigt intervall | Standardvärdet är användbart för den här snabb starten.|
   | **Nätverkssäkerhetsgrupp** | Ingen | Standardvärdet är användbart för den här snabb starten.|
   | **Routningstabell** | Ingen | Standardvärdet är användbart för den här snabb starten.|
   | **Serviceslutpunkter** | 0 har valts | Standardvärdet är användbart för den här snabb starten.|
   | **Under näts delegering** | Ingen | Standardvärdet är användbart för den här snabb starten.|

   ![Nytt hanterat instans under nät för virtuell klient dator](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Välj **OK** för att skapa ytterligare ett undernät i den hanterade instansens VNet.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Skapa en virtuell maskin i det nya undernätet i det virtuella nätverket

Följande steg visar hur du skapar en virtuell dator i det nya under nätet för att ansluta till den hanterade instansen.

## <a name="prepare-the-azure-virtual-machine"></a>Förbered den virtuella Azure-datorn

Eftersom SQL-hanterad instans placeras i din privata Virtual Network måste du skapa en virtuell Azure-dator med ett installerat SQL-klient verktyg, t. ex. SQL Server Management Studio eller Azure Data Studio. Med det här verktyget kan du ansluta till den hanterade instansen och köra frågor. Den här snabb starten använder SQL Server Management Studio.

Det enklaste sättet att skapa en virtuell klient dator med alla nödvändiga verktyg är att använda Azure Resource Manager mallar.

1. Kontrol lera att du är inloggad på Azure Portal på en annan flik i webbläsaren. Välj sedan följande knapp för att skapa en virtuell klient dator och installera SQL Server Management Studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Fyll i formuläret med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ---------------- | ----------------- | ----------- |
   | **Prenumeration** | En giltig prenumeration | Måste vara en prenumeration där du har behörighet att skapa nya resurser. |
   | **Resursgrupp** |Den resurs grupp som du angav i snabb starten för att [skapa hanterade instanser](sql-database-managed-instance-get-started.md) .|Den här resurs gruppen måste vara den som VNet finns i.|
   | **Plats** | Platsen för resurs gruppen | Det här värdet fylls i baserat på den valda resurs gruppen. |
   | **Namn på virtuell dator**  | Valfritt giltigt namn | Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).|
   |**Administratörens användar namn**|Alla giltiga användar namn|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging). Använd inte ”serveradmin” eftersom det är en reserverad servernivåroll.<br>Du använder det här användar namnet varje gången du [ansluter till den virtuella datorn](#connect-to-virtual-machine).|
   |**Lösenord**|Valfritt giltigt lösenord|Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Du använder det här lösen ordet när som helst när du [ansluter till den virtuella datorn](#connect-to-virtual-machine).|
   | **Storlek på virtuell dator** | Vilken giltig storlek som helst | Standardvärdet i den här mallen för **Standard_B2s** räcker för den här snabb starten. |
   | **Plats**|[resourceGroup (). location].| Ändra inte det här värdet. |
   | **Virtual Network namn**|Det virtuella nätverk där du skapade den hanterade instansen.|
   | **Namn på undernät**|Namnet på det undernät som du skapade i föregående procedur| Välj inte det undernät där du skapade den hanterade instansen.|
   | **artefakt plats** | [Deployment (). Properties. templateLink. URI] | Ändra inte det här värdet. |
   | **SAS-token för artefakt plats** | Lämna tomt | Ändra inte det här värdet. |

   ![Skapa en virtuell klientdator](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Om du använde det föreslagna VNet-namnet och standard under nätet för att [skapa din hanterade instans](sql-database-managed-instance-get-started.md), behöver du inte ändra de två senaste parametrarna. Annars bör du ändra dessa värden till de värden som du angav när du konfigurerade nätverks miljön.

3. Markera kryss rutan **Jag accepterar villkoren som anges ovan** .
4. Välj **köp** för att distribuera den virtuella Azure-datorn i nätverket.
5. Välj ikonen **Meddelanden** för att visa status för distributionen.

> [!IMPORTANT]
> Fortsätt inte förrän cirka 15 minuter efter det att den virtuella datorn har skapats för att ange tid för att de färdiga skripten ska installeras SQL Server Management Studio.

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Följande steg visar hur du ansluter till den nya virtuella datorn med hjälp av en fjärrskrivbordsanslutning.

1. När distributionen är klar går du till den virtuella datorresursen.

    ![Virtuell dator](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Välj **Anslut**.

   En Remote Desktop Protocol fil (. RDP-fil) visas med den offentliga IP-adressen och port numret för den virtuella datorn.

   ![RDP-formulär](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Välj **Ladda ned RDP-fil**.

   > [!NOTE]
   > Du kan också använda SSH för att ansluta till din virtuella dator.

4. Stäng formuläret **Anslut till virtuell dator** .
5. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn.
6. När du uppmanas väljer du **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

7. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn och välj sedan **OK**.

8. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

Du är ansluten till den virtuella datorn i Serverhanteraren instrument panelen.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Använd SSMS för att ansluta till den hanterade instansen

1. Öppna SQL Server Management Studio (SSMS) i den virtuella datorn.

   Det tar en stund att öppna eftersom den måste slutföra konfigurationen eftersom det är första gången SSMS har startats.
2. I dialog rutan **Anslut till Server** anger du det fullständigt kvalificerade **värd namnet** för din hanterade instans i rutan **Server namn** . Välj **SQL Server autentisering**, ange ditt användar namn och lösen ord och välj sedan **Anslut**.

    ![ssms anslut](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

När du ansluter kan du visa system- och användardatabaserna i noden för databaser och olika objekt i noderna för säkerhet, server-objekt, replikering, hantering, SQL Server Agent och XEvent Profiler.

## <a name="next-steps"></a>Nästa steg

- En snabb start som visar hur du ansluter från en lokal klient dator med en punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats](sql-database-managed-instance-configure-p2s.md)-anslutning.
- En översikt över anslutningsalternativen för olika program finns i [Ansluta dina program till Managed Instance](sql-database-managed-instance-connect-app.md).
- Om du vill återställa en befintlig SQL Server databas från en lokal plats till en hanterad instans kan du använda [Azure Database migration service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) eller [kommandot T-SQL Restore](sql-database-managed-instance-get-started-restore.md) för att återställa från en databas säkerhets kopia.
