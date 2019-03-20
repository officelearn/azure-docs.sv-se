---
title: Skapa en virtuell SQL Server 2017-dator för Linux i Azure | Microsoft Docs
description: Den här kursen visar hur du skapar virtuell Linux SQL Server 2017-dator i Azure Portal.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 12/5/2018
ms.topic: conceptual
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 468db9d62a98e079fbe6954843e23a518eaabd0a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011713"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Etablera en virtuell Linux-dator med SQL Server på Azure Portal

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

I den här snabba självstudiekursen använder du Azure Portal till att skapa en virtuell Linux-dator med SQL Server 2017 installerat.

I den här guiden får du lära dig att:

* [Skapa en virtuell Linux SQL-dator från galleriet](#create)
* [Ansluta till den nya virtuella datorn med ssh](#connect)
* [Ändra SA-lösenordet](#password)
* [Konfigurera för fjärranslutningar](#remote)

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a id="create"></a> Skapa en virtuell Linux-dator med SQL Server installerat

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Välj **Skapa en resurs** i fönstret till vänster.

1. Klicka på **Compute** i fönstret **Skapa en resurs**.

1. Välj **Visa alla** bredvid rubriken **Aktuella**.

   ![Se alla VM-avbildningar](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. Skriv **SQL Server 2017** i sökrutan och starta sökningen genom att trycka på **Retur**.

1. Begränsa sökresultaten genom att välja **Operativsystem** > **Redhat**. Välj sedan **Microsoft** under **Publisher**.

    ![Sökfilter för SQL Server 2017 VM-avbildningar](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Välj en SQL Server 2017 Linux-avbildning från sökresultaten. I den här självstudien används **Kostnadsfri SQL Server-licens: SQL Server 2017 Developer på Red Hat Enterprise Linux 7.4**.

   > [!TIP]
   > Med Developer-utgåvan kan du testa eller utveckla med funktionerna i Enterprise-utgåvan, men utan SQL Server-licenskostnader. Du betalar bara för kostnaden för att köra den virtuella Linux-datorn.

1. Välj en distributionsmodell som passar dina arbetsbelastningsbehov under **Välj en distributionsmodell**.

    > [!Note]
    > Använd **Resource Manager** för nya arbetsbelastningar. Om du vill ansluta till ett befintligt virtuellt nätverk väljer du det virtuella nätverkets distributionsmetod för din arbetsbelastning. Mer information om distributionsmodellerna finns i [Azure Resource Manager och klassiska distributionsmodeller](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

1. Välj **Skapa**.

### <a name="set-up-your-linux-vm"></a>Konfigurera din virtuella Linux-dator

1. Välj **Prenumeration** och **Resursgrupp** på fliken **Grunder**. 

    ![Fönstret Grunder](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. Ange ett namn för din nya virtuella Linux-dator i **Virtuellt datornamn**.
1. Ange eller välj sedan följande värden:
   * **Region**: Välj den Azure-region som är rätt för dig.
   * **Tillgänglighetsalternativ**: Välj det alternativ för tillgänglighet och redundans som passar bäst för dina appar och data.
   * **Ändra storlek**: Välj det här alternativet om du vill välja en datorstorlek, och välj sedan **Välj** när du är klar. Mer information om storlekar på virtuella datorer finns i [Linux-VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes).

     ![Välja storlek för virtuella datorer](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > För utveckling och funktionstestning bör du använda en storlek för virtuella datorer på **DS2** eller högre. För prestandatestning använder du **DS13** eller högre.

   * **Autentiseringstyp**: Välj **Offentlig SSH-nyckel**.

     > [!Note]
     > Du kan välja mellan offentlig SSH-nyckel eller lösenord för autentisering. SSH är säkrare. Instruktioner om hur du genererar en SSH-nyckel finns i [Skapa SSH-nycklar på Linux och Mac för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

   * **Användarnamn**: Ange den virtuella datorns administratörsnamn.
   * **Offentlig SSH-nyckel**: Ange din offentliga RSA-nyckel.
   * **Offentliga inkommande portar**: Välj **Tillåt valda portar** och välj porten **SSH (22)** i listan **Välj offentliga ingående portar**. I den här snabbstarten är det här steget nödvändigt för att ansluta och slutföra SQL Server-konfigurationen. Om du vill fjärransluta till SQL Server kan du också välja **MS SQL (1433)** för att öppna port 1433 för anslutningar över Internet.

   ![Ingående portar](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Gör de ändringar du vill i inställningarna i följande ytterligare flikar eller behåll standardinställningarna.
    * **Diskar**
    * **Nätverk**
    * **Hantering**
    * **Gästkonfiguration**
    * **Taggar**

1. Välj **Granska + skapa**.
1. Välj **Skapa** i fönstret **Granska + skapa**.

## <a id="connect"></a> Ansluta till den virtuella Linux-datorn

Om du redan använder ett BASH-gränssnitt ansluter du till den virtuella Azure-datorn med **ssh**-kommandot. I följande kommando ersätter du VM-användarnamnet och IP-adressen för att ansluta till din virtuella Linux-dator.

```bash
ssh azureadmin@40.55.55.555
```

Du hittar IP-adressen till den virtuella datorn i Azure Portal.

![IP-adress i Azure Portal](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Om du kör på Windows och inte har något BASH-gränssnitt kan du installera en SSH-klient som t.ex. PuTTY.

1. [Ladda ned och installera PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Kör PuTTY.

1. På PuTTY-konfigurationsskärmen anger du den virtuella datorns offentliga IP-adress.

1. Välj **Öppna** och ange ditt användarnamn och lösenord i prompterna.

Mer information om hur du ansluter till virtuella Linux-datorer finns i [Skapa en virtuell Linux-dator i Azure med hjälp av portalen](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal).

> [!Note]
> Om du ser en PuTTY-säkerhetsvarning om att serverns värdnyckel inte cachelagras i registret kan du välja bland följande alternativ. Om du litar på den här värden väljer du **Ja** för att lägga till nyckeln i PuTTy-cacheminnet och fortsätta anslutningen. Om du vill fortsätta med att bara ansluta en gång utan att lägga till nyckeln i cacheminnet väljer du **Nej**. Om du inte litar på den här värden lämnar du anslutningen genom att välja **Avbryt**.

## <a id="password"></a> Ändra SA-lösenordet

Den nya virtuella datorn installerar SQL Server med ett slumpmässigt SA-lösenord. Återställ det här lösenordet innan du ansluter till SQL Server med SA-inloggningen.

1. När du har anslutit till din virtuella Linux-dator öppnar du en ny kommandoterminal.

1. Ändra SA-lösenordet med följande kommandon:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Ange ett nytt SA-lösenord och bekräfta lösenordet när du uppmanas till det.

1. Starta om SQL Server-tjänsten.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Lägga till verktygen på sökvägen (valfritt)

Flera SQL Server-[paket](sql-server-linux-virtual-machines-overview.md#packages) är installerade som standard, inklusive SQL Server-kommandoradspaketet Tools. Paketet innehåller verktygen **sqlcmd** och **bcp**. För enkelhetens skull kan du lägga till verktygssökvägen, `/opt/mssql-tools/bin/`, i miljövariabeln **PATH**.

1. Kör följande kommandon för att ändra **PATH** för både inloggningssessioner och interaktiva/icke-inloggningssessioner:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Konfigurera för fjärranslutningar

Om du behöver fjärransluta till SQL Server på den virtuella Azure-datorn måste du konfigurera en regel för inkommande trafik för nätverkssäkerhetsgruppen. Med regeln tillåts trafik på porten som SQL Server lyssnar på (standard är 1433). Följande anvisningar visar hur du använder Azure Portal för det här steget.

> [!TIP]
> Om du har valt den inkommande porten **MS SQL (1433)** i inställningarna under etableringen har dessa ändringar har gjorts för dig. Du kan gå till nästa avsnitt om hur du konfigurerar brandväggen.

1. I Portal väljer du **Virtuella datorer** och väljer sedan din virtuella SQL Server-dator.
1. Välj **Nätverk** i det vänstra navigeringsfönstret under **Inställningar**.
1. Välj **Lägg till inkommande port** under **Inkommande portregler** i fönstret Nätverk.

   ![Regler för inkommande portar](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. I listan **Tjänst** väljer du **MS SQL**.

    ![Regel för MS SQL-säkerhetsgrupp](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Klicka på **OK** för att spara regeln för din virtuella dator.

### <a name="open-the-firewall-on-rhel"></a>Öppna brandväggen på RHEL

Den här kursen instruerade dig att skapa en virtuell dator i Red Hat Enterprise Linux (RHEL). Om du vill fjärransluta till virtuella RHEL-datorer måste du även öppna port 1433 på Linux-brandväggen.

1. [Anslut](#connect) till din virtuella RHEL-dator.

1. Kör följande kommandon i BASH-gränssnittet:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Nästa steg

Nu när du har en virtuell SQL Server 2017-dator i Azure kan du ansluta lokalt med **sqlcmd** för att köra Transact-SQL-frågor.

Om du har konfigurerat den virtuella Azure-datorn för SQL Server-fjärranslutningar bör du kunna fjärransluta. Du kan se ett exempel på hur du fjärransluter till SQL Server på Linux från Windows i [Använda SSMS på Windows till att ansluta till SQL Server på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms). Om du vill ansluta med Visual Studio Code läser du [Använda Visual Studio Code till att skapa och köra Transact-SQL-skript för SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode)

Mer allmän information om SQL Server på Linux finns i [Översikt över SQL Server 2017 på Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). Mer information om hur du använder virtuella SQL Server 2017-datorer för Linux finns i [Översikt över virtuella SQL Server 2017-datorer på Azure](sql-server-linux-virtual-machines-overview.md).
