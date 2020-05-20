---
title: Använd hanterad tjänst identitet för att vidarebefordra händelser Azure Event Grid prenumerationer
description: I den här artikeln beskrivs hur du aktiverar hanterad tjänst identitet för ett Azure Event Grid-ämne. Använd den för att vidarebefordra händelser till destinationer som stöds.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 0e1f46c0bd7ce22cae77db1a524336445fd3f795
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691028"
---
# <a name="enable-and-use-managed-service-identity-for-an-event-grid-topic-or-domain"></a>Aktivera och använda hanterad tjänst identitet för ett event Grid-ämne eller en domän 
I den här artikeln beskrivs hur du aktiverar [hanterad tjänst identitet](../active-directory/managed-identities-azure-resources/overview.md) för ett event Grid-ämne eller en domän. Använd den för att vidarebefordra händelser till stödda destinationer som Service Bus köer och ämnen, Event Hub och lagrings konton.

Här följer de steg som beskrivs i detalj i den här artikeln:
1. Skapa ett ämne eller en domän med en tilldelad identitet (eller) uppdatera ett befintligt ämne eller en domän för att aktivera identitet. 
2. Lägg till identiteten i en lämplig roll (exempel: Service Bus data avsändare) på målet (exempel: en Service Bus kö)
3. När du skapar händelse prenumerationer ska du aktivera användningen av identiteten för att leverera händelser till målet. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Skapa ett ämne eller en domän med en identitet
Först ska vi titta på hur du skapar ett ämne eller en domän med en Systemhanterad identitet.

### <a name="using-azure-portal"></a>Använda Azure Portal
Du kan aktivera systemtilldelad identitet för ett ämne/en domän när du skapar den i Azure Portal. Följande bild visar hur du aktiverar Systemhanterad identitet för ett ämne. I princip väljer du alternativet **Aktivera en systemtilldelad identitet** på sidan **Avancerat** i guiden skapa ämne. Du ser det här alternativet på sidan **Avancerat** i guiden skapa domän även. 

![Aktivera identitet när du skapar ett ämne](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan också använda Azure CLI för att skapa ett ämne eller en domän med en tilldelad identitet. Använd `az eventgrid topic create` kommandot med `--identity` parametern inställt på `systemassigned` . Om du inte anger något värde för den här parametern används standardvärdet `noidentity` . 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

På samma sätt kan du använda `az eventgrid domain create` kommandot för att skapa en domän med en Systemhanterad identitet.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Aktivera identitet för ett befintligt ämne eller en befintlig domän
I det sista avsnittet har du lärt dig hur du aktiverar Systemhanterad identitet när du skapar ett ämne eller en domän. I det här avsnittet får du lära dig hur du aktiverar Systemhanterad identitet för ett befintligt ämne eller en befintlig domän. 

### <a name="using-azure-portal"></a>Använda Azure Portal
1. Navigera till [Azure Portal](https://portal.azure.com)
2. Sök efter **händelse rutnäts ämnen** i Sök fältet.
3. Välj det **avsnitt** som du vill aktivera den hanterade identiteten för. 
4. Växla till fliken **identitet** . 
5. Aktivera växeln för att aktivera identiteten. 

    Du kan använda liknande steg för att aktivera identitet för en Event Grid-domän.

### <a name="using-azure-cli"></a>Använda Azure CLI
Använd `az eventgrid topic update` kommandot med `--identity` inställningen för att `systemassigned` Aktivera systemtilldelad identitet för ett befintligt ämne. Ange som värde om du vill inaktivera identiteten `noidentity` . 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Kommandot för att uppdatera en befintlig domän är liknande ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Mål som stöds och rollen rollbaserad åtkomst kontroll (RBAC)
När du har aktiverat identitet för ditt event Grid-ämne eller-domän skapar Azure automatiskt en identitet i Azure Active Directory (Azure AD). Lägg till den här identiteten i lämpliga RBAC-roller så att ämnet eller domänen kan vidarebefordra händelser till destinationer som stöds. Du kan till exempel lägga till identiteten i rollen **Azure Event Hubs data Sender** för ett Event Hubs-namnområde så att avsnittet Event Grid kan vidarebefordra händelser till händelse nav i det namn området.  

Azure Event Grid stöder för närvarande ämnen eller domäner som kon figurer ATS med systemtilldelad hanterad identitet för att vidarebefordra händelser till följande mål. I den här tabellen får du också de roller som identiteten ska vara i så att avsnittet kan vidarebefordra händelserna.

| Mål | RBAC-roll | 
| ----------- | --------- | 
| Service Bus köer och ämnen | [Azure Service Bus data avsändare](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| Händelsehubb | [Azure Event Hubs data avsändare](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Blob Storage | [Storage BLOB data-deltagare](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Queue Storage |[Avsändare av data meddelande i lagrings köer](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Lägg till identitet till RBAC-roller på destinationer
I det här avsnittet beskrivs hur du lägger till identiteten för ditt ämne eller en domän i en RBAC-roll. 

### <a name="using-azure-portal"></a>Använda Azure Portal
Du kan använda **Azure Portal** för att tilldela ämnet/domän identiteten till en lämplig roll så att avsnittet/domänen kan vidarebefordra händelser till målet. 

I följande exempel läggs en hanterad identitet för ett event Grid-ämne med namnet **msitesttopic** till rollen **Azure Service Bus data avsändare** för ett Service Bus **namn område** som innehåller en kö eller ämnes resurs. När du lägger till rollen på namn områdes nivån kan ämnet vidarebefordra händelser till alla entiteter med i namn området. 

1. Navigera till **Service Bus namn området** i [Azure Portal](https://portal.azure.com). 
2. Välj **Access Control** i det vänstra fönstret. 
3. Välj **Lägg till** i avsnittet **Lägg till en roll tilldelning** . 
4. Utför följande steg på sidan **Lägg till en roll tilldelning** :
    1. Välj rollen. I det här fallet är det **Azure Service Bus data avsändaren**. 
    2. Välj **identiteten** för ditt ämne eller din domän. 
    3. Välj **Spara** för att spara konfigurationen.

Stegen är liknande för att lägga till en identitet för andra roller som anges i tabellen. 

### <a name="using-azure-cli"></a>Använda Azure CLI
Exemplet i det här avsnittet visar hur du använder **Azure CLI** för att lägga till en identitet i en RBAC-roll. Exempel kommandona är för ämnen i Event Grid. Kommandona för events Grid-domäner liknar varandra. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Hämta ägar-ID för ämnets system identitet 
Börja med att hämta huvud-ID för ämnets systemhanterade identitet och tilldela identiteten till lämpliga roller.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Skapa en roll tilldelning för händelse nav i olika omfång 
Följande CLI-exempel visar hur du lägger till ett ämnes identitet till **Azure Event Hubs data Sender** -rollen på namn områdes nivån eller på händelsehubben. Om du skapar roll tilldelningen i namn området kan avsnittet vidarebefordra händelser till alla händelse nav i det namn området. Om du skapar på händelse Hub-nivån kan ämnet endast vidarebefordra händelser till just den aktuella händelsehubben. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Skapa en roll tilldelning för Service Bus ämnet i olika omfång 
Följande CLI-exempel visar hur du lägger till ett ämnes identitet i rollen **Azure Service Bus data avsändare** på namn områdes nivå eller på Service Bus ämnes nivå. Om du skapar roll tilldelningen i namn området kan händelse rutnäts avsnittet vidarebefordra händelser alla entiteter (Service Bus köer eller ämnen) inom det namn området. Om du skapar på Service Bus kö-eller ämnes nivå kan händelse rutnäts avsnittet vidarebefordra händelser enbart till den aktuella Service Bus kön eller ämnet. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Skapa händelse prenumerationer som använder identitet
När du har ett ämne eller en domän med en Systemhanterad identitet och lagt till identiteten i lämplig roll på målet, är du redo att skapa prenumerationer som använder identiteten. 

### <a name="using-azure-portal"></a>Använda Azure Portal
När du skapar en händelse prenumeration ser du ett alternativ för att aktivera användning av systemtilldelad identitet för en slut punkt i avsnittet **information om slut punkt** . 

![Aktivera identitet när en händelse prenumeration skapas för Service Bus kö](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Du kan också aktivera att använda systemtilldelad identitet som ska användas för obeställbara meddelanden på fliken **ytterligare funktioner** . 

![Aktivera systemtilldelad identitet för obeställbara meddelanden](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Använda Azure CLI – Service Bus kö 
I det här avsnittet får du lära dig hur du använder **Azure CLI** för att aktivera användning av systemtilldelad identitet för att leverera händelser till en Service Bus kö. Identiteten måste vara medlem i rollen **Azure Service Bus data avsändare** . Det måste också vara medlem i rollen **Storage BLOB data Contributor** på det lagrings konto som används för obeställbara meddelanden. 

#### <a name="define-variables"></a>Definiera variabler
Börja med att ange värden för följande variabler som ska användas i CLI-kommandot. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>skapa en händelse prenumeration med hanterad identitet för leverans 
Det här exempel kommandot skapar en händelse prenumeration för ett event Grid-ämne med en slut punkts typ som har angetts till **Service Bus kö**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>skapa en händelse prenumeration med hanterad identitet för leverans och obeställbara meddelanden
Det här exempel kommandot skapar en händelse prenumeration för ett event Grid-ämne med en slut punkts typ som har angetts till **Service Bus kö**. Den anger också att den systemhanterade identiteten ska användas för obeställbara meddelanden. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="azure-cli---event-hubs"></a>Azure CLI – Event Hubs 
I det här avsnittet får du lära dig hur du använder **Azure CLI** för att aktivera användning av systemtilldelad identitet för att leverera händelser till en Event Hub. Identiteten måste vara medlem i rollen **Azure Event Hubs data Sender** . Det måste också vara medlem i rollen **Storage BLOB data Contributor** på det lagrings konto som används för obeställbara meddelanden. 

#### <a name="define-variables"></a>Definiera variabler
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Skapa händelse prenumeration med hanterad identitet för leverans 
Det här exempel kommandot skapar en händelse prenumeration för ett event Grid-ämne med en slut punkts typ inställd på **Event Hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Skapa händelse prenumeration med hanterad identitet för leverans + obeställbara meddelanden kön 
Det här exempel kommandot skapar en händelse prenumeration för ett event Grid-ämne med en slut punkts typ inställd på **Event Hubs**. Den anger också att den systemhanterade identiteten ska användas för obeställbara meddelanden. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI – Azure Storage kö 
I det här avsnittet får du lära dig hur du använder **Azure CLI** för att aktivera användning av systemtilldelad identitet för att leverera händelser till en Azure Storage kö. Identiteten måste vara medlem i rollen **Storage BLOB data Contributor** på lagrings kontot.

#### <a name="define-variables"></a>Definiera variabler  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Skapa händelse prenumeration med hanterad identitet för leverans 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Skapa händelse prenumeration med hanterad identitet för leverans + obeställbara meddelanden kön 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>Nästa steg
Mer information om hanterade tjänst identiteter finns i [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). 