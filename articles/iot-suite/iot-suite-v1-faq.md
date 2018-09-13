---
title: Azure IoT Suite vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor och svar om IoT Suite
services: ''
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
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 7e7c4affee64a945900c02b6375ba4df5d085183
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35757812"
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Vanliga frågor och svar om IoT Suite

Se även den anslutna fabrik specifikt [vanliga frågor och svar](../iot-accelerators/iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Var hittar jag källkoden för de förkonfigurerade lösningarna?

Källkoden lagras i följande GitHub-databaser:
* [Förkonfigurerade lösningen för fjärrövervakning][lnk-remote-monitoring-github]
* [Förkonfigurerade lösningen förutsägande Underhåll][lnk-predictive-maintenance-github]
* [Förkonfigurerade lösningen ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Hur uppdaterar till den senaste versionen av den förkonfigurerade lösningen för fjärrövervakning som använder enhetshanteringsfunktionerna i IoT Hub?

* Om du distribuerar en förkonfigurerad lösning från den https://www.azureiotsuite.com/ plats, den distribuerar alltid en ny instans av den senaste versionen av lösningen.
* Om du distribuerar en förkonfigurerad lösning med hjälp av kommandoraden, kan du uppdatera en befintlig distribution med ny kod. Se [molnbaserad distribution] [ lnk-cloud-deployment] i GitHub [databasen][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Hur kan jag lägga till stöd för en ny enhetsmetod till den förkonfigurerade lösningen för fjärrövervakning?

Se avsnittet [lägger till stöd för en ny metod i simulatorn] [ lnk-add-method] i den [anpassa en förkonfigurerad lösning] [ lnk-customize] artikeln.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>Den simulerade enheten ignorerar ändringarna önskad egenskap varför?
I den förkonfigurerade lösningen för fjärrövervakning, den simulerade enheten koden bara den **Desired.Config.TemperatureMeanValue** och **Desired.Config.TelemetryInterval** önskade egenskaper till Uppdatera rapporterade egenskaper. Alla andra förfrågningar om önskad egenskap ignoreras.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Min enhet visas inte i listan över enheter i instrumentpanelen för lösningen, varför?

I listan med enheter i lösningens instrumentpanel använder en fråga för att returnera en lista med enheter. För närvarande kan inte en fråga returnera fler än 10K-enheter. Försök att göra sökvillkor för din fråga mer restriktiva.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Vad är skillnaden mellan att ta bort en resursgrupp på Azure-portalen och att klicka på Ta bort för en förkonfigurerad lösning på azureiotsuite.com?

* Om du tar bort den förkonfigurerade lösningen på [azureiotsuite.com][lnk-azureiotsuite], du tar bort alla resurser som etablerades när du skapade den förkonfigurerade lösningen. Om du har lagt till ytterligare resurser till resursgruppen raderas även dessa resurser. 
* Om du tar bort resursgruppen på den [Azure-portalen][lnk-azure-portal], du bara bort resurserna i resursgruppen. Du måste också ta bort Azure Active Directory-program som är associerade med den förkonfigurerade lösningen på den [Azure-portalen][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub-instanser kan jag etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration][link-azuresublimits]. Du kan skapa en [Azure-supportärende] [ link-azuresupportticket] att höja gränsen. Eftersom varje förkonfigurerade lösningen etablerar en ny IoT Hub, kan du därför bara etablera upp till 10 förkonfigurerade lösningar i en viss prenumeration. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure Cosmos DB-instanser kan jag etablera i en prenumeration?

Femtio. Du kan skapa en [Azure-supportärende] [ link-azuresupportticket] att höja gränsen, men som standard kan du bara etablera 50 Cosmos DB-instanser per prenumeration. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hur många kostnadsfria Bing Maps-API:er kan jag etablera i en prenumeration?

Två. Du kan skapa två interna transaktioner nivå 1 Bing Maps för Enterprise-planer i en Azure-prenumeration. Fjärrövervakningslösningen etableras som standard med interna transaktioner nivå 1-avtal. Detta betyder att du bara kan etablera upp till två fjärrövervakningslösningar i en prenumeration utan några ändringar.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Jag har en distribution av en fjärrövervakningslösning med en statisk karta. Hur lägger jag till en interaktiv Bing-karta?

1. Hämta Bing Maps-API för Enterprise QueryKey från [Azure-portalen][lnk-azure-portal]: 
   
   1. Navigera till resursgruppen där ditt Bing Maps API för företag finns i den [Azure-portalen][lnk-azure-portal].
   2. Klicka på **alla inställningar**, sedan **nyckelhantering**. 
   3. Du kan se två nycklar: **MasterKey** och **QueryKey**. Kopiera värdet för **QueryKey**.
      
      > [!NOTE]
      > Har du inget Bing Maps-API för Enterprise-konto? Skapar en i den [Azure-portalen] [ lnk-azure-portal] genom att klicka på + ny, söka efter Bing Maps API för företag och följ anvisningarna för att skapa.
      > 
      > 
2. Hämta den senaste koden från den [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Köra en lokal eller molnbaserad distribution genom att följa riktlinjerna för distribution i mappen /docs/ i databasen. 
4. När du har kört en lokal eller molnbaserad distribution tittar du i rotmappen efter *.user.config-filen som skapades under distributionen. Öppna den här filen i en textredigerare. 
5. Ändra följande rad med värdet som du kopierade från din **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en förkonfigurerad lösning om jag har Microsoft Azure för DreamSpark?

> [!NOTE]
> Microsoft Azure för DreamSpark kallas nu Microsoft Imagine för studenter.

För närvarande kan du kan inte skapa en förkonfigurerad lösning med en [Microsoft Azure för DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) konto. Du kan dock skapa en [kostnadsfritt utvärderingskonto för Azure](https://azure.microsoft.com/free/) på bara några minuter som du kan skapa en förkonfigurerad lösning.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Kan jag skapa en förkonfigurerad lösning om jag har Cloud Solution Provider (CSP)-prenumeration?

För närvarande kan skapa du inte en förkonfigurerad lösning med en prenumeration för Cloud Solution Provider (CSP). Du kan dock skapa en [kostnadsfritt utvärderingskonto för Azure] [ lnk-30daytrial] på bara några minuter som du kan skapa en förkonfigurerad lösning.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hur tar jag bort en Azure AD-klient?

Finns i Eric Golpes blogginlägg [genomgång av att ta bort en Azure AD-klient][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Nästa steg

Du kan även utforska några andra funktioner och möjligheter i de förkonfigurerade lösningarna i IoT Suite:

* [Översikt över förkonfigurerade lösning][lnk-predictive-overview]
* [Översikt över lösningen ansluten fabrik förkonfigurerade](../iot-accelerators/iot-accelerators-connected-factory-overview.md)
* [IoT-säkerhet från grunden][lnk-security-groundup]

[lnk-predictive-overview]:../iot-accelerators/iot-accelerators-predictive-overview.md
[lnk-security-groundup]:/azure/iot-fundamentals/iot-security-ground-up

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
