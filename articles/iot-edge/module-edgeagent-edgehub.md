---
title: Referens för Azure IoT EdgeAgent och EdgeHub | Microsoft Docs
description: Granska specifika egenskaper och deras värden för modultvillingar edgeAgent och edgeHub
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 259d61125828ee487b74daa525f3635cfa592ce7
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017712"
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Egenskaper för Edge-agenten och Edge hub modultvillingar

Edge-agent och Edge hub är två moduler som utgör IoT Edge-körningen. Mer information om vilka uppgifter som utförs av varje modul, se [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md). 

Den här artikeln innehåller önskade egenskaper och rapporterade egenskaper för modultvillingar runtime. Mer information om hur du distribuerar moduler på IoT Edge-enheter finns i [distribueringen och övervakningen][lnk-deploy].

## <a name="edgeagent-desired-properties"></a>EdgeAgent önskade egenskaper

Modultvillingen för Edge-agenten kallas `$edgeAgent` och samordnar kommunikationen mellan Edge-agenten som körs på en enhet och IoT Hub. Önskade egenskaper anges när du använder ett manifest för distribution på en specifik enhet som en del av en enskild enhet eller i skala distribution. 

| Egenskap  | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara ”1.0” | Ja |
| Runtime.Type | Måste vara ”docker” | Ja |
| runtime.settings.minDockerVersion | Inställt på den lägsta Docker-versionen som krävs av den här distributionen manifest | Ja |
| runtime.settings.loggingOptions | En Stringified.json som innehåller alternativ för loggning för behållaren Edge-agenten. [Alternativ för docker-loggning][lnk-docker-logging-options] | Nej |
| runtime.settings.registryCredentials<br>. {registryId} .username | Användarnamnet för behållarregistret. Användarnamnet är vanligtvis registernamnet för Azure Container Registry.<br><br> Autentiseringsuppgifter för registret är nödvändiga för modulen bilder som inte är offentliga. | Nej |
| runtime.settings.registryCredentials<br>. {registryId} .password | Lösenordet för behållarregistret. | Nej |
| runtime.settings.registryCredentials<br>. {registryId} .address | Adressen till behållarregistret. För Azure Container Registry adressen är vanligtvis *{registryname}.azurecr.IO/Azure-vote-front:v1*. | Nej |  
| systemModules.edgeAgent.type | Måste vara ”docker” | Ja |
| systemModules.edgeAgent.settings.image | URI för avbildning av Edge-agenten. Edge-agenten är för närvarande inte att uppdatera sig själv. | Ja |
| systemModules.edgeAgent.settings<br>.createOptions | En Stringified.json med alternativ för att skapa behållaren Edge-agenten. [Alternativ för att skapa docker][lnk-docker-create-options] | Nej |
| systemModules.edgeAgent.configuration.id | ID för den distribution som har distribuerat den här modulen. | Den här egenskapen anges av IoT Hub när den här manifestet tillämpas med hjälp av en distribution. Inte en del av ett manifest för distribution. |
| systemModules.edgeHub.type | Måste vara ”docker” | Ja |
| systemModules.edgeHub.status | Måste vara ”körs” | Ja |
| systemModules.edgeHub.restartPolicy | Måste vara ”alltid” | Ja |
| systemModules.edgeHub.settings.image | URI för avbildning av Edge hub. | Ja |
| systemModules.edgeHub.settings<br>.createOptions | En Stringified.json med alternativ för att skapa behållaren Edge hub. [Alternativ för att skapa docker][lnk-docker-create-options] | Nej |
| systemModules.edgeHub.configuration.id | ID för den distribution som har distribuerat den här modulen. | Den här egenskapen anges av IoT Hub när den här manifestet tillämpas med hjälp av en distribution. Inte en del av ett manifest för distribution. |
| moduler. {moduleId} .version | En användardefinierad sträng som representerar versionen av den här modulen. | Ja |
| moduler. {moduleId} .type | Måste vara ”docker” | Ja |
| moduler. {moduleId} .status | {”körs” \| ”stoppad”} | Ja |
| moduler. {moduleId} .restartPolicy | {”aldrig” \| ”på-misslyckades” \| ”på-defekta” \| ”alltid”} | Ja |
| modules.{moduleId}.settings.image | URI: N i modulen avbildningen. | Ja |
| moduler. {moduleId}.settings.createOptions | En Stringified.json med alternativ för att skapa behållaren modulen. [Alternativ för att skapa docker][lnk-docker-create-options] | Nej |
| moduler. {moduleId}.configuration.id | ID för den distribution som har distribuerat den här modulen. | Den här egenskapen anges av IoT Hub när den här manifestet tillämpas med hjälp av en distribution. Inte en del av ett manifest för distribution. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent rapporterade egenskaper

Edge-agenten rapporterade egenskaper omfattar tre huvudsakliga typer av information:

1. Status för tillämpningen av senaste sett önskade egenskaper.
2. Status för de moduler som för närvarande körs på enheten som rapporteras av Edge-agenten; och
3. En kopia av de egenskaper som körs på enheten.

Den här senaste del av informationen är användbar om de senaste egenskaperna inte har installerats av körningen och enheten fortfarande kör en tidigare distribution manifest.

> [!NOTE]
> De rapporterade egenskaperna för Edge-agenten är användbara eftersom de kan efterfrågas med den [IoT Hub-frågespråk] [ lnk-iothub-query] att undersöka statusen för distributioner i stor skala. Mer information om hur du använder egenskaper för Edge-agent för status finns i [förstå IoT Edge-distributioner för enskilda enheter eller i stor skala][lnk-deploy].

I följande tabell innehåller inte information som kopieras från önskade egenskaper.

| Egenskap  | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Heltalet refererar till den senaste versionen av de önskade egenskaperna som bearbetas av Edge-agenten. |
| lastDesiredStatus.code | Det här är den statuskod som refererar till senaste önskade egenskaper som setts av Edge-agenten. Tillåtna värden: `200` lyckas, `400` ogiltig konfiguration, `412` ogiltig schemaversion `417` önskade egenskaper är tomma, `500` misslyckades |
| lastDesiredStatus.description | Beskrivning av status |
| devicehealth koppla | `healthy` Om Körningsstatus för alla moduler är antingen `running` eller `stopped`, `unhealthy` annars |
| configurationHealth. {deploymentId} .health | `healthy` Om Körningsstatus för alla moduler som angetts för distributionen {deploymentId} är antingen `running` eller `stopped`, `unhealthy` annars |
| runtime.platform.OS | Rapportering av det operativsystem som körs på enheten |
| Runtime.Platform.Architecture | Rapporteringsarkitektur Processorn på enheten |
| systemModules.edgeAgent.runtimeStatus | Status rapporteras av Edge-agenten: {”körs” \| ”skadad”} |
| systemModules.edgeAgent.statusDescription | Beskrivning av den rapporterade statusen för Edge-agenten. |
| systemModules.edgeHub.runtimeStatus | Aktuell status för Edge hub: {”körs” \| ”stoppad” \| ”misslyckades” \| ”backoff” \| ”skadad”} |
| systemModules.edgeHub.statusDescription | Beskrivning av den aktuella statusen för Edge hub om feltillstånd. |
| systemModules.edgeHub.exitCode | Om avslutats slutkoden som rapporterats av behållaren Edge hub |
| systemModules.edgeHub.startTimeUtc | Tidpunkten då Edge hub senast startades |
| systemModules.edgeHub.lastExitTimeUtc | Tid när Edge hub senast avslutades |
| systemModules.edgeHub.lastRestartTimeUtc | När Edge hub senast startades om |
| systemModules.edgeHub.restartCount | Antal gånger som den här modulen startades som en del av principen för omstart. |
| moduler. {moduleId} .runtimeStatus | Aktuell status för modulen: {”körs” \| ”stoppad” \| ”misslyckades” \| ”backoff” \| ”skadad”} |
| moduler. {moduleId} .statusDescription | Beskrivning av den aktuella statusen för modulen om feltillstånd. |
| moduler. {moduleId} .exitCode | Om avslutats slutkoden som rapporterats av modulen behållaren |
| moduler. {moduleId} .startTimeUtc | Tidpunkten då modulen senast startades |
| moduler. {moduleId} .lastExitTimeUtc | Tid när modulen senast avslutades |
| moduler. {moduleId} .lastRestartTimeUtc | När modulen senast startades om |
| moduler. {moduleId} .restartCount | Antal gånger som den här modulen startades som en del av principen för omstart. |

## <a name="edgehub-desired-properties"></a>EdgeHub önskade egenskaper

Modultvillingen för Edge hub kallas `$edgeHub` och samordnar kommunikationen mellan Edge hub som körs på en enhet och IoT Hub. Önskade egenskaper anges när du använder ett manifest för distribution på en specifik enhet som en del av en enskild enhet eller i skala distribution. 

| Egenskap  | Beskrivning | Krävs i distributionen manifestet |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara ”1.0” | Ja |
| vägar. {routeName} | En sträng som representerar en Edge hub väg. | Den `routes` element kan vara närvarande men tom. |
| storeAndForwardConfiguration.timeToLiveSecs | Tid i sekunder som Edge hub behåller meddelanden vid frånkopplade routning slutpunkter, till exempel kopplas bort från IoT Hub, eller lokala module | Ja |

## <a name="edgehub-reported-properties"></a>EdgeHub rapporterade egenskaper

| Egenskap  | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Heltalet refererar till den senaste versionen av de önskade egenskaperna som bearbetas av Edge hub. |
| lastDesiredStatus.code | Det här är den statuskod som refererar till senaste önskade egenskaper som setts av Edge hub. Tillåtna värden: `200` lyckas, `400` ogiltig konfiguration, `500` misslyckades |
| lastDesiredStatus.description | Beskrivning av status |
| -klienter. {enhet eller moduleId} .status | Statusen för den här enheten eller modulen. Möjliga värden {”ansluten” \| ”frånkopplad”}. Endast modulen identiteter kan vara i frånkopplat läge. Underordnade enheter som ansluter till Edge hub visas bara när du är ansluten. |
| -klienter. {enhet eller moduleId} .lastConnectTime | Senaste gången enheten eller moduler som är anslutna |
| -klienter. {enhet eller moduleId} .lastDisconnectTime | Senaste gången enheten eller modulen frånkopplad |

## <a name="next-steps"></a>Nästa steg

Läs hur du använder dessa egenskaper för att bygga ut distribution manifesten i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
