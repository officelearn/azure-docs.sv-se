Tillgänglighetsgruppens lyssnare är en IP-adress och att SQL Server-tillgänglighetsgrupp som lyssnar på. Om du vill skapa tillgänglighetsgruppens lyssnare, gör du följande:

1. <a name="getnet"></a>Hämta namnet på klustret nätverksresurs.

    a. Använda RDP för att ansluta till den Azure-dator som är värd för den primära repliken. 

    b. Öppna Hanteraren för redundanskluster.

    c. Välj den **nätverk** nod och notera klustrets nätverksnamn. Använd det här namnet i den `$ClusterNetworkName` variabeln i PowerShell-skript. I följande bild är Klusternätverksnamnet **klustret nätverk 1**:

   ![Klustrets nätverksnamn](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Lägg till klientåtkomstpunkten.  
    Klientåtkomstpunkt är nätverksnamnet program använder för att ansluta till databaser i en tillgänglighetsgrupp. Skapa klientåtkomstpunkten i hanteraren för redundanskluster.

    a. Expandera klusternamnet och klicka sedan på **roller**.

    b. I den **roller** fönstret, högerklicka på tillgänglighetsgruppens namn och välj sedan **Lägg till resurs** > **klientåtkomstpunkt**.

   ![Klientåtkomstpunkt](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. I den **namn** skapar du ett namn för den här lyssnaren. 
   Namnet för lyssnaren är nätverksnamnet program använder för att ansluta till databaser i SQL Server-tillgänglighetsgrupp.
   
    d. Klicka på Slutför lyssnaren **nästa** två gånger, och klicka sedan på **Slutför**. Tar inte med lyssnare eller resursen i det här läget.

3. <a name="congroup"></a>Konfigurera IP-resurs för tillgänglighetsgruppen.

    a. Klicka på den **resurser** fliken och expandera sedan den klientåtkomstpunkt som du skapade.  
    Klientåtkomstpunkt är offline.

   ![Klientåtkomstpunkt](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Högerklicka på IP-resurs och klicka sedan på Egenskaper. Notera namnet på IP-adressen och använda den i den `$IPResourceName` variabeln i PowerShell-skript.

    c. Under **IP-adress**, klickar du på **statisk IP-adress**. Ange IP-adressen som samma adress som du använde när du ställer in adressen för belastningsutjämnaren på Azure-portalen.

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Se SQL Server tillgänglighetsgruppsresursen beroende på klientåtkomstpunkt.

    a. I hanteraren för redundanskluster, klickar du på **roller**, och klicka sedan på tillgänglighetsgruppen.

    b. På den **resurser** fliken, under **andra resurser**, högerklicka på resursgruppen tillgänglighet och klicka sedan på **egenskaper**. 

    c. Lägg till namnet på resursen klienten åtkomst punkt (lyssnaren) på fliken beroenden.

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klicka på **OK**.

5. <a name="listname"></a>Kontrollera klienten åtkomst punkt resursen beroende av IP-adressen.

    a. I hanteraren för redundanskluster, klickar du på **roller**, och klicka sedan på tillgänglighetsgruppen. 

    b. På den **resurser** genom att högerklicka på resursen klienten åtkomst punkt under **servernamn**, och klicka sedan på **egenskaper**. 

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klicka på den **beroenden** fliken. Kontrollera att IP-adressen är ett beroende. Om det inte ange ett beroende på IP-adressen. Om det finns flera resurser i listan, kontrollera att IP-adresser har eller inte och beroenden. Klicka på **OK**. 

   ![IP-resurs](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Högerklicka på grupplyssnarens namn och klicka sedan på **Anslut**. 

    >[!TIP]
    >Du kan verifiera att beroenden är korrekt konfigurerade. I hanteraren för redundanskluster, gå till roller, högerklicka tillgänglighetsgruppen, klicka på **fler åtgärder**, och klicka sedan på **visa beroenderapport**. När beroenden är korrekt konfigurerade tillgänglighetsgruppen är beroende av nätverksnamnet och nätverksnamnet är beroende av IP-adress. 


6. <a name="setparam"></a>Ange Klusterparametrar i PowerShell.
    
    a. Kopiera följande PowerShell-skript till en SQL Server-instanser. Uppdatera variablerna för din miljö.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Ange Klusterparametrar genom att köra PowerShell-skriptet på en av klusternoderna.  

Upprepa stegen ovan för att ange klusterparametrar för WSFC-klustrets IP-adress.

1. Hämta IP-adress namnet på WSFC-klustrets IP-adress. I **Klusterhanteraren** under **klustrets kärnresurser**, leta upp **servernamn**.

1. Högerklicka på **IP-adress**, och välj **egenskaper**.

1. Kopiera den **namn** av IP-adressen. Det kan vara `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Ange Klusterparametrar i PowerShell.
    
    a. Kopiera följande PowerShell-skript till en SQL Server-instanser. Uppdatera variablerna för din miljö.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
    $ILBIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Ange Klusterparametrar genom att köra PowerShell-skriptet på en av klusternoderna.  

    > [!NOTE]
    > Om SQL Server-instanser finns i olika områden, måste du köra PowerShell-skriptet två gånger. Första gången ska du använda den `$ILBIP` och `$ProbePort` från det första området. Den andra gången använder den `$ILBIP` och `$ProbePort` från andra regioner. Klustrets nätverksnamn och IP-resurs klusternamnet är samma. 
