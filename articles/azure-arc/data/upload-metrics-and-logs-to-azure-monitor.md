---
title: Överför resurs inventering, användnings data, mått och loggar till Azure Monitor
description: Överför resurs inventering, användnings data, mått och loggar till Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90941556"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Överför resurs inventering, användnings data, mått och loggar till Azure Monitor

Med Azure Arc Data Services kan du *överföra dina* mått och loggar till Azure Monitor så att du kan samla in och analysera mått, loggar, generera aviseringar, skicka meddelanden eller utlösa automatiserade åtgärder. Genom att skicka dina data till Azure Monitor kan du också lagra övervakning och logga data från en webbplats och i stor skala som möjliggör långsiktig lagring av data för avancerad analys.  Om du har flera platser som har Azure Arc Data Services kan du använda Azure Monitor som en central plats för att samla in alla dina loggar och mått på dina webbplatser.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Innan du börjar

Det finns några konfigurations steg som krävs för att göra det möjligt att ladda upp loggar och mått:

1) Skapa ett huvud namn för tjänsten/Azure Active Directory, inklusive att skapa en klient åtkomst hemlighet och tilldela tjänstens huvud namn till rollen övervakning av mått utfärdare för den eller de prenumerationer som databas instans resurserna finns i.
2) Skapa en Log Analytics-arbetsyta och hämta nycklarna och ange informationen i miljövariabler.

Det första objektet krävs för att ladda upp mått och det andra krävs för att ladda upp loggar.

Följ dessa kommandon för att skapa måtten för att ladda upp tjänstens huvud namn och tilldela dem till rollerna "övervakning av mått för utgivare" och "deltagar", så att tjänstens huvud namn kan överföra mått och utföra åtgärder för att skapa och ladda upp.

## <a name="create-service-principal-and-assign-roles"></a>Skapa tjänstens huvud namn och tilldela roller

Följ dessa kommandon för att skapa mått för att ladda upp tjänstens huvud namn och tilldela det till rollen övervakning av mått utfärdare:

Kör det här kommandot om du vill skapa ett huvud namn för tjänsten:

> [!NOTE]
> Att skapa ett huvud namn [för tjänsten kräver vissa behörigheter i Azure](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Exempel på utdata:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Spara appId-och klient värden i en miljö variabel för senare användning:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Kör det här kommandot för att tilldela tjänstens huvud namn till rollen "övervaknings mått Publisher" i prenumerationen där dina databas instans resurser finns:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Exempel på utdata:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Skapa en Log Analytics-arbetsyta

Kör sedan dessa kommandon för att skapa en Log Analytics arbets yta och ange åtkomst information till miljövariabler.

> [!NOTE]
> Hoppa över det här steget om du redan har en arbets yta.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Exempel på utdata:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
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

Om du vill överföra mått för dina Azure SQL-hanterade instanser och Azure Database for PostgreSQL storskaliga Server grupper kör du följande CLI-kommandon:

Med den här åtgärden exporteras alla mått till den angivna filen:

```console
azdata arc dc export -t metrics --path metrics.json
```

Detta laddar upp mått till Azure Monitor:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>Visa måtten i portalen

När dina mått har laddats upp bör du kunna visualisera dem från Azure-portalen.

Om du vill visa dina mått i portalen använder du den här länken för att öppna portalen: <https://portal.azure.com> Sök sedan efter din databas instans efter namn i Sök fältet:

Du kan visa processor användningen på sidan Översikt eller om du vill ha mer detaljerade mått som du kan klicka på mått från den vänstra navigerings panelen

Välj SQL Server som mått namn område:

Välj det mått som du vill visualisera (du kan också välja flera):

Ändra frekvensen till de senaste 30 minuterna:

> [!NOTE]
> Du kan bara ladda upp måtten för de senaste 30 minuterna. Azure Monitor avvisar mått som är äldre än 30 minuter.

## <a name="upload-logs-to-azure-monitor"></a>Ladda upp loggar till Azure Monitor

 Om du vill ladda upp loggar för dina Azure SQL-hanterade instanser och Azure Database for PostgreSQL storskaliga Server grupper kör du följande CLI-kommandon –

Med den här åtgärden exporteras alla loggar till den angivna filen:

```console
azdata arc dc export -t logs --path logs.json
```

Loggar överförs till en arbets yta i Azure Monitor Log Analytics:

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Visa dina loggar i Azure Portal

När loggarna har laddats upp kan du köra fråga på dem med hjälp av loggfrågeutforskaren:

1. Öppna Azure Portal och Sök sedan efter din arbets yta efter namn i Sök fältet längst upp och välj sedan den
2. Klicka på Loggar i den vänstra panelen
3. Klicka på kom igång (eller klicka på länkarna på sidan Komma igång om du vill veta mer om att Log Analytics om du är nybörjare)
4. Följ själv studie kursen för att lära dig mer om Log Analytics om du är första gången
5. Expandera Anpassade loggar längst ned i listan över tabeller så visas en tabell med namnet sql_instance_logs_CL.
6. Klicka på ögonikonen bredvid tabellnamnet
7. Klicka på knappen Visa i frågeredigeraren
8. Nu har du en fråga i frågeredigeraren som visar de senaste 10 händelserna i loggen
9. Härifrån kan du experimentera med att köra frågor mot loggarna med frågeredigeraren, ange aviseringar med mera.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Automatisera mått och loggar uppladdningar (valfritt)

Om du kontinuerligt vill överföra mått och loggar kan du skapa ett skript och köra det på en timer med några minuters mellanrum.  Nedan visas ett exempel på hur du automatiserar uppladdningar med hjälp av ett Linux-gränssnitts skript.

I din favorit text/kod redigerare lägger du till följande i skript innehållet i filen och sparar som en körbar skript fil, till exempel. sh (Linux/Mac) eller. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Skapa skript filens körbara fil

```console
chmod +x myuploadscript.sh
```

Kör skriptet var 2: e minut:

```console
watch -n 120 ./myuploadscript.sh
```

Du kan också använda en jobbschemaläggaren som cron eller Schemaläggaren för Windows eller en Orchestrator som Ansible, Puppet eller chef.
