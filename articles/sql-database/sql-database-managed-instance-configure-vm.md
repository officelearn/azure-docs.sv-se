---
title: Anslut klienten VM – Azure SQL Database Managed Instance | Microsoft Docs
description: Anslut till en Azure SQL Database Managed Instance med hjälp av SQL Server Management Studio från Azure-datorer.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, srbozovi, bonova
manager: craigg
ms.date: 09/13/2018
ms.openlocfilehash: 08b8e37493ea7bc549a2352aaa6714ef7c65bfdb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159524"
---
# <a name="configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Konfigurera virtuell Azure-dator att ansluta till en Azure SQL Database Managed Instance

Denna Snabbstart visar hur du konfigurerar en Azure-dator att ansluta till en Azure SQL Database Managed Instance med SQL Server Management Studio (SSMS). En Snabbstart som visar hur du ansluter från en lokal klientdator med en punkt-till-plats-anslutning, se [konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md) 

## <a name="prerequisites"></a>Förutsättningar

Den här snabbstarten används som startpunkt resurser som skapats i den här snabbstarten: [skapar en hanterad instans](sql-database-managed-instance-get-started.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Skapa ett nytt undernät i VNet för hanterad instans

Följande steg kan du skapa ett nytt undernät i hanterade instans virtuella nätverk för virtuella Azure-datorer att ansluta till den hanterade instansen. Hanterad instans-undernät som är dedikerad för hanterade instanser och du kan inte skapa andra resurser (till exempel Azure virtuella datorer) i det undernätet. 

1. Öppna resursgruppen för den hanterade instansen som du skapade i den [skapar en hanterad instans](sql-database-managed-instance-get-started.md) snabbstarten och klicka på det virtuella nätverket för din hanterade instans och klicka sedan på **undernät**.

   ![Den hanterade instansen resurser](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Klicka på den **+** logga bredvid **undernät** att skapa ett nytt undernät.

   ![Hanterad instans-undernät](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Fyll i formuläret med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ---------------- | ----------------- | ----------- | 
   | **Namn** | Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   | **Adressintervall (CIDR-block)** | Ett giltigt intervall | Standardvärdet är bra för den här snabbstarten.|
   | **Nätverkssäkerhetsgrupp** | Ingen | Standardvärdet är bra för den här snabbstarten.|
   | **Routningstabell** | Ingen | Standardvärdet är bra för den här snabbstarten.|
   | ** Tjänstslutpunkter ** | 0 som valts | Standardvärdet är bra för den här snabbstarten.|
   | **Undernät delegering** | Ingen | Standardvärdet är bra för den här snabbstarten.|
 
   ![Den nya Managed Instance undernät för Virtuella klientdatorer](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Klicka på **OK** att skapa det här extra undernätet i den hanterade instansen i virtuella nätverk.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Skapa en virtuell maskin i det nya undernätet i det virtuella nätverket

Följande steg visar hur du skapar en virtuell dator i det nya undernätet för att ansluta till den hanterade instansen. 

## <a name="prepare-the-azure-virtual-machine"></a>Förbereda virtuella Azure-datorer

Eftersom SQL Managed Instance placeras i ditt privata virtuella nätverk, måste du skapa en Azure-dator med några installerade SQL-klientverktyg som SQL Server Management Studio eller Azure Data Studio för att ansluta till den hanterade instansen och köra frågor. Den här snabbstarten använder SQL Server Management Studio.

Det enklaste sättet att skapa en virtuell dator för klienten med alla nödvändiga verktyg är att använda Azure Resource Manager-mallar.

1. Klicka på följande för att skapa en virtuell dator för klienten och installera SQL Server Management Studio (se till att du är inloggad på Azure Portal i en annan webbläsarflik):

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Fyll i formuläret med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ---------------- | ----------------- | ----------- |
   | **Prenumeration** | En giltig prenumeration | Måste vara en prenumeration där du har behörighet att skapa nya resurser |
   | **Resursgrupp** |Den resursgrupp som du angav i den [skapa Managed Instance](sql-database-managed-instance-get-started.md) Snabbstart.|Detta måste vara resursgruppen där det virtuella nätverket finns.|
   | **Plats** | Platsen för resursgruppen. | Det här värdet har fyllts i baserat på den valda resursgruppen | 
   | **Namn på virtuell dator**  | Valfritt giltigt namn | Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Administratörens användarnamn**|Giltigt användarnamn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). Använd inte ”serveradmin” eftersom det är en reserverad servernivåroll.| 
   |**Lösenord**|Valfritt giltigt lösenord|Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).|
   | **Storlek på virtuell dator** | Valfri giltig storlek | Standard i den här mallen för ** Standard_B2s är tillräcklig för den här snabbstarten. |
   | **Plats**|[resourceGroup () .location].| Ändra inte det här värdet |
   | **Namn på virtuellt nätverk**|Den plats som du tidigare valt|För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).|
   | **Namn på undernät**|Namnet på det undernät som du skapade i föregående procedur| Välj inte undernätet där du skapade den hanterade instansen|
   | **artefakter plats** | [distribution ().properties.templateLink.uri]  Ändra inte det här värdet |
   | **artefakter plats Sas-token** | Lämna tomt | Ändra inte det här värdet |

   ![Skapa en virtuell klientdator](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Om du använde de föreslagna namnet på virtuellt nätverk och standardundernät i [skapar din hanterade instans](sql-database-managed-instance-get-started.md), du behöver inte ändra sista två parametrar. Annars bör du ändra dessa värden till de värden som du angav när du konfigurerar nätverksmiljön.

3. Välj den **jag godkänner villkoren ovan** kryssrutan.
4. Klicka på **köp** att distribuera virtuella Azure-datorer i nätverket.
5. Klicka på **ikonen för meddelanden** för att visa status för distributionen.
   
   Fortsätt inte förrän Azure virtuell dator skapas. 

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Följande steg visar hur du ansluter till den nya virtuella datorn med hjälp av en fjärrskrivbordsanslutning.

1. När distributionen är klar går du till den virtuella datorresursen.

    ![Virtuell dator](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Klicka på **Anslut**. 
   
   Ett formulär för Remote Desktop Protocol (RDP-fil) visas med offentliga IP-adressen och porten för den virtuella datorn. 

   ![RDP-formulär](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Klicka på **ladda ned RDP-filen**.
 
   > [!NOTE]
   > Du kan också använda SSH för att ansluta till den virtuella datorn.

4. Stäng den **Anslut till den virtuella datorn** formuläret.
5. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. 
6. Klicka på **Anslut** när du uppmanas till det. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12) från Mac App Store.

6. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn och klicka sedan på **Ok**.

7. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

Du är ansluten till den virtuella datorn i Serverhanterarens instrumentpanel.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Använd SSMS för att ansluta till den hanterade instansen

1. Öppna SQL Server Management Studio (SSMS) på den virtuella datorn.
 
   Det tar en stund åt att öppna så att slutföra konfigurationen eftersom det är första gången SSMS har startats.
2. I den **Anslut till Server** dialogrutan anger du det fullständiga **värdnamn** för din hanterade instans i den **servernamn** väljer **SQL Server Autentisering**, ange ditt inloggningsnamn och lösenord och klicka sedan på **Connect**.

    ![ssms anslut](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

När du ansluter kan du visa system- och användardatabaserna i noden för databaser och olika objekt i noderna för säkerhet, server-objekt, replikering, hantering, SQL Server Agent och XEvent Profiler.

## <a name="next-steps"></a>Nästa steg

- En Snabbstart som visar hur du ansluter från en lokal klientdator med en punkt-till-plats-anslutning, se [konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md).
- En översikt över anslutningsalternativ för program, se [ansluta dina program till hanterad instans](sql-database-managed-instance-connect-app.md).
- Om du vill återställa en befintlig SQL Server-databas från en lokal plats till en hanterad instans, kan du använda den [Azure Database Migration Service (DMS) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) att återställa från en databassäkerhetskopia eller [T-SQL RESTORE kommandot](sql-database-managed-instance-get-started-restore.md) att återställa från en databassäkerhetskopia.
