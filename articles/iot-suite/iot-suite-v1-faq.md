---
title: "Azure IoT Suite vanliga frågor och svar | Microsoft Docs"
description: "Vanliga frågor och svar om IoT Suite"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: db928795cc38dea4ca9fc441e6053e3bb44da23e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Vanliga frågor och svar om IoT Suite

Se även anslutna factory specifika [vanliga frågor och svar](iot-suite-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Var hittar källkoden för de förkonfigurerade lösningarna?

Källkoden lagras i följande GitHub-databaser:
* [Fjärrövervaknings förkonfigurerade lösningen][lnk-remote-monitoring-github]
* [Förutsägande Underhåll förkonfigurerade lösningen][lnk-predictive-maintenance-github]
* [Anslutna factory förkonfigurerade lösningen](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Hur uppdatera till den senaste versionen av fjärråtkomst övervakning förkonfigurerade lösning som använder hanteringsfunktionerna för IoT Hub-enheter?

* Om du distribuerar en förkonfigurerade lösning från webbplatsen https://www.azureiotsuite.com/ distribuerar alltid en ny instans av den senaste versionen av lösningen.
* Om du distribuerar en förkonfigurerade lösning med hjälp av kommandoraden, kan du uppdatera en befintlig distribution med ny kod. Se [Cloud deployment] [ lnk-cloud-deployment] i GitHub [databasen][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Hur kan jag lägga till stöd för en ny enhet metod fjärråtkomst övervakning förkonfigurerade lösningen?

I avsnittet [lägga till stöd för en ny metod i simulatorn] [ lnk-add-method] i den [anpassa förkonfigurerade lösning] [ lnk-customize] artikel.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>Den simulerade enheten ignorerar min önskade egenskapsändringar varför?
I fjärråtkomst övervakning förkonfigurerade lösningen simulerade enheten kod endast används den **Desired.Config.TemperatureMeanValue** och **Desired.Config.TelemetryInterval** önskade egenskaper Uppdatera den rapporterade egenskaper. Alla andra ändringsbegäranden för önskad egenskapen ignoreras.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Min enhet visas inte i listan över enheter i instrumentpanelen för lösningen varför?

Listan över enheter i instrumentpanelen för lösningen används en fråga för att returnera en lista med enheter. För närvarande kan inte en fråga returnera fler än 10K-enheter. Försök att göra sökvillkor för frågan mer restriktiva.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Vad är skillnaden mellan att ta bort en resursgrupp på Azure-portalen och att klicka på Ta bort för en förkonfigurerad lösning på azureiotsuite.com?

* Om du tar bort förkonfigurerade lösningen i [azureiotsuite.com][lnk-azureiotsuite], du ta bort alla resurser som etablerades när du skapade den förkonfigurerade lösningen. Om du har lagt till fler resurser till resursgruppen raderas även dessa resurser. 
* Om du tar bort resursgrupp i den [Azure-portalen][lnk-azure-portal], bara ta bort resurserna i resursgruppen. Du måste också ta bort Azure Active Directory-programmet som är associerade med förkonfigurerade lösningen i den [Azure-portalen][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub-instanser kan etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration][link-azuresublimits]. Du kan skapa en [Azure supportärende] [ link-azuresupportticket] att höja gränsen. Eftersom varje förkonfigurerade lösning etablerar en ny IoT-hubb, kan du därför bara etablera upp till 10 förkonfigurerade lösningar i en viss prenumeration. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure DB som Cosmos-instanser kan etablera i en prenumeration?

Femtio. Du kan skapa en [Azure supportärende] [ link-azuresupportticket] att höja gränsen, men som standard kan du bara etablera 50 Cosmos DB instanser per prenumeration. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hur många kostnadsfria Bing Maps-API:er kan jag etablera i en prenumeration?

Två. Du kan skapa två interna transaktioner nivå 1 Bing Maps för Enterprise planer i en Azure-prenumeration. Fjärråtkomst övervakningslösning etableras som standard med interna transaktioner nivå 1-plan. Detta betyder att du bara kan etablera upp till två fjärrövervakningslösningar i en prenumeration utan några ändringar.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Jag har en distribution av en fjärrövervakningslösning med en statisk karta. Hur lägger jag till en interaktiv Bing-karta?

1. Hämta Bing Maps API för Enterprise QueryKey från [Azure-portalen][lnk-azure-portal]: 
   
   1. Gå till resursgruppen där dina Bing Maps API för företag finns i den [Azure-portalen][lnk-azure-portal].
   2. Klicka på **alla inställningar**, sedan **nyckelhantering**. 
   3. Du kan se två nycklar: **MasterKey** och **QueryKey**. Kopiera värdet för **QueryKey**.
      
      > [!NOTE]
      > Har du inget Bing Maps-API för Enterprise-konto? Skapa en i den [Azure-portalen] [ lnk-azure-portal] genom att klicka på + nya, söker efter Bing Maps API för företag och följ anvisningarna för att skapa.
      > 
      > 
2. Hämtar det senaste kod från de [Azure IoT-fjärr-övervakning][lnk-remote-monitoring-github].
3. Kör en lokal eller cloud deployment följande vägledning för distribution i mappen /docs/ i databasen. 
4. När du har kört en lokal eller molnbaserad distribution tittar du i rotmappen efter *.user.config-filen som skapades under distributionen. Öppna den här filen i en textredigerare. 
5. Ändra följande rad att inkludera det värde som du kopierade från din **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en förkonfigurerad lösning om jag har Microsoft Azure för DreamSpark?

För närvarande kan du inte skapa en förkonfigurerade lösning med en [Microsoft Azure för DreamSpark] [ lnk-dreamspark] konto. Du kan dock skapa en [kostnadsfri utvärderingsversion konto för Azure] [ lnk-30daytrial] på ett par minuter som du kan skapa en förkonfigurerade lösning.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Kan jag skapa en förkonfigurerade lösning om jag har Cloud Solution Providers (CSP)-prenumeration?

För närvarande kan skapa du inte en förkonfigurerade lösning med en prenumeration Cloud Solution Providers (CSP). Du kan dock skapa en [kostnadsfri utvärderingsversion konto för Azure] [ lnk-30daytrial] på ett par minuter som du kan skapa en förkonfigurerade lösning.

### <a name="how-do-i-delete-an-aad-tenant"></a>Hur tar jag bort en AAD-klient?

Finns i blogginlägget Eric Golpe [genomgång av du tar bort en Azure AD-klient][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Nästa steg

Du kan även utforska några andra funktioner och möjligheter i de förkonfigurerade lösningarna i IoT Suite:

* [Förutsägande Underhåll förkonfigurerade lösning: översikt][lnk-predictive-overview]
* [Anslutna factory förkonfigurerade lösning: översikt](iot-suite-connected-factory-overview.md)
* [IoT-säkerhet från grunden][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

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
