---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805217"
---
1. Använd den `dps-keygen` kommandoradsverktyget för att generera en anslutningssträng:

    Installera den [nyckeln generator verktyget](https://github.com/Azure/dps-keygen), kör du följande kommando:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Kör följande kommando i de anslutningsinformation som du antecknade tidigare för att generera en anslutningssträng:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Kopiera anslutningssträngen från den `dps-keygen` utdata och använda dem i din kod för enheten.