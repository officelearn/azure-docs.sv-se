---
title: Skapa en Azure Application Gateway - mallar
description: Den här artikeln innehåller instruktioner för att skapa en Programgateway i Azure med hjälp av Azure Resource Manager-mall
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 113e30ab65f2cd0ff420df1c79807147a62f79f1
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608470"
---
# <a name="create-an-application-gateway-by-using-the-azure-resource-manager-template"></a>Skapa en programgateway med hjälp av Azure Resource Manager-mallen

Azure Application Gateway är en Layer 7-lastbalanserare. Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt. Application Gateway innehåller många ADC-funktioner (Application Delivery Controller), inklusive HTTP-belastningsutjämning, cookie-baserad sessionstilldelning, SSL-avlastning (Secure Sockets Layer), anpassade hälsoavsökningar, stöd för flera platser och mycket mer. En fullständig lista över funktioner som stöds finns [översikt över Application Gateway](application-gateway-introduction.md)

Den här artikeln beskriver hur du laddar ned och ändra en befintlig [Azure Resource Manager-mall](../azure-resource-manager/resource-group-authoring-templates.md) från GitHub och distribuerar mallen från GitHub, PowerShell och Azure CLI.

Om du bara distribuerar mallen direkt från GitHub utan ändringar, kan du gå vidare och distribuera en mall från GitHub.

## <a name="scenario"></a>Scenario

I det här scenariot ska du:

* Skapa en Programgateway med brandväggen för webbaserade program.
* Skapa ett virtuellt nätverk med namnet VirtualNetwork1 med det reserverade CIDR-blocket 10.0.0.0/16.
* Skapa undernätet Appgatewaysubnet som använder 10.0.0.0/28 som dess CIDR-block.
* Konfigurera två redan definierade backend-IP-adresser för webbservrar vars trafik du vill belastningsutjämna. I det här mallexemplet är backend-IP-adresserna 10.0.1.10 och 10.0.1.11.

> [!NOTE]
> De inställningarna är parametrarna för den här mallen. För att anpassa mallen, kan du ändra regler, lyssnaren, SSL och andra alternativ i filen azuredeploy.JSON.

![Scenario](./media/application-gateway-create-gateway-arm-template/scenario.png)

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Hämta och förstå Azure Resource Manager-mallen

Du kan hämta den befintliga Azure Resource Manager-mallen för att skapa ett virtuellt nätverk och två undernät från GitHub, göra önskade ändringar och sedan återanvända den. Så här loggar du in:

1. Gå till [skapa Programgateway med brandväggen för webbaserade program aktiverad](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-waf).
1. Klicka på **azuredeploy.json** och klicka sedan på **RAW**.
1. Spara filen i en lokal mapp på datorn.
1. Om du är bekant med Azure Resource Manager-mallar går du vidare till steg 7.
1. Öppna filen som du sparade och titta på innehållet under **parametrar** i rad
1. Azure Resource Manager-mallens parametrar fungerar som platshållare för värden som kan anges under distributionen.

   | Parameter | Beskrivning |
   | --- | --- |
   | **subnetPrefix** |CIDR-blocket för programgatewayens undernät. |
   | **applicationGatewaySize** | Storleken på programgatewayen.  WAF tillåter endast medelstora och stora. |
   | **backendIpaddress1** |IP-adressen för den första webbservern. |
   | **backendIpaddress2** |IP-adressen för den andra webbservern. |
   | **wafEnabled** | Inställning för att avgöra om WAF är aktiverad.|
   | **wafMode** | Läget för brandväggen för webbaserade program.  Tillgängliga alternativ är **dataförlustskydd** eller **identifiering**.|
   | **wafRuleSetType** | RuleSet-typ för WAF.  OWASP är för närvarande det enda alternativet som stöds. |
   | **wafRuleSetVersion** |RuleSet-version. OWASP CRS 2.2.9 och 3.0 finns för närvarande alternativ som stöds. |

1. Kontrollera innehållet i **resurser** och Lägg märke till följande egenskaper:

   * **type**. Typ av resurs som skapas av mallen. I det här fallet typen är `Microsoft.Network/applicationGateways`, som representerar en Programgateway.
   * **name**. Namn på den virtuella resursen. Observera användningen av `[parameters('applicationGatewayName')]`, vilket innebär att namnet anges som indata av dig eller av en parameterfil under distributionen.
   * **properties**. Lista över egenskaper för resursen. Den här mallen använder det virtuella nätverket och den offentliga IP-adressen när programgatewayen skapas.

1. Gå tillbaka till [ https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf/ ](https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-waf).
1. Klicka på **azuredeploy-parameters.json**, och klicka sedan på **RAW**.
1. Spara filen i en lokal mapp på datorn.
1. Öppna filen som du sparade och ändra parametrarnas värden. Använd följande värden för att distribuera programgatewayen från vårt scenario.

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "addressPrefix": {
             "value": "10.0.0.0/16"
             },
             "subnetPrefix": {
             "value": "10.0.0.0/28"
             },
             "applicationGatewaySize": {
             "value": "WAF_Medium"
             },
             "capacity": {
             "value": 2
             },
             "backendIpAddress1": {
             "value": "10.0.1.10"
             },
             "backendIpAddress2": {
             "value": "10.0.1.11"
             },
             "wafEnabled": {
             "value": true
             },
             "wafMode": {
             "value": "Detection"
             },
             "wafRuleSetType": {
             "value": "OWASP"
             },
             "wafRuleSetVersion": {
             "value": "3.0"
             }
         }
     }
     ```

1. Spara filen. Du kan testa JSON-mallen och parametermallen med hjälp av webbaserade JSON-verifieringsverktyg som [JSlint.com](https://www.jslint.com/).

## <a name="deploy-the-azure-resource-manager-template-by-using-powershell"></a>Distribuera Azure Resource Manager-mallen med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du aldrig har använt Azure PowerShell finns: [Hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) och följ anvisningarna för att logga in på Azure och välj din prenumeration.

1. Logga in på PowerShell

    ```powershell
    Connect-AzAccount
    ```

1. Kontrollera prenumerationerna för kontot.

    ```powershell
    Get-AzSubscription
    ```

    Du ombeds att autentisera dig med dina autentiseringsuppgifter.

1. Välj vilka av dina Azure-prenumerationer som du vill använda.

    ```powershell
    Select-AzSubscription -Subscriptionid "GUID of subscription"
    ```

1. Om det behövs skapar du en resursgrupp med hjälp av cmdleten **New-AzureResourceGroup**. I följande exempel skapar du en resursgrupp kallad appgatewayrg med platsen East USA.

    ```powershell
    New-AzResourceGroup -Name AppgatewayRG -Location "West US"
    ```

1. Kör den **New AzResourceGroupDeployment** cmdlet för att distribuera nya virtuella nätverket med hjälp av den föregående mall- och parameterfilerna filer du hämtade och ändrade.
    
    ```powershell
    New-AzResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-the-azure-cli"></a>Distribuera Azure Resource Manager-mallen med hjälp av Azure CLI

Följ stegen nedan om du vill distribuera Azure Resource Manager-mallen som du hämtade med hjälp av Azure CLI:

1. Om du aldrig har använt Azure CLI läser du [Installera och konfigurera Azure CLI](/cli/azure/install-azure-cli) och följer anvisningarna fram till det steg då du väljer ditt Azure-konto och din Azure-prenumeration.

1. Om det behövs kan du köra den `az group create` kommando för att skapa en resursgrupp, enligt följande kodavsnitt. Observera kommandots utdata. Listan som visas efter utdatan beskriver de parametrar som används. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    ```azurecli
    az group create --location westus --name appgatewayRG
    ```
    
    **-n (eller --name)**. Namnet på den nya resursgruppen. I vårt scenario är det *appgatewayRG*.
    
    **-l (eller --location)**. Azure-region där den nya resursgruppen skapas. För vårt scenario, den har *westus*.

1. Kör den `az group deployment create` cmdlet för att distribuera nya virtuella nätverket med hjälp av mall- och parameterfilerna filer du hämtade och ändrade i föregående steg. Listan som visas efter utdatan beskriver de parametrar som används.

    ```azurecli
    az group deployment create --resource-group appgatewayRG --name TestAppgatewayDeployment --template-file azuredeploy.json --parameters @azuredeploy-parameters.json
    ```

## <a name="deploy-the-azure-resource-manager-template-by-using-click-to-deploy"></a>Distribuera Azure Resource Manager-mallen med hjälp av ”klicka för att distribuera”

”Klicka för att distribuera” är ett annat sätt att använda Azure Resource Manager-mallar. Det är ett enkelt sätt att använda mallar med Azure-portalen.

1. Gå till [skapa en Programgateway med brandväggen för webbaserade program](https://azure.microsoft.com/documentation/templates/101-application-gateway-waf/).

1. Klicka på **Distribuera till Azure**.

    ![Distribuera till Azure](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)
    
1. Fyll i parametrarna för distributionsmallen på portalen och klicka på **OK**.

    ![Parametrar](./media/application-gateway-create-gateway-arm-template/ibiza1.png)
    
1. Välj **jag godkänner villkoren som anges ovan** och klicka på **köp**.

1. Klicka på **Skapa** på bladet Anpassad distribution.

## <a name="providing-certificate-data-to-resource-manager-templates"></a>Tillhandahåller certifikatdata för till Resource Manager-mallar

När du använder SSL med en mall, måste certifikatet anges i en base64-sträng i stället för som laddas upp. Om du vill konvertera med en PFX- eller .cer till en base64-sträng någon av följande kommandon. Följande kommandon konvertera certifikatet till en base64-sträng som kan anges i mallen. Utdata som förväntas är en sträng som kan lagras i en variabel och klistras in i mallen.

### <a name="macos"></a>macOS
```bash
cert=$( base64 <certificate path and name>.pfx )
echo $cert
```

### <a name="windows"></a>Windows
```powershell
[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("<certificate path and name>.pfx"))
```

## <a name="delete-all-resources"></a>Ta bort alla resurser

Om du vill ta bort alla resurser som skapats i den här artikeln, gör du något av följande steg:

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzResourceGroup -Name appgatewayRG
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az group delete --name appgatewayRG
```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera SSL-avlastning finns: [Konfigurera en Programgateway för SSL-avlastning](application-gateway-ssl.md).

Om du vill konfigurera en Programgateway för användning med en intern belastningsutjämnare, gå till: [Skapa en Programgateway med en intern belastningsutjämnare (ILB)](application-gateway-ilb.md).

Om du vill ha mer information om belastningsutjämningsalternativ i allmänhet läser du:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)