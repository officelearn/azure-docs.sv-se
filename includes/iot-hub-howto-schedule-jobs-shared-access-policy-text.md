---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402653"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

I den här artikeln skapar du en backend-tjänst som schemalägger ett jobb för att anropa en direkt metod på en enhet, schemalägger ett jobb för att uppdatera enheten, och övervakar förloppet för varje jobb. För att utföra dessa åtgärder behöver tjänsten **register Läs** -och **Skriv** behörighet för registret. Som standard skapas varje IoT Hub med en delad åtkomst princip med namnet **registryReadWrite** som ger behörighet.
