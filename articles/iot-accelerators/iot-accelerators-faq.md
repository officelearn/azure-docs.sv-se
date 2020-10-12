---
title: Vanliga frågor om IoT Solution Accelerators – Azure | Microsoft Docs
description: I den här artikeln besvaras vanliga frågor och svar om IoT Solution-acceleratorer. Den innehåller länkar till GitHub-databaserna.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: abab80f8c790a170ec30fa228dd86576079a73a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462555"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Vanliga frågor och svar om IoT-lösningsacceleratorer

Se även de vanliga [vanliga frågorna](iot-accelerators-faq-cf.md) och vanliga frågor och [svar om fjärrövervakning](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Var hittar jag käll koden för lösnings acceleratorerna?

Käll koden lagras i följande GitHub-databaser:

* [Lösnings Accelerator för fjärr övervakning (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java (Remote Monitoring Solution Accelerator)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Lösnings Accelerator för förebyggande underhåll](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Lösnings Accelerator för ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Vilka SDK: er kan jag använda för att utveckla enhets klienter för lösnings acceleratorer?

Du hittar länkar till olika språk (C, .NET, Java, Node.js, python) IoT-enhets-SDK: er i [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) : er GitHub-databaser.

Om du använder DevKit-enheten kan du hitta resurser och exempel i [IoT DEVKIT SDK](https://github.com/Microsoft/devkit-sdk) GitHub-lagringsplatsen.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Är den nya mikrotjänster-arkitekturen tillgänglig för alla tre lösnings acceleratorer?

För närvarande använder bara lösningen för fjärrövervakning i arkitekturen för mikrotjänster, eftersom det täcker det bredaste scenariot.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Vilka fördelar tillhandahåller den nya mikrotjänsters arkitekturen med öppen källkod i den nya uppdateringen?

Under de senaste två åren har moln arkitekturen kraftigt utvecklats. Mikrotjänster har utsättts som ett bra mönster för att uppnå skalbarhet och flexibilitet utan att offra utvecklings hastigheten. Det här arkitektur mönstret används i flera Microsoft-tjänster internt med bättre Tillförlitlighets-och skalbarhets resultat. Microsoft placerar dessa lösningar i praxis i lösnings acceleratorer så att kunderna kan dra nytta av dem.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jag är tjänst administratör och vill ändra katalog mappningen mellan min prenumeration och en särskild Azure AD-klient. Vill du Hur gör jag för att slutföra den här uppgiften?

Se [så här lägger du till en befintlig prenumeration i din Azure AD-katalog](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Jag vill ändra en tjänst administratör eller Co-Administrator när du är inloggad med ett organisations konto

Se Support artikeln [ändra tjänst administratör och Co-Administrator när du är inloggad med ett organisations konto](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? "Ditt konto har inte rätt behörighet för att skapa en lösning. Kontakta konto administratören eller försök med ett annat konto. "

Se följande diagram för vägledning:

![Behörighets flöde](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Om du fortsätter att se felet när du har verifierat att du är global administratör för Azure AD-klienten och en medadministratör för prenumerationen, har du konto administratören som tar bort användaren och tilldelar om nödvändig behörighet i den här ordningen. Lägg först till användaren som global administratör och Lägg sedan till användare som en medadministratör för Azure-prenumerationen. Kontakta [hjälp & support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om problemen kvarstår.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför ser jag det här felet när jag har en Azure-prenumeration? "En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnads fritt utvärderings konto på bara några minuter. "

Om du är säker på att du har en Azure-prenumeration verifierar du klient mappningen för din prenumeration och kontrollerar att rätt klient organisation är markerad i list rutan. Om du har verifierat att klienten är korrekt följer du föregående diagram och validerar mappningen av prenumerationen och den här Azure AD-klienten.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Var kan jag hitta information om den tidigare versionen av lösningen för fjärrövervakning?

Den tidigare versionen av lösnings acceleratorn för fjärrövervakning kallades IoT Suite den förkonfigurerade lösningen för fjärrövervakning. Du hittar den arkiverade dokumentationen på [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/) .

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Är den nya Solution Accelerator som är tillgänglig i samma geografiska region som den befintliga lösningen?

Ja, den nya fjärr styrningen är tillgänglig i samma geografiska regioner.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Vad är skillnaden mellan att ta bort en resurs grupp i Azure Portal och klicka på ta bort på en lösnings Accelerator i azureiotsolutions.com?

* Om du tar bort Solution Accelerator i [azureiotsolutions.com](https://www.azureiotsolutions.com/)tar du bort alla resurser som distribuerades när du skapade Solution Accelerator. Om du har lagt till ytterligare resurser i resurs gruppen tas även dessa resurser bort.
* Om du tar bort resurs gruppen i [Azure Portal](https://portal.azure.com)tar du bara bort resurserna i den resurs gruppen. Du måste också ta bort det Azure Active Directory program som är kopplat till Solution Accelerator.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan jag fortsätta att använda mina befintliga investeringar i Azure IoT Solution-acceleratorer?

Ja. Alla lösningar som finns idag fortsätter att fungera i din Azure-prenumeration och käll koden förblir tillgänglig i GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub-instanser kan jag etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Du kan skapa ett [support ärende för Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att höja den här gränsen. Därför kan du, eftersom varje lösnings Accelerator etablerar en ny IoT Hub, bara etablera upp till 10 lösnings acceleratorer i en specifik prenumeration.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure Cosmos DB-instanser kan jag etablera i en prenumeration?

50. Du kan skapa ett [support ärende för Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att höja den här gränsen, men som standard kan du bara etablera 50 Cosmos DB-instanser per prenumeration.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hur många kostnadsfria Bing Maps-API:er kan jag etablera i en prenumeration?

Två. Du kan bara skapa två interna transaktions kartor på nivå 1 för företags planer i en Azure-prenumeration. Lösningen för fjärrövervakning är etablerad som standard med den interna transaktions nivån 1 plan. Det innebär att du bara kan etablera upp till två lösningar för fjärrövervakning i en prenumeration utan några ändringar.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en lösnings Accelerator om jag har Microsoft Azure för DreamSpark?

> [!NOTE]
> Microsoft Azure för DreamSpark kallas nu Microsoft Imagine för studenter.

För närvarande kan du inte skapa en lösnings Accelerator med ett [Microsoft Azure för DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) -konto. Du kan dock skapa ett [kostnads fritt utvärderings konto för Azure](https://azure.microsoft.com/free/) på bara några minuter som gör det möjligt att skapa en lösnings Accelerator.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hur gör jag för att ta bort en Azure AD-klient?

Se Eric Golpess blogg inlägg [genom att ta bort en Azure AD-klient](https://docs.microsoft.com/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Utforska funktionerna i lösnings acceleratorn för fjärr styrning](quickstart-remote-monitoring-deploy.md)
* [Översikt över lösningsacceleratorn Förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Distribuera lösnings Accelerator för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up)
