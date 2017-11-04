Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter parametrar som innehåller alla parametervärdena.
Du bör definiera en parameter för de värden som varierar baserat på projektet som du distribuerar eller baserat på miljön som du distribuerar till. Definiera inte parametrar för värden som alltid inte ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras. 

När du definierar parametrar, använda den **allowedValues** fältet för att ange vilka värden en användare kan ange under distributionen. Använd den **defaultValue** fält som du vill tilldela ett värde för parametern, om inget värde anges under distributionen.

Vi beskriver varje parameter i mallen.

### <a name="sitename"></a>Platsnamn
Namnet på webbprogrammet som du vill skapa.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Namnet på App Service-plan för att använda som värd för webbprogrammet.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU
Prisnivån för den värd planen.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Mallen definierar de värden som tillåts för den här parametern och tilldelar ett standardvärde (S1) om inget värde anges.

### <a name="workersize"></a>workerSize
Instansstorleken för värd planen (liten, medel eller hög).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Mallen definierar de värden som tillåts för den här parametern (0, 1 eller 2) och tilldelar ett standardvärde (0) om inget värde anges. Värdena motsvarar små, medelstora och stora.

