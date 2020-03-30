---
title: Vanliga frågor och svar om IoT-lösningsacceleratorer – Azure | Microsoft-dokument
description: Den här artikeln besvarar de vanliga frågorna för IoT-lösningsacceleratorer. Den innehåller länkar till GitHub-databaserna.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647721"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Vanliga frågor och svar om IoT-lösningsacceleratorer

Se även de [anslutna fabriksspecifika vanliga frågorna](iot-accelerators-faq-cf.md) och vanliga frågor om [fjärrövervakning](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Var hittar jag källkoden för lösningsacceleratorerna?

Källkoden lagras i följande GitHub-databaser:

* [Accelerator för fjärrövervakningslösning (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Accelerator för lösning för fjärrövervakning (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Accelerator för automatisk lösning för förutsägande underhåll](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Ansluten fabrikslösningsaccelerator](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Vilka SDK:er kan jag använda för att utveckla enhetsklienter för lösningsacceleratorer?

Du hittar länkar till olika språk (C, .NET, Java, Node.js, Python) IoT-enhet SDK:er i [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) GitHub-databaser.

Om du använder DevKit-enheten kan du hitta resurser och exempel i [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-databasen.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Är den nya mikrotjänstarkitekturen tillgänglig för alla tre lösningsacceleratorer?

För närvarande använder endast lösningen för fjärrövervakning arkitekturen mikrotjänstarkitekturen eftersom den täcker det bredaste scenariot.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Vilka fördelar ger den nya mikrotjänstbaserade arkitekturen med öppen källkod i den nya uppdateringen?

Under de senaste två åren har molnarkitektur utvecklats kraftigt. Mikrotjänster har vuxit fram som ett bra mönster för att uppnå skala och flexibilitet, utan att offra utvecklingshastighet. Det här arkitektoniska mönstret används i flera Microsoft-tjänster internt med stor tillförlitlighet och skalbarhetsresultat. Microsoft omsätter dessa lärdomar i praktiken i lösningsacceleratorerna så att kunderna kan dra nytta av dem.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jag är tjänstadministratör och vill ändra katalogmappningen mellan min prenumeration och en specifik Azure AD-klientorganisation. Hur slutför jag den här uppgiften?

Se [Så här lägger du till en befintlig prenumeration i din Azure AD-katalog](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Jag vill ändra en tjänstadministratör eller medadministratör när jag är inloggad med ett organisationskonto

Se supportartikeln [Ändra tjänstadministratör och medadministratör när du är inloggad med ett organisationskonto](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? "Ditt konto har inte rätt behörighet att skapa en lösning. Kontrollera med din kontoadministratör eller försök med ett annat konto."

Titta på följande diagram för vägledning:

![Flödesschema för behörigheter](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Om du fortsätter att se felet efter att du har verifierat att du är global administratör för Azure AD-klienten och en medadministratör för prenumerationen, ber kontoadministratören ta bort användaren och tilldela om nödvändiga behörigheter i den här ordningen. Lägg först till användaren som global administratör och lägger sedan till användare som medadministratör för Azure-prenumerationen. Om problemen kvarstår kontaktar du [hjälp & support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför visas det här felet när jag har en Azure-prenumeration? "En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnadsfritt utvärderingskonto på bara några minuter."

Om du är säker på att du har en Azure-prenumeration validerar du klientmappningen för din prenumeration och kontrollerar att rätt klient är vald i listrutan. Om du har validerat att klienten är korrekt följer du föregående diagram och validerar mappningen av din prenumeration och den här Azure AD-klienten.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Var hittar jag information om den tidigare versionen av lösningen för fjärrövervakning?

Den tidigare versionen av lösningsacceleratorn för fjärrövervakning kallades den förkonfigurerade lösningen för fjärrövervakning i IoT Suite. Du hittar den arkiverade [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)dokumentationen på .

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Är den nya lösningsacceleratorn tillgänglig i samma geografiska region som den befintliga lösningen?

Ja, den nya fjärrövervakningen är tillgänglig i samma geografiska regioner.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Vad är skillnaden mellan att ta bort en resursgrupp i Azure-portalen och att klicka på ta bort på en lösningsaccelerator i azureiotsolutions.com?

* Om du tar bort lösningsacceleratorn i [azureiotsolutions.com](https://www.azureiotsolutions.com/)tar du bort alla resurser som distribuerades när du skapade lösningsacceleratorn. Om du har lagt till ytterligare resurser i resursgruppen tas även dessa resurser bort.
* Om du tar bort resursgruppen i [Azure-portalen](https://portal.azure.com)tar du bara bort resurserna i resursgruppen. Du måste också ta bort Azure Active Directory-programmet som är associerat med lösningsacceleratorn.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan jag fortsätta att utnyttja mina befintliga investeringar i Azure IoT-lösningsacceleratorer?

Ja. Alla lösningar som finns idag fortsätter att fungera i din Azure-prenumeration och källkoden förblir tillgänglig i GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub-instanser kan jag etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Du kan skapa en [Azure-supportbiljett](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att höja den här gränsen. Eftersom varje lösningsaccelerator etablerar en ny IoT Hub kan du därför bara etablera upp till 10 lösningsacceleratorer i en viss prenumeration.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure Cosmos DB-instanser kan jag etablera i en prenumeration?

Femtio. Du kan skapa en [Azure-supportbiljett](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att höja den här gränsen, men som standard kan du bara etablera 50 Cosmos DB-instanser per prenumeration.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hur många kostnadsfria Bing Maps-API:er kan jag etablera i en prenumeration?

Två. Du kan bara skapa två interna transaktioner nivå 1 Bing-kartor för företag i en Azure-prenumeration. Lösningen för fjärrövervakning etableras som standard med plan för intern transaktionsnivå 1. Därför kan du bara etablera upp till två fjärrövervakningslösningar i en prenumeration utan ändringar.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en lösningsaccelerator om jag har Microsoft Azure för DreamSpark?

> [!NOTE]
> Microsoft Azure för DreamSpark kallas nu Microsoft Imagine för studenter.

För närvarande kan du inte skapa en lösningsaccelerator med ett [Microsoft Azure för DreamSpark-konto.](https://azure.microsoft.com/pricing/member-offers/imagine/) Du kan dock skapa ett [kostnadsfritt utvärderingskonto för Azure](https://azure.microsoft.com/free/) på bara några minuter som gör att du kan skapa en lösningsaccelerator.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hur tar jag bort en Azure AD-klient?

Se Eric Golpes blogginlägg [Genomgång av Att ta bort en Azure AD-klientorganisation](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Utforska funktionerna i lösningsacceleratorn för fjärrövervakning](quickstart-remote-monitoring-deploy.md)
* [Översikt över lösningsaccelerator för förutsägande underhåll](iot-accelerators-predictive-overview.md)
* [Distribuera accelerator för ansluten fabrikslösning](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](/azure/iot-fundamentals/iot-security-ground-up)
