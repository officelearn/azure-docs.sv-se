## <a name="deploy-your-function-code"></a>Distribuera din funktionskod  

Det finns flera sätt att skapa funktionskoden i din nya funktionsapp. I det här ämnet ansluter vi till en exempellagringsplats i GitHub. Precis som tidigare ersätter du platshållaren `<app_name>` med namnet på den funktionsapp du skapade. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --branch master \
--repo-url https://github.com/Azure-Samples/functions-quickstart \
--manual-integration 
```
När distributionskälla har angetts, visar Azure CLI information liknar följande exempel (null-värden bort för att läsa):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "kind": null,
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```
