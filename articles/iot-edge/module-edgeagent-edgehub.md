---
title: EdgeAgent och EdgeHub önskade egenskaper referens – Azure IoT Edge | Microsoft Docs
description: Granska specifika egenskaper och deras värden för modultvillingar edgeAgent och edgeHub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e8a8170023c8f529894522e27a4c6231325089af
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190988"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Egenskaper för IoT Edge-agenten och IoT Edge hub modultvillingar

IoT Edge-agenten och IoT Edge hub är två moduler som utgör IoT Edge-körningen. Mer information om vilka uppgifter som utförs av varje modul, se [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md). 

Den här artikeln innehåller önskade egenskaper och rapporterade egenskaper för modultvillingar runtime. Mer information om hur du distribuerar moduler på IoT Edge-enheter finns i [Lär dig hur du distribuerar moduler och upprätta vägar i IoT Edge](module-composition.md).

En modultvilling innehåller: 

* **Önskade egenskaper**. Modulen kan läsa dem lösning serverdelen kan ange önskade egenskaper. Modulen kan också ta emot meddelanden om ändringar i de önskade egenskaperna. Önskade egenskaper används tillsammans med rapporterade egenskaper för att synkronisera modulkonfigurationen eller villkor.

* **Rapporterade egenskaper**. Modulen kan ange rapporterade egenskaper och lösningen serverdelen kan läsa och skicka frågor mot dem. Rapporterade egenskaper används tillsammans med önskade egenskaper för att synkronisera modulkonfigurationen eller villkor. 

## <a name="edgeagent-desired-properties"></a>EdgeAgent önskade egenskaper

Modultvillingen för IoT Edge-agenten kallas `$edgeAgent` och samordnar kommunikationen mellan IoT Edge-agenten som körs på en enhet och IoT Hub. Önskade egenskaper anges när du använder ett manifest för distribution på en specifik enhet som en del av en enskild enhet eller i skala distribution. 

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara ”1.0” | Ja |
| Runtime.Type | Måste vara ”docker” | Ja |
| runtime.settings.minDockerVersion | Inställt på den lägsta Docker-versionen som krävs av den här distributionen manifest | Ja |
| runtime.settings.loggingOptions | En Stringified.json som innehåller alternativ för loggning för behållaren IoT Edge-agenten. [Alternativ för docker-loggning](https://docs.docker.com/engine/admin/logging/overview/) | Nej |
| runtime.settings.registryCredentials<br>. {registryId} .username | Användarnamnet för behållarregistret. Användarnamnet är vanligtvis registernamnet för Azure Container Registry.<br><br> Autentiseringsuppgifter för registret är nödvändiga för modulen bilder som inte är offentliga. | Nej |
| runtime.settings.registryCredentials<br>. {registryId} .password | Lösenordet för behållarregistret. | Nej |
| runtime.settings.registryCredentials<br>. {registryId} .address | Adressen till behållarregistret. För Azure Container Registry adressen är vanligtvis *{registernamn}.azurecr.IO/Azure-vote-front:v1*. | Nej |  
| systemModules.edgeAgent.type | Måste vara ”docker” | Ja |
| systemModules.edgeAgent.settings.image | URI för avbildning av IoT Edge-agenten. IoT Edge-agenten är för närvarande inte att uppdatera sig själv. | Ja |
| systemModules.edgeAgent.settings<br>.createOptions | En Stringified.json med alternativ för att skapa behållaren IoT Edge-agenten. [Alternativ för att skapa docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nej |
| systemModules.edgeAgent.configuration.id | ID för den distribution som har distribuerat den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett manifest för distribution. |
| systemModules.edgeHub.type | Måste vara ”docker” | Ja |
| systemModules.edgeHub.status | Måste vara ”körs” | Ja |
| systemModules.edgeHub.restartPolicy | Måste vara ”alltid” | Ja |
| systemModules.edgeHub.settings.image | URI för avbildning av IoT Edge hub. | Ja |
| systemModules.edgeHub.settings<br>.createOptions | En Stringified.json med alternativ för att skapa behållaren IoT Edge hub. [Alternativ för att skapa docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nej |
| systemModules.edgeHub.configuration.id | ID för den distribution som har distribuerat den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett manifest för distribution. |
| moduler. {moduleId} .version | En användardefinierad sträng som representerar versionen av den här modulen. | Ja |
| moduler. {moduleId} .type | Måste vara ”docker” | Ja |
| moduler. {moduleId} .status | {”körs” \| ”stoppad”} | Ja |
| moduler. {moduleId} .restartPolicy | {”aldrig” \| ”på fel” \| ”på-defekta” \| ”alltid”} | Ja |
| modules.{moduleId}.settings.image | URI: N i modulen avbildningen. | Ja |
| moduler. {moduleId}.settings.createOptions | En Stringified.json med alternativ för att skapa behållaren modulen. [Alternativ för att skapa docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Nej |
| moduler. {moduleId}.configuration.id | ID för den distribution som har distribuerat den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett manifest för distribution. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent rapporterade egenskaper

IoT Edge-agenten rapporterade egenskaper omfattar tre huvudsakliga typer av information:

1. Status för tillämpningen av senaste sett önskade egenskaper.
2. Status för de moduler som för närvarande körs på enheten, som rapporterats av IoT Edge-agent. och
3. En kopia av de egenskaper som körs på enheten.

Den här senaste del av informationen, en kopia av de aktuella önskade egenskaperna är användbar för att berätta om enheten har tillämpat de senast önskade egenskaperna eller är fortfarande kör en tidigare distribution manifest.

> [!NOTE]
> De rapporterade egenskaperna för IoT Edge-agenten är användbara eftersom de kan efterfrågas med den [IoT Hub-frågespråk](../iot-hub/iot-hub-devguide-query-language.md) att undersöka statusen för distributioner i stor skala. Mer information om hur du använder IoT Edge-agentegenskaper för status finns i [förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md).

I följande tabell innehåller inte information som kopieras från önskade egenskaper.

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Heltalet refererar till den senaste versionen av de önskade egenskaperna som bearbetas av IoT Edge-agenten. |
| lastDesiredStatus.code | Den här statuskoden refererar till de senaste önskade egenskaper som setts av IoT Edge-agenten. Tillåtna värden: `200` Åtgärden lyckades `400` ogiltig konfiguration, `412` ogiltig schemaversion `417` önskade egenskaper är tomma, `500` misslyckades |
| lastDesiredStatus.description | Beskrivning av status |
| devicehealth koppla | `healthy` Om Körningsstatus för alla moduler är antingen `running` eller `stopped`, `unhealthy` annars |
| configurationHealth. {deploymentId} .health | `healthy` Om Körningsstatus för alla moduler som angetts för distributionen {deploymentId} är antingen `running` eller `stopped`, `unhealthy` annars |
| runtime.platform.OS | Rapportering av det operativsystem som körs på enheten |
| Runtime.Platform.Architecture | Rapporteringsarkitektur Processorn på enheten |
| systemModules.edgeAgent.runtimeStatus | Rapporterade statusen för IoT Edge-agenten: {”körs” \| ”skadad”} |
| systemModules.edgeAgent.statusDescription | Beskrivning av den rapporterade statusen för IoT Edge-agenten. |
| systemModules.edgeHub.runtimeStatus | Status för IoT Edge hub: {”körs” \| ”stoppad” \| ”misslyckades” \| ”backoff” \| ”skadad”} |
| systemModules.edgeHub.statusDescription | Beskrivning av status för IoT Edge hub om feltillstånd. |
| systemModules.edgeHub.exitCode | Slutkoden som rapporterats av IoT Edge hub behållaren om behållaren avslutas |
| systemModules.edgeHub.startTimeUtc | Tidpunkten då IoT Edge hub senast startades |
| systemModules.edgeHub.lastExitTimeUtc | Tid när IoT Edge hub senast avslutades |
| systemModules.edgeHub.lastRestartTimeUtc | När IoT Edge hub senast startades om |
| systemModules.edgeHub.restartCount | Antal gånger som den här modulen startades som en del av principen för omstart. |
| moduler. {moduleId} .runtimeStatus | Status för modulen: {”körs” \| ”stoppad” \| ”misslyckades” \| ”backoff” \| ”skadad”} |
| moduler. {moduleId} .statusDescription | Beskrivning av status för modulen om feltillstånd. |
| moduler. {moduleId} .exitCode | Slutkoden som rapporterats av modulen behållaren om behållaren avslutas |
| moduler. {moduleId} .startTimeUtc | Tidpunkten då modulen senast startades |
| moduler. {moduleId} .lastExitTimeUtc | Tid när modulen senast avslutades |
| moduler. {moduleId} .lastRestartTimeUtc | När modulen senast startades om |
| moduler. {moduleId} .restartCount | Antal gånger som den här modulen startades som en del av principen för omstart. |

## <a name="edgehub-desired-properties"></a>EdgeHub önskade egenskaper

Modultvillingen för IoT Edge hub kallas `$edgeHub` och samordnar kommunikationen mellan IoT Edge hub som körs på en enhet och IoT Hub. Önskade egenskaper anges när du använder ett manifest för distribution på en specifik enhet som en del av en enskild enhet eller i skala distribution. 

| Egenskap | Beskrivning | Krävs i distributionen manifestet |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara ”1.0” | Ja |
| vägar. {routeName} | En sträng som representerar en IoT Edge hub väg. Mer information finns i [deklarera vägar](module-composition.md#declare-routes). | Den `routes` element kan vara närvarande men tom. |
| storeAndForwardConfiguration.timeToLiveSecs | Tiden i sekunder den IoT Edge-hubben ser till att meddelanden om kopplas bort från routning slutpunkter, om IoT-hubb eller en lokal modul. Värdet kan vara ett positivt heltal. | Ja |

## <a name="edgehub-reported-properties"></a>EdgeHub rapporterade egenskaper

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Heltalet refererar till den senaste versionen av de önskade egenskaperna som bearbetas av IoT Edge hub. |
| lastDesiredStatus.code | Statuskoden som refererar till senaste önskade egenskaper som setts av IoT Edge hub. Tillåtna värden: `200` Åtgärden lyckades `400` ogiltig konfiguration, `500` misslyckades |
| lastDesiredStatus.description | Beskrivning av status. |
| -klienter. {enhet eller moduleId} .status | Statusen för den här enheten eller modulen. Möjliga värden {”ansluten” \| ”frånkopplad”}. Endast modulen identiteter kan vara i frånkopplat läge. Underordnade enheter som ansluter till IoT Edge hub visas bara när du är ansluten. |
| -klienter. {enhet eller moduleId} .lastConnectTime | Senaste tid enheten eller moduler som är anslutna. |
| -klienter. {enhet eller moduleId} .lastDisconnectTime | Senaste gången enheten eller modulen frånkopplad. |

## <a name="next-steps"></a>Nästa steg

Läs hur du använder dessa egenskaper för att bygga ut distribution manifesten i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).
