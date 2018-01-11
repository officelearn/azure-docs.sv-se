## <a name="deploy-uploaded-zip-file"></a>Distribuera uppladdad ZIP-fil

I Cloud Shell distribuerar du den uppladdade ZIP-filen till din webbapp med hjälp av kommandot [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Kom ihåg att ersätta  *\<app_name>* med namnet på din webbapp.

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/myAppFiles.zip
```

Det här kommandot distribuerar filer och kataloger från ZIP-filen till standardprogrammappen för App Service (`\home\site\wwwroot`) och startar om appen. Om någon ytterligare, anpassad versionsprocessen har konfigurerats så körs den också.
