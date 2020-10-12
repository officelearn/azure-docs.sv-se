---
title: inkludera fil
description: inkludera fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "70049015"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

I den här artikeln skapar du en backend-tjänst som schemalägger ett jobb för att anropa en direkt metod på en enhet, schemalägger ett jobb för att uppdatera enheten med dubbla och övervakar förloppet för varje jobb. För att utföra dessa åtgärder behöver tjänsten **register Läs** -och **Skriv** behörighet för registret. Varje IoT-hubb skapas som standard med en delad åtkomst princip med namnet **registryReadWrite** som ger behörighet.
