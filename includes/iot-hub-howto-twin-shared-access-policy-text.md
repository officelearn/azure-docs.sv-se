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
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403960"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

I den här artikeln skapar du en backend-tjänst som lägger till önskade egenskaper till en enhet och skickar sedan en fråga till identitets registret för att hitta alla enheter med rapporterade egenskaper som har uppdaterats därefter. Tjänsten behöver tjänstens **Connect-** behörighet för att ändra önskade egenskaper för en enhet, och den måste ha **Läs** behörighet för registret för att fråga identitets registret. Det finns ingen standard princip för delad åtkomst som bara innehåller dessa två behörigheter, så du måste skapa en.
