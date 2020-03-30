---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050436"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

I den här artikeln skapar du en backend-tjänst som lägger till önskade egenskaper till en enhetstvilling och sedan frågar identitetsregistret för att hitta alla enheter med rapporterade egenskaper som har uppdaterats i enlighet med detta. Tjänsten behöver **tjänstens anslutningsbehörighet** för att ändra önskade egenskaper för en enhetstvilling, och den behöver **behörigheten registerläsning** för att fråga identitetsregistret. Det finns ingen standardprincip för delad åtkomst som bara innehåller dessa två behörigheter, så du måste skapa en.
