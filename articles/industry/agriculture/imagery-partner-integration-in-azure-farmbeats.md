---
title: Bildpartnerintegration
description: I den här artikeln beskrivs integrering av bildspråkspartner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131853"
---
# <a name="imagery-partner-integration"></a>Bildpartnerintegration

I den här artikeln beskrivs hur du använder azure FarmBeats Translator-komponenten för att skicka bilddata till FarmBeats. Jordbruksbilder kan genereras från olika källor, till exempel multispektrala kameror, satelliter och drönare. Jordbruksbilder partner kan integrera med FarmBeats att förse kunderna med skräddarsydda kartor för sina gårdar.

Data, en gång tillgängliga, kan visualiseras genom FarmBeats Accelerator och potentiellt användas för datafusion och maskininlärning / artificiell intelligens (ML / AI) modell byggnad av jordbruksföretag eller kundsystem integratörer.

FarmBeats ger möjlighet att:

- Definiera anpassade bildtyper, käll- och filformat med hjälp av /ExtendedType API:er.
- Inta bilddata från olika källor via API:erna /Scene och /SceneFile.

Följande information fokuserar på att få någon form av bilder i FarmBeats systemet.

När du väljer avsnittet **Drone Imagery** öppnas ett popup-fönster för att visa en högupplöst bild av drönarortoosaic. Du kan komma åt partnerprogramvaran, som hjälper till att planera drönarflygningar och få rådata. Du kommer att fortsätta att använda partnerns programvara för planering av vägar och ortoosaiska bildsömmar.

Drone-partner måste göra det möjligt för kunder att länka sitt kundkonto med sin FarmBeats-instans på Azure.

Du måste använda följande autentiseringsuppgifter i drönarpartnerprogrammet för att länka FarmBeats:

- API-slutpunkt
- Klient-ID:t
- Klientorganisations-ID
- Klienthemlighet

## <a name="api-development"></a>API-utveckling

API:erna innehåller teknisk dokumentation för Swagger. Information om API:erna och motsvarande begäranden eller svar finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="authentication"></a>Autentisering

FarmBeats använder Microsoft Azure [Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) (Azure AD). Azure App Service ger inbyggt stöd för autentisering och auktorisering. 

Mer information om Azure AD finns i [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

FarmBeats Datahub använder bärareautentisering, som behöver följande autentiseringsuppgifter:

- Klientorganisations-ID
- Klienthemlighet
- Klient-ID:t

Med hjälp av tidigare autentiseringsuppgifter kan anroparen begära en åtkomsttoken, som måste skickas i efterföljande API-begäranden, i rubrikavsnittet, enligt följande:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Följande Python-kodexempel hämtar åtkomsttoken. Du kan sedan använda token för efterföljande API-anrop till FarmBeats.

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

## <a name="http-request-headers"></a>HTTP-begäranden

Här är de vanligaste begäranderubrikerna som måste anges när du ringer ett API-anrop till FarmBeats Datahub.

**Huvudet** | **Beskrivning och exempel**
--- | ---
Content-Type  | Formatet för begäran (Innehållstyp: program/<format>). För FarmBeats Datahub API:er är formatet JSON. Innehållstyp: program/json
Auktorisering | Anger den åtkomsttoken som krävs för att ringa ett API-anrop. Auktorisering: Bärare <Access-Token>
Acceptera  | Svarsformatet. För FarmBeats Datahub API:er är formatet JSON. Acceptera: ansökan/json


## <a name="api-requests"></a>API-begäranden

Om du vill göra en REST API-begäran kombinerar du:

- HTTP-metoden (GET, POST och PUT).
- URL:en till API-tjänsten.
- Resurs-URI(för att fråga, skicka data, uppdatera eller ta bort).
- Ett eller flera HTTP-begäranden.

Du kan också inkludera frågeparametrar för GET-anrop för att filtrera, begränsa storleken på och sortera data i svaren.

Följande exempelbegäran är att hämta listan över enheter:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

De flesta GET-, POST- och PUT-samtal kräver en JSON-begäran.

Följande exempelbegäran är att skapa en enhet. Det här exemplet har en indata-JSON med begärandetexten.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Dataformat

JSON är ett vanligt språkoberoende dataformat som ger en enkel textrepresentation av godtyckliga datastrukturer. Mer information finns i [JSON org](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Inta bilder i FarmBeats

När partnern har autentiseringsuppgifter för att ansluta till FarmBeats Datahub tar partnern följande steg i translator-komponenten.

1.  Skapa en ny utökad typ för följande fält, i enlighet med vilken typ av bilder som ska överföras:

    - **Scenkälla**: Till exempel drone_partner_name
    - **Scentyp:** Till exempel drönare
    - **Scenfiltyp:** Till exempel klorofyllindex
    - **Scenfil innehållstyp:** Till exempel bild / tiff

2.  Anropa API:et /Farms för att få en lista över grupper från Azure FarmBeats-systemet.
3.  Ge kunden möjlighet att välja en enda gård från listan över gårdar.

    Partnersystemet måste visa gården i partnerprogramvaran för att göra banplaneringen och drönarflygningen och bildsamlingen.

4.  Anropa /Scene API och ge nödvändig information för att skapa en ny scen med ett unikt scen-ID.
5.  Få en sas-url för blob för att ladda upp de nödvändiga bilderna till FarmBeats Datahub, i samband med den valda servergruppen, i FarmBeats-systemet.

Här är ett detaljerat flöde på API-anrop.

### <a name="step-1-extendedtype"></a>Steg 1: ExtendedType

Kontrollera i /ExtendedType-API:et för att se om typen och filkällan är tillgängliga på FarmBeats. Det gör du genom att anropa ett GET på API:et /ExtendedType.

Här är de systemdefinierade värdena:

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

Det här steget är en engångsinställning. Omfattningen av den här nya scentypen är begränsad till prenumerationen där Azure FarmBeats är installerat.

Om du till exempel vill lägga till SceneSource: "SlantRange" gör du en PUT på ID:t för /ExtendedType-API:et med nyckeln "SceneSource" indatanyttolast.

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

Det gröna fältet är det nya tillägget till de systemdefinierade scenkällavärdena.

### <a name="step-2-get-farm-details"></a>Steg 2: Få information om jordbruksföretagen

Scenerna (.tiff- eller CSV-filer) är i samband med en servergrupp. Du måste hämta information om servergruppen genom att göra en GET på /Farm API. API:et returnerar listan över grupper som är tillgängliga i FarmBeats. Du kan välja den servergrupp som du vill använda data för.

SVAR PÅ GET /Farm:

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

### <a name="step-3-create-a-scene-id-post-call"></a>Steg 3: Skapa ett scen-ID (POST-anrop)

Skapa en ny scen (.tiff- eller CSV-fil) med den angivna informationen, som ger datum, sekvens och servergrupps-ID som scenen är associerad med. Metadata som är associerade med scenen kan definieras under egenskaper, vilket inkluderar varaktighet och typ av mått.

Om du skapar en ny scen skapas ett nytt scen-ID som är associerat med servergruppen. När scen-ID har skapats kan användaren använda samma för att skapa en ny fil (.tiff eller .csv) och lagra innehållet i filen.

Exempel på indatanyttolast för POST-anropet på /Scene API:

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

**Skapa en scenfil**

Scen-ID som returneras i steg 3 är ingången till scenfilen. Scenfilen returnerar en SAS-URL-token, som är giltig i 24 timmar.

Om användaren kräver ett programmatiskt sätt att ladda upp en ström av bilder kan bloblagringSDK användas för att definiera en metod med hjälp av scenfil-ID, plats och URL.

Exempel på indatanyttolast för POST-anropet på API:et /SceneFile:

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

POST-anropet till API:et /SceneFile returnerar en SAS-uppladdnings-URL som kan användas för att överföra CSV- eller .tiff-filen med hjälp av Azure Blob-lagringsklienten eller biblioteket.


## <a name="next-steps"></a>Nästa steg

Mer information om REST API-baserad integrationsinformation finns i [REST API](rest-api-in-azure-farmbeats.md).
