Om du använder en parameterfil för att ange parametervärden under distributionen, måste du skapa en JSON-fil med ett format som liknar följande exempel:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

Parametern-filens storlek kan inte vara mer än 64 KB.

Lägga till värdet i ett nyckelvalv om du behöver ange något känsligt värde för en parameter (till exempel ett lösenord). Hämta nyckelvalvet under distributionen som visas i föregående exempel. Mer information finns i [skicka säkra värden under distributionen av](../articles/azure-resource-manager/resource-manager-keyvault-parameter.md). 

