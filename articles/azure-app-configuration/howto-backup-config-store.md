---
title: Säkerhetskopiera nyckel värden automatiskt från Azure App konfigurations lager
description: Lär dig hur du konfigurerar en automatisk säkerhets kopiering av nyckel värden mellan konfigurations lager för appar
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 0b057172c822f893e602d60f77a285f0867cf368
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87367783"
---
# <a name="backup-app-configuration-stores-automatically"></a>Säkerhetskopiera program konfigurations Arkiv automatiskt

I den här artikeln får du lära dig hur du konfigurerar en automatisk säkerhets kopiering av nyckel värden från ett primärt program konfigurations lager till en sekundär lagrings plats. Den använder integrering av Azure Event Grid med app-konfiguration. När konfigurationen är aktive rad kommer app-konfigurationen att publicera händelser till Event Grid för alla ändringar som görs i nyckel värden i ett konfigurations lager. Event Grid stöder en mängd olika Azure-tjänster där användare kan prenumerera på de händelser som genereras när nyckel värden skapas, uppdateras eller tas bort.

## <a name="overview"></a>Översikt

I den här självstudien kommer du att använda en Azure Storage kö för att ta emot händelser från Event Grid och använda en timer-utlösare av Azure Functions för att bearbeta händelser i lagrings kön i batchar. När den utlöses baserat på händelser hämtar funktionen de senaste värdena för de nycklar som har ändrats från konfigurations arkivet för den primära appen och uppdaterar den sekundära lagrings platsen. Med den här inställningen kan du kombinera flera ändringar under en kort period i en säkerhets kopierings åtgärd och undvika alltför stora begär Anden som görs till dina konfigurations lager för appar.

![App Configuration Store säkerhets kopierings arkitektur](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Resursetablering

Motivation bakom att säkerhetskopiera konfigurations lager för appar är att använda flera konfigurations lager i olika Azure-regioner för att öka geo-återhämtningen för ditt program. För att uppnå detta bör dina primära och sekundära lager finnas i olika Azure-regioner. Alla andra resurser som skapas i den här självstudien kan tillhandahållas i valfri region. Detta beror på att om den primära regionen är nere, kommer det inte att finnas något nytt att säkerhetskopiera förrän den primära regionen är tillgänglig igen.

I den här självstudien kommer du att skapa en sekundär butik i `centralus` region och alla andra resurser i `westus` regionen.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/). Du kan också använda Azure Cloud Shell.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med arbets belastningen Azure Development.
- Ladda ned och installera [.net Core SDK](https://dotnet.microsoft.com/download).
- Senaste versionen av Azure CLI (2.3.1 eller senare). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du använder Azure CLI måste du först logga in med `az login` . Du kan också använda Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resursgruppen är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group).

I följande exempel skapas en resursgrupp med namnet `<resource_group_name>` på platsen `westus`.  Ersätt `<resource_group_name>` med ett unikt namn för din resursgrupp.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Skapa konfigurations lager för appar

Skapa dina primära och sekundära konfigurations lager för appar i olika regioner.
Ersätt  `<primary_appconfig_name>` och `<secondary_appconfig_name>` med unika namn för dina konfigurations lager. Butiks namnet måste vara unikt eftersom det används som ett DNS-namn.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>Skapa Azure Storage kö

Skapa ett lagrings konto och en lagrings kö för att ta emot de händelser som publiceras av Event Grid.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Prenumerera på dina program konfigurations lagrings händelser

Du prenumererar på dessa två händelser från den primära appens konfigurations Arkiv:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Följande kommando skapar en Event Grid-prenumeration för de två händelser som skickas till lagrings kön, där slut punkts typen har angetts till `storagequeue` och slut punkten anges till kön-ID. Ersätt `<event_subscription_name>` med namnet på ditt val för händelse prenumerationen.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Skapa Azure Functions för hantering av händelser från lagrings kön

### <a name="setup-with-ready-to-use-azure-functions"></a>Konfigurera med Azure Functions som är redo att användas

I den här självstudien kommer du att arbeta med C#-Azure Functions med följande egenskaper:
- Körnings stack .NET Core 3,1
- Azure Functions körnings version 3. x
- Funktionen utlöses av en timer var tionde minut

För att göra det enklare för dig att börja säkerhetskopiera dina data har vi testat och publicerat [Azure Functions](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) som du kan använda utan att göra några ändringar i koden. Ladda ned projektfilerna och [publicera den till din egen Azure-Funktionsapp från Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> Gör inga ändringar i miljövariablerna i den kod som du har laddat ned. Du kommer att skapa de inställningar för appar som krävs i nästa avsnitt.
>

### <a name="build-your-own-azure-functions"></a>Bygg din egen Azure Functions

Om den exempel kod som anges ovan inte uppfyller dina krav kan du också skapa en egen Azure Functions. Funktionen måste kunna utföra följande aktiviteter för att kunna slutföra säkerhets kopieringen:
- Läs innehållet i lagrings kön med jämna mellanrum för att se om den innehåller några meddelanden från Event Grid. Se SDK för [lagrings kön](/azure/storage/queues/storage-quickstart-queues-dotnet) för implementerings information.
- Om din lagrings kö innehåller [händelse meddelanden från Event Grid](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema)extraherar du alla unika <-nycklar, etiketter> från händelse meddelanden. Kombinationen av nyckel och etikett är den unika identifieraren för nyckel värdes ändringar i primärt lager.
- Läs alla inställningar från den primära lagrings platsen. Uppdatera endast de inställningar i sekundär lager som har en motsvarande händelse i lagrings kön. Ta bort alla inställningar från den sekundära lagrings platsen som fanns i lagringspoolen men inte i den primära lagringen. Du kan använda [app Configuration SDK](https://github.com/Azure/AppConfiguration#sdks) för att få åtkomst till dina konfigurations lager program mässigt.
- Ta bort meddelanden från lagrings kön om det inte fanns några undantag under bearbetningen.
- Se till att implementera fel hantering enligt dina behov. Du kan referera till kod exemplet ovan för att se några vanliga undantag som du kanske vill hantera.

Mer information om hur du skapar Azure Functions finns i: [skapa en funktion i Azure som utlöses av en timer](/azure/azure-functions/functions-create-scheduled-function) och [utveckla Azure Functions med Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Använd din bästa bedömning för att välja det tidsinställda schemat utifrån hur ofta du gör ändringar i din primära config-lagringsplats. Kom ihåg att köra funktionen för ofta kan leda till begränsnings begär Anden för butiken.
>


## <a name="create-azure-function-app-settings"></a>Skapa inställningar för Azure-Funktionsapp

Om du använder den Azure Functions vi har tillhandahållit, behöver du följande appinställningar i Azure-Funktionsapp:
- `PrimaryStoreEndpoint`: Slut punkt för konfigurations arkivet för den primära appen. Till exempel `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`: Slut punkt för konfigurations arkivet för den sekundära appen. Till exempel `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`: URI för lagrings kön. Till exempel `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

Följande kommando skapar de nödvändiga inställningarna för appen i Azure-Funktionsapp. Ersätt `<function_app_name>` med namnet på din Azure-Funktionsapp.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Bevilja åtkomst till den hanterade identiteten för Azure Funktionsapp

Använd följande kommando eller [Azure Portal](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) för att lägga till en systemtilldelad hanterad identitet för Azure-Funktionsapp.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> För att utföra den nödvändiga resurs skapande-och roll hanteringen måste ditt konto ha `'Owner'` behörighet i rätt omfång (din prenumeration eller resurs grupp). Om du behöver hjälp med roll tilldelning kan du läsa mer om [hur du lägger till eller tar bort roll tilldelningar i Azure med hjälp av Azure Portal](/azure/role-based-access-control/role-assignments-portal).

Använd följande kommandon eller [Azure Portal](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) för att ge den hanterade identiteten för din Azure Funktionsapp åtkomst till dina konfigurations lager för appar.
- Tilldela `App Configuration Data Reader` roll i konfigurations arkivet för den primära appen.
- Tilldela `App Configuration Data Owner` roll i konfigurations arkivet för den sekundära appen.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Använd följande kommando eller [Azure Portal](/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal) för att ge den hanterade identiteten för Azure Funktionsapp åtkomst till lagrings kön. 
- Tilldela `Storage Queue Data Contributor` roll i lagrings kön.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Utlös en konfigurations händelse för appen

För att testa att allt fungerar kan du skapa/uppdatera/ta bort ett nyckel värde från den primära lagringen. Du bör automatiskt se den här ändringen i det sekundära arkivet några sekunder efter att Azure Functions har utlösts av timern.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Du har utlöst händelsen och en liten stund Event Grid att skicka händelse meddelandet till din Azure Storage-kö. ***Efter nästa schemalagda körning av Azure Functions***kan du Visa konfigurations inställningarna i den sekundära lagrings platsen för att se om det innehåller det uppdaterade nyckelvärdet från den primära lagringen.

> [!NOTE]
> Du kan [utlösa Azure Functions manuellt](/azure/azure-functions/functions-manually-run-non-http) under testningen och fel sökningen utan att vänta på den schemalagda timer-utlösaren.

När du har kontrollerat att säkerhets kopierings funktionen har körts kan du se att nyckeln nu finns i ditt sekundära arkiv.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Felsökning

Om du inte ser den nya inställningen i det sekundära arkivet:

- Se till att säkerhets kopierings funktionen utlöstes ***efter*** att du har skapat inställningen i ditt primära arkiv.
- Det är möjligt att Event Grid inte kunde skicka händelse aviseringen till lagrings kön i tid. Kontrol lera att lagrings kön fortfarande innehåller händelse meddelandet från det primära arkivet, och om den gör det utlöses säkerhets kopierings funktionen igen.
- Kontrol lera [Azure Functions loggar](/azure/azure-functions/functions-create-scheduled-function#test-the-function) för fel eller varningar.
- Använd [Azure Portal](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) för att säkerställa att Azure-Funktionsapp innehåller korrekta värden för de program inställningar som din Azure Functions försöker läsa.
- Du kan också konfigurera övervakning och aviseringar för dina Azure Functions med hjälp av [Azure Application insikter.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här program konfigurationen och händelse prenumerationen ska du inte rensa resurserna som du skapade i den här artikeln. Om du inte planerar att fortsätta kan du använda kommandona nedan för att ta bort alla resurser som har skapats i den här artikeln.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du konfigurerar automatisk säkerhets kopiering av dina nyckel värden kan du läsa mer om hur du kan öka geo-återhämtningen för ditt program:

- [Återhämtning och haveriberedskap](concept-disaster-recovery.md)
