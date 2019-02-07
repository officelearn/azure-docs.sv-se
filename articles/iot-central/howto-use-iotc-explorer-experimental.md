---
title: Övervaka enhetsanslutning med Azure IoT Central Explorer
description: Övervaka meddelanden från enheten och se device twin ändras via IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: e510cfbd89ab8dcd8dccd9a8b95a49a057c9b54f
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824300"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Övervaka enhetsanslutning med Azure IoT Central Explorer

*Det här avsnittet gäller builders och administratörer.*

Använd IoT Central Explorer CLI för att se meddelanden enheterna skickar till IoT Central och notera ändringarna i IoT Hub-twin. Du kan använda det här verktyget med öppen källkod för att få bättre insikt i tillståndet för enhetsanslutning och diagnostisera problem med meddelanden som inte når molnet eller enheter som inte svarar på twin ändringar.

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[Besök iotc-explorer-lagringsplatsen i GitHub](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Förutsättningar
+ Node.js-version 8.x eller högre - https://nodejs.org
+ Du måste hämta administratör för din app för att generera en åtkomsttoken som du kan använda i iotc explorer

## <a name="installing-iotc-explorer"></a>Installera iotc-explorer

Kör följande kommando från kommandoraden och installera:

```
npm install -g iotc-explorer
```

> [!NOTE]
> Behöver du vanligtvis köra installationskommandot med `sudo` i Unix-liknande miljöer.

## <a name="running-iotc-explorer"></a>Kör iotc-explorer

Nedan följer några kommandon och gemensamma alternativ som du kan köra när du använder `iotc-explorer`. Om du vill visa den fullständiga uppsättningen kommandon och alternativ, kan du skicka `--help` till `iotc-explorer` eller någon av dess underkommandon.

### <a name="login"></a>Inloggning

Innan du startar måste du be en administratör för programmet IoT Central och få en åtkomsttoken som du kan använda. Administratören utför följande steg:
1. Gå till **Administration/åtkomsttoken**. 
1. Klicka på **generera**.
    ![Skärmbild för åtkomst-token sidan](media/howto-use-iotc-explorer-experimental/accesstokenspage.png)

1. Ange ett Tokennamn, klickar du på **nästa**, och **kopiera Token värdet**.
    > [!NOTE]
    > Token-värde visas bara en gång, så det måste kopieras innan du stänger dialogrutan. När du har stängt dialogrutan visas aldrig det igen.

    ![Kopiera åtkomst-token i dialogrutan skärmbild](media/howto-use-iotc-explorer-experimental/copyaccesstoken.png)

Du kan sedan använda den token för att logga in på CLI genom att köra:

```sh
iotc-explorer login "<Token value>"
```

Om du inte vill hellre ha token sparas i historiken shell, kan du lämna token ut och i stället tillhandahålla den när du uppmanas till detta:

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Övervaka meddelanden från enhet

Du kan se de meddelanden som skickas från en specifik enhet eller alla enheter i ditt program med hjälp av den `monitor-messages` kommando. Detta startar en Övervakare som resulterar i att kontinuerligt nya meddelanden när de kommer in.

Titta på alla enheter i ditt program, kör du följande kommando:

```
iotc-explorer monitor-messages
```

Utdata:

![Övervakare-meddelanden kommandoutdata](media/howto-use-iotc-explorer-experimental/monitormessages.png)

Om du vill se en specifik enhet kan du lägga till enhets-ID till slutet av kommandot:

```
iotc-explorer monitor-messages <your-device-id>
```

Du kan också ha ett mer datorn eget format för kommandoutdata genom att lägga till den `--raw` alternativ till kommandot:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Hämta enhetstvilling

Du kan använda den `get-twin` kommando för att hämta innehållet i läsningen för en enhet med IoT Central. Om du vill göra det kör du följande kommando:

```
iotc-explorer get-twin <your-device-id>
```

Utdata:

![Get-twin-kommandoutdata](media/howto-use-iotc-explorer-experimental/getdevicetwin.png)

Precis som med `monitor-messages`, du kan få en mer dator-vänlig utdata genom att skicka den `--raw` alternativet:

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du använder IoT Central Explorer, föreslagna nästa steg är att utforska [hantering av enheter IoT Central](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
