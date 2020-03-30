---
title: Inbyggda edgeAgent-direktmetoder - Azure IoT Edge
description: Övervaka och hantera en IoT Edge-distribution med hjälp av inbyggda direkta metoder i IoT Edge-agentkörningsmodulen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240353"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Kommunicera med edgeAgent med hjälp av inbyggda direkta metoder

Övervaka och hantera IoT Edge-distributioner med hjälp av de direkta metoder som ingår i IoT Edge-agentmodulen. Direkta metoder implementeras på enheten och kan sedan anropas från molnet. IoT Edge-agenten innehåller direkta metoder som hjälper dig att fjärrövervaka och hantera dina IoT Edge-enheter.

Mer information om direkta metoder, hur du använder dem och hur du implementerar dem i dina egna moduler finns i [Förstå och anropa direkta metoder från IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Namnen på dessa direkta metoder hanteras skiftlägesokänsliga.

## <a name="ping"></a>Pinga

**Ping-metoden** är användbar för att kontrollera om IoT Edge körs på en enhet eller om enheten har en öppen anslutning till IoT Hub. Använd den här direktmetoden för att pinga IoT Edge-agenten och få dess status. En lyckad ping returnerar en tom nyttolast och **"status": 200**.

Ett exempel:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Anropa metoden med metodnamnet `ping` och en tom JSON-nyttolast i Azure-portalen `{}`.

![Anropa direktmetoden "ping" i Azure-portalen](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Starta om modul

**Metoden RestartModule** möjliggör fjärrhantering av moduler som körs på en IoT Edge-enhet. Om en modul rapporterar ett misslyckat tillstånd eller annat felfritt beteende kan du utlösa IoT Edge-agenten för att starta om den. Ett lyckat omstartskommando returnerar en tom nyttolast och **"status": 200**.

Metoden RestartModule finns i IoT Edge version 1.0.9 och senare. 

Du kan använda metoden RestartModule direkt på valfri modul som körs på en IoT Edge-enhet, inklusive edgeAgent-modulen själv. Men om du använder den här direktmetoden för att stänga av edgeAgent får du inget lyckat resultat eftersom anslutningen avbryts medan modulen startas om.

Ett exempel:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Anropa metoden med metodnamnet `RestartModule` och följande JSON-nyttolast i Azure-portalen:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Anropa direktmetoden 'RestartModule' i Azure portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Experimentella metoder

Nya direktmetodalternativ finns tillgängliga som experimentella funktioner att testa, inklusive:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): Hämta modulloggar och ladda upp dem till Azure Blob Storage.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Kontrollera status för en begäran om uppladdningsloggar.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): Hämta modulloggar infogade i svaret på den direkta metoden.

## <a name="next-steps"></a>Nästa steg

[Egenskaper för tvillingarna IoT Edge-agent och IoT Edge-hubmodul](module-edgeagent-edgehub.md)
