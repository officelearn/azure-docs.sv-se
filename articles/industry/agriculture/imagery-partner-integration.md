---
title: Integrering av bilder-partner
description: Beskriver bilder partner integration
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c76fd151bf70bfff6eed3b45a673f94777e59467
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797912"
---
# <a name="imagery-partner-integration"></a>Integrering av bilder-partner

Den här artikeln beskriver hur du använder Azure FarmBeats Translator-komponenten för att skicka bilder-data till FarmBeats. Jordbruks bilder data kan vara från olika källor, inklusive Multispectral-kameror, satelliter och drönare. Bilder-partner kan integreras med FarmBeats för att förse kunder med anpassade, genererade kartor för sina grupper.

Data, när de är tillgängliga, kan visualiseras genom FarmBeats-acceleratorn och kan användas för data fusion och (Machine Learning/artificiell intelligens) ML/AI-modell som bygger på jordbruks företag, eller kund system integrerare.

FarmBeats ger dig möjlighet att:

- Definiera anpassade avbildnings typer, källa, fil format med utökat typ-API: er
- Mata in bilder-data från olika källor via scen & SceneFile-API: er.

Informationen nedan fokuserar på att få en form av bilder till FarmBeats-systemet.

När du väljer avsnittet drönare bilder öppnas ett popup-fönster för att visa en hög upplöst bild av drönare-orthomosaic. Du kan komma åt partner program varan, som hjälper dig att planera drönare-flygningar och hämta rå data. Du kommer att fortsätta att använda partnerns program vara för Sök vägs planering och orthomosaic bild häftning.

Drönare-partner måste göra det möjligt för kunder att koppla sitt kund konto till sina FarmBeats-instanser på Azure.

Du måste använda följande autentiseringsuppgifter i drönare-partner program för att länka FarmBeats:

- API-slutpunkt
- Klient-ID:t
- Klientorganisations-ID
- Klient hemlighet

## <a name="api-development"></a>API-utveckling

API: erna innehåller Swagger teknisk dokumentation. Granska [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) för information om API: er och motsvarande begär Anden/svar.

## <a name="authentication"></a>Autentisering

FarmBeats utnyttjar Microsoft Azure [Active Directory]((https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)). Autentisering Azure App Service tillhandahåller inbyggt stöd för autentisering och auktorisering. 

Mer information om finns i [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

FarmBeats-datahubben använder Bearer-autentisering, som behöver följande autentiseringsuppgifter:

- Klientorganisations-ID
- Klient hemlighet
- Klient-ID:t

Med hjälp av ovanstående autentiseringsuppgifter kan anroparen begära en åtkomsttoken som måste skickas i efterföljande API-begäranden i rubrik avsnittet enligt följande:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Nedan visas ett python-kod exempel som hämtar åtkomsttoken. Du kan sedan använda token för efterföljande API-anrop till FarmBeat:   
Importera Azure 

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```

## <a name="http-request-headers"></a>Rubriker för HTTP-begäran

Här är de vanligaste begärandehuvuden som måste anges när du gör ett API-anrop till FarmBeats-data hubben:

**Huvud** | **Beskrivning och exempel**
--- | ---
Content-Type  | Formatet för begäran (Content-Type: Application/<format>) för API: t för FarmBeats data Hub är JSON. Innehålls typ: Application/JSON
Auktorisering | Anger den åtkomsttoken som krävs för att göra ett API-anrop. Auktorisering: innehavare < åtkomst-token >
godkänt  | Svars formatet. För FarmBeats-API: er är formatet JSON accept: Application/JSON


## <a name="api-requests"></a>API-begäranden

Om du vill göra en REST API-begäran kombinerar du HTTP-metoden (GET/POST/service), URL: en till API-tjänsten, resurs-URI: n (för att fråga, skicka data, uppdatera eller ta bort) och en eller flera HTTP-begärandehuvuden.

Alternativt kan du inkludera frågeparametrar på GET-anrop för att filtrera, begränsa storleken på och sortera data i svaren.

Nedanstående exempel förfrågan är att hämta listan över enheter:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

De flesta GET-, POST-och parkera-anrop kräver en text för JSON-begäran.

Nedanstående exempel förfrågan är att skapa en enhet (detta har en indataport med en indataport med begär ande texten).


```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Data format

JSON (JavaScript Object Notation) är ett gemensamt, språk oberoende data format som ger en enkel text representation av godtyckliga data strukturer. Mer information finns i [JSON org](https://JSON.org).

## <a name="ingesting-imagery-into-farmbeats"></a>Mata in bilder i FarmBeats

När partnern har autentiseringsuppgifter för att ansluta till FarmBeats-datahubben gör partnern följande i Translator-komponenten:

1.  Skapa en ny utökad typ för följande fält, i enlighet med den typ av bilder som ska överföras:

    - Scen Källa: till exempel < drone_partner_name >
    - Scen typ: till exempel <drone>
    - Scen fil typ: till exempel <chlorophyll index>
    - Scen fil innehålls typ: till exempel < bild/TIFF >

2.  Anropa Server grupps-API: et för att hämta listan över Server grupper från Azure FarmBeats-systemet.
3.  Ge kunden möjlighet att välja en enda server grupp i listan över Server grupper.

    Partner systemet måste visa Server gruppen i partner programmet för att kunna utföra Sök vägs planeringen och drönare flyg-och bild insamling.

4.  Anropa API: et för scenen och ange nödvändig information för att skapa en ny scen med en unik SceneID.
5.  Ta emot en blobb-SAS-URL för att överföra de avbildningar som krävs till FarmBeats-datahubben, i kontexten för den valda Server gruppen, till FarmBeats-systemet.

Här är ett detaljerat flöde för API-anrop:

### <a name="step-1-extendedtype"></a>Steg 1: ExtendedType

Kontrol lera i ExtendedType-API: et om typ och fil källa är tillgängliga på FarmBeats. Du kan göra detta genom att anropa en GET-/ExtendedType-API.

Följande är systemdefinierade värden:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```
Detta kommer att vara en eng ång slö period och omfånget för den nya scenetype är begränsat till den prenumeration där FarmBeats-projektet distribueras.

Exempel: om du vill lägga till SceneSource: "SlantRange", lägger du till ID: t för/ExtendedType med nyckel: "SceneSource" indatakälla:

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

Grönt fält är det nya tillägget av de systemdefinierade scen källvärdena.

### <a name="step-2-get-farmdetails"></a>Steg 2: Hämta FarmDetails

Scener (TIFF-eller CSV-filer) kommer att ingå i en server grupps kontext. Du måste hämta server grupps informationen genom att göra en Get on/Farm-API. API: n kommer att returnera listan över Server grupper som är tillgängliga i FarmBeats och du kan välja den server grupp som du vill mata in data för.

Hämta/Farm-svar:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-ascene-id-post-call"></a>Steg 3: skapa ett/scen-ID (post-anrop)

Skapa en ny scen (TIFF-eller. csv-fil) med den information som anges, och ange datum, sekvens och Server grupp-ID som scenen ska associeras med. Metadata som är kopplade till scenen kan definieras under egenskaper, inklusive varaktighet och typ av mått.

Detta skapar en ny SceneID som kommer att associeras med Server gruppen. När SceneID har skapats kan användaren använda samma för att skapa en ny fil (TIFF eller. csv) & lagra innehållet i filen.

Exempel på indata-nyttolast för API: et efter anrop på/scen

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API-svar:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Skapa/SceneFile**

Det scen-ID som returnerades i steg tre är indatamängden för SceneFile. SceneFile returnerar en SAS-URL-token som är giltig i 24 timmar.

Om användaren behöver ett programmerings sätt för att ladda upp en avbildning av avbildningar kan du använda Blob Storage SDK för att definiera en metod med Scenefile-ID, plats & URL.

Exempel på indata-nyttolast för anropet efter anrop i/Scenefile-API:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API-svar:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

Anropet post-anrop till/SceneFile returnerar en SAS-uppladdnings-URL som kan användas för att ladda upp CSV-eller TIFF-filen med hjälp av Azure Blob Storage-klienten/-biblioteket.


## <a name="next-steps"></a>Nästa steg

Mer information om REST API-baserad integrerings information finns [REST API](references-for-farmbeats.md#rest-api).
