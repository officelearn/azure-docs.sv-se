---
title: "Översikt över Web Apps | Microsoft Docs"
description: "Ta reda på hur Azure App Service hjälper dig att utveckla och hantera webbappar."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f8510bb6b412e9af8aad30ba32bc74206c22042f
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="web-apps-overview"></a>Översikt över Web Apps

*Azure App Service Web Apps* (eller bara Web Apps) är en tjänst som värd för webbprogram, REST API: er och mobila tillbaka slutar. Du kan utveckla din favorit språk, det .NET, .NET Core, Java, Ruby, Node.js, PHP och Python. Du kan köra och skala appar på ett enkelt sätt på Windows- eller Linux-datorer (se [Apptjänst i Linux](containers/app-service-linux-intro.md)). 

Web Apps inte bara lägger till kraften i Microsoft Azure till ditt program, till exempel säkerhet, belastningsutjämning, autoskalning, och automatiserad hantering. Du kan också dra nytta av dess DevOps-funktioner, till exempel kontinuerlig distribution från VSTS, GitHub, Docker-hubb och andra källor, hantering av paketet mellanlagring miljöer, anpassade domäner och SSL-certifikat. 

Med App Service betalar för Azure-beräkningsresurser som du använder. De beräkningsresurser som du använder bestäms av den _programtjänstplanen_ att köra ditt webbprogram på. Mer information finns i [apptjänstplaner i Azure Web Apps](azure-web-sites-web-hosting-plans-in-depth-overview.md).

I följande 5 minuter långa video får du en introduktion till App Service Web Apps.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

## <a name="why-use-web-apps"></a>Fördelar med att använda Web Apps
Här följer några funktioner i App Service Web Apps:

* **Flera språk och ramverk** -webbprogram har förstklassigt stöd för ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP och Python. Du kan också köra [PowerShell och andra skript eller körbara filer](web-sites-create-web-jobs.md) som background tjänster.
* **DevOps-optimering** -Ställ in [kontinuerlig integrering och distribution](app-service-continuous-deployment.md) med Visual Studio Team Services, GitHub, BitBucket, Docker-hubb eller Azure Container Service. Flytta upp uppdateringar via [test- och mellanlagringsmiljöer](web-sites-staged-publishing.md). Hantera dina appar i Web Apps med hjälp av [Azure PowerShell](/powershell/azureps-cmdlets-docs) eller [plattformsoberoende kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli).
* **Global skala med hög tillgänglighet** – skala [upp](web-sites-scale.md) och [ned](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellt och automatiskt. Hantera appar var som helst i Microsofts globala datacenterinfrastruktur med hög tillgänglighet tack vare [serviceavtalet](https://azure.microsoft.com/support/legal/sla/app-service/) för App Service.
* **Anslutningar till SaaS-plattformar och lokala data** – Välj bland fler än 50 [kopplingar](../connectors/apis-list.md) för företagssystem (till exempel SAP), SaaS-tjänster (t.ex Salesforce) och Internettjänster (till exempel Facebook). Åtkomst till lokala data via [hybridanslutningar ](../biztalk-services/integration-hybrid-connection-overview.md) och [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Säkerhet och efterlevnad** – App Service [uppfyller ISO, SOC och PCI](https://www.microsoft.com/TrustCenter/). Autentisera användare med [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) eller med sociala inloggning ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md), och [ Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)). Skapa [IP-adressbegränsningar](app-service-ip-restrictions.md) och [hantera tjänstidentiteter](app-service-managed-service-identity.md).
* **Programmallar** -Välj en omfattande lista med programmallar i den [Azure Marketplace](https://azure.microsoft.com/marketplace/), till exempel WordPress, Joomla och Drupal.
* **Visual Studio-integration** – dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa, distribuera och felsöka.
* **API och mobila funktioner** -webbprogram har nyckelfärdig CORS-stöd för scenarier med RESTful-API och förenklar scenarier för mobila appar genom att aktivera autentisering, datasynkronisering offline, push-meddelanden med mera.
* **Serverlösa kod** – kör ett kodstycke eller skript på begäran utan att behöva etablera uttryckligen eller hantera infrastrukturen och betalar bara för beräkning tiden koden faktiskt använder (se [Azure Functions](/azure/azure-functions/)).

Förutom Web Apps i App Service erbjuder Azure andra tjänster som kan användas till att hantera webbplatser och webbappar. I de flesta fall är Web Apps det bästa alternativet.  Arkitektur för mikrotjänster överväga [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Om du behöver mer kontroll över de virtuella datorer koden körs på du [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Mer information om hur du väljer mellan dessa Azure-tjänster hittar du i [Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](choose-web-site-cloud-service-vm.md).

## <a name="next-steps"></a>Nästa steg

Skapa din första webbapp.

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python](app-service-web-get-started-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)

