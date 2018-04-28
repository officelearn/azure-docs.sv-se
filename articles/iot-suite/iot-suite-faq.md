---
title: Azure IoT Suite vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor och svar om IoT Suite
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 49e94e771deb4582b922400d81e8388faf164f40
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Vanliga frågor och svar om IoT Suite

Mer information hittar du i [anslutna factory-specifika vanliga frågor och svar](iot-suite-faq-cf.md) och [fjärråtkomst övervakning av specifika vanliga frågor och svar](iot-suite-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Var hittar källkoden för de förkonfigurerade lösningarna?

Källkoden lagras i följande GitHub-databaser:

* [Fjärrövervaknings förkonfigurerade lösningen (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Fjärrövervaknings förkonfigurerade lösningen (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Förutsägande Underhåll förkonfigurerade lösningen](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Anslutna factory förkonfigurerade lösningen](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-preconfigured-solutions"></a>Vilka SDK: er kan använda att utveckla enhetsklienter förkonfigurerade lösningar?

Du hittar länkar till olika språk (C, .NET, Java, Node.js, Python) IoT-enhet SDK: er i den [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub-lagringsplatser.

Om du använder enheten DevKit hittar du resurser och exempel i den [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-lagringsplatsen.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>Är den nya arkitekturen i mikrotjänster tillgängligt för alla tre förkonfigurerade lösningar?

För närvarande använder endast remote övervakningslösning arkitektur för mikrotjänster som det täcker de bredaste scenariot.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Vilka fördelar ger den nya öppen källkod mikrotjänster-baserad arkitekturen i den nya uppdateringen?

Under de senaste två åren har molnarkitektur avsevärt utvecklats. Mikrotjänster har vuxit fram som ett mönster som är bra att uppnå skalbarhet och flexibilitet, utan att kompromissa development hastighet. Den här arkitekturen mönster används internt i flera Microsoft-tjänster med bra tillförlitlighet och skalbarhet resultat. Vi håller på dessa learning i praktiken så att kunderna dra nytta av dem.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>Är den nya förkonfigurerade lösningen i samma geografiska region som den befintliga lösningen?

Ja, den nya fjärrövervaknings finns i samma geografiska regioner.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Vad är skillnaden mellan att ta bort en resursgrupp på Azure-portalen och att klicka på Ta bort för en förkonfigurerad lösning på azureiotsuite.com?

* Om du tar bort förkonfigurerade lösningen i [azureiotsuite.com](https://www.azureiotsuite.com/), du ta bort alla resurser som etablerades när du skapade den förkonfigurerade lösningen. Om du har lagt till fler resurser till resursgruppen raderas även dessa resurser.
* Om du tar bort resursgrupp i den [Azure-portalen](https://portal.azure.com), bara ta bort resurserna i resursgruppen. Du måste också ta bort Azure Active Directory-programmet som är kopplade till den förkonfigurerade lösningen.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Kan jag fortsätta att använda min befintliga investeringar i Azure IoT Suite?

Ja. Alla lösningar som finns idag fortsätter att fungera i din Azure-prenumeration och källkoden finns kvar i GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub-instanser kan etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration](../azure-subscription-service-limits.md#iot-hub-limits). Du kan skapa en [Azure supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att höja gränsen. Eftersom varje förkonfigurerade lösning etablerar en ny IoT-hubb, kan du därför bara etablera upp till 10 förkonfigurerade lösningar i en viss prenumeration.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure DB som Cosmos-instanser kan etablera i en prenumeration?

Femtio. Du kan skapa en [Azure supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att höja gränsen, men som standard kan du bara etablera 50 Cosmos DB instanser per prenumeration.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hur många kostnadsfria Bing Maps-API:er kan jag etablera i en prenumeration?

Två. Du kan skapa två interna transaktioner nivå 1 Bing Maps för Enterprise planer i en Azure-prenumeration. Fjärråtkomst övervakningslösning etableras som standard med interna transaktioner nivå 1-plan. Detta betyder att du bara kan etablera upp till två fjärrövervakningslösningar i en prenumeration utan några ändringar.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en förkonfigurerad lösning om jag har Microsoft Azure för DreamSpark?

> [!NOTE]
> Microsoft Azure för DreamSpark kallas nu Microsoft Imagine för studenter.

För närvarande kan du inte skapa en förkonfigurerade lösning med en [Microsoft Azure för DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konto. Du kan dock skapa en [kostnadsfri utvärderingsversion konto för Azure](https://azure.microsoft.com/free/) på ett par minuter som du kan skapa en förkonfigurerade lösning.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Kan jag skapa en förkonfigurerade lösning om jag har Cloud Solution Providers (CSP)-prenumeration?

För närvarande kan skapa du inte en förkonfigurerade lösning med en prenumeration Cloud Solution Providers (CSP). Du kan dock skapa en [kostnadsfri utvärderingsversion konto för Azure](https://azure.microsoft.com/free/) på ett par minuter som du kan skapa en förkonfigurerade lösning.

### <a name="how-do-i-delete-an-aad-tenant"></a>Hur tar jag bort en AAD-klient?

Finns i blogginlägget Eric Golpe [genomgång av du tar bort en Azure AD-klient](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska några andra funktioner och möjligheter i de förkonfigurerade lösningarna i IoT Suite:

* [Utforska funktionerna i fjärråtkomst övervakning förkonfigurerade lösningen](iot-suite-remote-monitoring-explore.md)
* [Förutsägande Underhåll förkonfigurerade lösning: översikt](iot-suite-predictive-overview.md)
* [Anslutna factory förkonfigurerade lösning: översikt](iot-suite-connected-factory-overview.md)
* [IoT-säkerhet från grunden](securing-iot-ground-up.md)