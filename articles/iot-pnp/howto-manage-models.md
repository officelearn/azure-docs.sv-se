---
title: Hantera förhandsgranskningsmodeller för IoT Plug and Play i databasen| Microsoft Dokument"
description: Hantera enhetskapacitetsmodeller i databasen med Azure Certified for IoT-portalen, Azure CLI och Visual Studio-koden.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159157"
---
# <a name="manage-models-in-the-repository"></a>Hantera modeller i databasen

IoT Plug and Play Preview-modellen lagrar modeller och gränssnitt för enhetskapacitet. Databasen gör att modeller och gränssnitt upptäcks och förbrukningsvaror av lösningsutvecklare.

Det finns tre verktyg som du kan använda för att hantera databasen:

- Azure-certifierad för IoT-portal
- Azure CLI
- Visual Studio-koden

## <a name="model-repositories"></a>Modelldatabaser

Det finns två typer av modelldatabaser för lagring av enhetsfunktioner modeller och gränssnitt:

- Det finns en enda _offentlig databas_ som lagrar enhetskapacitetsmodeller och gränssnitt för enheter i Azure Certified [for IoT-enhetskatalogen](https://aka.ms/iotdevcat). Den här databasen lagrar även [vanliga gränssnitt](./concepts-common-interfaces.md) och [DCM och gränssnitt som publicerats av Microsoft Partners](./howto-onboard-portal.md). Mer information om hur du certifierar en enhet och lägger till dess enhetskapacitetsmodell i den offentliga databasen finns i självstudien [Certifiera din IoT Plug and Play-enhet](./tutorial-certification-test.md).
- Det finns flera _företagsdatabaser_. En företagsdatabas skapas automatiskt för din organisation när du [går ombord på Azure Certified for IoT-portalen](./howto-onboard-portal.md). Du kan använda företagets databas för att lagra modeller och gränssnitt för enhetskapacitet under utveckling och test.

## <a name="azure-certified-for-iot-portal"></a>Azure-certifierad för IoT-portal

I [Azure Certified for IoT-portalen](https://preview.catalog.azureiotsolutions.com)kan du utföra följande uppgifter:

- [Slutför certifieringsprocessen för din IoT-enhet](./tutorial-certification-test.md).
- Hitta IoT Plug and Play-enhetskapacitetsmodeller. Du kan använda dessa modeller för att [snabbt bygga IoT-förberedda enheter och integrera dem med lösningar](./quickstart-connect-pnp-device-solution-node.md).

## <a name="azure-cli"></a>Azure CLI

Azure CLI innehåller kommandon för att hantera modeller och gränssnitt för enhetsfunktioner i IoT Plug and Play offentliga och företagsmodelldatabaser. Mer information finns i [installationstillägget Installera och använda Azure IoT-tillägget för Azure](./howto-install-pnp-cli.md) CLI-hjälpguiden.

## <a name="visual-studio-code"></a>Visual Studio-koden

Så här öppnar du **vyn Modelldatabas** i Visual Studio-kod.

1. Öppna Visual Studio-kod, använd **Ctrl+Skift+P**, skriv och välj **IoT Plug and Play: Öppna modelldatabasen**.

1. Du kan välja att **öppna databasen för offentliga modeller** eller Öppna **organisationsmodellarkivet**. För företagsmodelldatabasen måste du ange anslutningssträngen för modelldatabasen. Du hittar den här anslutningssträngen i [Azure Certified for IoT-portalen](https://preview.catalog.azureiotsolutions.com) på fliken **Anslutningssträngar** för **företagsdatabasen**.

1. En ny flik öppnar **vyn Modelldatabas.**

    Använd den här vyn om du vill lägga till, hämta och ta bort enhetskapacitetsmodeller och gränssnitt. Du kan använda ett filter för att hitta specifika objekt i listan.

1. Om du vill växla mellan företagsmodelldatabasen och den offentliga modelldatabasen använder du **Ctrl+Skift+P**, skriver och väljer **IoT Plug and Play: Logga ut Modelldatabas**. Använd sedan kommandot **IoT Plug and Play: Open Model Repository** igen.

> [!NOTE]
> I VS-kod är den offentliga modelldatabasen skrivskyddad. Microsoft Partners kan uppdatera den offentliga databasen i [Azure Certified for IoT-portalen](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steget är att lära sig hur [du skickar in en IoT Plug and Play-enhet för certifiering](tutorial-certification-test.md).
