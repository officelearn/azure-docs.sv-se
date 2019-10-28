---
title: Övervaka enhets anslutning med Azure IoT Central Explorer
description: Övervaka enhets meddelanden och Observera att enhetens dubbla ändringar görs via IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 09/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 2b1c4c64fc02df67f38e36194072efd5db3b8e38
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953179"
---
# <a name="monitor-device-connectivity-using-azure-cli-preview-features"></a>Övervaka enhets anslutning med Azure CLI (för hands versions funktioner)

*Det här avsnittet gäller för byggare och administratörer.*

Använd Azure CLI IoT-tillägget för att se meddelanden som dina enheter skickar till IoT Central och observera ändringar på enheten. Du kan använda det här verktyget för att felsöka och kontrol lera enhets anslutningen och diagnostisera problem med enhets meddelanden som inte når molnet eller enheter som inte svarar på dubbla ändringar.

[Mer information finns i referensen för Azure CLI-tillägg](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>Krav

+ Azure CLI installerat och är version 2.0.7 eller högre. Kontrol lera versionen av Azure CLI genom att köra `az --version`. Lär dig hur du installerar och uppdaterar från [Azure CLI-dokument](https://docs.microsoft.com/cli/azure/install-azure-cli)
+ Ett arbets-eller skol konto i Azure som har lagts till som en användare i ett IoT Central-program.

## <a name="install-the-iot-central-extension"></a>Installera IoT Central-tillägget

Kör följande kommando från kommando raden för att installera:

```cmd/sh
az extension add --name azure-cli-iot-ext
```

Kontrol lera versionen av tillägget genom att köra 
```cmd/sh
az --version
```
Du bör se tillägget Azure-CLI-IoT-ext 0.8.1 eller högre. Om så inte är fallet, kör
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>Använda tillägget

I följande avsnitt beskrivs vanliga kommandon och alternativ som du kan använda när du kör `az iot central`. Om du vill visa en fullständig uppsättning kommandon och alternativ kan du skicka `--help` till `az iot central` eller något av dess under kommandon.

### <a name="login"></a>Logga in

Börja med att logga in på Azure CLI. 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Hämta program-ID för din IoT Central-app
Kopiera **program-ID**i **Administration/program inställningar**. Du kommer att använda det här i senare steg.

### <a name="monitor-messages"></a>Övervaka meddelanden
Övervaka meddelanden som skickas till din IoT Central-app från dina enheter. Detta inkluderar alla rubriker och anteckningar.

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Visa enhets egenskaper
Visa aktuella egenskaper för läsning och läsning/skrivning för en specifik enhet.

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder IoT Central Explorer är det föreslagna nästa steg att utforska [hantering av enheter IoT Central](howto-manage-devices.md).
