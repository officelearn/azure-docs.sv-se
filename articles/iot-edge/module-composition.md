---
title: "Azure IoT kant modulsammansättningen | Microsoft Docs"
description: "Lär dig vad hamnar i Azure IoT kant-moduler och hur de kan återanvändas"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5de67b6f1ce79934a3a6aab623d2e77a56a8ce76
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="understand-how-iot-edge-modules-can-be-used-configured-and-reused---preview"></a>Förstå hur IoT kant moduler kan användas, konfigurerad, och återanvänds - förhandsgranskning

Azure IoT-gräns kan du skapa flera IoT kant-moduler innan du distribuerar dem till IoT-gränsenheterna. Den här artikeln förklarar mest viktiga begrepp runt fastställdes flera IoT kant-moduler före distributionen. 

## <a name="the-deployment-manifest"></a>Distributionsmanifestet
Den *distributionsmanifestet* är ett JSON-dokument som beskriver:

* Vilka IoT kant-moduler har distribueras tillsammans med deras skapas och hanteringsalternativ;
* Konfigurationen av Edge hubben, som beskriver hur meddelanden ska flöda mellan moduler och mellan moduler och IoT-hubb;
* Du kan också värdena som anges i modulen twins, önskade egenskaper att konfigurera enskilda modulen program.

I Azure IoT kant självstudier skapar du en distributionsmanifestet genom att gå via en guide i Azure IoT kant-portalen. Du kan också använda en distributionsmanifestet programmässigt med hjälp av REST- eller IoT-hubb Service SDK. Referera till [distribuera och övervaka] [ lnk-deploy] mer information om IoT kant-distributioner.

Distributionsmanifestet konfigurerar en modul dubbla egenskaper för IoT kant-moduler som har distribuerats på en IoT-enhet på en hög nivå. Två av dessa moduler finns alltid: Edge-agenten och kant-hubben.

Manifestet följer den här strukturen:

    {
        "moduleContent": {
            "$edgeAgent": {
                "properties.desired": {
                    // desired properties of the Edge agent
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    // desired properties of the Edge hub
                }
            },
            "{module1}": {  // optional
                "properties.desired": {
                    // desired properties of module with id {module1}
                }
            },
            "{module2}": {  // optional
                ...
            },
            ...
        }
    }

Ett exempel på en distributionsmanifestet rapporteras i slutet av det här avsnittet.

> [!IMPORTANT]
> Alla IoT-gränsenheterna måste konfigureras med en distributionsmanifestet. En nyligen installerade IoT kant runtime rapporterar en felkod förrän konfigurerats med ett giltigt manifest. 

### <a name="specify-the-modules"></a>Ange moduler
Innehåller egenskaperna i modulen dubbla Edge-Agent: önskade moduler, konfiguration och hantering av alternativen, tillsammans med konfigurationsparametrar för kant-agenten.

Det här avsnittet i manifestet innehåller referenser till modulen bilder och alternativ för IoT kant runtime moduler (kant agent och Edge hubb) samt modulerna som angetts av användaren på en hög nivå.

Referera till den [önskad egenskaperna för Edge agent] [ lnk-edgeagent-desired] detaljerad beskrivning av det här avsnittet i manifestet.

> [!NOTE]
> En distributionsmanifestet som innehåller endast IoT kant körningen (agent och hubb) är giltig.


### <a name="specify-the-routes"></a>Ange vägarna
Edge-hubben är ett sätt att deklarativt vidarebefordra meddelanden mellan moduler och mellan moduler och IoT-hubb.

Vägar har följande syntax:

        FROM <source>
        [WHERE <condition>]
        INTO <sink>

Den *källa* kan vara något av följande:

| Källa | Beskrivning |
| ------ | ----------- |
| `/*` | Alla meddelanden från enhet till moln från en enhet eller en modul |
| `/messages/*` | Varje enhet till moln-meddelande som skickas av en enhet eller en modul via vissa eller inga utdata |
| `/messages/modules/*` | Varje enhet till moln-meddelande som skickas av en modul via vissa eller inga utdata |
| `/messages/modules/{moduleId}/*` | Varje enhet till moln-meddelande som skickas av {moduleId} med inga utdata |
| `/messages/modules/{moduleId}/outputs/*` | Varje enhet till moln-meddelande som skickas av {moduleId} med vissa utdata |
| `/messages/modules/{moduleId}/outputs/{output}` | Alla enhet till moln-meddelanden som skickas med hjälp av {moduleId} {utdata} |

Villkoret kan vara något villkor som stöds av den [IoT-hubb frågespråket] [ lnk-iothub-query] för IoT-hubb routningsregler.

Sink kan vara något av följande:

| Mottagare | Beskrivning |
| ---- | ----------- |
| `$upstream` | Skicka meddelandet till IoT-hubb |
| `BrokeredEndpoint("/modules/{moduleId}/inputs/{input}")` | Skicka meddelandet till indata `{input}` för modulen `{moduleId}` |

Det är viktigt att notera att Edge hubb tillhandahåller garantier för på-minst en gång, vilket innebär att meddelanden kommer att lagras lokalt om en väg går inte att leverera meddelandet till dess mottagare, t.ex. Edge-hubb kan inte ansluta till IoT-hubb eller mål-modulen är inte anslutet.

Edge hubb lagrar meddelanden upp till den tid som anges i den `storeAndForwardConfiguration.timeToLiveSecs` egenskapen Edge hubben önskade egenskaper.

### <a name="specifying-the-desired-properties-of-the-module-twin"></a>Ange önskade egenskaper för modulen dubbla

Distributionsmanifestet kan ange önskade egenskaper för modulen dubbla för varje användare-moduler som anges i avsnittet gräns agent.

När egenskaperna har angetts i distributionsmanifestet över de eventuella egenskaper som finns i modulen dubbla.

Om du inte anger en modul dubbla önskade egenskaper i distributionsmanifestet IoT-hubb kan inte ändra modulen dubbla på något sätt och du kommer att kunna ange de önskade egenskaperna programmässigt.

Av samma metoder som gör det möjligt att ändra enheten twins används för att ändra modulen twins. Mer information finns i [enheten dubbla Utvecklarhandbok](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-device-twins) för ytterligare information.   

### <a name="deployment-manifest-example"></a>Exempel på distribution manifest

Detta exempel på distribution manifestet JSON-dokument.

    {
    "moduleContent": {
        "$edgeAgent": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "runtime": {
                    "type": "docker",
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "loggingOptions": ""
                    }
                },
                "systemModules": {
                    "edgeAgent": {
                        "type": "docker",
                        "settings": {
                        "image": "microsoft/azureiotedge-agent:1.0-preview",
                        "createOptions": ""
                        }
                    },
                    "edgeHub": {
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-hub:1.0-preview",
                        "createOptions": ""
                        }
                    }
                },
                "modules": {
                    "tempSensor": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
                        "createOptions": "{}"
                        }
                    },
                    "filtermodule": {
                        "version": "1.0",
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always",
                        "settings": {
                        "image": "myacr.azurecr.io/filtermodule:latest",
                        "createOptions": "{}"
                        }
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "schemaVersion": "1.0",
                "routes": {
                    "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
                    "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
                },
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 10
                }
            }
        }
    }
    }

## <a name="reference-edge-agent-module-twin"></a>Referens: Edge agent modulen dubbla

Modulen dubbla för kant-agent kallas `$edgeAgent` och samordnar kommunikationen mellan kant-agenten som körs på en enhet och IoT-hubb.
Egenskaperna anges när du använder en distributionsmanifestet på en specifik enhet som en del av en enskild enhet eller med skalning distribution. Se [distributionen och övervakar] [ lnk-deploy] för mer information om hur du distribuerar moduler på IoT Edge-enheter.

### <a name="edge-agent-twin-desired-properties"></a>Edge dubbla önskad agentegenskaper

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara ”1.0” | Ja |
| Runtime.Type | Måste vara ”docker” | Ja |
| runtime.settings.minDockerVersion | Ange att den minsta Docker-version som krävs för den här distributionsmanifestet | Ja |
| runtime.settings.loggingOptions | En stringified JSON som innehåller loggningsalternativen för behållaren Edge agent. [Alternativ för docker-loggning][lnk-docker-logging-options] | Nej |
| systemModules.edgeAgent.type | Måste vara ”docker” | Ja |
| systemModules.edgeAgent.settings.image | URI för bilden på Edge-agenten. Edge-agenten är för närvarande inte kan uppdatera sig själv. | Ja |
| systemModules.edgeAgent.settings.createOptions | En stringified JSON som innehåller alternativ för att skapa behållaren Edge agent. [Docker skapa alternativ][lnk-docker-create-options] | Nej |
| systemModules.edgeAgent.configuration.id | ID för distributionen som distribueras den här modulen. | Detta har angetts i IoT Hub när manifestet tillämpas med hjälp av en distribution. Inte en del av en distributionsmanifestet. |
| systemModules.edgeHub.type | Måste vara ”docker” | Ja |
| systemModules.edgeHub.status | Måste vara ”körs” | Ja |
| systemModules.edgeHub.restartPolicy | Måste vara ”always” | Ja |
| systemModules.edgeHub.settings.image | URI för bilden på hubben kant. | Ja |
| systemModules.edgeHub.settings.createOptions | En stringified JSON som innehåller alternativ för att skapa behållaren Edge hubb. [Docker skapa alternativ][lnk-docker-create-options] | Nej |
| systemModules.edgeHub.configuration.id | ID för distributionen som distribueras den här modulen. | Detta har angetts i IoT Hub när manifestet tillämpas med hjälp av en distribution. Inte en del av en distributionsmanifestet. |
| modules.{moduleId}.version | En användardefinierad sträng som representerar versionen av den här modulen. | Ja |
| moduler. {moduleId} .type | Måste vara ”docker” | Ja |
| modules.{moduleId}.restartPolicy | {”aldrig” \| ”på-misslyckades” \| ”på-ohälsosamt” \| ”always”} | Ja |
| modules.{moduleId}.settings.image | URI: N på modulen avbildningen. | Ja |
| modules.{moduleId}.settings.createOptions | En stringified JSON som innehåller alternativ för att skapa behållaren modulen. [Docker skapa alternativ][lnk-docker-create-options] | Nej |
| modules.{moduleId}.configuration.id | ID för distributionen som distribueras den här modulen. | Detta har angetts i IoT Hub när manifestet tillämpas med hjälp av en distribution. Inte en del av en distributionsmanifestet. |

### <a name="edge-agent-twin-reported-properties"></a>Edge agent dubbla rapporterade egenskaper

Edge-agenten rapporterade egenskaper innehåller tre huvudsakliga uppgifter:

1. Status för programmet för de senaste visas egenskaperna;
2. Status för de moduler som körs på enheten som rapporteras av Edge-agent. och
3. En kopia av de egenskaper som körs på enheten.

Den här senaste information är användbart om de senaste egenskaperna inte har installerats av körningen och enheten fortfarande kör en tidigare distributionsmanifestet.

> [!NOTE]
> Rapporterat egenskaperna för kant-agenten är användbara som de kan efterfrågas med den [IoT-hubb frågespråket] [ lnk-iothub-query] att undersöka statusen för distributioner i större skala. Referera till [distributioner] [ lnk-deploy] mer information om hur du använder den här funktionen.

Följande tabell innehåller inte information som kopieras från egenskaperna.

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Den här int refererar till den senaste versionen av de egenskaper bearbetas av agenten kant. |
| lastDesiredStatus.code | Detta är statuskoden hänvisar till senaste önskade egenskaper som setts av agenten kant. Tillåtna värden: `200` lyckades, `400` ogiltig konfiguration `412` ogiltig schemaversion `417` egenskaperna som är tomma, `500` misslyckades |
| lastDesiredStatus.description | Beskrivning av status |
| DeviceHealth | `healthy` Om Körningsstatus för alla moduler som är antingen `running` eller `stopped`, `unhealthy` annars |
| configurationHealth.{deploymentId}.health | `healthy` Om Körningsstatus för alla moduler som anges av distributionen {deploymentId} är antingen `running` eller `stopped`, `unhealthy` annars |
| runtime.platform.OS | Rapportering av Operativsystemet som körs på enheten |
| runtime.platform.architecture | Rapporteringsarkitektur Processorn på enheten |
| systemModules.edgeAgent.runtimeStatus | Status rapporteras av Edge agent: {”körs” \| ”ohälsosamt”} |
| systemModules.edgeAgent.statusDescription | Beskrivning av rapporterade statusen för agenten kant. |
| systemModules.edgeHub.runtimeStatus | Aktuell status för kant-hubb: {”körs” \| ”stoppades” \| ”misslyckades” \| ”backoff” \| ”ohälsosamt”} |
| systemModules.edgeHub.statusDescription | Beskrivning för den aktuella statusen för Edge hubb om feltillstånd. |
| systemModules.edgeHub.exitCode | Om avslutades slutkoden som rapporterats av behållaren Edge-hubb |
| systemModules.edgeHub.startTimeUtc | Tidpunkten då Edge hubb senast startades |
| systemModules.edgeHub.lastExitTimeUtc | Tid när Edge hubb senast avslutades |
| systemModules.edgeHub.lastRestartTimeUtc | När Edge hubb senast startades om |
| systemModules.edgeHub.restartCount | Antal gånger som den här modulen startades som en del av principen för omstart. |
| modules.{moduleId}.runtimeStatus | Aktuell status för modulen: {”körs” \| ”stoppades” \| ”misslyckades” \| ”backoff” \| ”ohälsosamt”} |
| moduler. {moduleId} .statusDescription | Beskrivning för den aktuella statusen för modulen om feltillstånd. |
| modules.{moduleId}.exitCode | Om avslutades slutkoden som rapporterats av modulen behållaren |
| modules.{moduleId}.startTimeUtc | Tidpunkten då modulen senast startades |
| modules.{moduleId}.lastExitTimeUtc | Tid när modulen senast avslutades |
| modules.{moduleId}.lastRestartTimeUtc | När modulen senast startades om |
| moduler. {moduleId} .restartCount | Antal gånger som den här modulen startades som en del av principen för omstart. |

## <a name="reference-edge-hub-module-twin"></a>Referens: Edge hubb modulen dubbla

Modulen dubbla för kant-hubb kallas `$edgeHub` och samordnar kommunikationen mellan kant-hubb som körs på en enhet och IoT-hubb.
Egenskaperna anges när du använder en distributionsmanifestet på en specifik enhet som en del av en enskild enhet eller med skalning distribution. Se [distributioner] [ lnk-deploy] för mer information om hur du distribuerar moduler på IoT Edge-enheter.

### <a name="edge-hub-twin-desired-properties"></a>Edge hubb dubbla önskade egenskaper

| Egenskap | Beskrivning | Krävs i distributionsmanifestet |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara ”1.0” | Ja |
| routes.{routeName} | En sträng som representerar en kant hubb väg. | Den `routes` elementet kan finnas men tomt. |
| storeAndForwardConfiguration.timeToLiveSecs | Tid i sekunder som Edge hubb håller meddelanden vid frånkopplade routning slutpunkter, t.ex. kopplas bort från IoT-hubb eller lokala modul | Ja |

### <a name="edge-hub-twin-reported-properties"></a>Edge hubb dubbla rapporterade egenskaper

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Den här int refererar till den senaste versionen av de egenskaper bearbetas av Edge-hubben. |
| lastDesiredStatus.code | Detta är statuskoden hänvisar till senaste önskade egenskaper som setts av Edge-hubben. Tillåtna värden: `200` lyckades, `400` ogiltig konfiguration `500` misslyckades |
| lastDesiredStatus.description | Beskrivning av status |
| -klienter. {enhet eller modulen identitet} .status | Statusen för den här enheten eller modul. Möjliga värden {”ansluten” \| ”frånkopplad”}. Endast modulen identiteter kan vara i frånkopplat tillstånd. Underordnade enheter som ansluter till Edge hubb visas endast när ansluten. |
| -klienter. {enhet eller modulen identitet} .lastConnectTime | Senaste tid för enheten eller modulen som är ansluten |
| -klienter. {enhet eller modulen identitet} .lastDisconnectTime | Tid för senaste enheten eller modulen frånkopplad |

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur IoT kant moduler används [förstå de krav och verktyg för att utveckla IoT kant moduler][lnk-module-dev].

[lnk-deploy]: module-deployment-monitoring.md
[lnk-edgeagent-desired]: #edge-agent-twin-desired-properties
[lnk-edgeagent-reported]: #edge-agent-twin-reported-properties
[lnk=edgehub-desired]: #edge-hub-twin-desired-properties
[lnk-edgehub-reported]: #edge-hub-twin-reported-properties
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
