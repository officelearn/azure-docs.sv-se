---
title: Övervaka enhetsanslutning med Azure IoT Central Explorer
description: Övervaka meddelanden från enheten och se device twin ändras via IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 16cb27ab330118d1bb59cf4f3d782bf55fa28d43
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779750"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Övervaka enhetsanslutning med Azure IoT Central Explorer

*Det här avsnittet gäller builders och administratörer.*

Använd IoT Central Explorer CLI för att se meddelanden enheterna skickar till IoT Central och notera ändringarna i IoT Hub-twin. Du kan använda det här verktyget med öppen källkod för att få bättre insikt i tillståndet för enhetsanslutning och diagnostisera problem med meddelanden som inte når molnet eller enheter som inte svarar på twin ändringar.

[Besök iotc-explorer-lagringsplatsen i GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Förutsättningar

+ Node.js-version 8.x eller högre - https://nodejs.org
+ En administratör för ditt program måste generera en åtkomsttoken som du kan använda i iotc explorer

## <a name="install-iotc-explorer"></a>Installera iotc explorer

Kör följande kommando från kommandoraden och installera:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Vanligtvis måste du köra installationskommandot med `sudo` i Unix-liknande miljöer.

## <a name="run-iotc-explorer"></a>Kör iotc explorer

I följande avsnitt beskrivs vanliga kommandon och alternativ som du kan använda när du kör `iotc-explorer`. Om du vill visa den fullständiga uppsättningen kommandon och alternativ, skicka `--help` till `iotc-explorer` eller någon av dess underkommandon.

### <a name="login"></a>Inloggning

Innan du startar måste du be en administratör för programmet IoT Central och få en åtkomsttoken som du kan använda. Administratören utför följande steg:

1. Gå till **Administration** sedan **åtkomsttoken**.
1. Välj **generera Token**.
    ![Skärmbild för åtkomst-token sidan](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Ange ett Tokennamn, Välj **nästa**, och sedan **kopiera**.
    > [!NOTE]
    > Token-värde visas bara en gång, så det måste kopieras innan du stänger dialogrutan. När du har stängt dialogrutan visas den aldrig igen.

    ![Kopiera åtkomst-token i dialogrutan skärmbild](media/howto-use-iotc-explorer/copyaccesstoken.png)

Du kan använda token för att logga in CLI på följande sätt:

```cmd/sh
iotc-explorer login "<Token value>"
```

Om du föredrar att inte installera token som sparas i historiken shell, kan du lämna token ut och i stället tillhandahålla den när du uppmanas till detta:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Övervaka meddelanden från enhet

Du kan se de meddelanden som skickas från en specifik enhet eller alla enheter i ditt program med hjälp av den `monitor-messages` kommando. Det här kommandot startar en Övervakare som visar kontinuerligt nya meddelanden när de tas emot:

Titta på alla enheter i ditt program, kör du följande kommando:

```cmd/sh
iotc-explorer monitor-messages
```

Utdata:

![Övervakare-meddelanden kommandoutdata](media/howto-use-iotc-explorer/monitormessages.png)

Om du vill se en specifik enhet kan du lägga till enhets-id till slutet av kommandot:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Du kan också skickas ett mer dator-vänlig format genom att lägga till den `--raw` alternativ till kommandot:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Hämta enhetstvilling

Du kan använda den `get-twin` kommando för att hämta innehållet i läsningen för en enhet med IoT Central. Om du vill göra det kör du följande kommando:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Utdata:

![Get-twin-kommandoutdata](media/howto-use-iotc-explorer/getdevicetwin.png)

Precis som med `monitor-messages`, du kan få en mer dator-vänlig utdata genom att skicka den `--raw` alternativet:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder IoT Central Explorer, föreslagna nästa steg är att utforska [hantering av enheter IoT Central](howto-manage-devices.md).
