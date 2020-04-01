---
title: Konfigurera tillgänglighetsgruppavlyssnare för SQL Server på virtuella RHEL-datorer i Azure - Linux Virtual Machines | Microsoft-dokument
description: Lär dig mer om hur du konfigurerar en lyssnare i tillgänglighetsgruppen i SQL Server på virtuella RHEL-datorer i Azure
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 80557eb3776ba17a4922d1fc384b87419ffbd67e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79096586"
---
# <a name="tutorial-configure-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Självstudiekurs: Konfigurera tillgänglighetsgruppavlyssnare för SQL Server på virtuella RHEL-datorer i Azure

> [!NOTE]
> Handledningen presenteras är i **offentlig förhandsvisning**. 
>
> Vi använder SQL Server 2017 med RHEL 7.6 i den här självstudien, men det är möjligt att använda SQL Server 2019 i RHEL 7 eller RHEL 8 för att konfigurera HA. Kommandona för att konfigurera resurser för tillgänglighetsgrupper har ändrats i RHEL 8 och du bör titta på artikeln, [Skapa tillgänglighetsgruppresurs](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) och RHEL 8-resurser för mer information om rätt kommandon.

Den här självstudien går igenom steg om hur du skapar en tillgänglighetsgruppavlyssnare för dina SQL-servrar på virtuella RHEL-datorer i Azure. Du lär dig att göra följande:

> [!div class="checklist"]
> - Skapa en belastningsutjämnare i Azure-portalen
> - Konfigurera backend-poolen för belastningsutjämnaren
> - Skapa en avsökning för belastningsutjämnaren
> - Ange regler för belastningsutjämning
> - Skapa belastningsutjämnarens resurs i klustret
> - Skapa lyssnaren för tillgänglighetsgruppen
> - Testa att ansluta till lyssnaren
> - Testa en redundans

## <a name="prerequisite"></a>Krav

Slutförd [ **självstudiekurs: Konfigurera tillgänglighetsgrupper för SQL Server på virtuella RHEL-datorer i Azure**](sql-server-linux-rhel-ha-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure-portalen

Följande instruktioner tar dig igenom steg 1 till 4 från [skapa och konfigurera belastningsutjämnaren i Azure-portalavsnittet i](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md#create-and-configure-the-load-balancer-in-the-azure-portal) artikeln [Belastningsutjämnad - Azure-portal.](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)

### <a name="create-the-load-balancer"></a>Skapa lastbalanseraren

1. Öppna resursgruppen som innehåller virtuella SQL Server-datorer i Azure-portalen. 

2. Klicka på Lägg **till**i resursgruppen .

3. Sök efter **belastningsutjämnare** och välj sedan **Belastningsutjämnare**i sökresultaten , som publiceras av **Microsoft**.

4. Klicka på **Skapa**på bladet **Belastningsutjämnare** .

5. Konfigurera belastningsutjämnaren i dialogrutan **Skapa belastningsutjämnare** på följande sätt:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett textnamn som representerar belastningsutjämnaren. Till exempel **sqlLB**. |
   | **Typ** |**Intern** |
   | **Virtuellt nätverk** |Standard-VNet som skapades bör heta **VM1VNET**. |
   | **Undernät** |Markera det undernät som SQL Server-instanserna finns i. Standardvärdet ska vara **VM1Subnet**.|
   | **TILLDELNING AV IP-adress** |**Statisk** |
   | **Privat IP-adress** |Använd `virtualip` IP-adressen som skapades i klustret. |
   | **Prenumeration** |Använd prenumerationen som användes för resursgruppen. |
   | **Resursgrupp** |Markera den resursgrupp som SQL Server-instanserna finns i. |
   | **Location** |Välj den Azure-plats som SQL Server-instanserna finns i. |

### <a name="configure-the-back-end-pool"></a>Konfigurera backend-poolen
Azure anropar *backend-adresspoolens backend-pool*. I det här fallet är backend-poolen adresserna för de tre SQL Server-instanserna i din tillgänglighetsgrupp. 

1. Klicka på den belastningsutjämnare som du skapade i resursgruppen. 

2. Klicka på **Backend-pooler i** **Inställningar**.

3. I **Backend-pooler**klickar du på **Lägg till** för att skapa en backend-adresspool. 

4. Skriv ett namn för backend-poolen under **Namn**i Poolen Lägg **till backend.**

5. Under **Associerad till**väljer du **Virtuell dator**. 

6. Välj varje virtuell dator i miljön och associera lämplig IP-adress till varje val.

    :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-backend-pool.png" alt-text="Lägga till backend-pool":::

7. Klicka på **Lägg till**. 

### <a name="create-a-probe"></a>Skapa en avsökning

Avsökningen definierar hur Azure verifierar vilken av SQL Server-instanserna som för närvarande äger tillgänglighetsgruppavlyssnaren. Azure avsöker tjänsten baserat på IP-adressen på en port som du definierar när du skapar avsökningen.

1. Klicka på Hälsoavsökningar på bladet **Inställningar för belastningsutjämnare** . **Health probes** 

2. Klicka på **Lägg till**på bladet **Hälsa avsökningar** .

3. Konfigurera avsökningen på **Bladet Lägg till sond.** Använd följande värden för att konfigurera avsökningen:

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett textnamn som representerar avsökningen. Till exempel **SQLAlwaysOnEndPointProbe**. |
   | **Protokollet** |**TCP** |
   | **Port** |Du kan använda vilken tillgänglig port som helst. Till exempel *59999*. |
   | **Intervall** |*5* |
   | **Felfritt tröskelvärde** |*2* |

4.  Klicka på **OK**. 

5. Logga in på alla virtuella datorer och öppna avsökningsporten med följande kommandon:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure skapar avsökningen och använder den sedan för att testa vilken SQL Server-instans som har lyssnaren för tillgänglighetsgruppen.

### <a name="set-the-load-balancing-rules"></a>Ange regler för belastningsutjämning

Belastningsutjämningsreglerna konfigurerar hur belastningsutjämnaren dirigerar trafik till SQL Server-instanserna. För den här belastningsutjämnaren aktiverar du direkt serverretur eftersom endast en av de tre SQL Server-instanserna äger tillgänglighetsgruppens lyssnarresurs åt gången.

1. Klicka på **Belastningsutjämningsregler**på bladet Inställningar för belastningsutjämnare . **Settings** 

2. Klicka på **Lägg till**på bladet **Belastningsutjämningsregler** .

3. Konfigurera belastningsutjämningsregeln på bladet **Lägg till belastningsutjämningsregler.** Använd följande inställningar: 

   | Inställning | Värde |
   | --- | --- |
   | **Namn** |Ett textnamn som representerar belastningsutjämningsreglerna. **SqlAlwaysOnEndPointListener**. |
   | **Protokollet** |**TCP** |
   | **Port** |*1433* |
   | **Backend-port** |*1433*. Det här värdet ignoreras eftersom den här regeln använder **flytande IP (direkt serverretur)**. |
   | **Avsökning** |Använd namnet på den avsökning som du skapade för den här belastningsutjämnaren. |
   | **Sessionspermanens** |**Inget** |
   | **Tidsgränsen för inaktiv tid (minuter)** |*4* |
   | **Flytande IP (direkt serverretur)** |**Enabled** |

   :::image type="content" source="media/sql-server-linux-rhel-ha-listener-tutorial/add-load-balancing-rule.png" alt-text="Lägga till belastningsutjämningsregel":::

4. Klicka på **OK**. 
5. Azure konfigurerar belastningsutjämningsregeln. Nu är belastningsutjämnaren konfigurerad för att dirigera trafik till SQL Server-instansen som är värd för lyssnaren för tillgänglighetsgruppen. 

Nu har resursgruppen en belastningsutjämnare som ansluter till alla SQL Server-datorer. Belastningsutjämnaren innehåller också en IP-adress för SQL Server Always On availability group listener, så att alla datorer kan svara på begäranden för tillgänglighetsgrupperna.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Skapa belastningsutjämnarens resurs i klustret

1. Logga in på den primära virtuella datorn. Vi måste skapa resursen för att aktivera Azure load balancer-avsökningsporten (59999 används i vårt exempel). Kör följande kommando:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Skapa en grupp som `virtualip` `azure_load_balancer` innehåller resursen och:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Lägga till begränsningar

1. En samlokaliseringsbegränsning måste konfigureras för att säkerställa azure load balancer IP-adress och AG-resursen körs på samma nod. Kör följande kommando:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Skapa ett ordervillkor för att säkerställa att AG-resursen är igång före AZURE-belastningsutjämnarens IP-adress. Även om samlokaliseringsbegränsningen innebär ett beställningsvillkor, verkställer detta det.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Om du vill kontrollera begränsningarna kör du följande kommando:

    ```bash
    sudo pcs constraint list --full
    ```

    Du bör se följande utdata:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Skapa lyssnaren för tillgänglighetsgruppen

1. Kör följande kommando i SQLCMD eller SSMS på den primära noden:

    - Ersätt IP-adressen som `virtualip` används nedan med IP-adressen.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Logga in på varje VM-nod. Använd följande kommando för att öppna hosts-filen och `ag1-listener` ställa in värdnamnsmatchning för på varje dator.

    ```
    sudo vi /etc/hosts
    ```

    I **vi** vi-redigeraren `i` anger du för att infoga text och `ag1-listener`på en tom rad lägger du till IP-adressen för . Lägg `ag1-listener` sedan till efter ett blanksteg bredvid IP-adressen.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Om du vill avsluta vi-redigeraren trycker du `:wq` först på **Esc-tangenten** och anger sedan kommandot för att skriva filen och avsluta. **vi** Gör detta på varje nod.

## <a name="test-the-listener-and-a-failover"></a>Testa lyssnaren och en redundans

### <a name="test-logging-into-sql-server-using-the-availability-group-listener"></a>Testa att logga in på SQL Server med hjälp av tillgänglighetsgrupplyssnaren

1. Använd SQLCMD för att logga in på den primära noden i SQL Server med hjälp av tillgänglighetsgruppens lyssnarenamn:

    - Använd en inloggning som tidigare `<YourPassword>` har skapats och ersätts med rätt lösenord. I exemplet nedan `sa` används inloggningen som skapades med SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Kontrollera namnet på den server som du är ansluten till. Kör följande kommando i SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Utdata ska visa den aktuella primära noden. Detta bör `VM1` vara om du aldrig har testat en redundans.

    Avsluta SQL-sessionen genom `exit` att skriva kommandot.

### <a name="test-a-failover"></a>Testa en redundans

1. Kör följande kommando för att manuellt redundans den primära repliken till `<VM2>` eller en annan replik. Ersätt `<VM2>` med värdet på servernamnet.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Om du kontrollerar dina begränsningar ser du att ett annat villkor har lagts till på grund av den manuella redundansen:

    ```bash
    sudo pcs constraint list --full
    ```

    Du kommer att se att `cli-prefer-ag_cluster-master` ett villkor med ID har lagts till.

1. Ta bort villkoret `cli-prefer-ag_cluster-master` med ID med följande kommando:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Kontrollera klusterresurserna med `sudo pcs resource`kommandot och du bör se `<VM2>`att den primära instansen är nu .

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Använd SQLCMD för att logga in på din primära replik med lyssnarens namn:

    - Använd en inloggning som tidigare `<YourPassword>` har skapats och ersätts med rätt lösenord. I exemplet nedan `sa` används inloggningen som skapades med SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Kontrollera servern som du är ansluten till. Kör följande kommando i SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Du bör se att du nu är ansluten till den virtuella datorn som du misslyckades med.

## <a name="next-steps"></a>Nästa steg

Mer information om belastningsutjämnare i Azure finns i:

> [!div class="nextstepaction"]
> [Konfigurera en belastningsutjämnare för en tillgänglighetsgrupp på virtuella Azure SQL Server-datorer](../../../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)
