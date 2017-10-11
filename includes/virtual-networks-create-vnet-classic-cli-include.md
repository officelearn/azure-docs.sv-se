## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Så här skapar du ett klassiska VNet med Azure CLI
Du kan använda Azure CLI för att hantera dina Azure-resurser från kommandotolken från valfri dator som kör Windows, Linux eller OSX. Följ stegen nedan för att skapa ett VNet med hjälp av Azure CLI.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../articles/cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **azure network vnet create** för att skapa en VNet och ett undernät som det visas nedan. Listan som visas efter alla utdata förklarar parametrarna som använts.
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Förväntad utdata:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. Namnet på den VNet som ska skapas. I vårt exempel, *TestVNet*
   * **-e (eller---adressutrymmet)**. VNet-adressutrymmet. I vårt scenario, *192.168.0.0*
   * **-i (eller - cidr)**. Nätverksmasken i CIDR-format. I vårt scenario, *16*.
   * **-n (eller--undernätsnamn**). Namnet på det första undernätet. I vårt scenario, *FrontEnd*.
   * **-p (eller--undernät-start-ip)**. Första IP-adressen för undernätet eller undernätsadressutrymmet. I vårt scenario, *192.168.1.0*.
   * **-r (eller--undernät cidr)**. Nätverksmasken i CIDR-format för undernätet. I vårt scenario, *24*.
   * **-l (eller --location)**. Azure-region där VNet kommer att skapas. I vårt scenario, *centrala USA*.
3. Kör kommandot **azure network vnet subnet create** för att skapa ett undernät som det visas nedan. Listan som visas efter alla utdata förklarar parametrarna som använts.
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Följande utdata förväntas från kommandot ovan:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (eller--vnet-name**. Namnet på VNet där undernätet kommer att skapas. I vårt scenario, *TestVNet*.
   * **-n (eller --name)**. Namnet på det nya undernätet. I vårt scenario, *BackEnd*.
   * **-a (eller --address-prefix)**. CIDR-block för undernätet. Fyra vårt scenario, *192.168.2.0/24*.
4. Kör kommandot **azure network vnet show** för att visa egenskaperna för den nya VNet som det visas nedan.
   
            azure network vnet show
   
    Följande utdata förväntas från kommandot ovan:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

