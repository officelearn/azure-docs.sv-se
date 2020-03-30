---
title: Använda hanterad identitet för att autentisera ditt Azure Stream Analytics-jobb till Power BI-utdata
description: I den här artikeln beskrivs hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics-jobb till Power BI-utdata.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 8a7dfd7c690d79d8430f7c33a25b38949dbd06c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086322"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Använda hanterad identitet för att autentisera ditt Azure Stream Analytics-jobb till Power BI

[Hanterad identitetsautentisering](../active-directory/managed-identities-azure-resources/overview.md) för utdata till Power BI ger Stream Analytics-jobb direkt åtkomst till en arbetsyta i ditt Power BI-konto. Den här funktionen gör att distributioner av Stream Analytics-jobb kan automatiseras helt, eftersom det inte längre krävs för en användare att interaktivt logga in på Power BI via Azure-portalen. Dessutom stöds tidskrävande jobb som skriver till Power BI nu bättre, eftersom du inte behöver godkänna jobbet regelbundet.

Den här artikeln visar hur du aktiverar hanterad identitet för Power BI-utdata för ett Stream Analytics-jobb via Azure-portalen och via en Azure Resource Manager-distribution.

## <a name="prerequisites"></a>Krav

Följande krävs för att använda den här funktionen:

- Ett Power BI-konto med en [Pro-licens](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).

- En uppgraderad arbetsyta i ditt Power BI-konto. Mer information finns i [Power BI:s tillkännagivande](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) av den här funktionen.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Skapa ett Stream Analytics-jobb med Azure-portalen

1. Skapa ett nytt Stream Analytics-jobb eller öppna ett befintligt jobb i Azure-portalen. Välj **Hanterad identitet** som finns under **Konfigurera**på menyraden som finns till vänster på skärmen. Kontrollera att "Använd systemtilldelad hanterad identitet" är markerat och välj sedan knappen **Spara** längst ned på skärmen.

   ![Konfigurera Hanterad identitet för Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Innan du konfigurerar utdata ger du Stream Analytics-jobbet åtkomst till din Power BI-arbetsyta genom att följa anvisningarna i [avsnittet Ge Stream Analytics-jobbet åtkomst till din Power BI-arbetsyta](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) i den här artikeln.

3. Navigera till avsnittet **Utdata** i streamalytens jobb, välj **+ Lägg till**och välj sedan Power **BI**. Välj sedan knappen **Auktorisera** och logga in med ditt Power BI-konto.

   ![Auktorisera med Power BI-konto](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. När du har auktoriserats fylls en listruta med alla arbetsytor som du har åtkomst till. Välj den arbetsyta som du godkände i föregående steg. Välj sedan **Hanterad identitet** som "Autentiseringsläge". Slutligen väljer du knappen **Spara.**

   ![Konfigurera Power BI-utdata med hanterad identitet](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-distribution

Med Azure Resource Manager kan du helt automatisera distributionen av ditt Stream Analytics-jobb. Du kan distribuera Resource Manager-mallar med antingen Azure PowerShell eller [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Nedanstående exempel använder Azure CLI.


1. Du kan skapa en **Microsoft.StreamAnalytics/streamingjobs-resurs** med en hanterad identitet genom att inkludera följande egenskap i resursavsnittet i Resource Manager-mallen:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Den här egenskapen talar om för Azure Resource Manager att skapa och hantera identiteten för ditt Stream Analytics-jobb. Nedan finns en exempelmall för Resource Manager som distribuerar ett Stream Analytics-jobb med aktiverad hanterad identitet och en Power BI-utflödesmottagare som använder hanterad identitet:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Distribuera jobbet ovan till **exempelgruppen Resursgrupp** med kommandot nedan Azure CLI:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. När jobbet har skapats använder du Azure Resource Manager för att hämta jobbets fullständiga definition.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Ovanstående kommando kommer att returnera ett svar som nedan:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Om du planerar att använda Power BI:s REST API för att lägga till Stream Analytics-jobbet på din Power BI-arbetsyta bör du anteckna den returnerade "principalId".

3. Nu när jobbet har skapats fortsätter du till [avsnittet Ge Stream Analytics-jobbet åtkomst till din Power BI-arbetsyta i den](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) här artikeln.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Ge Stream Analytics-jobbet åtkomst till din Power BI-arbetsyta

Nu när Stream Analytics-jobbet har skapats kan det få åtkomst till en Power BI-arbetsyta.

### <a name="use-the-power-bi-ui"></a>Använda Power BI-användargränssnittet

   > [!Note]
   > För att lägga till Stream Analytics-jobbet på din Power BI-arbetsyta med hjälp av användargränssnittet måste du också aktivera tjänstens huvudåtkomst i **utvecklarinställningarna** i Power BI-administratörsportalen. Se [Kom igång med ett tjänsthuvudnamn](https://docs.microsoft.com/power-bi/developer/embed-service-principal#get-started-with-a-service-principal) för mer information.

1. Navigera till arbetsytans åtkomstinställningar. Se den här artikeln för mer information: [Ge tillgång till din arbetsyta](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Skriv namnet på stream analytics-jobbet i textrutan och välj **Deltagare** som åtkomstnivå.

3. Välj **Lägg till** och stäng fönstret.

   ![Lägga till Stream Analytics-jobb i Power BI-arbetsytan](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Använda Power BI PowerShell-cmdlets

1. Installera Power `MicrosoftPowerBIMgmt` BI PowerShell-cmdlets.

   > [!Important]
   > Se till att du använder version 1.0.821 eller senare av cmdlets.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Logga in på Power BI.

```powershell
Login-PowerBI
```

3. Lägg till ditt Stream Analytics-jobb som deltagare på arbetsytan.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Använda Power BI REST API

Stream Analytics-jobbet kan också läggas till som deltagare på arbetsytan med hjälp av REST-API:et "Lägg till gruppanvändare" direkt. Fullständig dokumentation för det här API:et finns här: [Grupper - Lägg till gruppanvändare](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Exempelförfrågan**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Begärandetext
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Begränsningar
Nedan finns begränsningarna i den här funktionen:

- Klassiska Power BI-arbetsytor stöds inte.

- Azure-konton utan Azure Active Directory.

- Åtkomst för flera innehavare stöds inte. Tjänsthuvudhuvudet som skapats för ett givet Stream Analytics-jobb måste finnas i samma Azure Active Directory-klientorganisation där jobbet skapades och kan inte användas med en resurs som finns i en annan Azure Active Directory-klientorganisation.

- [Användartilldelad identitet](../active-directory/managed-identities-azure-resources/overview.md) stöds inte. Det innebär att du inte kan ange ditt eget tjänsthuvudnamn som ska användas av deras Stream Analytics-jobb. Tjänstens huvudnamn måste genereras av Azure Stream Analytics.

## <a name="next-steps"></a>Nästa steg

* [Power BI-instrumentpanelsintegrering med Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Förstå utdata från Azure Stream Analytics](./stream-analytics-define-outputs.md)
