---
title: Konfigurera IP-brandvägg för ämnen eller domäner i Azure Event Grid (förhandsversion)
description: I den här artikeln beskrivs hur du konfigurerar brandväggsinställningar för ämnen eller domäner i Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299873"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Konfigurera IP-brandvägg för ämnen eller domäner i Azure Event Grid (förhandsversion)
Som standard är ämne och domän tillgängliga från internet så länge begäran levereras med giltig autentisering och auktorisering. Med IP-brandväggen kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adressintervall i [CIDR-notation (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Utgivare som kommer från någon annan IP-adress kommer att avvisas och kommer att få ett 403 (förbjudet) svar. Mer information om nätverkssäkerhetsfunktioner som stöds av Event Grid finns i [Nätverkssäkerhet för Händelserutnät](network-security.md).

I den hÃ¤r artikeln beskrivs hur du konfigurerar INVÃ¤nder IP-brandvÃ¤nder fÃ¤r Azure Event Grid-ämnen eller domäner.

## <a name="use-azure-portal"></a>Använda Azure-portalen
I det här avsnittet visas hur du använder Azure-portalen för att skapa regler för inkommande IP-brandväggar. Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa inkommande IP-regler för **domäner**. 

1. I [Azure-portalen](https://portal.azure.com)navigerar du till ditt ämne eller domän för händelserutnät och växlar till fliken **Nätverk.**
2. Välj **Offentliga nätverk** om du vill att alla nätverk, inklusive Internet, ska kunna komma åt resursen. 

    Du kan begränsa trafiken med hjälp av IP-baserade brandväggsregler. Ange en enda IPv4-adress eller ett intervall med IP-adresser i CIDR-notation (Classless inter-domain routing). 

    ![Sida för offentliga nätverk](./media/configure-firewall/public-networks-page.png)
3. Välj **Endast privata slutpunkter** om du bara vill tillåta privata slutpunktsanslutningar att komma åt den här resursen. Använd fliken **Privata slutpunktsanslutningar** på den här sidan för att hantera anslutningar. 

    ![Sida för offentliga nätverk](./media/configure-firewall/private-endpoints-page.png)
4. Välj **Spara** i verktygsfältet. 



## <a name="use-azure-cli"></a>Använda Azure CLI
I det här avsnittet visas hur du använder Azure CLI-kommandon för att skapa ämnen med inkommande IP-regler. Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa inkommande IP-regler för **domäner**. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Aktivera offentlig nätverksåtkomst för ett befintligt ämne
Som standard är den offentliga nätverksåtkomsten aktiverad för ämnen och domäner. Du kan begränsa trafiken genom att konfigurera regler för inkommande IP-brandväggar. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Inaktivera offentlig nätverksåtkomst för ett befintligt ämne
När offentlig nätverksåtkomst är inaktiverad för ett ämne eller en domän är trafik via offentligt internet inte tillåtet. Endast privata slutpunktsanslutningar kan komma åt dessa resurser. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Skapa ämne med inkommande IP-regler
Följande exempel CLI-kommando skapar ett händelserutnätsämne med inkommande IP-regler i ett steg. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Skapa ämne först och lägg sedan till inkommande IP-regler
I det här exemplet skapas ett händelserutnätsämne först och sedan läggs inkommande IP-regler till för ämnet i ett separat kommando. Den uppdaterar också de inkommande IP-regler som angavs i det andra kommandot. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>Använd PowerShell
I det här avsnittet visas hur du använder Azure PowerShell-kommandon för att skapa Azure Event Grid-ämnen med inkommande IP-brandväggsregler. Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa inkommande IP-regler för **domäner**. 

### <a name="prerequisite"></a>Krav
Följ instruktionerna från [How to: Use the portal to create an Azure AD application and service principal that can access resources](../active-directory/develop/howto-create-service-principal-portal.md) to create an Azure Active Directory application and note down the following values:

- Katalog-ID (klient)
- Program-ID (klient)
- Program (klient) hemlighet

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Förbereda token och rubriker för REST API-anrop 
Kör följande nödvändiga kommandon för att hämta en autentiseringstoken som ska användas med REST API-anrop och auktorisering och annan rubrikinformation. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Aktivera offentlig nätverksåtkomst för ett befintligt ämne
Som standard är den offentliga nätverksåtkomsten aktiverad för ämnen och domäner. Du kan begränsa trafiken genom att konfigurera regler för inkommande IP-brandväggar. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Inaktivera offentlig nätverksåtkomst för ett befintligt ämne
När offentlig nätverksåtkomst är inaktiverad för ett ämne eller en domän är trafik via offentligt internet inte tillåtet. Endast privata slutpunktsanslutningar kan komma åt dessa resurser. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Skapa ett händelserutnätsämne med inkommande regler i ett steg

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Skapa händelserutnätsämne först och lägg sedan till inkommande IP-regler

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelseleveranser finns i [Övervaka leverans av händelserutnätsmeddelanden](monitor-event-delivery.md).
* Mer information om autentiseringsnyckeln finns i [säkerhet och autentisering av Händelserutnät](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
