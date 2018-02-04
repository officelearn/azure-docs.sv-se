## <a name="rest"></a>Distribuera med hjälp av REST API: er 
 
Du kan använda den [deployment service REST API: er](https://github.com/projectkudu/kudu/wiki/REST-API) distribuera .zip-filen till din app i Azure. Skicka bara en POST-begäran till https://<app_name>.scm.azurewebsites.net/api/zipdeploy. POST-begäran måste innehålla ZIP-filen i meddelandetexten. Autentiseringsuppgifter för distribution för din app tillhandahålls i begäran med hjälp av grundläggande autentisering. Mer information finns i [.zip push distribution referens](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

I följande exempel använder cURL-verktyget för att skicka en förfrågan som innehåller ZIP-filen. Du kan köra cURL från terminalen på en Mac- eller Linux-dator eller genom att använda Bash i Windows. Ersätt den `<zip_file_path>` med sökvägen till platsen för ditt projekt ZIP-fil. Även ersätta `<app_name>` med det unika namnet för din app.

Ersätt den `<deployment_user>` med användarnamnet för dina autentiseringsuppgifter för distribution. När du uppmanas av cURL, Skriv in lösenordet. Information om hur du ställer in autentiseringsuppgifter för distribution för din app finns [ange och återställa användarnivå autentiseringsuppgifter](../articles/app-service/app-service-deployment-credentials.md#userscope).   

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Denna begäran utlöser push-distribution från den överförda ZIP-filen. Du kan granska aktuella och tidigare distributioner med hjälp av https://<app_name>.scm.azurewebsites.net/api/deployments-slutpunkt som visas i exemplet nedan cURL. Igen och Ersätt `<app_name>` med namnet på din app och `<deployment_user>` med användarnamnet för dina autentiseringsuppgifter för distribution.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```