---
title: Bygg Server lös mobil program Server del med Azure Functions och andra tjänster
description: Lär dig mer om beräknings tjänsterna för att bygga solid Server-Server lös mobil program Server del.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795866"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Bygg komponenter för mobila backend-komponenter med beräknings tjänster
Varje mobil program behöver en server del som ansvarar för data lagring, affärs logik och säkerhet. Genom att hantera infrastrukturen som värd och köra Server dels kod måste du ändra storlek, etablera och skala en mängd servrar, hantera OS-uppdateringar och maskin vara, tillämpa säkerhets korrigeringar och övervaka alla infrastruktur komponenter för prestanda. tillgänglighet och fel tolerans. Detta är när Server lös arkitektur är i praktiska skick som utvecklare inte har några servrar att hantera, inga operativ system eller relaterade program/maskin varu uppdateringar att hantera. Det sparar mycket tid och pengar för utvecklare, vilket innebär snabbare tid till marknad och fokuserat på att utveckla program.

## <a name="benefits-of-compute"></a>Fördelar med beräkning
- Abstraktion av servrar: du behöver inte bekymra dig om värd, uppdatering och säkerhet, så att utvecklarna bara kan fokusera på koden.
- Med snabb och effektiv skalning säkerställs att resurser tillhandahålls automatiskt eller på begäran, oavsett vilken skala du behöver.
- Hög tillgänglighet och fel tolerans.
- Micro Billing garanterar att du bara debiteras för när din kod körs.
- Skriv kod som körs i molnet på valfritt språk.

Använd följande tjänster för att aktivera server lös beräknings funktioner i dina mobila appar.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) är en händelse driven beräknings upplevelse som gör att du kan köra din kod, skrivet i valfritt programmeringsspråk utan att oroa dig för servrar. Utvecklare behöver inte hantera programmet eller infrastrukturen för att köra det. Funktioner skalas på begäran och du betalar bara för den tid som koden körs. Azure Functions är ett bra sätt att implementera ett API för ett mobilt program eftersom de är mycket enkla att implementera och underhålla, och kan nås via HTTP.

**Viktiga funktioner**
- **Händelse driven och skalbart** där utvecklare kan använda **utlösare och bindningar** för att definiera när en funktion anropas och vilka data som den ansluter till.
- **Ta med dina egna beroenden** – Functions stöder NuGet och NPM, så du kan använda dina favoritbibliotek.
- Med **integrerad säkerhet** kan du skydda http-utlösta funktioner med OAuth-leverantörer som Azure Active Directory, Facebook, Google, Twitter och Microsoft-konto.
- **Förenklad integrering** med olika [Azure-tjänster](/azure/azure-functions/functions-overview#integrations) och SaaS-erbjudanden (Software-as-a-Service).
- Med **flexibel utveckling** kan du koda dina funktioner direkt i portalen eller konfigurera kontinuerlig integrering och distribuera din kod via GitHub, Azure DevOps Services och andra utvecklingsverktyg som stöds.
- Functions-körningen är **öppen källkod** och tillgänglig på [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
-  **Förbättrad utvecklings upplevelse** där utvecklare kan koda, testa och felsöka lokalt med hjälp av sitt prioriterade redigerings program eller lättanvända webb gränssnitt med övervakning med integrerade verktyg och inbyggda DevOps-funktioner.
- **Olika programmeringsspråk och värd alternativ** – utveckla med hjälp av C#Node. js, Java, java script eller python.
- **Prismodell – Betala per användning** – Betala endast för tiden som använts för att köra koden.

**Reference**
- [Azure-portalen](https://portal.azure.com)
- [Handlingar](/azure/azure-functions/)
- [Guide för Azure Functions utvecklare](/azure/azure-functions/functions-reference)
- [Snabbstarter](/azure/azure-functions/functions-create-first-function-vs-code)
- [Exempel](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>App Service
[Azure App Service](https://azure.microsoft.com/services/app-service/) Med Azure App Service kan du bygga och vara värd för webbappar och RESTful-API: er i valfritt programmeringsspråk utan att behöva hantera infrastrukturen. Azure Web Apps tillhandahåller automatisk skalning och hög tillgänglighet, har stöd för både Windows och Linux och möjliggör automatiska distributioner från GitHub, Azure DevOps eller valfri Git-lagringsplats.

**Viktiga funktioner**
- Stöd för **flera språk och ramverk** för ASP.NET, ASP.net Core, Java, ruby, Node. js, php eller python. Du kan också köra PowerShell och andra skript eller körbara filer som bakgrunds tjänster.
- **DevOps-optimering** – konfigurera kontinuerlig integrering och distribution med Azure DevOps, GitHub, BitBucket, Docker Hub eller Azure Container Registry. Hantera dina appar i App Service genom att använda Azure PowerShell eller plattforms oberoende kommando rads gränssnitt (CLI).
- **Global skala med hög tillgänglighet** för att skala upp eller ut manuellt eller automatiskt.
- **Anslutningar till SaaS-plattformar och lokala data** som du kan välja mellan 50 anslutningar för företags system (till exempel SAP), SaaS-tjänster (till exempel Salesforce) och Internet tjänster (till exempel Facebook). Komma åt lokala data med hjälp av Hybridanslutningar och virtuella Azure-nätverk.
- **Säkerhet och efterlevnad** – Azure App Service är ISO, SOC och PCI-kompatibel. Autentisera användare med Azure Active Directory eller med social inloggning (Google, Facebook, Twitter och Microsoft). Skapa IP-adressbegränsningar och hantera tjänst identiteter.
- **Programmallar** för att välja bland en omfattande lista över programmallar på Azure Marketplace, till exempel WordPress, Joomla och Drupal.
- **Visual Studio-integrering** med dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa, distribuera och felsöka.

**Reference**
- [Azure-portalen](https://portal.azure.com/)
- [Handlingar](/azure/app-service/)
- [Snabbstarter](/azure/app-service/app-service-web-get-started-dotnet)
- [Exempel](/azure/app-service/samples-cli)
- [Självstudier](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) hanterar din värdmiljö för Kubernetes, vilket gör det enkelt att snabbt distribuera och hantera containerbaserade program utan kunskaper om orkestrering av containrar. Det eliminerar också problem med pågående åtgärder och underhåll genom etablering, uppgradering och skalning av resurser på begäran, utan att koppla från dina program. 

**Viktiga funktioner**
- Du **kan enkelt migrera befintliga program** till behållare och köra dem i AKS.
- **Förenkla distributionen och hanteringen** av mikrotjänster baserade program.
- **Skydda DevOps för AKS** för att uppnå balans mellan hastighet och säkerhet och leverera kod snabbare i stor skala.
- **Skala enkelt med AKS och ACI** för att etablera poddar inuti ACI som startar på några sekunder.
- **Distribution och hantering av IoT-enheter** på begäran.
- **Utbildning av maskin inlärnings modell** med verktyg som TensorFlow och KubeFlow.

**Reference**
- [Azure-portalen](https://portal.azure.com/)
- [Handlingar](/azure/aks/)
- [Snabbstarter](/azure/aks/kubernetes-walkthrough-portal)
- [Självstudier](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container instances (ACI)](https://azure.microsoft.com/services/container-instances/) är en bra lösning för alla scenarier som kan användas i isolerade behållare, inklusive enkla program, uppgifts automatisering och bygg jobb. Utveckla appar snabbt utan att hantera virtuella datorer.

**Viktiga funktioner**
- **Snabba start tider** som ACI kan starta behållare i Azure på några sekunder, utan att behöva etablera och hantera virtuella datorer.
- **Offentlig IP-anslutning och anpassat DNS-namn**.
- **Säkerhet på hypervisor-nivå** som garanterar att ditt program är isolerat i en behållare som i en virtuell dator.
- **Anpassade storlekar** för optimal användning genom att tillåta exakta specifikationer för processor kärnor och minne. Du betalar för vad du behöver och faktureras per sekund, så att du kan finjustera dina utgifter utifrån dina faktiska behov.
- **Beständig lagring** för att hämta och spara tillstånd, ACI erbjuder direkt montering av Azure Filess resurser.
- **Linux-och Windows-behållare** som schemalagts med samma API.

**Reference**
- [Azure-portalen](https://portal.azure.com/)
- [Handlingar](/azure/container-instances/)
- [Snabbstarter](/azure/container-instances/container-instances-quickstart-portal)
- [Exempel](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Självstudier](/azure/container-instances/container-instances-tutorial-prepare-app)