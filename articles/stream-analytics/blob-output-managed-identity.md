---
title: Autentisera blob-utdata med Managed Identity Azure Stream Analytics
description: I den här artikeln beskrivs hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics-jobb till Azure Blob-lagringsutdata.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129968"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Använda hanterad identitet för att autentisera ditt Azure Stream Analytics-jobb till Azure Blob Storage-utdata

[Hanterad identitetsautentisering](../active-directory/managed-identities-azure-resources/overview.md) för utdata till Azure Blob-lagring ger Stream Analytics-jobb direkt åtkomst till ett lagringskonto i stället för att använda en anslutningssträng. Förutom förbättrad säkerhet kan du med den här funktionen också skriva data till ett lagringskonto i ett virtuellt nätverk (VNET) i Azure.

Den här artikeln visar hur du aktiverar hanterad identitet för Blob-utdata för ett Stream Analytics-jobb via Azure-portalen och via en Azure Resource Manager-distribution.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Skapa Stream Analytics-jobbet med Azure-portalen

1. Skapa ett nytt Stream Analytics-jobb eller öppna ett befintligt jobb i Azure-portalen. Välj **Hanterad identitet** som finns under **Konfigurera**på menyraden som finns till vänster på skärmen. Kontrollera att "Använd systemtilldelad hanterad identitet" är markerat och klicka sedan på knappen **Spara** längst ned på skärmen.

   ![Konfigurera Hanterad identitet för Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. I fönstret utdataegenskaper i Azure Blob storage output sink väljer du listrutan Autentiseringsläge och väljer **Hanterad identitet**. Information om andra utdataegenskaper finns i [Förstå utdata från Azure Stream Analytics](./stream-analytics-define-outputs.md). När du är klar klickar du på **Spara**.

   ![Konfigurera Azure Blob-lagringsutdata](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Nu när jobbet har skapats läser du [avsnittet Ge Stream Analytics-jobbet åtkomst till ditt lagringskonto](#give-the-stream-analytics-job-access-to-your-storage-account) i den här artikeln.

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-distribution

Med Hjälp av Azure Resource Manager kan du helt automatisera distributionen av ditt Stream Analytics-jobb. Du kan distribuera Resource Manager-mallar med antingen Azure PowerShell eller [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Nedanstående exempel använder Azure CLI.


1. Du kan skapa en **Microsoft.StreamAnalytics/streamingjobs-resurs** med en hanterad identitet genom att inkludera följande egenskap i resursavsnittet i Resource Manager-mallen:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Den här egenskapen talar om för Azure Resource Manager att skapa och hantera identiteten för ditt Stream Analytics-jobb. Nedan finns en exempelmall för Resource Manager som distribuerar ett Stream Analytics-jobb med aktiverad hanterad identitet och en Blob-utdatamottagare som använder hanterad identitet:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    Ovanstående jobb kan distribueras till **exempelgruppen Resursgrupp** med kommandot nedan Azure CLI:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. När jobbet har skapats kan du använda Azure Resource Manager för att hämta jobbets fullständiga definition.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Ovanstående kommando kommer att returnera ett svar som nedan:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Ta del av **principalId** från jobbets definition, som identifierar jobbets hanterade identitet i Azure Active Directory och kommer att användas i nästa steg för att ge Stream Analytics-jobbet åtkomst till lagringskontot.

3. Nu när jobbet har skapats läser du [avsnittet Ge Stream Analytics-jobbet åtkomst till ditt lagringskonto](#give-the-stream-analytics-job-access-to-your-storage-account) i den här artikeln.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Ge Stream Analytics-jobbet åtkomst till ditt lagringskonto

Det finns två åtkomstnivåer som du kan välja att ge ditt Stream Analytics-jobb:

1. **Åtkomst på behållarnivå:** Det här alternativet ger jobbåtkomst till en specifik befintlig behållare.
2. **Åtkomst på kontonivå:** Det här alternativet ger jobbet allmän åtkomst till lagringskontot, inklusive möjligheten att skapa nya behållare.

Om du inte behöver jobbet för att skapa behållare för din räkning, bör du välja åtkomst på **behållarnivå** eftersom det här alternativet ger jobbet den lägsta åtkomstnivå som krävs. Båda alternativen förklaras nedan för Azure-portalen och kommandoraden.

### <a name="grant-access-via-the-azure-portal"></a>Bevilja åtkomst via Azure-portalen

#### <a name="container-level-access"></a>Åtkomst på behållarnivå

1. Navigera till behållarens konfigurationsfönster i ditt lagringskonto.

2. Välj **Åtkomstkontroll (IAM)** till vänster.

3. Klicka på **Lägg till**under avsnittet "Lägg till en rolltilldelning".

4. I fönstret för rolltilldelning:

    1. Ange **rollen** till "Storage Blob Data Contributor"
    2. Kontrollera att **rullgardinsmenyn Tilldela åtkomst** är inställd på "Azure AD-användare, grupp eller tjänsthuvudnamn".
    3. Skriv namnet på Stream Analytics-jobbet i sökfältet.
    4. Välj ditt Stream Analytics-jobb och klicka på **Spara**.

   ![Bevilja åtkomst till behållare](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Åtkomst på kontonivå

1. Navigera till ditt lagringskonto.

2. Välj **Åtkomstkontroll (IAM)** till vänster.

3. Klicka på **Lägg till**under avsnittet "Lägg till en rolltilldelning".

4. I fönstret för rolltilldelning:

    1. Ange **rollen** till "Storage Blob Data Contributor"
    2. Kontrollera att **rullgardinsmenyn Tilldela åtkomst** är inställd på "Azure AD-användare, grupp eller tjänsthuvudnamn".
    3. Skriv namnet på Stream Analytics-jobbet i sökfältet.
    4. Välj ditt Stream Analytics-jobb och klicka på **Spara**.

   ![Åtkomst till bevilja konto](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Bevilja åtkomst via kommandoraden

#### <a name="container-level-access"></a>Åtkomst på behållarnivå

Om du vill ge åtkomst till en viss behållare kör du följande kommando med Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Åtkomst på kontonivå

Om du vill ge åtkomst till hela kontot kör du följande kommando med Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Aktivera VNET-åtkomst

När du konfigurerar ditt lagringskontos **brandväggar och virtuella nätverk**kan du eventuellt tillåta nätverkstrafik från andra betrodda Microsoft-tjänster. När Stream Analytics autentiserar med hanterad identitet ger den bevis på att begäran kommer från en betrodd tjänst. Nedan finns instruktioner för att aktivera detta VNET-åtkomstundantag.

1.  Navigera till fönstret Brandväggar och virtuella nätverk i konfigurationsfönstret för lagringskontot.
2.  Kontrollera att alternativet "Tillåt betrodda Microsoft-tjänster att komma åt det här lagringskontot" är aktiverat.
3.  Om du aktiverade den klickar du på **Spara**.

   ![Aktivera VNET-åtkomst](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Begränsningar
Nedan följer de aktuella begränsningarna för den här funktionen:

1. Klassiska Azure Storage-konton.

2. Azure-konton utan Azure Active Directory.

3. Åtkomst för flera innehavare stöds inte. Tjänsthuvudhuvudet som skapats för ett givet Stream Analytics-jobb måste finnas i samma Azure Active Directory-klientorganisation där jobbet skapades och kan inte användas med en resurs som finns i en annan Azure Active Directory-klientorganisation.

4. [Användartilldelad identitet](../active-directory/managed-identities-azure-resources/overview.md) stöds inte. Det innebär att användaren inte kan ange sitt eget tjänsthuvudnamn som ska användas av deras Stream Analytics-jobb. Tjänstens huvudnamn måste genereras av Azure Stream Analytics.

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Anpassad blob-utdatapartitionering i Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
