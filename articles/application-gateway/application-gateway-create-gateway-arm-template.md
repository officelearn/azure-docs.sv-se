
<properties
   pageTitle="Skapa en programgateway med hjälp av Azure Resource Manager-mallar | Microsoft Azure"
   description="Den här sidan innehåller anvisningar för hur du skapar en programgateway i Azure med hjälp av Azure Resource Manager-mallen"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma"/>


# Skapa en programgateway med hjälp av Azure Resource Manager-mallen

Azure Application Gateway är en Layer 7-belastningsutjämnare. Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt. Application Gateway tillhandahåller följande funktioner för programleverans: HTTP-belastningsutjämning, cookiebaserad sessionstillhörighet och SSL-avlastning (Secure Sockets Layer).

> [AZURE.SELECTOR]
- [PowerShell och den klassiska Azure-portalen](application-gateway-create-gateway.md)
- [PowerShell och Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager-mall](application-gateway-create-gateway-arm-template.md)

<BR>

Du lär dig hur du hämtar och ändrar en befintlig Azure Resource Manager-mall från GitHub och hur du distribuerar mallen från GitHub, PowerShell och Azure CLI.

Om du bara distribuerar Azure Resource Manager-mallen direkt från GitHub utan ändringar kan du gå vidare och distribuera en mall från GitHub.


## Scenario

I det här scenariot ska du:

- Skapa en programgateway med två instanser.
- Skapa ett virtuellt nätverk med namnet VirtualNetwork1 med det reserverade CIDR-blocket 10.0.0.0/16.
- Skapa undernätet Appgatewaysubnet som använder 10.0.0.0/28 som dess CIDR-block.
- Konfigurera två redan definierade backend-IP-adresser för webbservrar vars trafik du vill belastningsutjämna. I det här mallexemplet är backend-IP-adresserna 10.0.1.10 och 10.0.1.11.

>[AZURE.NOTE] Det här är parametrarna för den här mallen. Du kan anpassa mallen genom att ändra regler, lyssnaren och SSL som öppnar azuredeploy.json.



![Scenario](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## Hämta och förstå Azure Resource Manager-mallen

Du kan hämta den befintliga Azure Resource Manager-mallen för att skapa ett virtuellt nätverk och två undernät från GitHub, göra önskade ändringar och sedan återanvända den. Följ bara stegen nedan.

1. Gå till [Skapa programgateway](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create).
2. Klicka på **azuredeploy.json** och sedan på **RAW**.
3. Spara filen i en lokal mapp på datorn.
4. Om du är bekant med Azure Resource Manager-mallar går du vidare till steg 7.
5. Öppna filen som du precis har sparat och titta på innehållet under **parameters** på rad 5. Azure Resource Manager-mallens parametrar fungerar som platshållare för värden som kan anges under distributionen.

  	| Parameter | Beskrivning |
  	|---|---|
  	| **location** | Azure-regionen där programgatewayen ska skapas |
  	| **VirtualNetwork1** | Namnet på det nya virtuella nätverket |
  	| **addressPrefix** | Adressutrymme för det virtuella nätverket i CIDR-format |
  	| **ApplicationGatewaysubnet** | Namnet på programgatewayens undernät |
  	| **subnetPrefix** | CIDR-blocket för programgatewayens undernät |
  	| **skuname** | SKU-instansens storlek |
  	| **capacity** | Antal instanser |
  	| **backendaddress1** | IP-adressen för den första webbservern |
  	| **backendaddress2** | IP-adressen för den andra webbservern |


>[AZURE.IMPORTANT] Azure Resource Manager-mallar som finns på GitHub kan ändras med tiden. Var noga med att granska mallen innan du använder den.

6. Granska innehållet under **resources** och notera följande:

    - **type**. Typ av resurs som skapas av mallen. I detta fall är typen **Microsoft.Network/applicationGateways**, som representerar en programgateway.
    - **name**. Resursens namn. Observera användningen av **[parameters('applicationGatewayName')]**, som innebär att namnet anges som indata av användaren eller av en parameterfil under distributionen.
    - **properties**. Lista över egenskaper för resursen. Den här mallen använder det virtuella nätverket och den offentliga IP-adressen när programgatewayen skapas.

7. Gå tillbaka till https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/.
8. Klicka på **azuredeploy-parameters.json** och sedan på **RAW**.
9. Spara filen i en lokal mapp på datorn.
10. Öppna filen som du precis har sparat och ändra parametrarnas värden. Använd värdena nedan för att distribuera programgatewayen i vårt scenario.

        {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }

11. Spara filen. Du kan testa JSON-mallen och parametermallen med hjälp av webbaserade JSON-verifieringsverktyg som [JSlint.com](http://www.jslint.com/).

## Distribuera Azure Resource Manager-mallen med hjälp av PowerShell

Om du aldrig har använt Azure PowerShell tidigare läser du [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md) och följer alla anvisningar ända till slutet för att logga in i Azure och välja din prenumeration.

### Steg 1

        Login-AzureRmAccount



### Steg 2

Kontrollera prenumerationerna för kontot.

        get-AzureRmSubscription

Du uppmanas att autentisera dig med dina autentiseringsuppgifter.<BR>

### Steg 3

Välj vilka av dina Azure-prenumerationer som du vill använda. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Steg 4


Om det behövs skapar du en ny resursgrupp med hjälp av cmdleten **New-AzureResourceGroup**. I exemplet nedan ska du skapa en ny resursgrupp kallad AppgatewayRG med platsen East USA.

     New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
        VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


        ResourceGroupName : AppgatewayRG
        Location          : eastus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                     Actions  NotActions
                     =======  ==========
                      *

        ResourceId        : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/AppgatewayRG

Kör cmdleten **New-AzureRmResourceGroupDeployment** för att distribuera det nya virtuella nätverket med hjälp av mall- och parameterfilerna som du hämtade och ändrade ovan.

        New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
           -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

Följande utdata genereras av kommandoraden:

        DeploymentName    : testappgatewaydeployment
        ResourceGroupName : appgatewayRG
        ProvisioningState : Succeeded
        Timestamp         : 9/19/2015 1:49:41 AM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                   Name             Type                       Value
                   ===============  =========================  ==========
                   location         String                     East US
                   addressPrefix    String                     10.0.0.0/16
                   subnetPrefix     String                     10.0.0.0/24
                   skuName          String                     Standard_Small
                   capacity         Int                        2
                   backendIpAddress1  String                     10.0.1.10
                   backendIpAddress2  String                     10.0.1.11

        Outputs           :


## Distribuera Azure Resource Manager-mallen med hjälp av Azure CLI

Distribuera Azure Resource Manager-mallen som du hämtade med hjälp av Azure CLI genom att följa stegen nedan:

1. Om du aldrig har använt Azure CLI läser du [Installera och konfigurera Azure CLI](../xplat-cli-install.md) och följer anvisningarna fram till det steg då du väljer ditt Azure-konto och din Azure-prenumeration.
2. Växla till Resource Manager-läge genom att köra kommandot **azure config mode** (se nedan).

        azure config mode arm

Följande utdata förväntas från kommandot ovan:

        info:   New mode is arm

3. Om det behövs skapar du en ny resursgrupp genom att köra **azure group create** (se nedan). Observera kommandots utdata. Listan som visas efter alla utdata förklarar parametrarna som använts. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../resource-group-overview.md).

        azure group create -n appgatewayRG -l eastus

**-n (eller --name)**. Namnet på den nya resursgruppen. I vårt scenario är det *appgatewayRG*.

**-l (eller --location)**. Azure-regionen som den nya resursgruppen ska skapas i. I vårt scenario är det *eastus*.

4. Kör cmdleten **azure group deployment create** för att distribuera det nya virtuella nätverket med hjälp av mall- och parameterfilerna som du hämtade och ändrade ovan. Listan som visas efter alla utdata förklarar parametrarna som använts.

        azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

Följande utdata förväntas från kommandot ovan:

        azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestAppgatewayDeployment"
        + Waiting for deployment to complete
        data:    DeploymentName     : TestAppgatewayDeployment
        data:    ResourceGroupName  : appgatewayRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
        data:    Mode               : Incremental
        data:    Name               Type    Value
        data:    -----------------  ------  --------------
        data:    location           String  East US
        data:    addressPrefix      String  10.0.0.0/16
        data:    subnetPrefix       String  10.0.0.0/24
        data:    skuName            String  Standard_Small
        data:    capacity           Int     2
        data:    backendIpAddress1  String  10.0.1.10
        data:    backendIpAddress2  String  10.0.1.11
        info:    group deployment create command OK

**-g (eller --resource-group)**. Namnet på resursgruppen som det nya virtuella nätverket skapas i.

**-f (eller --template-file)**. Sökvägen till Azure Resource Manager-mallfilen.

**-e (eller --parameters-file)**. Sökvägen till Azure Resource Manager-parameterfilen.

## Distribuera Azure Resource Manager-mallen med hjälp av ”klicka för att distribuera”

”Klicka för att distribuera” är ett annat sätt att använda Azure Resource Manager-mallar. Det är ett enkelt sätt att använda mallar med Azure-portalen.


### Steg 1
Gå till [Skapa en programgateway med en offentlig IP-adress](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/).


### Steg 2

Klicka på **Distribuera till Azure**.

![Distribuera till Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### Steg 3

Fyll i parametrarna för distributionsmallen på portalen och klicka på **OK**.

![Parametrar](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### Steg 4

Välj **Juridiska villkor** och klicka på **Köp**.

### Steg 5

Klicka på **Skapa** på bladet Anpassad distribution.



## Nästa steg

Om du vill konfigurera SSL-avlastning läser du [Konfigurera en programgateway för SSL-avlastning](application-gateway-ssl.md).

Om du vill konfigurera en programgateway för användning med en intern belastningsutjämnare läser du [Skapa en programgateway med en intern belastningsutjämnare (ILB)](application-gateway-ilb.md).

Om du vill ha mer information om belastningsutjämningsalternativ i allmänhet läser du:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=jun16_HO2-->


