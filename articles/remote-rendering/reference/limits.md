---
title: Begränsningar
description: Kodbegränsningar för SDK-funktioner
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680341"
---
# <a name="limits"></a>Begränsningar

Ett antal funktioner har storleks- eller räknebegränsningar på grund av interna detaljer om det pågående systemet.

## <a name="azure-frontend"></a>Azure Frontend

* Totalt AzureFrontend-instanser per process: 16.
* Totalt Antal AzureSession-instanser per AzureFrontend: 16.

## <a name="objects"></a>Objekt

* Totalt tillåtna objekt av en enda typ (Entitet, CutPlaneComponent, etc.): 16 777 215.
* Totalt tillåtna aktiva snittplan: 8.

## <a name="materials"></a>Material

* Totalt tillåtna material i en tillgång: 65 535.

## <a name="overall-number-of-polygons"></a>Totalt antal polygoner

Det tillåtna antalet polygoner för alla inlästa modeller beror på storleken på den virtuella datorn som skickas till [rest-API:et för sessionshantering:](../how-tos/session-rest-api.md#create-a-session)

| Storlek på virtuell dator | Maximalt antal polygoner |
|:--------|:------------------|
|standard| 20 miljoner |
|Premium| ingen gräns |



