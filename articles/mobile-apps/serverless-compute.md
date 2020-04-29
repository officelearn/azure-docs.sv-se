---
title: Bygg upp en server lös mobil program Server del med Azure Functions och andra tjänster
description: Lär dig mer om de beräknings tjänster som används för att bygga en solid Server lös Server lös mobil program Server del.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240150"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Bygg mobila Server dels komponenter med beräknings tjänster
Varje mobil program behöver en server del som ansvarar för data lagring, affärs logik och säkerhet. Genom att hantera infrastrukturen som värd och köra backend-kod måste du ändra storlek, etablera och skala flera servrar. Du måste också hantera OS-uppdateringar och den berörda maskin varan och använda säkerhets korrigeringar. Sedan måste du övervaka alla dessa infrastruktur komponenter för prestanda, tillgänglighet och fel tolerans. 

Server lös arkitektur är användbart för den här typen av scenario eftersom du inte har några servrar att hantera och inga operativ system eller relaterade program-eller maskin varu uppdateringar att hantera. Server lös arkitektur sparar tid och kostnad för utvecklare, vilket innebär snabbare tid till marknad och fokuserat på att utveckla program.

## <a name="benefits-of-compute"></a>Fördelar med beräkning
- Abstraktion av servrar innebär att du inte behöver bekymra dig om värd, uppdatering och säkerhet, vilket innebär att du bara kan fokusera på koden.
- Med snabb och effektiv skalning säkerställs att resurser tillhandahålls automatiskt eller på begäran, oavsett vilken skala du behöver.
- Hög tillgänglighet och fel tolerans.
- Micro-fakturering garanterar att du bara faktureras för när din kod körs.
- Kod körningar i molnet skrivet på valfritt språk.

Använd följande tjänster för att aktivera server lös beräknings funktioner i dina mobila appar.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) är en händelse driven beräknings upplevelse som du kan använda för att köra din kod, skrivet i valfritt programmeringsspråk, utan att oroa dig för servrar. Du behöver inte hantera programmet eller infrastrukturen för att köra det. Funktioner skalas på begäran och du betalar bara för den tid som koden körs. Azure Functions är ett bra sätt att implementera ett API för ett mobil program. De är enkla att implementera och underhålla och är tillgängliga via HTTP.

**Huvudfunktioner**
- Händelse driven och skalbart där du kan använda utlösare och bindningar för att definiera när en funktion anropas och vilka data som den ansluter till.
- Ta med dina egna beroenden eftersom Functions har stöd för NuGet och NPM, så att du kan använda dina favorit bibliotek.
- Integrerad säkerhet så att du kan skydda HTTP-utlösta funktioner med OAuth-leverantörer som Azure Active Directory, Facebook, Google, Twitter och Microsoft-konto.
- Förenklad integrering med olika [Azure-tjänster](/azure/azure-functions/functions-overview) och SaaS-erbjudanden (Software as a Service).
- Flexibel utveckling så att du kan koda dina funktioner direkt i Azure Portal eller konfigurera kontinuerlig integrering och distribuera din kod via GitHub, Azure DevOps Services och andra utvecklingsverktyg som stöds.
- Functions runtime är öppen källkod och tillgänglig på [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Förbättrad utvecklings upplevelse där du kan koda, testa och felsöka lokalt med hjälp av deras prioriterade redigerings program eller lättanvända webb gränssnitt med övervakning med integrerade verktyg och inbyggda DevOps-funktioner.
- Olika programmeringsspråk och värd alternativ för utveckling, till exempel C#, Node. js, Java, java script eller python.
- Pris modellen betala per användning innebär att du bara betalar för den tid som du ägnat åt att köra din kod.

**Referenser**
- [Azure Portal](https://portal.azure.com)
- [Azure Functions-dokumentation](/azure/azure-functions/)
- [Utvecklarguide för Azure Functions](/azure/azure-functions/functions-reference)
- [Snabbstarter](/azure/azure-functions/functions-create-first-function-vs-code)
- [Exempel](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Med [Azure App Service](https://azure.microsoft.com/services/app-service/)kan du bygga och vara värd för webbappar och RESTful-API: er i valfritt programmeringsspråk utan att behöva hantera infrastrukturen. Den erbjuder automatisk skalning och hög tillgänglighet, stöder både Windows och Linux och aktiverar automatiserade distributioner från GitHub, Azure DevOps eller git lagrings platsen.

**Huvudfunktioner**
- Stöd för flera språk och ramverk för ASP.NET, ASP.NET Core, Java, ruby, Node. js, PHP eller python. Du kan också köra PowerShell och andra skript och körbara filer som bakgrundstjänster.
- DevOps-optimering genom kontinuerlig integrering och distribution med Azure DevOps, GitHub, BitBucket, Docker Hub eller Azure Container Registry. Hantera dina appar i App Service med Azure PowerShell eller det plattformsoberoende kommandoradsgränssnittet (CLI).
- Global skala med hög tillgänglighet för att skala upp eller ut manuellt eller automatiskt.
- Anslutningar till SaaS-plattformar och lokala data som du kan välja mellan fler än 50 anslutningar för företags system som SAP, SaaS-tjänster som Salesforce och Internet tjänster som Facebook. Få åtkomst till lokala data med hjälp av hybrid anslutningar och virtuella Azure-nätverk.
- Azure App Service är ISO, SOC och PCI-kompatibel. Autentisera användare med Azure Active Directory eller med inloggning för sociala medier som Google, Facebook, Twitter och Microsoft. Skapa IP-adressbegränsningar och hantera tjänstidentiteter.
- Programmallar för att välja bland en omfattande lista över programmallar i Azure Marketplace, till exempel WordPress, Joomla och Drupal.
- Visual Studio-integrering med dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa, distribuera och felsöka.

**Referenser**
- [Azure Portal](https://portal.azure.com/)
- [Azure App Service dokumentation](/azure/app-service/)
- [Snabbstarter](/azure/app-service/app-service-web-get-started-dotnet)
- [Exempel](/azure/app-service/samples-cli)
- [Självstudier](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) hanterar din värdbaserade Kubernetes-miljö. AKS gör det snabbt och enkelt att distribuera och hantera containerbaserade program utan kunskaper om orkestrering av containrar. Dessutom elimineras belastningen för pågående åtgärder och underhåll. AKS-regler, uppgraderingar och skalning av resurser på begäran, utan att ta dina program offline.

**Huvudfunktioner**
- Du kan enkelt migrera befintliga program till behållare och köra dem i AKS.
- Förenkla distributionen och hanteringen av mikrotjänster-baserade program.
- Skydda DevOps för AKS för att uppnå balans mellan hastighet och säkerhet och leverera kod snabbare i stor skala.
- Skala enkelt med hjälp av AKS och Azure Container Instances för att etablera poddar inuti Container Instances som börjar på några sekunder.
- Distribuera och hantera IoT-enheter på begäran.
- Träna maskin inlärnings modeller med hjälp av verktyg som TensorFlow och KubeFlow.

**Referenser**
- [Azure Portal](https://portal.azure.com/)
- [Dokumentation om Azure Kubernetes service](/azure/aks/)
- [Snabbstarter](/azure/aks/kubernetes-walkthrough-portal)
- [Självstudier](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container instances](https://azure.microsoft.com/services/container-instances/) är en bra lösning för alla scenarier som kan användas i isolerade behållare, till exempel enkla program, uppgifts automatisering och bygg jobb. Utveckla appar snabbt utan att hantera virtuella datorer.

**Huvudfunktioner**
- Snabba start tider som Container Instances kan starta behållare i Azure på några sekunder, utan att behöva etablera och hantera virtuella datorer.
- Offentlig IP-anslutning och anpassat DNS-namn.
- Säkerhet på hypervisor-nivå som garanterar att ditt program är isolerat i en behållare som i en virtuell dator.
- Anpassade storlekar för optimal användning genom att tillåta exakta specifikationer för processor kärnor och minne. Du betalar för vad du behöver och faktureras per sekund, så att du kan finjustera dina utgifter utifrån dina faktiska behov.
- Beständig lagring för att hämta och spara tillstånd. Container Instances erbjuder direkt montering av Azure Filess resurser.
- Linux-och Windows-behållare som schemalagts med samma API.

**Referenser**
- [Azure Portal](https://portal.azure.com/)
- [Azure Container Instances dokumentation](/azure/container-instances/)
- [Snabbstarter](/azure/container-instances/container-instances-quickstart-portal)
- [Exempel](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Självstudier](/azure/container-instances/container-instances-tutorial-prepare-app)