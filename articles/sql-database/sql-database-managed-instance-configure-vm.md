---
title: Ansluta klient-VM - hanterad instans
description: Anslut till en Hanterad Azure SQL-databas-instans med SQL Server Management Studio från en virtuell Azure-dator.
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
ms.openlocfilehash: 8b5dce0b43fac7cfd0e974f26451338ca1541f8f
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528423"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Snabbstart: Konfigurera Azure VM för att ansluta till en hanterad Azure SQL-databas-instans

Den här snabbstarten visar hur du konfigurerar en virtuell Azure-dator för att ansluta till en Hanterad Azure SQL-databas-instans med SQL Server Management Studio (SSMS). En snabbstart som visar hur du ansluter från en lokal klientdator med en punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>Krav

Den här snabbstarten använder de resurser som skapas i [Skapa en hanterad instans](sql-database-managed-instance-get-started.md) som utgångspunkt.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Skapa ett nytt undernät i det hanterade instansens virtuella nätverk

Följande steg skapar ett nytt undernät i det hanterade instansens virtuella nätverk så att en virtuell Azure-dator kan ansluta till den hanterade instansen. Undernätet Hanterad instans är dedikerat till hanterade instanser. Du kan inte skapa andra resurser, som virtuella Azure-datorer, i det undernätet.

1. Öppna resursgruppen för den hanterade instans som du skapade i [snabbstarten Skapa en hanterad instans.](sql-database-managed-instance-get-started.md) Välj det virtuella nätverket för den hanterade instansen.

   ![Hanterade instansresurser](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Välj **Undernät** och välj sedan **+ Undernät** om du vill skapa ett nytt undernät.

   ![Undernät för hanterad instans](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Fyll i formuläret med hjälp av informationen i den här tabellen:

   | Inställning| Föreslaget värde | Beskrivning |
   | ---------------- | ----------------- | ----------- |
   | **Namn** | Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   | **Adressintervall (CIDR-block)** | Ett giltigt intervall | Standardvärdet är bra för den här snabbstarten.|
   | **Säkerhetsgrupp för nätverk** | Inget | Standardvärdet är bra för den här snabbstarten.|
   | **Routningstabell** | Inget | Standardvärdet är bra för den här snabbstarten.|
   | **Tjänstslutpunkter** | 0 valda | Standardvärdet är bra för den här snabbstarten.|
   | **Delegering av undernät** | Inget | Standardvärdet är bra för den här snabbstarten.|

   ![Nytt hanterat instansundernät för klient-VM](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Välj **OK** om du vill skapa ytterligare ett undernät i det hanterade instansens virtuella nätverk.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Skapa en virtuell maskin i det nya undernätet i det virtuella nätverket

Följande steg visar hur du skapar en virtuell dator i det nya undernätet för att ansluta till den hanterade instansen.

## <a name="prepare-the-azure-virtual-machine"></a>Förbereda den virtuella Azure-datorn

Eftersom SQL Managed Instance placeras i ditt privata virtuella nätverk måste du skapa en Virtuell Azure-dator med ett installerat SQL-klientverktyg, till exempel SQL Server Management Studio eller Azure Data Studio. Med det här verktyget kan du ansluta till den hanterade instansen och köra frågor. Den här snabbstarten använder SQL Server Management Studio.

Det enklaste sättet att skapa en klient virtuell dator med alla nödvändiga verktyg är att använda Azure Resource Manager mallar.

1. Kontrollera att du är inloggad på Azure-portalen på en annan webbläsarflik. Välj sedan följande knapp för att skapa en virtuell klientdator och installera SQL Server Management Studio:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Fyll i formuläret med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ---------------- | ----------------- | ----------- |
   | **Prenumeration** | En giltig prenumeration | Måste vara en prenumeration där du har behörighet att skapa nya resurser. |
   | **Resursgrupp** |Resursgruppen som du angav i [snabbstarten Skapa hanterad instans.](sql-database-managed-instance-get-started.md)|Den här resursgruppen måste vara den där det virtuella nätverket finns.|
   | **Location** | Platsen för resursgruppen | Det här värdet fylls i baserat på den resursgrupp som valts. |
   | **Namn på virtuell dator**  | Valfritt giltigt namn | Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|
   |**Administratörsanvändarnamn**|Alla giltiga användarnamn|Giltiga namn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). Använd inte ”serveradmin” eftersom det är en reserverad servernivåroll.<br>Du använder det här [användarnamnet](#connect-to-virtual-machine)varje gång du ansluter till den virtuella datorn .|
   |**Lösenord**|Valfritt giltigt lösenord|Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Du använder det här lösenordet när du [ansluter till den virtuella datorn](#connect-to-virtual-machine).|
   | **Storlek på virtuell dator** | Valfri giltig storlek | Standardmallen i **den** här mallen för Standard_B2s är tillräcklig för den här snabbstarten. |
   | **Location**|[resourceGroup().location].| Ändra inte det här värdet. |
   | **Namn på virtuellt nätverk**|Det virtuella nätverk där du skapade den hanterade instansen.|
   | **Namn på undernät**|Namnet på undernätet som du skapade i föregående procedur| Välj inte det undernät där du skapade den hanterade instansen.|
   | **artefakter Plats** | [deployment().properties.templateLink.uri] | Ändra inte det här värdet. |
   | **artefakter Plats Sas token** | lämna tomt | Ändra inte det här värdet. |

   ![Skapa en virtuell klientdator](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Om du använde det föreslagna VNet-namnet och standardundernätet för att [skapa den hanterade instansen](sql-database-managed-instance-get-started.md)behöver du inte ändra de två senaste parametrarna. Annars bör du ändra dessa värden till de värden som du angav när du konfigurerade nätverksmiljön.

3. Välj **kryssrutan Jag godkänner de villkor som anges ovan.**
4. Välj **Inköp för** att distribuera den virtuella Azure-datorn i nätverket.
5. Välj ikonen **Meddelanden** för att visa status för distributionen.

> [!IMPORTANT]
> Fortsätt inte förrän cirka 15 minuter efter att den virtuella datorn har skapats för att ge tid för skript för efterskapning att installera SQL Server Management Studio.

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Följande steg visar hur du ansluter till den nya virtuella datorn med hjälp av en fjärrskrivbordsanslutning.

1. När distributionen är klar går du till den virtuella datorresursen.

    ![Virtuell dator](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Välj **Anslut**.

   Ett filformulär för fjärrskrivbordsprotokoll (.rdp-fil) visas med den offentliga IP-adressen och portnumret för den virtuella datorn.

   ![RDP-formulär](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Välj **Ladda ned RDP-fil**.

   > [!NOTE]
   > Du kan också använda SSH för att ansluta till din virtuella dator.

4. Stäng formuläret **Anslut till virtuell dator.**
5. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn.
6. När du uppmanas till det väljer du **Anslut**. På en Mac-dator behöver du en RDP-klient som denna [Fjärrskrivbordsklient](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) från Mac App Store.

7. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn och välj sedan **OK**.

8. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** att fortsätta med anslutningen.

Du är ansluten till den virtuella datorn på instrumentpanelen i Serverhanteraren.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Använda SSMS för att ansluta till den hanterade instansen

1. Öppna SQL Server Management Studio (SSMS) på den virtuella datorn.

   Det tar en stund att öppna eftersom den behöver för att slutföra sin konfiguration eftersom detta är första gången SSMS har startats.
2. I dialogrutan **Anslut till server** anger du det fullständigt kvalificerade **värdnamnet** för den hanterade instansen i rutan **Servernamn.** Välj **SQL Server-autentisering**, ange ditt användarnamn och lösenord och välj sedan **Anslut**.

    ![ssms anslut](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

När du ansluter kan du visa system- och användardatabaserna i noden för databaser och olika objekt i noderna för säkerhet, server-objekt, replikering, hantering, SQL Server Agent och XEvent Profiler.

## <a name="next-steps"></a>Nästa steg

- En snabbstart som visar hur du ansluter från en lokal klientdator med en point-to-site-anslutning finns i [Konfigurera en punkt-till-plats-anslutning](sql-database-managed-instance-configure-p2s.md).
- En översikt över anslutningsalternativen för program finns i [Ansluta dina program till hanterad instans](sql-database-managed-instance-connect-app.md).
- Om du vill återställa en befintlig SQL Server-databas från lokalt till en hanterad instans kan du använda [DMS (Azure Database Migration Service) för migrering](../dms/tutorial-sql-server-to-managed-instance.md) eller [kommandot T-SQL RESTORE](sql-database-managed-instance-get-started-restore.md) för att återställa från en säkerhetskopia av databasen.
