---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 73ba78eca710f0b98b2a209494519cb8003e554b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75469438"
---
Tillgänglighetsgruppavlyssnaren är en IP-adress och ett nätverksnamn som sql server-tillgänglighetsgruppen lyssnar på. Så här skapar du lyssnaren för tillgänglighetsgruppen:

1. <a name="getnet"></a>Hämta namnet på klusternätverksresursen.

    a. Använd RDP för att ansluta till den virtuella Azure-datorn som är värd för den primära repliken. 

    b. Öppna Redundansklusterhanteraren.

    c. Markera noden **Nätverk** och notera klusternätverksnamnet. Använd det här `$ClusterNetworkName` namnet i variabeln i PowerShell-skriptet. I följande avbildning är klusternätverksnamnet **Klusternätverk 1:**

   ![Namn på klusternätverk](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Lägg till klientåtkomstpunkten.  
    Klientåtkomstpunkten är det nätverksnamn som program använder för att ansluta till databaserna i en tillgänglighetsgrupp. Skapa klientåtkomstpunkten i Redundansklusterhanteraren.

    a. Expandera klusternamnet och klicka sedan på **Roller**.

    b. Högerklicka på namnet på tillgänglighetsgruppen i fönstret **Roller** och välj sedan Lägg till åtkomstpunkt för**resursklient** **Add Resource** > .

   ![Åtkomstpunkt för klient](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Skapa ett namn för den nya lyssnaren i rutan **Namn.** 
   Namnet på den nya lyssnaren är det nätverksnamn som program använder för att ansluta till databaser i tillgänglighetsgruppen SQL Server.

    d. Om du vill slutföra skapandet av lyssnaren klickar du på **Nästa** två gånger och sedan på **Slutför**. Ta inte lyssnaren eller resursen online just nu.

1. Koppla bort rollen för tillgänglighetsgruppkluster. Högerklicka på rollen i **Klusterhanteraren för redundans** under **Roller**och välj **Stoppa roll**.

1. <a name="congroup"></a>Konfigurera IP-resursen för tillgänglighetsgruppen.

    a. Klicka på fliken **Resurser** och expandera sedan klientåtkomstpunkten som du skapade.  
    Klientåtkomstpunkten är offline.

   ![Åtkomstpunkt för klient](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Högerklicka på IP-resursen och klicka sedan på egenskaper. Observera namnet på IP-adressen och använd `$IPResourceName` den i variabeln i PowerShell-skriptet.

    c. Klicka på **Statisk IP-adress**under **IP-adress**. Ange IP-adressen som samma adress som du använde när du anger belastningsutjämnarens adress på Azure-portalen.

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Gör SQL Server-tillgänglighetsgruppresursen beroende av klientåtkomstpunkten.

    a. Klicka på **Roller**i Klusterhanteraren för växling och klicka sedan på din tillgänglighetsgrupp.

    b. Högerklicka på resursgruppen för tillgänglighet under **Andra resurser**på fliken **Resurser** och klicka sedan på **Egenskaper**. 

    c. Lägg till namnet på klientåtkomstpunkten (lyssnarresursen) på fliken beroenden.

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klicka på **OK**.

1. <a name="listname"></a>Gör klientåtkomstpunktens resurs beroende av IP-adressen.

    a. Klicka på **Roller**i Klusterhanteraren för växling och klicka sedan på din tillgänglighetsgrupp. 

    b. Högerklicka på klientåtkomstpunktsresursen under **Servernamn**på fliken **Resurser** och klicka sedan på **Egenskaper**. 

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klicka på fliken **Beroenden.** Kontrollera att IP-adressen är ett beroende. Om så inte är fallet anger du ett beroende av IP-adressen. Om det finns flera resurser i listan kontrollerar du att IP-adresserna har ELLER, inte OCH, beroenden. Klicka på **OK**. 

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >Du kan verifiera att beroendena är korrekt konfigurerade. I Redundansklusterhanteraren går du till Roller, högerklickar på tillgänglighetsgruppen, klickar på **Fler åtgärder**och klickar sedan på **Visa beroenderapport**. När beroenden är korrekt konfigurerade är tillgänglighetsgruppen beroende av nätverksnamnet och nätverksnamnet är beroende av IP-adressen. 


1. <a name="setparam"></a>Ange klusterparametrar i PowerShell.

   a. Kopiera följande PowerShell-skript till en av dina SQL Server-instanser. Uppdatera variablerna för din miljö.

   - `$ListenerILBIP`är IP-adressen som du skapade på Azure-belastningsutjämnaren för tillgänglighetsgruppens lyssnare.
    
   - `$ListenerProbePort`är den port som du har konfigurerat på Azure-belastningsutjämnaren för tillgänglighetsgrupplyssnaren.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ange klusterparametrarna genom att köra PowerShell-skriptet på en av klusternoderna.  

   > [!NOTE]
   > Om SQL Server-instanserna finns i separata regioner måste du köra PowerShell-skriptet två gånger. Första gången använder `$ListenerILBIP` `$ListenerProbePort` du och från den första regionen. Den andra gången `$ListenerILBIP` `$ListenerProbePort` använder du och från den andra regionen. Klusternätverksnamnet och kluster-IP-resursnamnet är också olika för varje region.

1. Ta med rollen för tillgänglighetsgruppklusteret online. Högerklicka på rollen i **Klusterhanteraren för växling** under **Roller**och välj **Starta roll**.

Om det behövs upprepar du stegen ovan för att ställa in klusterparametrarna för WSFC-kluster-IP-adressen.

1. Hämta IP-adressnamnet för WSFC Cluster IP-adressen. Leta reda på **servernamn**i **Redundansklusterhanteraren** under **Klusterkärnresurser**.

1. Högerklicka på **IP-adress**och välj **Egenskaper**.

1. Kopiera **namnet** på IP-adressen. Det kan `Cluster IP Address`vara . 

1. <a name="setwsfcparam"></a>Ange klusterparametrar i PowerShell.
  
   a. Kopiera följande PowerShell-skript till en av dina SQL Server-instanser. Uppdatera variablerna för din miljö.

   - `$ClusterCoreIP`är IP-adressen som du skapade på Azure-belastningsutjämnaren för WSFC-kärnklusterresursen. Det skiljer sig från IP-adressen för tillgänglighetsgruppen lyssnare.

   - `$ClusterProbePort`är porten som du har konfigurerat på Azure-belastningsutjämnaren för WSFC-hälsoavsökningen. Det skiljer sig från avsökningen för tillgänglighetsgruppen lyssnare.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Ange klusterparametrarna genom att köra PowerShell-skriptet på en av klusternoderna.  

>[!WARNING]
>Hälsoavsökningsporten för tillgänglighetsgruppen lyssnare måste skilja sig från hälsoavsökningsporten för klusterkärnor. I dessa exempel är lyssnarporten 59999 och hälsoavsökningsporten för klusterkärnor ip-adress är 58888. Båda portarna kräver en tillåt inbound-brandväggsregel.
