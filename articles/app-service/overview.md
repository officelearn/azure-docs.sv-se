---
title: Översikt över App Service – Azure | Microsoft Docs
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
ms.custom: seodec18
ms.openlocfilehash: a3ff9b6fc1abf36bf2feddf518e4e920f18a3c23
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835353"
---
# <a name="app-service-overview"></a>Översikt över App Service

*Azure App Service* är en värdtjänst för webbprogram, REST API:er och mobila backend-servrar. Du kan utveckla i det språk du föredrar, oavsett om det är .NET, .NET Core, Java, Ruby, Node.js, PHP eller Python. Program körs och skalas enkelt i både Windows- och Linux-baserade miljöer. Mer information om Linux-baserade miljöer finns i [App Service i Linux](containers/app-service-linux-intro.md). 

App Services lägger inte bara till kraften hos Microsoft Azure i ditt program, t.ex. säkerhet, belastningsutjämning, automatisk skalning och automatiserad hantering. Du kan även dra nytta av dess DevOps-funktioner, t.ex. löpande utveckling från Azure DevOps, GitHub, Docker Hub och andra källor, pakethantering, mellanlagringsmiljöer, anpassade domäner och SSL-certifikat. 

Med App Service betalar du för de Azure-beräkningsresurer du använder. De beräkningsresurser du använder fastställs av den _App Service-plan_ som du använder för att köra appar på. Mer information finns i [Översikt över Azure App Service planer](overview-hosting-plans.md).

## <a name="why-use-app-service"></a>Varför använda Azure Apptjänst?

Här följer några funktioner i App Service:

* **Flera språk och ramverk** – App Service har förstklassigt stöd för ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP och Python. Du kan också köra [PowerShell och andra skript och körbara filer](webjobs-create.md) som bakgrundstjänster.
* **DevOps-optimering** – Konfigurera [kontinuerlig integrering och distribution](deploy-continuous-deployment.md) med Azure DevOps, GitHub, BitBucket, Docker Hub eller Azure Container Registry. Flytta upp uppdateringar via [test- och mellanlagringsmiljöer](deploy-staging-slots.md). Hantera dina appar i App Service med [Azure PowerShell](/powershell/azureps-cmdlets-docs) eller det [plattformsoberoende kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli).
* **Global skala med hög tillgänglighet** – skala [upp](web-sites-scale.md) och [ned](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellt och automatiskt. Hantera appar var som helst i Microsofts globala datacenterinfrastruktur med hög tillgänglighet tack vare [serviceavtalet](https://azure.microsoft.com/support/legal/sla/app-service/) för App Service.
* **Anslutningar till SaaS-plattformar och lokala data** – välj bland fler än 50 [anslutningsappar](../connectors/apis-list.md) för företagssystem (till exempel SAP), SaaS-tjänster (till exempel Salesforce) och internettjänster (till exempel Facebook). Åtkomst till lokala data via [hybridanslutningar ](app-service-hybrid-connections.md) och [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Säkerhet och efterlevnad** – App Service [uppfyller ISO, SOC och PCI](https://www.microsoft.com/en-us/trustcenter). Autentisera användare med [Azure Active Directory](configure-authentication-provider-aad.md) eller med social inloggning ([Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) och [Microsoft](configure-authentication-provider-microsoft.md)). Skapa [IP-adressbegränsningar](app-service-ip-restrictions.md) och [hantera tjänstidentiteter](overview-managed-identity.md).
* **Programmallar** – välj från en omfattande lista med programmallar i [Azure Marketplace](https://azure.microsoft.com/marketplace/), till exempel WordPress, Joomla och Drupal.
* **Visual Studio-integration** – dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa, distribuera och felsöka.
* **API och mobila funktioner** – App Service ger nyckelfärdigt CORS-stöd för RESTful API-scenarier, och förenklar mobilappscenarier genom att möjliggöra autentisering, synkronisering av offlinedata, push-meddelanden med mera.
* **Serverlös kod** – kör ett kodfragment eller skript på begäran utan att explicit behöva etablera eller hantera infrastruktur, och betala bara för den beräkningstid som koden faktiskt använder (se [Azure Functions](/azure/azure-functions/)).

Förutom App Service erbjuder Azure andra tjänster som kan användas till att hantera webbplatser och webbappar. För de flesta scenarier är App Service det bästa valet.  För arkitektur för mikrotjänster kan du använda [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric). Om du behöver större kontroll över de virtuella datorer koden körs på kan du använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Mer information om hur du väljer mellan dessa Azure-tjänster hittar du i [Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](overview-compare.md).

## <a name="next-steps"></a>Nästa steg

Skapa din första webbapp.

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (på Linux)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (på Linux)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
