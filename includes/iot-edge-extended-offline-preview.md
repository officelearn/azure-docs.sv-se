---
title: ta med fil
description: IOT edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004687"
---
## <a name="enabling-extended-offline-operation-preview"></a>Aktivera utökad offline åtgärden (förhandsgranskning)
Från och med den [v1.0.2 versionen](https://aka.ms/edge102) av Edge-körningen Edge-enhet och underordnade enheter som ansluter till den kan konfigureras för utökade offline åtgärden. 

Med den här funktionen lokala moduler eller efterföljande enheter autentiseras igen med Edge-enhet efter behov och kommunicera med varandra med hjälp av meddelanden och metoder även när kopplas bort från IoT Hub. Se den här [blogginlägget](https://aka.ms/iot-edge-offline) och [konceptet artikeln](../articles/iot-edge/offline-capabilities.md) för mer information om omfånget för den här funktionen.

För att aktivera utökad offline i ett gateway-scenario skapar du en överordnad-underordnad relation mellan edge-enhet och efterföljande enheter som ska ansluta till den.

1. Edge bladet med enhetsinformation i IoT Hub-portalen, klickar du på den **hantera underordnade enheter (förhandsversion)** knappen i det översta kommandofältet.

1. Klicka på den **+ Lägg till** knappen.

1. Markera enheterna som underordnade från enhetslistan över och använda högerpilen för att välja de adresser som ska läggas till som underordnade.

1. Klicka på **OK** att bekräfta.

1. I den **ange moduler** skärmbild från Edge-enhetsinformation, klickar du på **konfigurera avancerade Edge-körningsinställningar**, och under **Edge Hub** miljövariabler lägga till en post  **UpstreamProtocol** med värdet **MQTT**. Lägg till samma miljövariabeln och värdet för den **Edge-agenten** samt. 

1. Klicka på **spara** och se till att **skicka** ändringarna när du klickar på **nästa** två gånger.

Edge-enhet och dess underordnade enheter har nu aktiverats för utökade offline igen.  