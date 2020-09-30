---
title: Använd ett verktyg för att redigera och validera DTDL-modeller | Microsoft Docs
description: Installera DTDL-redigeraren för Visual Studio Code eller Visual Studio 2019 och Använd den för att redigera IoT Plug and Play-modeller.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 59ea0258d6262f652e9ac563bbda5ec89c4c5819
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580693"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>Installera och Använd DTDL redigerings verktyg

[DTDL-modeller (Digital Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) är JSON-filer. Du kan använda ett tillägg för Visual Studio Code eller Visual Studio 2019 för att redigera och validera dessa modell filer.

## <a name="install-and-use-the-vs-code-extension"></a>Installera och använda VS Code-tillägget

DTDL-tillägget för VS Code lägger till följande DTDL redigerings funktioner:

- DTDL v2-syntax.
- IntelliSense, inklusive komplettera automatiskt, som hjälper dig med språksyntaxen.
- Möjligheten att skapa gränssnitt från kommando paletten.

Om du vill installera DTDL-tillägget går du till [DTDL-redigeraren för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). Du kan också söka efter **DTDL** i vyn tillägg i vs Code.

När du har installerat tillägget använder du det för att skapa DTDL-modell-filer i VS Code:

- Tillägget ger DTDL för autentisering och markerar fel som visas på följande skärm bild:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Modell validering i VS-kod":::

- Använd IntelliSense och Autoavsluta när du redigerar DTDL-modeller:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Modell validering i VS-kod":::

- Skapa ett nytt DTDL-gränssnitt. Följande kommando skapar en JSON-fil med ett nytt gränssnitt. Gränssnittet innehåller exempel på telemetri, egenskaper och kommando definitioner.

## <a name="install-and-use-the-visual-studio-extension"></a>Installera och använda Visual Studio-tillägget

DTDL-tillägget för Visual Studio 2019 lägger till följande DTDL redigerings funktioner:

- DTDL v2-syntax.
- IntelliSense, inklusive komplettera automatiskt, som hjälper dig med språksyntaxen.

Om du vill installera DTDL-tillägget går du till [DTDL språk stöd för VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). Du kan också söka efter **DTDL** i **Hantera tillägg** i Visual Studio.

När du har installerat tillägget kan du använda det för att skapa DTDL-modell-filer i Visual Studio:

- Tillägget ger DTDL för autentisering och markerar fel som visas på följande skärm bild:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Modell validering i VS-kod":::

- Använd IntelliSense och Autoavsluta när du redigerar DTDL-modeller:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Modell validering i VS-kod":::

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig hur du använder DTDL-tilläggen för Visual Studio Code och Visual Studio 2019 för att skapa och validera DTDL-modellens filer. Ett förslag till nästa steg är att lära dig hur du använder [Azure IoT Explorer med dina modeller och enheter](./howto-use-iot-explorer.md).
