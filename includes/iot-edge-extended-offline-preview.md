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
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264027"
---
## <a name="enabling-extended-offline-operation-preview"></a>Aktivera utökad offline åtgärden (förhandsgranskning)
Med den [v1.0.4 versionen](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) av Edge-körningen Edge-enhet och underordnade enheter som ansluter till den kan konfigureras för utökade offline åtgärden. 

Med den här funktionen lokala moduler eller efterföljande enheter autentiseras igen med Edge-enhet efter behov och kommunicera med varandra med hjälp av meddelanden och metoder även när kopplas bort från IoT Hub. Se den här [blogginlägget](https://aka.ms/iot-edge-offline) och [konceptet artikeln](../articles/iot-edge/offline-capabilities.md) för mer information om omfånget för den här funktionen.

För att aktivera utökad offline i ett gateway-scenario skapar du en överordnad-underordnad relation mellan edge-enhet och efterföljande enheter som ska ansluta till den.

1. Edge bladet med enhetsinformation i IoT Hub-portalen, klickar du på den **hantera underordnade enheter (förhandsversion)** knappen i det översta kommandofältet.

1. Klicka på den **+ Lägg till** knappen.

1. Markera enheterna som underordnade från enhetslistan över och använda högerpilen för att välja de adresser som ska läggas till som underordnade.

1. Klicka på **OK** att bekräfta.

Edge-enhet och dess underordnade enheter har nu aktiverats för utökade offline igen.  