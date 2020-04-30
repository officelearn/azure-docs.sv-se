---
title: Övervaka enhets anslutning med Azure IoT Central Explorer
description: Övervaka enhets meddelanden och Observera att enhetens dubbla ändringar görs via IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 1a6106a45f5062850ceb12205528a05ed1d494be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81756666"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Övervaka enhetsanslutningar med Azure CLI

*Det här avsnittet gäller enhets utvecklare och Solution Builder.*

Använd Azure CLI IoT-tillägget för att se meddelanden som dina enheter skickar till IoT Central och observera ändringar på enheten. Du kan använda det här verktyget för att felsöka och kontrol lera enhets anslutningen och diagnostisera problem med enhets meddelanden som inte når molnet eller enheter som inte svarar på dubbla ändringar.

[Mer information finns i referensen för Azure CLI-tillägg](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Krav

+ Azure CLI installerat och är version 2.0.7 eller högre. Kontrol lera versionen av Azure CLI genom att köra `az --version`. Lär dig hur du installerar och uppdaterar från [Azure CLI-dokument](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Ett arbets-eller skol konto i Azure som har lagts till som en användare i ett IoT Central-program.

## <a name="install-the-iot-central-extension"></a>Installera IoT Central-tillägget

Kör följande kommando från kommando raden för att installera:

```azurecli
az extension add --name azure-iot
```

Kontrol lera versionen av tillägget genom att köra:

```azurecli
az --version
```

Du bör se att Azure-IoT-tillägget är 0.8.1 eller högre. Om så inte är fallet kör du:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Använda tillägget

I följande avsnitt beskrivs vanliga kommandon och alternativ som du kan använda när du kör `az iot central`. Om du vill visa en fullständig uppsättning kommandon och alternativ, `--help` skicka `az iot central` till eller något av dess under kommandon.

### <a name="login"></a>Inloggning

Börja med att logga in på Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Hämta program-ID för din IoT Central-app
Kopiera **program-ID**i **Administration/program inställningar**. Du använder det här värdet i senare steg.

### <a name="monitor-messages"></a>Övervaka meddelanden
Övervaka meddelanden som skickas till din IoT Central-app från dina enheter. Utdata innehåller alla rubriker och anteckningar.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Visa enhets egenskaper
Visa aktuella egenskaper för läsning och läsning/skrivning för en specifik enhet.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Nästa steg

Om du är en enhets utvecklare är ett förslag till nästa steg att läsa om [enhets anslutning i Azure IoT Central](./concepts-get-connected.md).
