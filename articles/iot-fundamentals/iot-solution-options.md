---
title: Alternativ för Azure Internet of Things (IoT)
description: Vägledning om hur du väljer mellan en plattformstjänster eller en hanterad appplattformsmetod för att skapa en IoT-lösning. Plattformstjänsten använder tjänster som IoT Hub och Digital Twins som byggstenar. Metoden för hanterad appplattform använder IoT Central för att snabbt komma igång.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 02/05/2020
ms.author: dobett
ms.openlocfilehash: 046116addf643d6c0eb0faf2f821b6ef78d440c6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77050342"
---
# <a name="choose-the-right-iot-solution"></a>Välj den rätta IoT-lösningen

Om du vill skapa en IoT-lösning för ditt företag väljer du vanligtvis att använda antingen *plattformstjänsterna* eller metoden *med hanterad appplattform.*

Plattformstjänster tillhandahåller byggstenarna för skräddarsydda och flexibla IoT-applikationer. Du har fler alternativ att välja och koda när du ansluter enheter och inta, lagra och analysera dina data. Azure IoT-plattformstjänster inkluderar produkterna Azure IoT Hub och Azure Digital Twins.

Med en hanterad appplattform kan du komma igång med att skapa appar snabbare än plattformstjänster genom att minska antalet beslut som krävs för att uppnå resultat. Den hanterade appplattformen tar hand om de flesta delar av din lösning, så att du kan fokusera på att lägga till branschkunskap och skala och ansluta enheter. Azure IoT Central är en hanterad appplattform.

Om du vill välja mellan dessa två metoder bör du tänka på följande:

- Hur du vill hantera din lösning.
- Vilken nivå av anpassning och kontroll du vill ha över din lösning.
- Vilken prisstruktur du vill ha.

## <a name="management"></a>Hantering

Var vill du spendera tid och resurser för systemhantering? 

- Välj plattformstjänster för att ha full kontroll över de underliggande tjänsterna i din lösning. Du vill till exempel:

    - Hantera skalning och säkring av tjänster för att uppfylla dina behov.
    - Utnyttja intern expertis eller partnerexpertis för att använda inbyggda enheter och tillhandahållandestjänster.

- Välj metoden för hanterad appplattform för att dra nytta av en plattform som hanterar skalning, säkerhet och hantering av dina IoT-program och enheter.

## <a name="control"></a>Kontroll

Vilka delar av din lösning vill du anpassa?

- Välj plattformstjänster för total anpassning och kontroll över lösningsarkitekturen.

- Välj metoden för hanterad appplattform för att anpassa varumärke, instrumentpaneler, användarroller, enheter och telemetri. Du vill dock inte hantera de underliggande IoT-systemhanteringskostnaderna.

## <a name="pricing"></a>Prissättning

Vilken prisstruktur passar bäst dina behov?

- Välj plattformstjänster för att finjustera tjänster och kontrollera mina totala kostnader.

- Välj metoden för hanterad appplattform för en enkel och förutsägbar prisstruktur.

## <a name="summary"></a>Sammanfattning

Plattformstjänster är lämplig för ett företag med molnlösning och enhetsexpertis som vill:

- Finjustera tjänsterna i lösningen.
- Ha en hög grad av kontroll över tjänsterna i lösningen.
- Anpassa lösningen helt.

Metoden med hanterad appplattform är lämplig för ett företag som:

- Vill inte avsätta omfattande resurser till systemdesign, utveckling och hantering.
- Vill ha en förutsägbar prisstruktur.
- Vill ha några anpassningsfunktioner.

## <a name="next-steps"></a>Nästa steg

En mer omfattande förklaring av de olika tjänsterna och plattformarna och hur de används finns i [Azure IoT-tjänster och -tekniker](iot-services-and-technologies.md).

Mer information om de viktigaste attributen för framgångsrika IoT-lösningar finns i [faktabladets 8 attribut för framgångsrika IoT-lösningar.](https://aka.ms/8attributes)

En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture](https://aka.ms/iotrefarchitecture) (Referensarkitektur för Microsoft Azure IoT).
