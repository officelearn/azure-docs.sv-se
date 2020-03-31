---
title: Felsökning av Azure IoT Hub-fel 409002 LinkCreationConflict
description: Förstå hur du åtgärdar fel 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3c7515be573a0b74a39a77a91fbc554862c7f7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960781"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

I den här artikeln beskrivs orsaker och lösningar för **409002 LinkCreationConflict-fel.**

## <a name="symptoms"></a>Symtom

Felet **409002 LinkCreationConflict** loggade in i diagnostikloggar tillsammans med frånkoppling av enheter eller meddelandefel från molnet till enheten. 

<!-- When using AMQP? -->

## <a name="cause"></a>Orsak

I allmänhet inträffar det här felet när IoT Hub upptäcker att en klient har mer än en anslutning. Faktum är att när en ny anslutningsbegäran anländer för en enhet med en befintlig anslutning, stänger IoT Hub den befintliga anslutningen med det här felet.

### <a name="cause-1"></a>Orsak 1

I det vanligaste fallet gör ett separat problem (till exempel [404104 DeviceConnectionClosedRemotely)](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)att enheten kopplas från. Enheten försöker återupprätta anslutningen omedelbart, men IoT Hub anser fortfarande att enheten är ansluten. IoT Hub stänger den tidigare anslutningen och loggar det här felet.

### <a name="cause-2"></a>Orsak 2

Felaktig logik på enheten gör att enheten upprättar anslutningen när en redan är öppen.

## <a name="solution"></a>Lösning

Det här felet visas vanligtvis som en bieffekt av ett annat, tillfälligt problem, så leta efter andra fel i loggarna för att felsöka ytterligare. Annars måste du bara utfärda en ny anslutningsbegäran om anslutningen sjunker.
