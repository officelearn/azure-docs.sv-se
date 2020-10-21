---
title: Ladda upp användnings data, mått och loggar till Azure Monitor
description: Överför resurs inventering, användnings data, mått och loggar till Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 869bfcb87aa4846674db233c4268e9269929cd04
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320170"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Ladda upp användnings data, mått och loggar till Azure Monitor

Med jämna mellanrum kan du exportera användnings information för fakturerings syfte, övervaka mått och loggar och sedan ladda upp den till Azure.  Export och uppladdning av någon av dessa tre typer av data kommer också att skapa och uppdatera datakontrollanten, SQL-hanterad instans och PostgreSQL skalnings resurser för Server grupper i Azure.

> [!NOTE] 
> Under för hands versions perioden kostar det inget att använda Azure Arc-aktiverade data tjänster.

## <a name="prerequisites"></a>Förutsättningar

Du behöver Azure CLI (AZ) och [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] installerat.  [Installera verktyg](./install-client-tools.md).

Innan du laddar upp data till Azure måste du se till att Azure-prenumerationen har Microsoft. AzureData-Resurshanterarens resurs leverantör registrerad.

Du kan kontrol lera detta genom att köra följande kommando:

```console
az provider show -n Microsoft.AzureData -o table
```

Om resurs leverantören för närvarande inte är registrerad i din prenumeration kan du registrera den genom att köra följande kommando.  Det här kommandot gör en minut eller två att slutföra.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>Ladda upp användnings data

Användnings information som inventering och Resursanvändning kan laddas upp till Azure på följande två sätt:

1. Exportera användnings data med hjälp av `azdata export` kommandot enligt följande:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   Det här kommandot skapar en `usage.json` fil med alla Azure Arc-aktiverade data resurser, till exempel SQL-hanterade instanser och postgresql storskaliga instanser osv. som skapas på data styrenheten.

2. Överför användnings data med `azdata upload` kommandot

   > [!NOTE]
   > Vänta minst 24 timmar efter att du har skapat data styrenheten för Azure-bågen innan du kör överföringen

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Ladda upp mått och loggar

Med Azure Arc Data Services kan du överföra dina mått och loggar till Azure Monitor så att du kan samla in och analysera mått, loggar, generera aviseringar, skicka meddelanden eller utlösa automatiserade åtgärder. 

Genom att skicka dina data till Azure Monitor kan du också lagra övervakning och logga data från andra platser och på enorma skala för långsiktig lagring av data för avancerad analys.

Om du har flera webbplatser som har Azure Arc Data Services kan du använda Azure Monitor som en central plats för att samla in alla dina loggar och mått på dina webbplatser.

### <a name="before-you-begin"></a>Innan du börjar

Det finns några konfigurations steg som krävs för att göra det möjligt att ladda upp loggar och mått:

1. Skapa ett huvud namn för tjänsten/Azure Active Directory, inklusive att skapa en klient åtkomst hemlighet och tilldela tjänstens huvud namn till rollen övervakning av mått utfärdare för den eller de prenumerationer som databas instans resurserna finns i.
2. Skapa en Log Analytics-arbetsyta och hämta nycklarna och ange informationen i miljövariabler.

Det första objektet krävs för att ladda upp mått och det andra krävs för att ladda upp loggar.

Följ dessa kommandon för att skapa måtten för att ladda upp tjänstens huvud namn och tilldela dem till rollerna "övervakning av mått för utgivare" och "deltagar", så att tjänstens huvud namn kan överföra mått och utföra åtgärder för att skapa och ladda upp.

## <a name="create-service-principal-and-assign-roles"></a>Skapa tjänstens huvud namn och tilldela roller

Följ dessa kommandon för att skapa mått för att ladda upp tjänstens huvud namn och tilldela det till rollen övervakning av mått utfärdare:

Kör det här kommandot om du vill skapa ett huvud namn för tjänsten:

> [!NOTE]
> Att skapa ett huvud namn [för tjänsten kräver vissa behörigheter i Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Exempel på utdata:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Spara appId-och klient värden i en miljö variabel för senare användning. 

Följ det här exemplet för att spara appId-och klient värden med PowerShell:

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

I Linux eller macOS kan du också spara appId-och klient värden med det här exemplet:

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Kör det här kommandot för att tilldela tjänstens huvud namn till rollen "övervaknings mått Publisher" i prenumerationen där dina databas instans resurser finns:


> [!NOTE]
> Du måste använda dubbla citat tecken för roll namn när du kör från en Windows-miljö.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Exempel på utdata:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Kör sedan dessa kommandon för att skapa en Log Analytics arbets yta och ange åtkomst information till miljövariabler.

> [!NOTE]
> Hoppa över det här steget om du redan har en arbets yta.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Exempel på utdata:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Tilldela variabler för ID och delad nyckel till miljövariabler

Spara customerId (arbetsyte-ID) som en miljö variabel som ska användas senare:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Det här kommandot skriver ut de åtkomst nycklar som krävs för att ansluta till din Log Analytics-arbetsyta:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Exempel på utdata:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Spara primär nyckeln i en miljö variabel som ska användas senare:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Ange de slutgiltiga miljövariablerna och bekräfta

Ange URL: en för SPN-auktoritet i en miljö variabel:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Kontrol lera att alla miljövariabler som krävs är inställda om du vill:

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Ladda upp mått till Azure Monitor

Om du vill överföra mått för Azure Arc-aktiverade SQL-hanterade instanser och Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper kör du följande CLI-kommandon:

1. Exportera alla mått till den angivna filen:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Ladda upp mått till Azure Monitor:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Vänta minst 30 minuter efter att de Azure Arc-aktiverade data instanserna har skapats för den första överföringen
   >
   >Se till att `upload` måtten omedelbart efter `export` Azure Monitor bara accepterar mått under de senaste 30 minuterna. [Läs mer](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Om du ser några fel som indikerar att "det inte går att hämta mått" under exporten, kontrollerar du om data insamling har angetts till ```true``` genom att köra följande kommando:

```console
azdata arc dc config show
```

och titta under avsnittet "säkerhet"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Kontrol lera om- `allowNodeMetricsCollection` och- `allowPodMetricsCollection` egenskaperna har angetts till `true` .

## <a name="view-the-metrics-in-the-portal"></a>Visa måtten på portalen

När dina mått har överförts kan du visa dem från Azure Portal.
> [!NOTE]
> Observera att det kan ta några minuter innan de överförda data bearbetas innan du kan visa måtten i portalen.


Om du vill visa dina mått i portalen använder du den här länken för att öppna portalen: <https://portal.azure.com> Sök sedan efter din databas instans efter namn i Sök fältet:

Du kan visa processor användningen på sidan Översikt eller om du vill ha mer detaljerade mått som du kan klicka på mått från den vänstra navigerings panelen

Välj SQL Server som mått namn område:

Välj det mått som du vill visualisera (du kan också välja flera):

Ändra frekvensen till de senaste 30 minuterna:

> [!NOTE]
> Du kan bara ladda upp måtten för de senaste 30 minuterna. Azure Monitor avvisar mått som är äldre än 30 minuter.

## <a name="upload-logs-to-azure-monitor"></a>Ladda upp loggar till Azure Monitor

 Om du vill ladda upp loggar för Azure Arc-aktiverade SQL-hanterade instanser och AzureArc-aktiverade PostgreSQL för storskaliga Server grupper kör du följande CLI-kommandon –

1. Exportera alla loggar till den angivna filen:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Ladda upp loggar till en Azure Monitor Log Analytics-arbetsyta:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Visa dina loggar i Azure Portal

När loggarna har laddats upp kan du köra fråga på dem med hjälp av loggfrågeutforskaren:

1. Öppna Azure Portal och Sök sedan efter din arbets yta efter namn i Sök fältet längst upp och välj sedan den
2. Klicka på Loggar i den vänstra panelen
3. Klicka på kom igång (eller klicka på länkarna på sidan Komma igång om du vill veta mer om att Log Analytics om du är nybörjare)
4. Följ själv studie kursen för att lära dig mer om Log Analytics om det är första gången du använder Log Analytics
5. Expandera Anpassade loggar längst ned i listan över tabeller så visas en tabell med namnet sql_instance_logs_CL.
6. Klicka på ögonikonen bredvid tabellnamnet
7. Klicka på knappen Visa i frågeredigeraren
8. Nu har du en fråga i Frågeredigeraren som visar de senaste 10 händelserna i loggen
9. Härifrån kan du experimentera med att köra frågor mot loggarna med frågeredigeraren, ange aviseringar med mera.

## <a name="automating-uploads-optional"></a>Automatisera uppladdningar (valfritt)

Om du vill överföra mått och loggar regelbundet kan du skapa ett skript och köra det på en timer med några minuters mellanrum. Nedan visas ett exempel på hur du automatiserar uppladdningar med hjälp av ett Linux-gränssnitts skript.

I din favorit text/kod redigerare lägger du till följande skript i filen och sparar som en körbar skript fil, till exempel. sh (Linux/Mac) eller. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Skapa skript filens körbara fil

```console
chmod +x myuploadscript.sh
```

Kör skriptet var 20: e minut:

```console
watch -n 1200 ./myuploadscript.sh
```

Du kan också använda en jobbschemaläggaren som cron eller Schemaläggaren för Windows eller en Orchestrator som Ansible, Puppet eller chef.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Allmänna rikt linjer för att exportera och ladda upp användning, mått

Åtgärder för att skapa, läsa, uppdatera och ta bort (CRUD) i Azure Arc-aktiverade data tjänster loggas för fakturerings-och övervaknings syfte. Det finns bakgrunds tjänster som övervakar för dessa CRUD-åtgärder och beräknar förbrukningen på lämpligt sätt. Den faktiska beräkningen av användning eller förbrukning sker enligt schema och görs i bakgrunden. 

Under för hands versionen sker den här processen natt. Den allmänna vägledningen är att bara överföra användningen en gång per dag. När användnings informationen exporteras och överförs flera gånger under samma 24-timmarsperiod, uppdateras bara resurs lagret i Azure Portal, men inte resursanvändningen.

För att ladda upp mått accepterar Azure Monitor bara de senaste 30 minuterna data ([Läs mer](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). Rikt linjerna för att ladda upp mått är att överföra måtten direkt efter att du har skapat export filen så att du kan visa hela data uppsättningen i Azure Portal. Om du till exempel exporterade måtten till 2:00 PM och körde kommandot upload på 2:50 PM. Eftersom Azure Monitor bara accepterar data under de senaste 30 minuterna, kanske du inte ser några data i portalen. 

## <a name="next-steps"></a>Nästa steg

[Ladda upp fakturerings data till Azure och visa dem i Azure Portal](view-billing-data-in-azure.md)

[Visa resursen Azure Arc data Controller i Azure Portal](view-data-controller-in-azure-portal.md)