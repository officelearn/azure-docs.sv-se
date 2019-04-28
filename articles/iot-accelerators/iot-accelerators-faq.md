---
title: IoT-Lösningsacceleratorer vanliga frågor och svar – Azure | Microsoft Docs
description: Vanliga frågor om IoT-Lösningsacceleratorer
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: b2f08e811217572e09a254e9ab3306ab954b14b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447968"
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

Om du använder enheten DevKit du hittar resurser och exempel i den [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-lagringsplatsen.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Är den nya mikrotjänster-arkitekturen tillgänglig för alla tre Lösningsacceleratorer?

För närvarande använder endast av lösningen för fjärrövervakning arkitektur för mikrotjänster som den tar upp den bredaste.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Vilka fördelar ger den nya öppen källkod bygger på mikrotjänster-arkitekturen i den nya uppdateringen?

Molnarkitektur har avsevärt utvecklats under de senaste två åren. Mikrotjänster har utsågs till ett bra mönster för att uppnå skalbarhet och flexibilitet, utan att offra utvecklingshastigheten. Den här arkitekturmönster används internt i flera Microsoft-tjänster med fantastiska tillförlitlighet och skalbarhet resultat. Microsoft skapar dessa erfarenheter i praktiken i Lösningsacceleratorer så att kunderna dra nytta av dem.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jag har en tjänstadministratör och jag skulle vilja ändra directory mappningen mellan min prenumeration och en specifik Azure AD-klient. Hur jag för att slutföra den här uppgiften?

Se [att lägga till en befintlig prenumeration i Azure AD-katalogen](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Jag vill ändra en tjänstadministratör eller delad administratör när du har loggat in med ett organisationskonto

Se supportartikeln [ändra tjänstadministratör och delad administratör när du har loggat in med ett organisationskonto](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? ”Ditt konto har inte behörighet att skapa en lösning. Kontrollera kontoadministratören eller försök med ett annat konto ”.

Titta på diagrammet nedan anvisningar:

![Flödesschema för behörigheter](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Om du ser felet när du har validerat du är global administratör för Azure AD-klient och en medadministratör för prenumerationen, har din kontoadministratör tar bort användare och tilldela nödvändiga behörigheter i den här ordningen. Först lägger du till användaren som global administratör och Lägg sedan till användaren som en medadministratör för Azure-prenumerationen. Om problemen kvarstår, kontakta [hjälp och Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför ser jag det här felet när jag har en Azure-prenumeration? ”En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnadsfritt utvärderingskonto på bara några minuter ”.

Om du är säker på att du har en Azure-prenumeration kan du validera klientmappning för din prenumeration och kontrollera att rätt klient har valts i listrutan. Om du har verifierat klienten är korrekt, följer du föregående diagram och verifiera mappningen av din prenumeration och Azure AD-klient.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Var hittar jag information om den tidigare versionen av lösningen för fjärrövervakning

Den tidigare versionen av lösningsacceleratorn för fjärrövervakning kallades IoT Suite av den förkonfigurerade lösningen för fjärrövervakning. Du kan hitta arkiverad dokumentation på [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Är den senaste solution acceleratorn tillgänglig i samma geografiska region som den befintliga lösningen?

Ja, den nya fjärrövervakning finns i samma geografiska regioner.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Vad är skillnaden mellan att ta bort en resursgrupp i Azure-portalen och klicka på Ta bort på en lösningsaccelerator i azureiotsolutions.com?

* Om du tar bort lösningsaccelerator i [azureiotsolutions.com](https://www.azureiotsolutions.com/), du ta bort alla resurser som har distribuerats när du skapade solution accelerator. Om du har lagt till ytterligare resurser till resursgruppen raderas även dessa resurser.
* Om du tar bort resursgruppen på den [Azure-portalen](https://portal.azure.com), du bara bort resurserna i resursgruppen. Du måste också ta bort Azure Active Directory-program som är associerade med lösningsaccelerator.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan jag fortsätta att använda min befintliga investeringar i Azure IoT-Lösningsacceleratorer?

Ja. Alla lösningar som finns idag fortsätter att fungera i Azure-prenumerationen och källkoden är tillgängliga i GitHub.

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

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hur tar jag bort en Azure AD-klient?

Finns i Eric Golpes blogginlägg [genomgång av att ta bort en Azure AD-klient](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Utforska funktionerna i lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md)
* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Distribuera lösningsaccelerator för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up)
