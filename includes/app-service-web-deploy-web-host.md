### App Service-Plan

Erstellt den Dienstplan zum Hosten der Web-App. Geben Sie den Namen des Plans über den **HostingPlanName** Parameter. Der Speicherort des Plans ist die 
derselbe Speicherort für die Web-app. Tarif Größe und die workergröße werden gemäß der **Sku** und **WorkerSize** Parameter

    {
      "apiVersion": "2014-06-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[parameters('siteLocation')]",
      "properties": {
        "name": "[parameters('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "numberOfWorkers": 1
      }
    },


