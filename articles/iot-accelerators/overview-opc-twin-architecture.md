---
title: OPC-Twin-arkitektur – Azure | Microsoft Docs
description: OPC-Twin-arkitektur
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 002e2e06201688638df98e16c45282187f593a3a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444328"
---
# <a name="opc-twin-architecture"></a>OPC-Twin-arkitektur

Följande diagram visar OPC-Twin-arkitekturen.

## <a name="discover-and-activate"></a>Identifiera och aktivera

1. Operatorn aktiverar nätverk i modulen eller gör en gång identifiering med hjälp av en identifierings-URL. Identifierade slutpunkter och programinformation skickas via telemetri till onboarding-agenten för bearbetning.  OPC UA device onboarding agenten bearbetar OPC UA-servern identifiering händelser skickas från OPC Twin IoT Edge-modul i identifiering eller skanning läge. Identifiering av händelser resultatet i appregistrering och uppdateringar i enhetsregistret OPC UA.

   ![Så här fungerar OPC-Twin](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operatorn inspekterar certifikatet för den identifierade slutpunkten och aktiverar den registrerade endpoint läsningen för åtkomst. 

   ![Så här fungerar OPC-Twin](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Bläddra och övervaka

1. När aktiverat kan använda operatorn Twin service REST API Bläddra eller granska information servermodellen, Läs/Skriv objektvariabler och anropa metoder.  Användaren använder ett förenklat OPC UA API fullständigt uttrycks i HTTP och JSON.

   ![Så här fungerar OPC-Twin](media/overview-opc-twin-architecture/opc-twin3.png)

1. Twin service REST-gränssnittet kan också användas för att skapa övervakade objekt och prenumerationer i OPC-utgivaren. OPC Publisher kan telemetri skickas från OPC UA-serversystem till IoT Hub. Mer information om OPC Publisher finns i [vad är OPC Publisher](overview-opc-publisher.md).

   ![Så här fungerar OPC-Twin](media/overview-opc-twin-architecture/opc-twin4.png)
