---
title: OPC Twin-arkitektur – Azure | Microsoft-dokument
description: Den här artikeln innehåller en översikt över OPC Twin-arkitekturen. Den beskriver om identifiering, aktivering, surfning och övervakning av servern.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73819856"
---
# <a name="opc-twin-architecture"></a>OPC Twin-arkitektur

Följande diagram illustrerar OPC Twin-arkitekturen.

## <a name="discover-and-activate"></a>Upptäck och aktivera

1. Operatören aktiverar nätverksskanning på modulen eller gör en engångsidentifiering med hjälp av en identifierings-URL. De identifierade slutpunkterna och programinformationen skickas via telemetri till introduktionsagenten för bearbetning.  OPC UA-enhetens introduktionsagent bearbetar identifieringshändelser för OPC UA-servrar som skickas av OPC Twin IoT Edge-modulen när den är i identifierings- eller skanningsläge. Identifieringshändelserna resulterar i programregistrering och uppdateringar i OPC UA-enhetsregistret.

   ![Så här fungerar OPC Twin](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operatören inspekterar certifikatet för den identifierade slutpunkten och aktiverar den registrerade slutpunktstvillingen för åtkomst. 

   ![Så här fungerar OPC Twin](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Bläddra och övervaka

1. När operatören har aktiverats kan den använda REST API för dubbla tjänster för att bläddra i eller inspektera serverinformationsmodellen, läsa/skriva objektvariabler och anropa metoder.  Användaren använder ett förenklat OPC UA API uttryckt helt i HTTP och JSON.

   ![Så här fungerar OPC Twin](media/overview-opc-twin-architecture/opc-twin3.png)

1. REST-gränssnittet med dubbla tjänster kan också användas för att skapa övervakade objekt och prenumerationer i OPC Publisher. OPC Publisher gör att telemetri kan skickas från OPC UA-serversystem till IoT Hub. Mer information om OPC Publisher finns i [Vad är OPC Publisher](overview-opc-publisher.md).

   ![Så här fungerar OPC Twin](media/overview-opc-twin-architecture/opc-twin4.png)
