## Überprüfen von API-App-Metadaten

Die Metadaten, mit denen eine Web-API bereitgestellt werden, wie in eine API-app enthalten ist ein *apiapp.json* Datei und ein *Metadaten* Ordner.

![](./media/app-service-api-review-metadata/metadatainse.png)

Der standardmäßige Inhalt der der *apiapp.json* Datei wie im folgende Beispiel aussehen:

        {
            "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
            "id": "ContactsList",
            "namespace": "microsoft.com",
            "gateway": "2015-01-14",
            "version": "1.0.0",
            "title": "ContactsList",
            "summary": "",
            "author": "",
            "endpoints": {
                "apiDefinition": "/swagger/docs/v1",
                "status": null
            }
        }

Beachten Sie, dass die `apiDefinition` Endpunkt `/swagger/docs/v1`: standardmäßig API-app-Projekten der [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet-Paket automatisch [Swagger](http://swagger.io/) Metadatengenerierung. 

Im Rahmen dieses Lernprogramms können Sie die Standardwerte übernehmen. Die [-API-app-Metadaten](#api-app-metadata) Abschnitt weiter unten in diesem Lernprogramm wird erläutert, wie Sie diese Metadaten anpassen.


