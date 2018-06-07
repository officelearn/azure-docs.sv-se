---
title: Azure IoT solution Accelerator vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor om IoT solution Accelerator
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: decae2fee0d040d0857950bec507df173e2820b9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627167"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Vanliga frågor om IoT solution Accelerator

Mer information hittar du i [anslutna Factory-specifika vanliga frågor och svar](iot-accelerators-faq-cf.md) och [fjärråtkomst övervakning av specifika vanliga frågor och svar](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Var hittar källkoden för solution Accelerator?

Källkoden lagras i följande GitHub-databaser:

* [Fjärråtkomst övervakning solution accelerator (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Fjärråtkomst övervakning solution accelerator (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Förutsägande Underhåll solution accelerator](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Anslutna Factory solution accelerator](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Vilka SDK: er kan använda att utveckla enhetsklienter för solution Accelerator?

Du hittar länkar till olika språk (C, .NET, Java, Node.js, Python) IoT-enhet SDK: er i den [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub-lagringsplatser.

Om du använder enheten DevKit hittar du resurser och exempel i den [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-lagringsplatsen.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Är den nya arkitekturen i mikrotjänster tillgängligt för alla tre solution Accelerator?

För närvarande använder endast Fjärrövervaknings-lösningen arkitektur för mikrotjänster det täcker de bredaste scenariot.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Vilka fördelar ger den nya öppen källkod mikrotjänster-baserad arkitekturen i den nya uppdateringen?

Under de senaste två åren har molnarkitektur avsevärt utvecklats. Mikrotjänster har vuxit fram som ett mönster som är bra att uppnå skalbarhet och flexibilitet, utan att kompromissa development hastighet. Den här arkitekturen mönster används internt i flera Microsoft-tjänster med bra tillförlitlighet och skalbarhet resultat. Vi håller på dessa learning i praktiken så att kunderna dra nytta av dem.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Är den nya solution acceleratorn i samma geografiska region som den befintliga lösningen?

Ja, den nya Fjärrövervaknings finns i samma geografiska regioner.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Vad är skillnaden mellan att ta bort en resursgrupp i Azure-portalen och klicka på Ta bort på en lösningsaccelerator i azureiotsuite.com?

* Om du tar bort solution accelerator i [azureiotsuite.com](https://www.azureiotsolutions.com/), du ta bort alla resurser som etablerades när du skapade solution accelerator. Om du har lagt till fler resurser till resursgruppen raderas även dessa resurser.
* Om du tar bort resursgrupp i den [Azure-portalen](https://portal.azure.com), bara ta bort resurserna i resursgruppen. Du måste också ta bort Azure Active Directory-programmet som är associerade med solution accelerator.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan jag fortsätta att använda min befintliga investeringar i Azure IoT solution Accelerator?

Ja. Alla lösningar som finns idag fortsätter att fungera i din Azure-prenumeration och källkoden finns kvar i GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub-instanser kan etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration](../azure-subscription-service-limits.md#iot-hub-limits). Du kan skapa en [Azure supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att höja gränsen. Därför kan etablera sedan varje solution accelerator tillhandahåller en ny IoT-hubb du bara upp till 10 solution Accelerator för en viss prenumeration.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure DB som Cosmos-instanser kan etablera i en prenumeration?

Femtio. Du kan skapa en [Azure supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att höja gränsen, men som standard kan du bara etablera 50 Cosmos DB instanser per prenumeration.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hur många kostnadsfria Bing Maps-API:er kan jag etablera i en prenumeration?

Två. Du kan skapa två interna transaktioner nivå 1 Bing Maps för Enterprise planer i en Azure-prenumeration. Fjärrövervaknings lösning etableras som standard med interna transaktioner nivå 1-plan. Du kan därför endast etablera upp till två Fjärrövervaknings lösningar i en prenumeration utan ändringar.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en lösningsaccelerator om jag har Microsoft Azure för DreamSpark?

> [!NOTE]
> Microsoft Azure för DreamSpark kallas nu Microsoft Imagine för studenter.

För närvarande kan du inte skapa en lösningsaccelerator med en [Microsoft Azure för DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konto. Du kan dock skapa en [kostnadsfri utvärderingsversion konto för Azure](https://azure.microsoft.com/free/) i ett par minuter som gör att du skapar en lösningsaccelerator.

### <a name="can-i-create-a-solution-accelerator-if-i-have-cloud-solution-provider-csp-subscription"></a>Kan jag skapa en lösningsaccelerator om jag har Cloud Solution Providers (CSP)-prenumeration?

För närvarande kan du skapa en lösningsaccelerator med en prenumeration Cloud Solution Providers (CSP). Du kan dock skapa en [kostnadsfri utvärderingsversion konto för Azure](https://azure.microsoft.com/free/) i ett par minuter som gör att du skapar en lösningsaccelerator.

### <a name="how-do-i-delete-an-aad-tenant"></a>Hur tar jag bort en AAD-klient?

Finns i blogginlägget Eric Golpe [genomgång av du tar bort en Azure AD-klient](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Utforska funktionerna i Fjärrövervaknings solution accelerator](iot-accelerators-remote-monitoring-explore.md)
* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Anslutna Factory solution accelerator översikt](iot-accelerators-connected-factory-overview.md)
* [IoT-säkerhet från grunden](securing-iot-ground-up.md)
