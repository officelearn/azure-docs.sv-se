---
title: Övervaka enhets anslutning med Azure IoT Central Explorer
description: Övervaka enhets meddelanden och Observera att enhetens dubbla ändringar görs via IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 38cbe43e9038a47c4e222fd4744f0b844f9ddb4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845681"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Övervaka enhets anslutning med Azure IoT Central Explorer

*Det här avsnittet gäller för byggare och administratörer.*

Använd IoT Central Explorer CLI för att se meddelanden som dina enheter skickar till IoT Central och Observera att ändringarna i IoT Hub är dubbla. Du kan använda det här verktyget med öppen källkod för att få djupare insikt i status för enhets anslutning och diagnostisera problem med enhets meddelanden som inte når molnet eller enheter som inte svarar på dubbla ändringar.

[Besök IOTC-Explorer-lagringsplatsen i GitHub.](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>Förutsättningar

+ Node. js version 8. x eller högre – https://nodejs.org
+ En administratör för ditt program måste generera en åtkomsttoken som du kan använda i IOTC-Explorer

## <a name="install-iotc-explorer"></a>Installera IOTC-Explorer

Kör följande kommando från kommando raden för att installera:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Du måste vanligt vis köra kommandot Install med `sudo` i UNIX-liknande miljöer.

## <a name="run-iotc-explorer"></a>Kör IOTC-Explorer

I följande avsnitt beskrivs vanliga kommandon och alternativ som du kan använda när du kör `iotc-explorer`. Om du vill visa en fullständig uppsättning kommandon och alternativ, `--help` skicka `iotc-explorer` till eller något av dess under kommandon.

### <a name="login"></a>Logga in

Innan du börjar måste du ha en administratör av ditt IoT Central-program för att få en åtkomsttoken som du kan använda. Administratören utför följande steg:

1. Gå till **Administration** och **få åtkomst till tokens**.
1. Välj **generera token**.
    ![Skärm bild av sidan åtkomsttoken](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Ange ett namn på token, Välj **Nästa**och sedan **Kopiera**.
    > [!NOTE]
    > Värdet för token visas bara en gång, så det måste kopieras innan dialog rutan stängs. När du har stängt dialog rutan visas den aldrig igen.

    ![Skärm bild av dialog rutan Kopiera åtkomsttoken](media/howto-use-iotc-explorer/copyaccesstoken.png)

Du kan använda token för att logga in på CLI enligt följande:

```cmd/sh
iotc-explorer login "<Token value>"
```

Om du inte vill att token sparas i din gränssnitts historik kan du lämna token och i stället ange den när du uppmanas till det:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Övervaka enhets meddelanden

Du kan titta på meddelanden från en specifik enhet eller alla enheter i programmet med hjälp `monitor-messages` av kommandot. Det här kommandot startar en Övervakare som kontinuerligt matar ut nya meddelanden när de tas emot:

Kör följande kommando för att se alla enheter i programmet:

```cmd/sh
iotc-explorer monitor-messages
```

Resultat:

![övervaka-meddelanden kommandots utdata](media/howto-use-iotc-explorer/monitormessages.png)

Om du vill se en speciell enhet lägger du bara till enhets-ID i slutet av kommandot:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

Du kan också skriva ut ett mer maskin vänligt format genom att `--raw` lägga till alternativet i kommandot:

```cmd/sh
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Hämta enhetens dubbla

Du kan använda `get-twin` kommandot för att hämta innehållet i den dubbla för en IoT central-enhet. Det gör du genom att köra följande kommando:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Resultat:

![Get-dubbel kommandoutdata](media/howto-use-iotc-explorer/getdevicetwin.png)

Precis som `monitor-messages`med kan du få en mer maskin vänlig utmatning genom att `--raw` skicka alternativet:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder IoT Central Explorer är det föreslagna nästa steg att utforska [hantering av enheter IoT Central](howto-manage-devices.md).
