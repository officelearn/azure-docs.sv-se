---
title: Anslut ditt Azure IoT Central-program med Dynamics 365 Field Services | Microsoft Docs
description: Lär dig hur du skapar en lösning från slut punkt till slut punkt med Azure IoT Central-och Dynamics 365-fälttjänster
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d8eda3aefb317b9c6383561dd2094913e5d8f7d1
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026626"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Bygg lösningen från slut punkt till slut punkt med Azure IoT Central-och Dynamics 365-fälttjänster 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Som ett verktyg kan du aktivera integrering av ditt Azure IoT Central-program till andra företags system. 


I en ansluten avfalls hanterings lösning kan du till exempel optimera sändningen av korg korgs Last bilar baserat på IoT-sensorer från anslutna avfalls lager. I ditt [IoT Central anslutet avfalls hanterings program](./tutorial-connected-waste-management.md) kan du konfigurera regler och åtgärder och ange att de ska utlösa skapandet av aviseringar i Dynamics Field service. Det här scenariot uppnås med hjälp av Microsoft Flow, som du kan konfigurera direkt i IoT Central för att automatisera arbets flöden mellan program och tjänster. Baserat på service aktiviteter i fält tjänsten kan du också skicka information tillbaka till Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Så här ansluter du ditt Azure IoT Central-program med Dynamics 365 Field Services 

De lägre integrerings processerna från slut punkt till slut punkt kan enkelt implementeras baserat på en ren konfigurations upplevelse:
* Azure IoT Central kan skicka information om enhets avvikelser till anslutna fält tjänster (som en IoT-avisering) för diagnos.
* Ansluten fält tjänst kan skapa ärenden eller arbets order som utlöses från enhets avvikelser.
* Med den anslutna fält tjänsten kan du schemalägga tekniker för inspektion för att förhindra drift stopp incidenter.
* Instrument panelen för Azure IoT Central-enheten kan uppdateras med relevant tjänst och schemaläggnings information.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [mallar för IoT Central myndigheter](./overview-iot-central-government.md)
* Läs mer om [IoT Central](https://docs.microsoft.com/azure/iot-central/overview-iot-central)
* Läs mer om [Dynamics 365 Field Services](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
