---
title: Anslut ditt Azure IoT Central-program med Dynamics 365 Field Services | Microsoft-dokument
description: Lär dig hur du skapar en heltäckande lösning med Azure IoT Central och Dynamics 365 Field Service
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7c708268eaeade1cfb625c73ff8758a1941ff5b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157457"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Skapa heltäckande lösning med Azure IoT Central och Dynamics 365 Field Service 



Som byggare kan du aktivera integrering av ditt Azure IoT Central-program till andra affärssystem. 


I en ansluten avfallshanteringslösning kan du till exempel optimera sändningen av sophämtningsbilar. Optimeringen kan göras baserat på IoT-sensorer data från anslutna soptunnor. I ditt [IoT Central-anslutna avfallshanteringsprogram](./tutorial-connected-waste-management.md) kan du konfigurera regler och åtgärder och ställa in det så att de utlöser aviseringar i Dynamics Field Service. Det här scenariot uppnås med hjälp av Microsoft Flow, som konfigureras direkt i IoT Central för automatisering av arbetsflöden mellan program och tjänster. Dessutom, baserat på tjänstaktiviteter i Field Service, kan information skickas tillbaka till Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Så här ansluter du ditt Azure IoT Central-program med Dynamics 365 Field Services 

Nedanstående integrationsprocesser kan enkelt implementeras baserat på en ren konfigurationsupplevelse:
* Azure IoT Central kan skicka information om enhetsavvikelser till Connected Field Service (som en IoT-avisering) för diagnos.
* Connected Field Service kan skapa ärenden eller arbetsorder som utlöses från enhetsavvikelser.
* Connected Field Service kan schemalägga tekniker för inspektion för att förhindra driftstopp incidenter.
* Instrumentpanelen för Azure IoT Central-enheten kan uppdateras med relevant service- och schemaläggningsinformation.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [IoT Centrala myndigheters mallar](./overview-iot-central-government.md)
* Läs mer om [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Läs mer om [Dynamics 365 Field Services](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)
