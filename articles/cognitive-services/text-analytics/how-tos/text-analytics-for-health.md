---
title: Använda Textanalys för hälso tillstånd
titleSuffix: Azure Cognitive Services
description: Lär dig att extrahera och märka medicinsk information från ostrukturerad klinisk text med Textanalys för hälso tillstånd.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: e3e0ae444e2b3b6ac195a83653baf4b71bac6644
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363875"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Gör så här: använda Textanalys för hälsa (för hands version)

> [!NOTE]
> Textanalys för hälso containern har nyligen uppdaterats. Se [Nyheter](../whats-new.md) för mer information om nya ändringar. Kom ihåg att hämta den senaste behållaren för att använda de uppdateringar som anges.

> [!IMPORTANT] 
> Textanalys för hälsa är en förhands gransknings funktion som tillhandahålls "i befintligt skick" och "med alla fel". Därför **bör textanalys för hälsa (för hands version) inte implementeras eller distribueras i produktions användningen.** Textanalys för hälsa är inte avsedd eller görs tillgänglig för användning som medicinsk enhet, klinisk support, diagnostikverktyg eller annan teknik som är avsedd att användas i diagnosen, härdning, minskning, behandling eller förebyggande av sjukdom eller andra villkor, och ingen licens eller rättighet beviljas av Microsoft för att använda den här funktionen i sådana syften. Den här funktionen är inte utformad eller avsedd att implementeras eller distribueras som en ersättning för yrkes rådgivning eller hälso besked, diagnos, behandling eller klinisk dom från en sjukvårds expert och bör inte användas som sådan. Kunden är ensam ansvarig för all användning av Textanalys för hälsa. Microsoft garanterar inte att Textanalys för hälsa eller material som tillhandahålls i samband med funktionen kommer att vara tillräckligt för medicinska skäl eller på annat sätt uppfylla hälso tillståndet eller hälso tillståndet för en person. 


Textanalys för hälsa är en behållar tjänst som extraherar och namnger relevant medicinsk information från ostrukturerade texter som läkares anteckningar, Utskicks sammanfattning, kliniska dokument och elektroniska hälso poster.  

## <a name="features"></a>Funktioner

Textanalys för hälso containern utför för närvarande namngiven entitets igenkänning (NER), Relations extrahering, enhets negation och entitet länkning för engelsk text i din egen utvecklings miljö som uppfyller dina specifika krav för säkerhet och data styrning.

#### <a name="named-entity-recognition"></a>[Igenkänning av namngiven entitet](#tab/ner)

Igenkänning av namngivna enheter identifierar ord och fraser som nämns i ostrukturerad text som kan associeras med en eller flera semantiska typer, till exempel diagnos, medicinens namn, symptom/signering eller ålder.

> [!div class="mx-imgBorder"]
> ![Hälso NER](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Relations extrahering](#tab/relation-extraction)

Relations extrahering identifierar meningsfulla anslutningar mellan begrepp som anges i text. Till exempel hittas en relation för "villkor" för att associera ett villkors namn med en tid. 

> [!div class="mx-imgBorder"]
> ![Hälso vård](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Entity Linking](#tab/entity-linking)

Entiteten länkar disambiguates distinkta entiteter genom att associera namngivna entiteter som nämns i text till begrepp som finns i en fördefinierad databas med begrepp. Till exempel det enhetliga medicinska språk systemet (UMLS).

> [!div class="mx-imgBorder"]
> ![Hälsa, EL](../media/ta-for-health/health-entity-linking.png)

Textanalys för hälso tillstånd stöder länkning till hälso-och biomedicin-vokabulär som finns i Metathesaurus-kunskaps källan (Unified medicin language system) ([UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)).

#### <a name="negation-detection"></a>[Negation identifiering](#tab/negation-detection) 

Innebörden av medicinskt innehåll påverkas starkt av modifierare, till exempel negation, som kan ha kritiska indirekt om de feldiagnostiseras. Textanalys för hälso tillstånd stöder negation identifiering för de olika entiteter som anges i texten. 

> [!div class="mx-imgBorder"]
> ![Hälso NEG](../media/ta-for-health/health-negation.png)

---

Se [entitets kategorier](../named-entity-types.md?tabs=health) som returneras av textanalys för hälsan för en fullständig lista över entiteter som stöds.

## <a name="supported-languages"></a>Språk som stöds

Textanalys för hälsa stöder endast språk dokument i engelska.

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållar registret

Fyll i och skicka [fråge formuläret för Cognitive Services containers](https://aka.ms/csgate) för att begära åtkomst till behållaren. För närvarande debiteras du inte för Textanalys för hälso tillstånds användning. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>Installera behållaren

Det finns flera sätt att installera och köra behållaren. 

- Använd [Azure Portal](text-analytics-how-to-install-containers.md?tabs=healthcare) för att skapa en textanalys resurs och Använd Docker för att hämta din behållare.
- Använd följande PowerShell-och [Azure CLI](/cli/azure/?view=azure-cli-latest) -skript för att automatisera konfigurationen av resurs distributions behållare.

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Installera behållaren med Azure Web App for Containers

Azure [Web App for containers](https://azure.microsoft.com/services/app-service/containers/) är en Azure-resurs som är dedikerad till att köra behållare i molnet. Den innehåller färdiga funktioner som autoskalning, stöd för Docker-behållare och Docker-form, HTTPS-stöd och mycket mer.

> [!NOTE]
> Med hjälp av Azure Web App får du automatiskt en domän i form av `<appservice_name>.azurewebsites.net`

Kör det här PowerShell-skriptet med Azure CLI för att skapa en Web App for Containers med hjälp av din prenumeration och behållar avbildningen över HTTPS. Vänta tills skriptet har slutförts (cirka 25-30 minuter) innan du skickar den första begäran.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Installera behållaren med Azure Container instance

Du kan också använda en Azure Container Instance (ACI) för att förenkla distributionen. ACI är en resurs som gör att du kan köra Docker-behållare på begäran i en hanterad, Server lös Azure-miljö. 

Se [hur du använder Azure Container instances](text-analytics-how-to-use-container-instances.md) för steg som beskriver hur du distribuerar en ACI-resurs med hjälp av Azure Portal. Du kan också använda PowerShell-skriptet nedan med hjälp av Azure CLI, som skapar en ACI i din prenumeration med hjälp av behållar avbildningen.  Vänta tills skriptet har slutförts (cirka 25-30 minuter) innan du skickar den första begäran.  På grund av gränsen för maximalt antal processorer per ACI-resurs väljer du inte det här alternativet om du förväntar dig att skicka fler än 5 stora dokument (cirka 5000 tecken var) per begäran.
Se [ACI regional support](../../../container-instances/container-instances-region-availability.md) artikel för tillgänglighets information. 

> [!NOTE] 
> Azure Container Instances inkluderar inte HTTPS-stöd för de inbyggda domänerna. Om du behöver HTTPS måste du konfigurera den manuellt, inklusive att skapa ett certifikat och registrera en domän. Du hittar anvisningar om hur du gör detta med NGINX nedan.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Säker ACI-anslutning

Som standard tillhandahålls ingen säkerhet när du använder ACI med container-API. Detta beror på att behållare som vanligt vis körs som en del av en pod som skyddas från utsidan av en nätverks brygga. Du kan dock ändra en behållare med en klient som är klient del och hålla behållarens slut punkt privat. I följande exempel används [nginx](https://www.nginx.com) som en ingress-Gateway för att stödja HTTPS/SSL och autentisering av klient certifikat.

> [!NOTE]
> NGINX är en HTTP-server och proxy med öppen källkod med hög prestanda. En NGINX-behållare kan användas för att avsluta en TLS-anslutning för en enda behållare. Det är också möjligt att använda mer komplexa NGINX ingångs-baserade TLS-lösningar.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Konfigurera NGINX som en ingress-Gateway

NGINX använder [konfigurationsfiler](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) för att aktivera funktioner vid körning. För att aktivera TLS-avslutning för en annan tjänst måste du ange ett SSL-certifikat för att avsluta TLS-anslutningen och  `proxy_pass` Ange en adress för tjänsten. Ett exempel anges nedan.


> [!NOTE]
> `ssl_certificate` förväntar sig att en sökväg anges i NGINX-behållarens lokala fil system. Den angivna adressen `proxy_pass` måste vara tillgänglig i nginx-behållarens nätverk.

NGINX-behållaren läser in alla filer i `_.conf_` som är monterade under `/etc/nginx/conf.d/` i http-konfigurationen.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Exempel på Docker Compose-fil

Exemplet nedan visar hur du kan skapa en [Docker](https://docs.docker.com/compose/reference/overview) -fil för att distribuera NGINX och textanalys för hälso behållarna:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Starta den här Docker-filen genom att köra följande kommando från en-konsol på rot nivån för filen:

```bash
docker-compose up
```

Mer information finns i NGINX-dokumentationen om [nginx SSL-avslutning](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).


## <a name="example-api-request"></a>Exempel på API-begäran
Containern innehåller REST-baserade slutpunkts-API:er för frågeförutsägelse.  Vi har också tillhandahållit ett visualiserings verktyg i behållaren som är tillgängligt genom att lägga till **demonstrationen** till slut punkten för behållaren, till exempel:

```bash
http://<serverURL>:5000/demo
```

Använd exemplet nedan för att skicka en fråga till behållaren som du har distribuerat och som ersätter `serverURL` variabeln med lämpligt värde.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Följande JSON är ett exempel på en JSON-fil som är kopplad till Textanalys för hälso-API-begärans INLÄGGs text:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>API-svars text

Följande JSON är ett exempel på Textanalys för svars texten för hälso-API:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Identifierings resultat för negation

När du använder negation identifiering kan i vissa fall en enskild negations period hantera flera villkor samtidigt. Negationen av en identifierad entitet representeras i JSON-utdata av flaggans booleska värde `isNegated` :

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Utmatning för Relations extrahering

Utmatningen för Relations extrahering innehåller URI-referenser till relationens *källa* och dess *mål*. Entiteter med relations roll för `ENTITY` är tilldelade till `target` fältet. Entiteter med relations roll för `ATTRIBUTE` är tilldelade till `source` fältet. Förkortnings relationer innehåller dubbelriktade `source` och `target` fält, och `bidirectional` kommer att anges till `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Namngivna enhets kategorier](../named-entity-types.md)
* [Nyheter](../whats-new.md)