### <a name="app-service-plan"></a>App Service-plan
Skapar service-plan som värd för webbprogrammet. Du anger namnet på planen via den **hostingPlanName** parameter. Planen finns på samma plats som används för resursgruppen. Prisnivå nivå och worker storlek har angetts i den **sku** och **workerSize** parametrar

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

