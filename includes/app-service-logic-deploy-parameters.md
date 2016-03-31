Mit dem Azure-Ressourcen-Manager definieren Sie die Parameter für Werte, die Sie bei der Bereitstellung der Vorlage angeben möchten. Die Vorlage enthält einen Abschnitt namens "Parameters", der alle Parameterwerte enthält.
Sie sollten einen Parameter definieren, für das Projekt die Werte, die variiert abhängig von bereitstellen, oder basierend auf der 
die Umgebung bereitgestellt werden. Definieren Sie keine Parameter für Werte, die sich nicht ändern. Jeder Parameterwert wird in der Vorlage verwendet, um die bereitgestellten Ressourcen zu definieren. 

Verwenden Sie beim Definieren von Parametern, die **AllowedValues** Feld, um anzugeben, welche Werte ein Benutzer während der Bereitstellung angeben kann. Verwenden der **DefaultValue** Feld für den Parameter einen Wert zuweisen, wenn kein Wert, während der Bereitstellung angegeben wird.

Jeder Parameter wird in der Vorlage beschrieben.

### logicAppName

Der Name der zu erstellenden Logik-App.

    "logicAppName": {
        "type": "string"
    }

### svcPlanName

Der Name des App Service-Plans zum Hosten der Logik-App.
    
    "svcPlanName": {
        "type": "string"
    }

### sku

Der Tarif für die Logik-App.

    "sku": {
        "type": "string",
        "defaultValue": "Standard",
        "allowedValues": [
            "Free",
            "Basic",
            "Standard",
            "Premium"
        ]
    }

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind (Free, Basic, Standard oder Premium), und weist einen Standardwert (Standard) zu, wenn kein Wert angegeben wird.

### svcPlanSize

Die Instanzgröße der App.

    "svcPlanSize": {
        "defaultValue": "0",
        "type": "string",
        "allowedValues": [
            "0",
            "1",
            "2"
        ]
    }

Die Vorlage definiert die Werte, die für diesen Parameter zulässig sind (0, 1 oder 2), und weist einen Standardwert (0) zu, wenn kein Wert angegeben wird. Die Werte entsprechen klein, mittel und groß.


