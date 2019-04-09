---
title: Skapa en SQL Server Windows-VM i portalen | Microsoft Docs
description: Den här kursen visar hur du skapar virtuell Windows SQL Server 2017-dator i Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bd535aeb034a17f1844c1d19379c1811b43d27e5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260246"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Snabbstart: Skapa en virtuell Windows-dator med SQL Server 2017 i Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Den här snabbstarten beskriver hur du skapar en virtuell SQL Server-dator i Azure Portal.

> [!TIP]
> I snabbstarten finns en sökväg för snabb etablering och anslutning till en SQL-VM. Mer information om andra etableringsalternativ för SQL-VM finns i [Etableringsguide för Windows SQL Server-datorer i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md).

> [!TIP]
> Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Skaffa en Azure-prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a id="select"></a> Välj en avbildning av en virtuell SQL Server-dator

1. Logga in på den [Azure-portalen](https://portal.azure.com) med ditt konto.

1. I Azure-portalen väljer du **Skapa en resurs**. 

1. I sökfältet skriver `SQL Server 2017 Developer on Windows Server 2016`, och tryck på RETUR.

1. Välj **Kostnadsfri SQL Server-licens: SQL Server 2017 Developer i Windows Server 2016**.

   ![Nytt sökfönster](./media/quickstart-sql-vm-create-portal/newsearch.png)

   > [!TIP]
   > Vi använder Developer-versionen i den här självstudiekursen eftersom det är en komplett version av SQL Server som är kostnadsfri i samband med utvecklingstester. Du betalar endast för kostnaden för den VM som körs. Fullständig prisinformation finns i [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. Välj **Skapa**.

## <a id="configure"></a> Ange grundläggande information

På den **grunderna** och ange följande information:

1. I den **projektinformation** markerar du din Azure-prenumeration och välj sedan **Skapa nytt** att skapa en ny resursgrupp. Typ _SQLVM-RG_ för namnet.

   ![Prenumeration](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. Under **instans information**:
    1. Typ _SQLVM_ för den **virtuellt datornamn**. 
    1. Välj en plats för din **Region**. 
    1. I den här snabbstarten lämna **tillgänglighetsalternativ** inställd _ingen redundans för infrastruktur som krävs för_. Du hittar mer information om alternativ för tillgänglighet [Azure-regioner och tillgänglighet](../../windows/regions-and-availability.md). 
    1. I den **bild** väljer _kostnadsfri SQL Server-licens: SQL Server 2017 Developer på Windows Server 2016_. 
    1. Välja att **ändra storleken på** för den **storlek** av den virtuella datorn och välj den **A2 grundläggande** erbjuder. Glöm inte att rensa dina resurser när du är klar med att förebygga eventuella oväntade avgifter. 

   ![Instansinformation](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. Under **administratörskontot**, ange ett användarnamn som _azureuser_ och ett lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administratörskonto](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. Under **regler för inkommande portar**, Välj **valda portar** och välj sedan **RDP (port 3389)** från listrutan. 

   ![Regler för inkommande portar](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server-inställningar

På den **SQL Server-inställningar** konfigurerar följande alternativ:

1. Under **säkerhet och nätverk**väljer _offentlig (Internet_) för **SQL-anslutning** och ändra porten som ska `1401` att undvika att använda ett välkänt portnummer i det offentliga scenariot. 
1. Under **SQL-autentisering**väljer **aktivera**. SQL-inloggningen konfigureras till samma användarnamn och lösenord som du konfigurerade för den virtuella datorn. Använd standardinställningarna för **Azure Key Vault-integrering** och **lagringskonfiguration**.  

   ![Säkerhetsinställningar för SQL server](media/quickstart-sql-vm-create-portal/sql-server-settings.png)

1. Ändra andra inställningar om det behövs och välj sedan **granska + skapa**. 

   ![Granska + skapa](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>Skapa den virtuella SQL Server-datorn

På den **granska + skapa** , granskar du sammanfattningen och sedan **skapa** att skapa SQL Server, resursgrupp och resurser som angetts för den här virtuella datorn.

Du kan övervaka distributionen från Azure Portal. Knappen **Meddelanden** längst upp på skärmen visar grundläggande status för distributionen.

> [!TIP]
> Det kan ta flera minuter att distribuera en virtuell Windows SQL Server-dator.

## <a name="connect-to-sql-server"></a>Ansluta till SQL Server

1. I portalen, hitta den **offentliga IP-adressen** av SQL Server-VM i den **översikt** delen av den virtuella datorns egenskaper.

1. Öppna SQL Server Management Studio (SSMS) från en annan dator som är ansluten till Internet.

   > [!TIP]
   > Om du inte har SQL Server Management Studio kan du hämta det från [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. I dialogrutan **Anslut till server** eller **Anslut till databasmotor**, redigerar du värdet för **Servernamn**. Ange den virtuella datorns offentliga IP-adress. Sedan lägger du till ett kommatecken och den anpassade porten **1401** som angavs när du konfigurerade den nya virtuella datorn. Till exempel `11.22.33.444,1401`.

1. I rutan **Autentisering**, markerar du **SQL Server-autentisering**.

1. I rutan **Inloggning**, skriver du namnet på en giltig SQL-inloggning.

1. I rutan **Lösenord**, skriver du lösenordet för inloggningen.

1. Välj **Anslut**.

    ![ssms anslut](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> Logga in på den virtuella datorn via en fjärranslutning

Använd följande anvisningar för att ansluta till den virtuella SQL Server-datorn med Fjärrskrivbord:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

När du ansluter till den virtuella SQL Server-datorn kan du starta SQL Server Management Studio och ansluta med Windows-autentisering med hjälp av dina autentiseringsuppgifter som lokal administratör. Om du har aktiverat SQL Server-autentisering kan du också ansluta med SQL-autentisering med hjälp av SQL-inloggningsnamnet och SQL-lösenordet som du konfigurerade under etableringen.

När du har anslutit till datorn kan du direkt ändra inställningarna för datorn och SQL Server efter behov. Du kan till exempel konfigurera brandväggsinställningarna eller ändra konfigurationsinställningarna för SQL Server.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver köra den virtuella SQL-datorn kontinuerligt kan du undvika onödiga kostnader genom att stoppa den när den inte används. Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn genom att ta bort deras kopplade resursgrupper i portalen. Det här tar även permanent bort den virtuella datorn, så använd det här kommandot med försiktighet. Mer information finns i [Manage Azure resources through portal](../../../azure-resource-manager/manage-resource-groups-portal.md) (Hantera Azure-resurser genom portalen).


## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du en virtuell dator i SQL Server 2017 i Azure-portalen. Mer information om hur du migrerar data till den nya SQL-servern finns i följande artikel.

> [!div class="nextstepaction"]
> [Migrera en databas till en SQL-VM](virtual-machines-windows-migrate-sql.md)
