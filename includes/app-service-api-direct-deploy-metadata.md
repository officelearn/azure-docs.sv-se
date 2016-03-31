## API-App-Metadaten

In diesem Abschnitt werden zusätzliche Informationen zu API-App-Metadaten bereitgestellt, die Sie anpassen können.

Die meisten Eigenschaften in der *apiapp.json* -Datei und die Dateien in den *Metadaten* Ordner, wirkt wie ein API-app-Pakets wird angezeigt, in Azure Marketplace. In den folgenden Abschnitten wird erläutert, welche Eigenschaften und Dateien sich auf API-Apps auswirken, wenn Sie Ihren Code in einer API-App für Ihr Azure-Abonnement bereitstellen. 

### API-App-ID 

Die `id`-Eigenschaft legt den Namen der API-App fest.  Beispiel:

        "id": "ContactsList",

![](./media/app-service-api-direct-deploy-metadata/apiappname.png)

### Namespace

Legen Sie die `namespace`-Eigenschaft auf die Domäne Ihres Azure Active Directory-Mandanten fest. Um Ihre Domäne zu suchen, öffnen Sie in Ihrem Browser die [klassischen Azure-Portal](https://manage.windowsazure.com/), Durchsuchen **Active Directory**, und wählen Sie die **Domänen** Registerkarte. Beispiel:

        "namespace": "contoso.onmicrosoft.com",

### Dynamische Swagger-API-Definition

Wenn die API-app eine dynamische zurückgeben kann [Swagger](http://swagger.io/) -API-Definition, speichern Sie die relative URL für eine GET-Anforderung, die die API-Definition JSON zurückgibt in der `endpoints.apiDefinition` Eigenschaft. Beispiel:  

        "endpoints": {
            "apiDefinition": "/swagger/docs/v1"
        }

> **Hinweis:** wenn Sie Swashbuckle verwenden, um eine Swagger-API-Definition zu generieren, HTTP-methodenüberladungen in Ihren Web-API-Controllern dazu führen, dass doppelte Vorgangs-Ids. Weitere Informationen finden Sie unter [Anpassen von mit Swashbuckle generierten Vorgangs-IDs](../article/app-service-api/app-service-api-dotnet-swashbuckle-customize.md).
  
### Statische Swagger-API-Definition

Angeben einen statischen [Swagger](http://swagger.io/) 2.0-API-Definition, speichern Sie die Datei in die *Metadaten* Ordner und benennen Sie die Datei *apiDefinition.swagger.json*

![](./media/app-service-api-direct-deploy-metadata/apidefinmetadata.png)

Lassen Sie `endpoints.apiDefinition` aus der *apiapp.json* Datei oder seinen Wert auf null festgelegt. Wenn Sie beide enthalten eine `endpoints.apiDefinition` URL und ein *apiDefinition.swagger.json* -Datei, die URL Vorrang, und die Datei wird ignoriert.


