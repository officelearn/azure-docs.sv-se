---
title: Övervaka enhetsanslutning med Azure IoT Central Explorer
description: Övervaka enhetsmeddelanden och observera enhetstvillingändringar via IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 1a6106a45f5062850ceb12205528a05ed1d494be
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756666"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Övervaka enhetsanslutningar med Azure CLI

*Det här avsnittet gäller enhetsutvecklare och lösningsbyggare.*

Använd Azure CLI IoT-tillägget för att se meddelanden som dina enheter skickar till IoT Central och observera ändringar i enhetstvillingen. Du kan använda det här verktyget för att felsöka och observera enhetsanslutning och diagnostisera problem med enhetsmeddelanden som inte når molnet eller enheter som inte svarar på dubbla ändringar.

[Mer information finns i referensen för Azure CLI-tillägg](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/central?view=azure-cli-latest)

## <a name="prerequisites"></a>Krav

+ Azure CLI installerat och är version 2.0.7 eller senare. Kontrollera versionen av din Azure `az --version`CLI genom att köra . Lär dig hur du installerar och uppdaterar från [Azure CLI-dokumenten](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Ett arbets- eller skolkonto i Azure, som läggs till som användare i ett IoT Central-program.

## <a name="install-the-iot-central-extension"></a>Installera IoT Central-tillägget

Kör följande kommando från kommandoraden för att installera:

```azurecli
az extension add --name azure-iot
```

Kontrollera versionen av tillägget genom att köra:

```azurecli
az --version
```

Du bör se azure-iot-tillägget är 0.8.1 eller högre. Om så inte är det kör du:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Använda tillägget

I följande avsnitt beskrivs vanliga kommandon och alternativ `az iot central`som du kan använda när du kör . Om du vill visa hela uppsättningen `--help` kommandon `az iot central` och alternativ går du vidare till eller någon av dess underkommandon.

### <a name="login"></a>Inloggning

Börja med att logga in på Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Hämta program-ID för din IoT Central-app
Kopiera **program-ID:t**i **administrations-/programinställningar**. Du använder det här värdet i senare steg.

### <a name="monitor-messages"></a>Övervaka meddelanden
Övervaka de meddelanden som skickas till din IoT Central-app från dina enheter. Utdata innehåller alla rubriker och anteckningar.

```azurecli
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Visa enhetsegenskaper
Visa de aktuella läs- och läs-/skrivenhetsegenskaperna för en viss enhet.

```azurecli
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Nästa steg

Om du är enhetsutvecklare är ett förslag på nästa steg att läsa om [enhetsanslutning i Azure IoT Central](./concepts-get-connected.md).
