---
title: Inbyggda edgeAgent Direct-metoder – Azure IoT Edge
description: Övervaka och hantera en IoT Edge-distribution med hjälp av inbyggda direkta metoder i modulen IoT Edge agent körning
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240353"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Kommunicera med edgeAgent med hjälp av inbyggda direkta metoder

Övervaka och hantera IoT Edge-distributioner med hjälp av de direkta metoder som ingår i modulen för IoT Edge agent. Direkta metoder implementeras på enheten och kan sedan anropas från molnet. IoT Edge agenten innehåller direkta metoder som hjälper dig att övervaka och hantera dina IoT Edge-enheter via fjärr anslutning.

Mer information om direkta metoder, hur du använder dem och hur du implementerar dem i dina egna moduler finns i [förstå och anropa direkt metoder från IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Namnen på dessa direkta metoder hanteras Skift läges okänsliga.

## <a name="ping"></a>Pinga

**Ping** -metoden är användbar för att kontrol lera om IoT Edge körs på en enhet eller om enheten har en öppen anslutning till IoT Hub. Använd den här direkta metoden för att pinga IoT Edge-agenten och hämta dess status. En lyckad ping returnerar en tom nytto last och **"status": 200**.

Ett exempel:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

I Azure Portal anropar du metoden med metod namnet `ping` och en tom JSON-nyttolast. `{}`

![Anropa direkt metoden ping i Azure Portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Starta om modul

**RestartModule** -metoden möjliggör fjärrhantering av moduler som körs på en IoT Edge enhet. Om en modul rapporterar ett felaktigt tillstånd eller ett dåligt fel, kan du aktivera den IoT Edge agenten för att starta om den. Ett slutfört omstart-kommando returnerar en tom nytto last och **"status": 200**.

Metoden RestartModule finns i IoT Edge version 1.0.9 och senare. 

Du kan använda metoden RestartModule Direct i alla moduler som körs på en IoT Edge enhet, inklusive själva edgeAgent-modulen. Men om du använder den här direkta metoden för att stänga av edgeAgent får du inte ett lyckat resultat eftersom anslutningen avbryts medan modulen startas om.

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

Anropa metoden med metod namnet `RestartModule` och följande JSON-nyttolast i Azure Portal:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Anropa Direct-metoden ' RestartModule ' i Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Experimentella metoder

Nya alternativ för direkt metod är tillgängliga som experimentella funktioner att testa, inklusive:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): Hämta modul loggar och ladda upp dem till Azure Blob Storage.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): kontrol lera statusen för en begäran om uppladdning av loggar.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): Hämta modul loggar infogat i svaret på den direkta metoden.

## <a name="next-steps"></a>Nästa steg

[Egenskaperna för IoT Edge agenten och IoT Edge Hub-modulen är dubbla](module-edgeagent-edgehub.md)
