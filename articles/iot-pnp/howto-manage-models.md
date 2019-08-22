---
title: Hantera IoT Plug and Play Preview-modeller i lagrings platsen | Microsoft Docs
description: Hantera enhets kapacitets modeller i lagrings platsen med hjälp av Azure-certifierad för IoT-portalen, Azure CLI och Visual Studio Code.
author: YasinMSFT
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: yahajiza
ms.openlocfilehash: ec36bef9c2b65a5c88a52621bbf3a1db708c235c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880506"
---
# <a name="manage-models-in-the-repository"></a>Hantera modeller i databasen

IoT Plug and Play för hands versions modellen lagrar enhets kapacitets modeller och gränssnitt. Lagrings platsen gör att modeller och gränssnitt kan identifieras och konsumeras av lösnings utvecklare.

Det finns tre verktyg som du kan använda för att hantera lagrings platsen:

- Azure-certifierad för IoT-portalen
- Azure CLI
- Visual Studio-koden

## <a name="model-repositories"></a>Modell databaser

Det finns två typer av modell lager för lagring av enhets kapacitets modeller och gränssnitt:

- Det finns ett enda _offentligt lagrings lager_ som lagrar enhets kapacitets modeller och-gränssnitt för enheter i [katalogen Azure Certified för IoT-enheter](https://aka.ms/iotdevcat). Den här lagrings platsen lagrar också [vanliga gränssnitt](./concepts-common-interfaces.md) och [DCMs och gränssnitt som publicerats av Microsoft-partner](./howto-onboard-portal.md). Information om hur du certifierar en enhet och lägger till dess enhets kapacitets modell i den offentliga lagrings platsen finns i självstudien [certifiera din IoT plug and Play-enhet](./tutorial-certification-test.md).
- Det finns flera _företags databaser_. En företags databas skapas automatiskt för din organisation när du registrerar dig [på Azure-certifierad för IoT-portalen](./howto-onboard-portal.md). Du kan använda företagets lagrings plats för att lagra dina enhets kapacitets modeller och gränssnitt under utveckling och testning.

## <a name="azure-certified-for-iot-portal"></a>Azure-certifierad för IoT-portalen

I [Azure-certifierad för IoT-portalen](https://preview.catalog.azureiotsolutions.com)kan du utföra följande uppgifter:

- [Slutför certifierings processen för din IoT-enhet](./tutorial-certification-test.md).
- Hitta IoT Plug and Play enhets kapacitets modeller. Du kan använda dessa modeller för att [snabbt bygga IoT Ready-enheter och integrera dem med lösningar](./quickstart-connect-pnp-device-solution.md).

## <a name="azure-cli"></a>Azure CLI

Azure CLI innehåller kommandon för att hantera enhets kapacitets modeller och gränssnitt i IoT Plug and Play offentliga och företagets modell databaser.

## <a name="visual-studio-code"></a>Visual Studio-koden

För att öppna vyn **modell databas** i Visual Studio Code.

1. Öppna Visual Studio Code, Använd **Ctrl + Shift + P**, skriv och välj **IoT-plug and Play: Öppna modellens**lagrings plats.

1. Du kan välja att **öppna den offentliga modell databasen** eller **Öppna organisationens modell lagrings plats**. För företags modellens lagrings plats måste du ange anslutnings strängen för modell databasen. Du hittar den här anslutnings strängen i [Azure-certifierad för IoT-portalen](https://preview.catalog.azureiotsolutions.com) på fliken **anslutnings strängar** för din **företags lagrings plats**.

1. En ny flik öppnar vyn **modell databas** .

    Använd den här vyn för att lägga till, ladda ned och ta bort enhets kapacitets modeller och gränssnitt. Du kan använda ett filter för att hitta vissa objekt i listan.

1. Om du vill växla mellan företagets modell databas och den offentliga modellens lagrings plats använder du **Ctrl + Shift + P**, skriver och väljer **IoT plug and Play: Logga ut modell lagrings plats**. Använd **sedan IoT-plug and Play: Öppna modell databas** kommandot igen.

> [!NOTE]
> I VS Code är den offentliga modellens lagrings plats skrivskyddad. Microsoft-partner kan uppdatera den offentliga lagrings platsen i [Azure Certified för IoT-portalen](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Nästa steg

Det föreslagna nästa steg är att lära dig att [skicka en IoT plug and Play-enhet för certifiering](tutorial-certification-test.md).
