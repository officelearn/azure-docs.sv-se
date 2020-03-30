---
title: Konfigurera privata slutpunkter för Azure Event Grid-ämnen eller domäner
description: I den här artikeln beskrivs hur du konfigurerar privata slutpunkter för Azure Event Grid-ämnen eller domän.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299912"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Konfigurera privata slutpunkter för Azure Event Grid-ämnen eller domäner (förhandsversion)
Du kan använda [privata slutpunkter](../private-link/private-endpoint-overview.md) för att tillåta inträngning av händelser direkt från ditt virtuella nätverk till dina ämnen och domäner på ett säkert sätt via en [privat länk](../private-link/private-link-overview.md) utan att gå igenom det offentliga internet. Den privata slutpunkten använder en IP-adress från VNet-adressutrymmet för ditt ämne eller din domän. Mer begreppsmässig information finns i [Nätverkssäkerhet](network-security.md).

I den här artikeln beskrivs hur du konfigurerar privata slutpunkter för ämnen eller domäner.

> [!IMPORTANT]
> Funktionen Privata slutpunkter är endast tillgänglig för ämnen och domäner på premiumnivå. Information om hur du uppgraderar från grundläggande nivå till premiumnivå finns i artikeln [Uppdatera prisnivå.](update-tier.md) 

## <a name="use-azure-portal"></a>Använda Azure-portalen 
I det här avsnittet visas hur du använder Azure-portalen för att skapa en privat slutpunkt för ett ämne eller en domän.

> [!NOTE]
> Stegen som visas i det här avsnittet är oftast för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för **domäner**. 

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till ditt ämne eller din domän.
2. Växla till fliken **Nätverk** på ämnessidan. Välj **+ Privat slutpunkt** i verktygsfältet.

    ![Lägg till privat slutpunkt](./media/configure-private-endpoints/add-button.png)
2. En av **grunderna** sidan, gör så här: 
    1. Välj en **Azure-prenumeration** där du vill skapa den privata slutpunkten. 
    2. Välj en **Azure-resursgrupp** för den privata slutpunkten. 
    3. Ange ett **namn** för slutpunkten. 
    4. Välj **region** för slutpunkten. Din privata slutpunkt måste finnas i samma region som det virtuella nätverket, men kan i en annan region än den privata länkresursen (i det här exemplet ett händelserutnätsämne). 
    5. Välj sedan **Knappen Nästa: Resurs >** längst ned på sidan. 

      ![Privat slutpunkt - grunderna sida](./media/configure-private-endpoints/basics-page.png)
3. Gör så här på sidan **Resurs:** 
    1. Om du väljer Anslut till en Azure-resurs i min katalog gör du så här om du väljer **Anslut till en Azure-resurs i min katalog.** Det här exemplet visar hur du ansluter till en Azure-resurs i katalogen. 
        1. Välj den **Azure-prenumeration** där **ditt ämne/domän** finns. 
        1. För **resurstyp**väljer du **Microsoft.EventGrid/topics** eller **Microsoft.EventGrid/domains** for the **Resource type**.
        2. För **Resurs**väljer du ett ämne/en domän i listrutan. 
        3. Bekräfta att **underresursen Mål** är inställd på **ämne** eller **domän** (baserat på den resurstyp du har valt).    
        4. Välj **Nästa: Knappen Konfiguration >** längst ned på sidan. 

            ![Privat slutpunkt - resurssida](./media/configure-private-endpoints/resource-page.png)
    2. Om du väljer **Anslut till en resurs med hjälp av ett resurs-ID eller ett alias**gör du så här:
        1. Ange resursens ID. Till exempel: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. För **Resurs**anger du **ämne** eller **domän**. 
        3. (valfritt) Lägg till ett meddelande om begäran. 
        4. Välj **Nästa: Knappen Konfiguration >** längst ned på sidan. 

            ![Privat slutpunkt - resurssida](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. På sidan **Konfiguration** väljer du undernätet i ett virtuellt nätverk dit du vill distribuera den privata slutpunkten. 
    1. Välj ett **virtuellt nätverk**. Endast virtuella nätverk i den valda prenumerationen och platsen visas i listrutan. 
    2. Markera ett **undernät** i det virtuella nätverk som du har valt. 
    3. Välj **Nästa: Taggar >** knappen längst ned på sidan. 

    ![Privat slutpunkt - konfigurationssida](./media/configure-private-endpoints/configuration-page.png)
5. På sidan **Taggar** skapar du alla taggar (namn och värden) som du vill associera med den privata slutpunktsresursen. Välj sedan **knappen Granska + skapa** längst ned på sidan. 
6. Granska alla inställningar i **granskning + skapa**och välj **Skapa** för att skapa den privata slutpunkten. 

    ![Privat slutpunkt - granska & skapa sida](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>Hantera privat länkanslutning

När du skapar en privat slutpunkt måste anslutningen godkännas. Om resursen som du skapar en privat slutpunkt för finns i katalogen kan du godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta på att resursägaren godkänner din anslutningsbegäran.

Det finns fyra etableringstillstånd:

| Åtgärd för tjänst | Privat slutpunktstillstånd för tjänstkonsument | Beskrivning |
|--|--|--|
| Inget | Väntande åtgärder | Anslutningen skapas manuellt och väntar på godkännande från den privata resursägaren för Link. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är klar att användas. |
| Avvisa | Avvisad | Anslutningen avvisades av ägaren till den privata länkresursen. |
| Ta bort | Frånkopplad | Anslutningen togs bort av ägaren till den privata länkresursen, den privata slutpunkten blir informativ och bör tas bort för rensning. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Hantera en privat slutpunktsanslutning
I följande avsnitt visas hur du godkänner eller avvisar en privat slutpunktsanslutning. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Skriv in **ämnen i händelserutnät** eller **domäner för händelserutnät**i sökfältet .
1. Markera det **ämne** eller **den domän** som du vill hantera.
1. Välj fliken **Nätverk.**
1. Om det finns några anslutningar som väntar visas en anslutning som visas med **Väntande** i etableringstillståndet. 

### <a name="to-approve-a-private-endpoint"></a>Så här godkänner du en privat slutpunkt
Du kan godkänna en privat slutpunkt som är i väntande tillstånd. Så här godkänner du följande: 

> [!NOTE]
> Stegen som visas i det här avsnittet är oftast för ämnen. Du kan använda liknande steg för att godkänna privata slutpunkter för **domäner**. 

1. Välj den **privata slutpunkt** som du vill godkänna och välj **Godkänn** i verktygsfältet.

    ![Privat slutpunkt - väntande tillstånd](./media/configure-private-endpoints/pending.png)
1. I dialogrutan **Godkänn anslutning** anger du en kommentar (valfritt) och väljer **Ja**. 

    ![Privat slutpunkt - godkänn](./media/configure-private-endpoints/approve.png)
1. Bekräfta att du ser **slutpunktens**status som Godkänd . 

    ![Privat slutpunkt - godkänt tillstånd](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Så här avvisar du en privat slutpunkt
Du kan avvisa en privat slutpunkt som är i väntande tillstånd eller godkänt tillstånd. Så här avvisar du: 

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att avvisa privata slutpunkter för **domäner**. 

1. Markera den **privata slutpunkt** som du vill avvisa och välj **Avvisa** i verktygsfältet.

    ![Privat slutpunkt - avvisa](./media/configure-private-endpoints/reject-button.png)
1. I dialogrutan **Avvisa anslutning** anger du en kommentar (valfritt) och väljer **Ja**. 

    ![Privat slutpunkt - avvisa](./media/configure-private-endpoints/reject.png)
1. Bekräfta att du ser slutpunktens status som **Avvisad**. 

    ![Privat slutpunkt - avvisat tillstånd](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Du kan inte godkänna en privat slutpunkt i Azure-portalen när den har avvisats. 


## <a name="use-azure-cli"></a>Använda Azure CLI
Om du vill skapa en privat slutpunkt använder du metoden [för att skapa az-nätverk privat slutpunkt](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) enligt följande exempel:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Beskrivningar av de parametrar som används i exemplet finns i dokumentationen för [az-nätverks-private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). Några punkter att notera i det här exemplet är: 

- För `private-connection-resource-id`anger du resurs-ID för **ämnet** eller **domänen**. I föregående exempel används typen: avsnittet.
- för `group-ids`, `topic` `domain`ange eller . I föregående exempel `topic` används. 

Om du vill ta bort en privat slutpunkt använder du metoden för borttagning av [az-nätverk privat slutpunkt](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) enligt följande exempel:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för **domäner**. 

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
Här är ett exempelskript som skapar följande Azure-resurser:

- Resursgrupp
- Virtuellt nätverk
- Undernät i det virtuella nätverket
- Azure Event Grid-ämne (premiumnivå)
- Privat slutpunkt för ämnet

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för domäner.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slutpunktsanslutning
Följande CLI-kodavsnitt visar hur du godkänner en privat slutpunktsanslutning. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>Avvisa en privat slutpunktsanslutning
Följande CLI-kodavsnitt visar hur du avvisar en privat slutpunktsanslutning. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>Använd PowerShell
I det här avsnittet visas hur du skapar en privat slutpunkt för ett ämne eller en domän med PowerShell. 

### <a name="prerequisite"></a>Krav
Följ instruktionerna från [How to: Use the portal to create an Azure AD application and service principal that can access resources](../active-directory/develop/howto-create-service-principal-portal.md) to create an Azure Active Directory application and note down the values for Directory **(tenant) ID**, **Application (Client) ID**, and **Application (client) secret**. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Förbereda token och rubriker för REST API-anrop 
Kör följande nödvändiga kommandon för att hämta en autentiseringstoken som ska användas med REST API-anrop och auktorisering och annan rubrikinformation. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Skapa ett undernät med principer för slutpunktsnätverk inaktiverat

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Skapa ett händelserutnätsämne med en privat slutpunkt

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för **domäner**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

När du kontrollerar att slutpunkten skapades bör du se resultatet som liknar följande:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slutpunktsanslutning
Följande exempel på PowerShell-kodavsnitt visar hur du godkänner en privat slutpunkt. 

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att godkänna privata slutpunkter för **domäner**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Avvisa en privat slutpunktsanslutning
I följande exempel visas hur du avvisar en privat slutpunkt med PowerShell. Du kan hämta GUID för den privata slutpunkten från resultatet av föregående GET-kommando. 

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att avvisa privata slutpunkter för **domäner**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

Du kan godkänna anslutningen även efter att den har avvisats via API. Om du använder Azure-portalen kan du inte godkänna en slutpunkt som har avvisats. 

## <a name="next-steps"></a>Nästa steg
Mer information om hur du konfigurerar inställningar för IP-brandväggen finns i [Konfigurera IP-brandvägg för Azure Event Grid-ämnen eller domäner](configure-firewall.md).