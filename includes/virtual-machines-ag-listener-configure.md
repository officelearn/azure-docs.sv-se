Tillgänglighetsgruppens lyssnare är en IP-adress och ett namn som SQL Server-tillgänglighetsgrupp som lyssnar på. Om du vill skapa tillgänglighetsgruppens lyssnare, gör du följande:

1. <a name="getnet"></a>Hämta namnet på nätverket klusterresursen.

    a. Du kan använda RDP för att ansluta till Azure-dator som är värd för den primära repliken. 

    b. Öppna Hanteraren för redundanskluster.

    c. Välj den **nätverk** noden och anteckna klustrets nätverksnamn. Använd det här namnet i den `$ClusterNetworkName` variabel i PowerShell-skriptet. I följande bild är Klusternätverksnamnet **Cluster nätverk 1**:

   ![Klusternätverksnamn](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Lägg till klientåtkomstpunkten.  
    Klientåtkomstpunkten är det nätverksnamn som program som använder för att ansluta till databaser i en tillgänglighetsgrupp. Skapa klientåtkomstpunkten i hanteraren för redundanskluster.

    a. Expandera klusternamnet och klicka sedan på **roller**.

    b. I den **roller** fönstret högerklickar du på tillgänglighetsgruppens namn och välj sedan **Lägg till resurs** > **klientåtkomstpunkt**.

   ![Klientåtkomstpunkt](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. I den **namn** skapar du ett namn för den här nya lyssnaren. 
   Namnet på den nya lyssnaren är det nätverksnamn som program som använder för att ansluta till databaser i SQL Server-tillgänglighetsgrupp.

    d. För att slutföra skapandet lyssnaren, klickar du på **nästa** två gånger, och klicka sedan på **Slutför**. Tar inte med lyssnaren eller resursen i det här läget.

3. <a name="congroup"></a>Konfigurera IP-resurs för tillgänglighetsgruppen.

    a. Klicka på den **resurser** fliken och expandera sedan den klientåtkomstpunkt som du skapade.  
    Klientåtkomstpunkten är offline.

   ![Klientåtkomstpunkt](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Högerklicka på IP-resursen och klicka sedan på Egenskaper. Anteckna namnet på IP-adressen och använda det i den `$IPResourceName` variabel i PowerShell-skriptet.

    c. Under **IP-adress**, klickar du på **statisk IP-adress**. Ange IP-adressen som samma adress som du använde när du ställer in adressen för belastningsutjämnaren på Azure portal.

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Kontrollera SQL Server-tillgänglighetsgruppresursen beroende på klientåtkomstpunkten.

    a. I Klusterhanteraren klickar du på **roller**, och klicka sedan på tillgänglighetsgruppen.

    b. På den **resurser** fliken, under **andra resurser**, högerklicka på resursgruppen för tillgänglighet och klicka sedan på **egenskaper**. 

    c. Lägg till namnet på klienten åtkomst punkt (lyssnare) resursen på fliken beroenden.

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klicka på **OK**.

5. <a name="listname"></a>Kontrollera klienten åtkomst punkt resursen beroende på IP-adress.

    a. I Klusterhanteraren klickar du på **roller**, och klicka sedan på tillgänglighetsgruppen. 

    b. På den **resurser** , högerklickar på klienten åtkomst punkt resurs under **servernamn**, och klicka sedan på **egenskaper**. 

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klicka på den **beroenden** fliken. Kontrollera att IP-adressen är ett beroende. Om den inte ange ett beroende på IP-adress. Om det finns flera resurser som räknas upp, kontrollera att IP-adresser har eller inte och beroenden. Klicka på **OK**. 

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Högerklicka på namnet på lyssnare och klicka sedan på **Anslut**. 

    >[!TIP]
    >Du kan validera att beroenden är korrekt konfigurerade. I Klusterhanteraren går du till roller, högerklicka på tillgänglighetsgruppen, klickar du på **fler åtgärder**, och klicka sedan på **visa beroenderapport**. När beroenden är korrekt konfigurerade tillgänglighetsgruppen är beroende av nätverksnamn och nätverksnamn är beroende av IP-adress. 


6. <a name="setparam"></a>Ange Klusterparametrar i PowerShell.

  a. Kopiera följande PowerShell-skript till en av dina SQL Server-instanser. Uppdatera variablerna för din miljö.

  - `$ILBIP` är den IP-adress som du skapade på Azure-belastningsutjämnaren för tillgänglighetsgruppens lyssnare.
    
  - `$ProbePort` är den port som du har konfigurerat på Azure-belastningsutjämnaren för tillgänglighetsgruppens lyssnare.

  ```PowerShell
  $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
  $IPResourceName = "<IPResourceName>" # the IP Address resource name
  $ILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  [int]$ProbePort = <nnnnn>
  
  Import-Module FailoverClusters

  Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
  ```

  b. Ange Klusterparametrar genom att köra PowerShell-skriptet på en av noderna i klustret.  

Upprepa stegen ovan för att ange klusterparametrar för WSFC-klustrets IP-adress.

1. Hämta IP-adressnamn i WSFC-kluster-IP-adress. I **Klusterhanteraren** under **Klusterkärnresurser**, leta upp **servernamn**.

1. Högerklicka på **IP-adress**, och välj **egenskaper**.

1. Kopiera den **namn** för IP-adressen. Det kan vara `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Ange Klusterparametrar i PowerShell.
  
  a. Kopiera följande PowerShell-skript till en av dina SQL Server-instanser. Uppdatera variablerna för din miljö.

  - `$ILBIP` är IP-adressen som du skapade på Azure-belastningsutjämnaren för klusterresursen för WSFC-kärnor. Den skiljer sig från IP-adressen för tillgänglighetsgruppens lyssnare.

  - `$ProbePort` är den port som du har konfigurerat på Azure-belastningsutjämnaren för WSFC-hälsoavsökningen. Den skiljer sig från avsökningen för tillgänglighetsgruppens lyssnare.

  ```PowerShell
  $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
  $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
  $ILBIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
  [int]$ProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability grouop listener probe port.
  
  Import-Module FailoverClusters
  
  Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
  ```

  b. Ange Klusterparametrar genom att köra PowerShell-skriptet på en av noderna i klustret.  

  > [!NOTE]
  > Om din SQL Server-instanser är i olika områden kan behöva du köra PowerShell-skriptet två gånger. Första gången använder den `$ILBIP` och `$ProbePort` från den första regionen. Den andra gången använder den `$ILBIP` och `$ProbePort` från den andra regionen. Klustrets nätverksnamn och IP-resurs klusternamnet är samma.
