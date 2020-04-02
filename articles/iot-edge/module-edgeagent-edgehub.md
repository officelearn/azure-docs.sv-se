---
title: Egenskaper för agent- och hubbmodulstvillingar - Azure IoT Edge
description: Granska de specifika egenskaperna och deras värden för edgeAgent- och edgeHub-modultvillingarna
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f2d6603c264c9da3f2700f460a8c61b24681fac6
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546192"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>Egenskaper för tvillingarna IoT Edge-agent och IoT Edge-hubmodul

IoT Edge-agenten och IoT Edge-hubben är två moduler som utgör IoT Edge-körningen. Mer information om ansvarsområdena för varje körningsmodul finns [i Förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln innehåller önskade egenskaper och rapporterade egenskaper för runtime-modultvillingarna. Mer information om hur du distribuerar moduler på IoT Edge-enheter finns i [Lär dig hur du distribuerar moduler och upprättar vägar i IoT Edge](module-composition.md).

En modultvilling innehåller:

* **Önskade egenskaper**. Lösningens backend kan ange önskade egenskaper och modulen kan läsa dem. Modulen kan också ta emot meddelanden om ändringar i önskade egenskaper. Önskade egenskaper används tillsammans med rapporterade egenskaper för att synkronisera modulkonfiguration eller villkor.

* **Rapporterade egenskaper**. Modulen kan ange rapporterade egenskaper och lösningens backend kan läsa och fråga dem. Rapporterade egenskaper används tillsammans med önskade egenskaper för att synkronisera modulkonfiguration eller villkor.

## <a name="edgeagent-desired-properties"></a>Önskade egenskaper för EdgeAgent

Modultvillingen för IoT `$edgeAgent` Edge-agenten anropas och samordnar kommunikationen mellan IoT Edge-agenten som körs på en enhet och IoT Hub. De önskade egenskaperna anges när du använder ett distributionsmanifest på en viss enhet som en del av en enhet eller i skalningsdistribution.

| Egenskap | Beskrivning | Krävs |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara "1,0" | Ja |
| runtime.type | Måste vara "docker" | Ja |
| runtime.settings.minDockerVersion | Ange till den minsta Docker-version som krävs av det här distributionsmanifestet | Ja |
| runtime.settings.loggingOptions | En strängfäst JSON som innehåller loggningsalternativen för IoT Edge-agentbehållaren. [Loggningsalternativ för Docker](https://docs.docker.com/engine/admin/logging/overview/) | Inga |
| runtime.settings.registryCredentials<br>. {registryId}.användarnamn | Användarnamnet för behållarregistret. För Azure Container Registry är användarnamnet vanligtvis registernamnet.<br><br> Registerautentiseringsuppgifter är nödvändiga för alla privata modulavbildningar. | Inga |
| runtime.settings.registryCredentials<br>. {registryId}.password {registryId}.password {registryId}.password { | Lösenordet för behållarregistret. | Inga |
| runtime.settings.registryCredentials<br>. {registryId}.address {registryId}.address {registryId}.address { | Adressen till behållarregistret. För Azure Container Registry är adressen vanligtvis *{registry name}.azurecr.io*. | Inga |  
| systemModules.edgeAgent.type | Måste vara "docker" | Ja |
| systemModules.edgeAgent.settings.image | URI för bilden av IoT Edge-agenten. För närvarande kan IoT Edge-agenten inte uppdatera sig själv. | Ja |
| systemModules.edgeAgent.inställningar<br>.createOptions | En strängfäst JSON som innehåller alternativen för att skapa IoT Edge-agentbehållaren. [Skapa alternativ för Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Inga |
| systemModules.edgeAgent.configuration.id | ID:et för distributionen som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett distributionsmanifest. |
| systemModules.edgeHub.type | Måste vara "docker" | Ja |
| systemModules.edgeHub.status | Måste vara "igång" | Ja |
| systemModules.edgeHub.restartPolicy | Måste vara "alltid" | Ja |
| systemModules.edgeHub.settings.image | URI för bilden av IoT Edge-hubben. | Ja |
| systemModules.edgeHub.settings systemModules.edgeHub.settings systemModules.edgeHub.settings systemModul<br>.createOptions | En strängfäst JSON som innehåller alternativen för att skapa IoT Edge-hubbbehållaren. [Skapa alternativ för Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Inga |
| systemModules.edgeHub.configuration.id | ID:et för distributionen som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett distributionsmanifest. |
| Modules. {moduleId}.version | En användardefinierad sträng som representerar versionen av den här modulen. | Ja |
| Modules. {moduleId}.type | Måste vara "docker" | Ja |
| Modules. {moduleId}.status | {"running" \| "stopped"} | Ja |
| Modules. {moduleId}.restartPolicy | {"never" \| "on-failure" \| "on-unhealthy" \| "always"} | Ja |
| Modules. {moduleId}.imagePullPolicy | {"on-create" \| "never"} | Inga |
| Modules. {moduleId}.env | En lista över miljövariabler som ska gå vidare till modulen. Tar formatet`"<name>": {"value": "<value>"}` | Inga |
| Modules. {moduleId}.settings.image {moduleId}.settings.image {moduleId}.settings.image {module | URI:en till modulavbildningen. | Ja |
| Modules. {moduleId}.settings.createOptions | En strängfäst JSON som innehåller alternativen för att skapa modulbehållaren. [Skapa alternativ för Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | Inga |
| Modules. {moduleId}.configuration.id | ID:et för distributionen som distribuerade den här modulen. | IoT Hub anger den här egenskapen när manifestet tillämpas med hjälp av en distribution. Inte en del av ett distributionsmanifest. |

## <a name="edgeagent-reported-properties"></a>EdgeAgent rapporterade egenskaper

IoT Edge-agentens rapporterade egenskaper innehåller tre huvudinformation:

1. Status för tillämpningen av de senast sedda önskade egenskaperna.
2. Status för de moduler som för närvarande körs på enheten, som rapporterats av IoT Edge-agenten. Och
3. En kopia av de önskade egenskaperna som för närvarande körs på enheten.

Kopian av de aktuella önskade egenskaperna är användbar för att avgöra om enheten har tillämpat den senaste distributionen eller fortfarande kör ett tidigare distributionsmanifest.

> [!NOTE]
> De rapporterade egenskaperna för IoT Edge-agenten är användbara eftersom de kan efterfrågas med [frågespråket IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) för att undersöka status för distributioner i stor skala. Mer information om hur du använder IoT Edge-agentegenskaperna för status finns i [Förstå IoT Edge-distributioner för enskilda enheter eller i skala](module-deployment-monitoring.md).

I följande tabell ingår inte den information som kopieras från de önskade egenskaperna.

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Det här heltalet refererar till den senaste versionen av de önskade egenskaperna som bearbetas av IoT Edge-agenten. |
| lastDesiredStatus.code | Den här statuskoden refererar till de senast önskade egenskaperna som visas av IoT Edge-agenten. Tillåtna `200` värden: `400` Lyckad, Ogiltig `412` `417` konfiguration, Ogiltig schemaversion, de önskade egenskaperna är tomma, `500` Misslyckades |
| lastDesiredStatus.description | Textbeskrivning av status |
| deviceHealth | `healthy`om körningsstatusen för alla `running` moduler `stopped` `unhealthy` är antingen eller , på annat sätt |
| konfigurationHälsa. {deploymentId}.health {deploymentId}.health {deploymentId}.health {deployment | `healthy`om körningsstatusen för alla moduler som anges av `running` distributionen {deploymentId} är antingen eller `stopped`, `unhealthy` på annat sätt |
| runtime.platform.OS | Rapportera att operativsystemet körs på enheten |
| runtime.platform.architecture | Rapportera cpu-processorns arkitektur på enheten |
| systemModules.edgeAgent.runtimeStatus | Den rapporterade statusen för IoT Edge-agenten: {"kör" \| "ohälsosamt"} |
| systemModules.edgeAgent.statusDeskription | Textbeskrivning av den rapporterade statusen för IoT Edge-agenten. |
| systemModules.edgeHub.runtimeStatus | Status för IoT Edge-hubben: \| { \| "running" "stopped" "failed" \| "backoff" \| "unhealthy" } |
| systemModules.edgeHub.statusDescription | Textbeskrivning av status för IoT Edge-hubben om det inte är fel. |
| systemModules.edgeHub.exitCode | Stängningskoden som rapporteras av IoT Edge-hubbbehållaren om behållaren avslutas |
| systemModules.edgeHub.startTimeUtc | Tid när IoT Edge-hubben senast startades |
| systemModules.edgeHub.lastExitTimeUtc | Tid när IoT Edge-hubben senast avslutades |
| systemModules.edgeHub.lastRestartTimeUtc | Tid när IoT Edge-hubben senast startades om |
| systemModules.edgeHub.restartCount | Antal gånger som modulen har startats om som en del av omstartsprincipen. |
| Modules. {moduleId}.runtimeStatus {moduleId}.runtimeStatus {moduleId}.runtimeStatus {module | Status för modulen: { \| "running" "stopped" \| "failed" \| "backoff" \| "unhealthy" } |
| Modules. {moduleId}.statusDescription | Textbeskrivning av modulens status om den inte är fel. |
| Modules. {moduleId}.exitCode | Stängningskoden som rapporteras av modulbehållaren om behållaren avslutas |
| Modules. {moduleId}.startTimeUtc | Tid när modulen senast startades |
| Modules. {moduleId}.lastExitTimeUtc {moduleId}.lastExitTimeUtc {moduleId}.lastExitTimeUtc {module | Tid när modulen senast avslutades |
| Modules. {moduleId}.lastRestartTimeUtc | Tid då modulen senast startades om |
| Modules. {moduleId}.restartCount | Antal gånger som modulen har startats om som en del av omstartsprincipen. |

## <a name="edgehub-desired-properties"></a>Önskade egenskaper för EdgeHub

Modultvillingen för IoT `$edgeHub` Edge-hubben anropas och samordnar kommunikationen mellan IoT Edge-hubben som körs på en enhet och IoT Hub. De önskade egenskaperna anges när du använder ett distributionsmanifest på en viss enhet som en del av en enhet eller i skalningsdistribution.

| Egenskap | Beskrivning | Krävs i distributionsmanifestet |
| -------- | ----------- | -------- |
| schemaVersion | Måste vara "1,0" | Ja |
| Vägar. {routename} | En sträng som representerar en IoT Edge-hubbväg. Mer information finns i [Deklarera vägar](module-composition.md#declare-routes). | Elementet `routes` kan vara närvarande men tomt. |
| storeAndForwardConfiguration.timeToLiveSecs | Tiden i sekunder som IoT Edge-hubben behåller meddelanden om de kopplas från routningsslutpunkter, oavsett om det är IoT Hub eller en lokal modul. Värdet kan vara vilket positivt heltal som helst. | Ja |

## <a name="edgehub-reported-properties"></a>EdgeHub-rapporterade egenskaper

| Egenskap | Beskrivning |
| -------- | ----------- |
| lastDesiredVersion | Det här heltalet refererar till den senaste versionen av de önskade egenskaperna som bearbetas av IoT Edge-hubben. |
| lastDesiredStatus.code | Statuskoden som refererar till önskade egenskaper senast som visas av IoT Edge-hubben. Tillåtna `200` värden: `400` Lyckad, Ogiltig konfiguration, `500` Misslyckades |
| lastDesiredStatus.description | Textbeskrivning av statusen. |
| Klienter. {device or moduleId}.status {device or moduleId}.status {device or moduleId}.status {device | Anslutningsstatusen för den här enheten eller modulen. Möjliga värden {"anslutna" \| "frånkopplad"}. Endast modulidentiteter kan vara i frånkopplat tillstånd. Nedströmsenheter som ansluter till IoT Edge-hubben visas bara när de är anslutna. |
| Klienter. {device or moduleId}.lastConnectTime {device or moduleId}.lastConnectTime {device or moduleId}.lastConnectTime {device | Senast enheten eller modulen var ansluten. |
| Klienter. {device or moduleId}.lastDisconnectTime {device or moduleId}.lastDisconnectTime {device or moduleId}.lastDisconnectTime {device | Senast enheten eller modulen kopplades från. |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder dessa egenskaper för att skapa distributionsmanifest finns i [Förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).
