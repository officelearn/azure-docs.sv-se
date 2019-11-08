---
title: OPC, dubbel arkitektur – Azure | Microsoft Docs
description: Den här artikeln innehåller en översikt över OPCs dubbla arkitektur. Det beskriver om identifiering, aktivering, bläddring och övervakning av servern.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819856"
---
# <a name="opc-twin-architecture"></a>OPC, dubbel arkitektur

Följande diagram illustrerar OPCs dubbla arkitektur.

## <a name="discover-and-activate"></a>Identifiera och aktivera

1. Operatorn aktiverar nätverks genomsökning i modulen eller utför en engångs identifiering med en identifierings-URL. Identifierade slut punkter och programinformation skickas via telemetri till onboarding-agenten för bearbetning.  Onboarding-agenten för OPC UA Device bearbetar OPC UA Server Discovery-händelser som skickas av den dubbla IoT Edge-modulen i identifierings-eller genomsöknings läge. Identifierings händelser resulterar i program registrering och uppdateringar i OPC UA Device-registret.

   ![Hur OPCs dubbla fungerar](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operatorn kontrollerar certifikatet för den identifierade slut punkten och aktiverar den registrerade slut punkten för åtkomst. 

   ![Hur OPCs dubbla fungerar](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Bläddra och övervaka

1. När den är aktive rad kan operatören använda den dubbla tjänst REST API för att bläddra i eller granska Server informations modellen, läsa/skriva-objektvariabler och anropa metoder.  Användaren använder ett förenklat OPC UA API som uttrycks fullständigt i HTTP och JSON.

   ![Hur OPCs dubbla fungerar](media/overview-opc-twin-architecture/opc-twin3.png)

1. Det dubbla tjänst REST-gränssnittet kan också användas för att skapa övervakade objekt och prenumerationer i OPC-utgivaren. OPC-utgivaren tillåter att telemetri skickas från OPC UA server-system till IoT Hub. Mer information om OPC-utgivare finns i [Vad är OPC Publisher](overview-opc-publisher.md).

   ![Hur OPCs dubbla fungerar](media/overview-opc-twin-architecture/opc-twin4.png)
