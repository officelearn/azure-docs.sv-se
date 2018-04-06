---
title: Azure IoT EdgeAgent och EdgeHub referens | Microsoft Docs
description: Granska de specifika egenskaperna och deras värden för modul-twins edgeAgent och edgeHub
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0971d5bba59ce3c7b1a6409ef3248f33a41e37c9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="properties-of-the-edge-agent-and-edge-hub-module-twins"></a>Egenskaper för Edge agent och Edge hubb modulen twins

Edge agenten och kant-hubben är två moduler som utgör IoT kant-körningsmiljön. Mer information om vilka uppgifter som utför varje modul, se [förstå Azure IoT kant-runtime och dess arkitektur](iot-edge-runtime.md). 

Den här artikeln innehåller egenskaper och rapporterade egenskaperna för runtime modulen twins. Se [distributionen och övervakar] [ lnk-deploy] för mer information om hur du distribuerar moduler på IoT Edge-enheter.

## <a name="edgeagent-desired-properties"></a>EdgeAgent önskade egenskaper

Modulen dubbla för kant-agent kallas `$edgeAgent` och samordnar kommunikationen mellan kant-agenten som körs på en enhet och IoT-hubb. Egenskaperna anges när du använder en distributionsmanifestet på en specifik enhet som en del av en enskild enhet eller med skalning distribution. 

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

## <a name="edgeagent-reported-properties"></a>EdgeAgent rapporterade egenskaper

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
| lastDesiredVersion | Heltalet refererar till den senaste versionen av de egenskaper bearbetas av agenten kant. |
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

## <a name="edgehub-desired-properties"></a>EdgeHub önskade egenskaper

Modulen dubbla för kant-hubb kallas `$edgeHub` och samordnar kommunikationen mellan kant-hubb som körs på en enhet och IoT-hubb. Egenskaperna anges när du använder en distributionsmanifestet på en specifik enhet som en del av en enskild enhet eller med skalning distribution. 

| Egenskap | Beskrivning | Krävs i distributionsmanifestet |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara ”1.0” | Ja |
| routes.{routeName} | En sträng som representerar en kant hubb väg. | Den `routes` elementet kan finnas men tomt. |
| storeAndForwardConfiguration.timeToLiveSecs | Tid i sekunder som Edge hubb håller meddelanden vid frånkopplade routning slutpunkter, till exempel kopplas bort från IoT-hubb eller lokala modul | Ja |

## <a name="edgehub-reported-properties"></a>EdgeHub rapporterade egenskaper

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Heltalet refererar till den senaste versionen av de egenskaper bearbetas av Edge-hubben. |
| lastDesiredStatus.code | Detta är statuskoden hänvisar till senaste önskade egenskaper som setts av Edge-hubben. Tillåtna värden: `200` lyckades, `400` ogiltig konfiguration `500` misslyckades |
| lastDesiredStatus.description | Beskrivning av status |
| -klienter. {enhet eller modulen identitet} .status | Statusen för den här enheten eller modul. Möjliga värden {”ansluten” \| ”frånkopplad”}. Endast modulen identiteter kan vara i frånkopplat tillstånd. Underordnade enheter som ansluter till Edge hubb visas endast när ansluten. |
| -klienter. {enhet eller modulen identitet} .lastConnectTime | Senaste tid för enheten eller modulen som är ansluten |
| -klienter. {enhet eller modulen identitet} .lastDisconnectTime | Tid för senaste enheten eller modulen frånkopplad |

## <a name="next-steps"></a>Nästa steg

Information om hur du använder dessa egenskaper för att bygga ut distributionsmanifest finns [förstå hur IoT kant moduler kan användas, konfigurerats och återanvändas](module-composition.md).

<!--links -->
[lnk-deploy]: module-deployment-monitoring.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-query-language.md
