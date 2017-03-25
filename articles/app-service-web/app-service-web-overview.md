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
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 83c063bb003ccda80ba6c1a74aadcd624d77ae47
ms.lasthandoff: 03/21/2017


---
# <a name="web-apps-overview"></a>Översikt över Web Apps
*App Service Web Apps* är en helt hanterad beräkningsplattform som är optimerad för hantering av webbplatser och webbappar. Det här PaaS-erbjudandet [(platform as a service)](https://en.wikipedia.org/wiki/Platform_as_a_service) från Microsoft Azure gör att du fokusera på affärslogiken medan Azure tar hand om infrastrukturen för att köra och skala appar.

I följande 5 minuter långa video får du en introduktion till App Service Web Apps.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>Vad är en webbapp i App Service för något?
I App Service är en *webbapp* de beräkningsresurser som Azure tillhandahåller för hantering av en webbplats eller webbapp.  

Beräkningsresurserna kan finnas på delade eller dedikerade virtuella datorer, beroende på prisnivå. Programkoden körs på en hanterad virtuell dator som är isolerad från andra kunder.

Koden kan vara i valfritt språk eller ramverk som hanteras av [Azure App Service](../app-service/app-service-value-prop-what-is.md), till exempel ASP.NET, Node.js, Java, PHP och Python. Du kan också köra skript som använder [PowerShell och andra skriptspråk](web-sites-create-web-jobs.md#acceptablefiles) i en webbapp.

Exempel på vanliga programscenarier som du kan använda Web Apps för finns i avsnitten [Webbapp-scenarier](https://azure.microsoft.com/documentation/scenarios/web-app/) och **Scenarier och rekommendationer** i [Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Fördelar med att använda Web Apps
Här följer några funktioner i App Service som gäller för Web Apps:

* **Flera språk och ramverk** – App Service har förstklassigt stöd för ASP.NET, Node.js, Java, PHP och Python. Du kan också köra [PowerShell och andra skript och körbara filer](web-sites-create-web-jobs.md) på virtuella datorer i App Service.
* **DevOps-optimering** – Konfigurera [kontinuerlig integrering och distribution](app-service-continuous-deployment.md) med Visual Studio Team Services, GitHub eller BitBucket. Flytta upp uppdateringar via [test- och mellanlagringsmiljöer](web-sites-staged-publishing.md). Utför [A/B-test](app-service-web-test-in-production-get-start.md). Hantera dina appar i App Service med [Azure PowerShell](/powershell/azureps-cmdlets-docs) eller det [plattformsoberoende kommandoradsgränssnittet (CLI)](../cli-install-nodejs.md).
* **Global skala med hög tillgänglighet** – skala [upp](web-sites-scale.md) och [ned](../monitoring-and-diagnostics/insights-how-to-scale.md) manuellt och automatiskt. Hantera appar var som helst i Microsofts globala datacenterinfrastruktur med hög tillgänglighet tack vare [serviceavtalet](https://azure.microsoft.com/support/legal/sla/app-service/) för App Service.
* **Anslutningar till SaaS-plattformar och lokala data** – välj bland fler än 50 [anslutningsappar](../connectors/apis-list.md) för företagssystem (till exempel SAP, Siebel och Oracle), SaaS-tjänster (till exempel Salesforce och Office 365) och internettjänster (till exempel Facebook och Twitter). Åtkomst till lokala data via [hybridanslutningar ](../biztalk-services/integration-hybrid-connection-overview.md) och [Azure Virtual Networks](web-sites-integrate-with-vnet.md).
* **Säkerhet och efterlevnad** – App Service [uppfyller ISO, SOC och PCI](https://www.microsoft.com/TrustCenter/).
* **Programmallar** – välj från en omfattande lista med programmallar i [Azure Marketplace](https://azure.microsoft.com/marketplace/) där du kan använda en guide till att installera populära program med öppen källkod som WordPress, Joomla och Drupal.
* **Visual Studio-integration** – dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa, distribuera och felsöka.

Dessutom kan en webbapp dra nytta av funktioner i [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) (t.ex. stöd för CORS) och [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) (t.ex. push-meddelanden). Mer information om apptyperna i App Service finns i [Översikt över Azure App Service](../app-service/app-service-value-prop-what-is.md).

Förutom Web Apps i App Service erbjuder Azure andra tjänster som kan användas till att hantera webbplatser och webbappar. I de flesta fall är Web Apps det bästa alternativet.  För arkitektur för mikrotjänster kan du använda [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric), och om du behöver större kontroll över de virtuella datorer koden körs på kan du använda [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/). Mer information om hur du väljer mellan dessa Azure-tjänster hittar du i [Jämförelse mellan Azure App Service, Virtual Machines, Service Fabric och Cloud Services](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Komma igång
Kom igång med att distribuera exempelkod till en ny webbapp i App Service genom att följa en av självstudiekurserna i följande nedrullningsbara listruta. Du behöver ett kostnadsfritt Azure-konto.

> [!div class="op_single_selector"]
> * [Distribuera din första HTML-webbplats till Azure på fem minuter](app-service-web-get-started-html-cli-nodejs.md)
> * [Distribuera din första ASP.NET-webbapp till Azure på fem minuter](app-service-web-get-started-dotnet-cli-nodejs.md)
> * [Distribuera din första PHP-webbapp till Azure på fem minuter](app-service-web-get-started-php-cli-nodejs.md)
> * [Distribuera din första Node.js-webbapp till Azure på fem minuter](app-service-web-get-started-nodejs-cli-nodejs.md)
> * [Distribuera din första Python-webbapp till Azure på fem minuter](app-service-web-get-started-python-cli-nodejs.md)
> * [Distribuera din första Java-webbapp till Azure på fem minuter](app-service-web-get-started-java.md)
> 
> 

> [!NOTE]
> Du kan [Prova App Service](https://azure.microsoft.com/try/app-service/) utan ett Azure-konto. Skapa en startapp och testa den i upp till en timme – inget kreditkort behövs, inga åtaganden.
> 
> 

