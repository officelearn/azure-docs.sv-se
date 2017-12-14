## <a name="test"></a>Testa funktionen

Använd cURL till att testa den distribuerade funktionen på en Mac- eller Linux-dator, eller Bash i Windows. Kör följande cURL-kommando och ersätt platshållaren `<app_name>` med namnet på din funktionsapp. Lägg till frågesträngen `&name=<yourname>` i webbadressen.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Funktionssvaret visas i en webbläsare.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Om du inte har cURL tillgängligt på kommandoraden anger du samma webbadress i webbläsarens adressfält. På samma sätt ersätter du platshållaren `<app_name>` med namnet på funktionsappen och lägger till frågesträngen `&name=<yourname>` i webbadressen. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Funktionssvaret visas i en webbläsare.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
