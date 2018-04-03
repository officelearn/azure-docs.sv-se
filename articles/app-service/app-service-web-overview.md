---
title: Översikt över Web Apps | Microsoft Docs
description: Ta reda på hur Azure App Service hjälper dig att utveckla och hantera webbappar.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: e789d3c6e10399491eb055b850a590ae10aacd11
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="web-apps-overview"></a>Översikt över Web Apps

*Azure App Service Web Apps* (eller bara Web Apps) är en tjänst som kan användas som värd för webbprogram, REST API:er och mobila serverdelar. Du kan utveckla i det språk du föredrar, oavsett om det är .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python. Du kan enkelt köra och skala appar på virtuella Windows- eller Linux-datorer (se [App Service på Linux](containers/app-service-linux-intro.md)). 

Web Apps lägger inte bara till kraften hos Microsoft Azure i ditt program, t.ex. säkerhet, belastningsutjämning, automatisk skalning och automatiserad hantering. Du kan även dra nytta av dess DevOps-funktioner, t.ex. löpande utveckling från VSTS, GitHub, Docker Hub och andra källor, pakethantering, mellanlagringsmiljöer, anpassade domäner och SSL-certifikat. 

Med App Service betalar du för de Azure-beräkningsresurer du använder. De beräkningsresurser du använder fastställs av _App Service-planen_ som du använder för att köra Web Apps. Mer information finns i [App Service plans in Azure Web Apps](azure-web-sites-web-hosting-plans-in-depth-overview.md) (App Service-planer i Azure Web Apps).

## <a name="why-use-web-apps"></a>Fördelar med att använda Web Apps

Här följer några funktioner i App Service Web Apps:

* **Flera språk och ramverk** – Web Apps har förstklassigt stöd för ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP och Python. Du kan också köra [PowerShell och andra skript och körbara filer](web-sites-create-web-jobs.md) som bakgrundstjänster.
* **DevOps-optimering** – Konfigurera [kontinuerlig integrering och distribution](app-service-continuous-deployment.md) med Visual Studio Team Services, GitHub, BitBucket, Docker Hub eller Azure Container Registry. Flytta upp uppdateringar via [test- och mellanlagringsmiljöer](web-sites-staged-publishing.md). Hantera dina appar i Web Apps med [Azure PowerShell](/powershell/azureps-cmdlets-docs) eller det [plattformsoberoende kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli).
* **Global skala med hög tillgänglighet** – skala [upp](web-sites-scale.md) och [ned](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellt och automatiskt. Hantera appar var som helst i Microsofts globala datacenterinfrastruktur med hög tillgänglighet tack vare [serviceavtalet](https://azure.microsoft.com/support/legal/sla/app-service/) för App Service.
* **Anslutningar till SaaS-plattformar och lokala data** – välj bland fler än 50 [anslutningsappar](../connectors/apis-list.md) för företagssystem (till exempel SAP), SaaS-tjänster (till exempel Salesforce) och internettjänster (till exempel Facebook). Åtkomst till lokala data via [hybridanslutningar ](../biztalk-services/integration-hybrid-connection-overview.md) och [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Säkerhet och efterlevnad** – App Service [uppfyller ISO, SOC och PCI](https://www.microsoft.com/TrustCenter/). Autentisera användare med [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) eller med social inloggning ([Google](app-service-mobile-how-to-configure-google-authentication.md), [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md), [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) och [Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)). Skapa [IP-adressbegränsningar](app-service-ip-restrictions.md) och [hantera tjänstidentiteter](app-service-managed-service-identity.md).
* **Programmallar** – välj från en omfattande lista med programmallar i [Azure Marketplace](https://azure.microsoft.com/marketplace/), till exempel WordPress, Joomla och Drupal.
* **Visual Studio-integration** – dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa, distribuera och felsöka.
* **API och mobila funktioner** – Web Apps ger nyckelfärdigt CORS-stöd för RESTful API-scenarier, och förenklar mobilappscenarier genom att möjliggöra autentisering, synkronisering av offlinedata, push-meddelanden med mera.
* **Serverlös kod** – kör ett kodfragment eller skript på begäran utan att explicit behöva etablera eller hantera infrastruktur, och betala bara för den beräkningstid som koden faktiskt använder (se [Azure Functions](/azure/azure-functions/)).

Förutom Web Apps i App Service erbjuder Azure andra tjänster som kan användas till att hantera webbplatser och webbappar. I de flesta fall är Web Apps det bästa alternativet.  För arkitektur för mikrotjänster kan du använda [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Om du behöver större kontroll över de virtuella datorer koden körs på kan du använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Mer information om hur du väljer mellan dessa Azure-tjänster hittar du i [Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](choose-web-site-cloud-service-vm.md).

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
