## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Distribuera ARM-mallen med Azure CLI
Följ stegen nedan för att distribuera ARM-mallen som du hämtade med Azure CLI.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../articles/cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **`azure config mode`** för att byta till Resource Manager-läge enligt nedan.
   
        azure config mode arm
   
    Följande utdata förväntas från kommandot ovan:
   
        info:    New mode is arm
3. Vid behov kan du köra **`azure group create`** för att skapa en ny resursgrupp, enligt nedan. Notera kommandots utdata. Listan som visas efter alla utdata förklarar parametrarna som använts. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
   
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
4. Kör cmdleten **`azure group deployment create`** för att distribuera det nya VNet med hjälp av mall- och parameterfilerna som du hämtade och ändrade ovan. Listan som visas efter utdatan beskriver de parametrar som används.
   
        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
   
    Följande utdata förväntas från kommandot ovan:
   
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK
   
   * **-g (eller --resource-group)**. Namnet på resursgruppen där det nya VNet kommer att skapas.
   * **-f (eller --template-file)**. Sökväg till din ARM-mallfil.
   * **-e (eller --parameters-file)**. Sökväg till din ARM-parameterfil.
5. Kör kommandot **`azure network vnet show`** för att visa egenskaperna för det nya vnet, enligt nedan.
   
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

