---
title: Skapa anpassade analytics för tillämpningsprogrammet Azure IoT centrala | Microsoft Docs
description: Som operatör, hur du skapar anpassade analytics för Azure IoT centrala programmet.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5facdf3f02b71e154a23d8f26c7bcc40b5c71e35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629311"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Hur du använder analytics för att analysera dina data på enheten

Microsoft Azure IoT Central ger omfattande analytics funktioner för att vara meningsfullt av stora mängder data från dina enheter. Du kan använda analytics för att visa och analysera data för en [enhetsuppsättning](howto-use-device-sets.md) i ditt program. En enhet är en användardefinierad grupp av enheter. Du kan begränsa analysen att en liten uppsättning enheter eller en enskild enhet.

Som operatör kan välja **Analytics** på den vänstra navigeringsmenyn anger vilka enheter och välj sedan de mått som ska visas i diagrammet. Här följer några verktyg som du kan använda för att dela informationen vidare:

* **Mått:** välja mått att visa, till exempel temperatur- och fuktighetskonsekvens.
* **Aggregering:** Välj aggregeringsfunktionen för mätningar. Till exempel **minsta** eller **genomsnittlig**.
* **Delning av:** detaljnivån genom att dela data av enhetens egenskaper eller enhetsnamn. Till exempel dela av enhetsplats:

     ![Analytics huvudsida](media\howto-create-analytics\analytics-main.png)

* **Tidsintervall:** du kan välja någon av de fördefinierade tidsintervall tidsintervall eller skapa ett anpassat tidsintervall: ![Analytics tidsintervallet](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>Ändra visualiseringar

Du kan ändra diagram för att uppfylla kraven för visualisering genom att välja ett av tre lägen:

* **Staplad:** ett diagram för varje mått är staplade och varje diagram har sina egna y-axeln. Staplade diagram är användbara när du har flera mått som har valts och vill ha en enskild vy av dessa mätningar.
* **Ostaplade:** ett diagram för varje åtgärd ritas mot en y-axeln, men värdena för y-axeln ändras baserat på det markerade måttet. Ostaplade diagram är användbara när du vill överlagra flera mått och vill se mönster över dessa åtgärder för samma tidsintervall.
* **Delade y-axeln:** alla diagram dela samma y-axeln och ändra värdena för axeln inte. Diagram för delade y-axeln är användbara när du vill titta på en enda åtgärd vid uppdelning delning av data.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar anpassade analytics för tillämpningsprogrammet Azure IoT centrala är här föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Förbereda och ansluta ett Node.js-program](howto-connect-nodejs.md)