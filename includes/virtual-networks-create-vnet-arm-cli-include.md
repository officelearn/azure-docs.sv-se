## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Skapa ett VNet med Azure CLI
Du kan använda Azure CLI för att hantera dina Azure-resurser från kommandotolken från valfri dator som kör Windows, Linux eller OSX. Följ stegen nedan för att skapa ett VNet med hjälp av Azure CLI.

1. Om du aldrig har använt Azure CLI, se [Installera och konfigurera Azure CLI](../articles/cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **azure config mode** för att växla till Resource Manager-läge, som det visas nedan.
   
        azure config mode arm
   
    Här är den utdata som förväntas för det ovanstående kommandot:
   
        info:    New mode is arm
3. Kör vid behov **azure group create** för att skapa en ny resursgrupp som det visas nedan. Observera kommandots utdata. Listan som visas efter alla utdata förklarar parametrarna som använts. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
        azure group create -n TestRG -l centralus
   
    Följande utdata förväntas från kommandot ovan:
   
        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
   
   * **-n (eller --name)**. Namn för den nya resursgruppen. I vårt exempel, *TestRG*.
   * **-l (eller --location)**. Azure-region där den nya resursgruppen kommer att skapas. I vårt scenario, *centralus*.
4. Kör kommandot **azure network vnet create** för att skapa en VNet och ett undernät som det visas nedan. 
   
        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus
   
    Följande utdata förväntas från kommandot ovan:
   
        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK
   
   * **-g (eller --resource-group)**. Namnet på resursgruppen där VNet kommer att skapas. I vårt exempel, *TestRG*.
   * **-n (eller --name)**. Namnet på den VNet som ska skapas. I vårt exempel, *TestVNet*
   * **-a (eller --address-prefixes)**. Lista över CIDR-block som används för VNet-adressutrymmet. I vårt scenario, *192.168.0.0/16*
   * **-l (eller --location)**. Azure-region där VNet kommer att skapas. I vårt scenario, *centralus*.
5. Kör kommandot **azure network vnet subnet create** för att skapa ett undernät som det visas nedan. Observera kommandots utdata. Listan som visas efter alla utdata förklarar parametrarna som använts.
   
        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
   
    Följande utdata förväntas från kommandot ovan:
   
        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK
   
   * **-e (eller --vnet-name**. Namnet på VNet där undernätet kommer att skapas. I vårt scenario, *TestVNet*.
   * **-n (eller --name)**. Namnet på det nya undernätet. I vårt scenario, *FrontEnd*.
   * **-a (eller --address-prefix)**. CIDR-block för undernätet. I vårt scenario, *192.168.1.0/24*.
6. Upprepa steg 5 ovan för att skapa andra undernät vid behov. I vårt scenario, kör du kommandot nedan för att skapa undernätet *BackEnd*.
   
        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24
7. Kör kommandot **azure network vnet show** för att visa egenskaperna för den nya VNet som det visas nedan.
   
        azure network vnet show -g TestRG -n TestVNet
   
    Följande utdata förväntas från kommandot ovan:
   
        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK

