---
title: Azure IoT-Lösningsacceleratorer vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor om IoT-Lösningsacceleratorer
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 66494ca6bd9b4d8276469e85f4c6bb435ae245f7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716408"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Vanliga frågor om IoT-Lösningsacceleratorer

Se även de [ansluten fabrik-specifika vanliga frågor och svar](iot-accelerators-faq-cf.md) och [Remote Monitoring-specifika vanliga frågor och svar](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Var hittar jag källkoden för Lösningsacceleratorer?

Källkoden lagras i följande GitHub-databaser:

* [Remote Monitoring solution accelerator (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Remote Monitoring solution accelerator (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Förutsägande Underhåll lösningsaccelerator](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Ansluten fabrik lösningsaccelerator](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Vilken SDK: er kan jag använda för att utveckla enhetsklienter för Lösningsacceleratorer?

Du hittar länkar till olika språk (C, .NET, Java, Node.js, Python) IoT-enhetens SDK: er i den [Microsoft Azure IoT SDK: er](https://github.com/Azure/azure-iot-sdks) GitHub-lagringsplatser.

Om du använder den DevKit, du kan hitta resurser och exempel i den [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-lagringsplatsen.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Är den nya mikrotjänster-arkitekturen tillgänglig för alla tre Lösningsacceleratorer?

För närvarande använder endast av lösningen för fjärrövervakning arkitektur för mikrotjänster som den tar upp den bredaste.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Vilka fördelar ger den nya öppen källkod bygger på mikrotjänster-arkitekturen i den nya uppdateringen?

Molnarkitektur har avsevärt utvecklats under de senaste två åren. Mikrotjänster har utsågs till ett bra mönster för att uppnå skalbarhet och flexibilitet, utan att offra utvecklingshastigheten. Den här arkitekturmönster används internt i flera Microsoft-tjänster med fantastiska tillförlitlighet och skalbarhet resultat. Vi använder dessa utbildning i praktiken så att våra kunder att dra nytta av dem.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Är den senaste solution acceleratorn tillgänglig i samma geografiska region som den befintliga lösningen?

Ja, den nya fjärrövervakning finns i samma geografiska regioner.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Vad är skillnaden mellan att ta bort en resursgrupp i Azure-portalen och klicka på Ta bort på en lösningsaccelerator på azureiotsuite.com?

* Om du tar bort lösningsaccelerator i [azureiotsuite.com](https://www.azureiotsolutions.com/), du tar bort alla resurser som etablerades när du skapade solution accelerator. Om du har lagt till ytterligare resurser till resursgruppen raderas även dessa resurser.
* Om du tar bort resursgruppen på den [Azure-portalen](https://portal.azure.com), du bara bort resurserna i resursgruppen. Du måste också ta bort Azure Active Directory-program som är associerade med lösningsaccelerator.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan jag fortsätta att använda min befintliga investeringar i Azure IoT-Lösningsacceleratorer?

Ja. Alla lösningar som finns idag fortsätter att fungera i Azure-prenumerationen och källkoden finns kvar i GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub-instanser kan jag etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration](../azure-subscription-service-limits.md#iot-hub-limits). Du kan skapa en [Azure-supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att höja gränsen. Resultatet blir att, kan eftersom varje solution accelerator etablerar en ny IoT Hub du bara etablera upp till 10 Lösningsacceleratorer för en viss prenumeration.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure Cosmos DB-instanser kan jag etablera i en prenumeration?

Femtio. Du kan skapa en [Azure-supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att höja gränsen, men som standard kan du bara etablera 50 Cosmos DB-instanser per prenumeration.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hur många kostnadsfria Bing Maps-API:er kan jag etablera i en prenumeration?

Två. Du kan skapa två interna transaktioner nivå 1 Bing Maps för Enterprise-planer i en Azure-prenumeration. Lösningen för fjärrövervakning etableras som standard med interna transaktioner nivå 1-avtal. Därför kan kan du bara etablera upp till två fjärrövervakning lösningar i en prenumeration utan några ändringar.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en lösningsaccelerator om jag har Microsoft Azure för DreamSpark?

> [!NOTE]
> Microsoft Azure för DreamSpark kallas nu Microsoft Imagine för studenter.

För närvarande kan du inte skapa en lösningsaccelerator med en [Microsoft Azure för DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konto. Du kan dock skapa en [kostnadsfritt utvärderingskonto för Azure](https://azure.microsoft.com/free/) på bara några minuter som du kan skapa en lösningsaccelerator.

### <a name="how-do-i-delete-an-aad-tenant"></a>Hur tar jag bort en AAD-klient?

Finns i Eric Golpes blogginlägg [genomgång av att ta bort en Azure AD-klient](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Utforska funktionerna i lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md)
* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Distribuera lösningsaccelerator för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up)
