---
title: Skapa en serverlös mobilprogram back end med Azure Functions och andra tjänster
description: Lär dig mer om de beräkningstjänster som används för att skapa en solid, serverlös mobilappens serverdel.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240150"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Skapa mobila backend-komponenter med beräkningstjänster
Varje mobilprogram behöver en back end som ansvarar för datalagring, affärslogik och säkerhet. För att hantera infrastrukturen så att den är värd för och kör backend-kod måste du ändra storlek, etablera och skala flera servrar. Du måste också hantera OS-uppdateringar och den berörda maskinvaran och tillämpa säkerhetskorrigeringar. Då måste du övervaka alla dessa infrastrukturkomponenter för prestanda, tillgänglighet och feltolerans. 

Serverlös arkitektur är praktiskt för den här typen av scenario eftersom du inte har några servrar att hantera och inga os eller relaterade program- eller maskinvaruuppdateringar att hantera. Serverlös arkitektur sparar utvecklare tid och kostnad, vilket innebär snabbare tid att marknadsföra och fokuserad energi på att bygga applikationer.

## <a name="benefits-of-compute"></a>Fördelar med beräkning
- Abstraktion av servrar innebär att det inte finns någon anledning att oroa sig för hosting, patchning och säkerhet, vilket gör att du kan fokusera enbart på koden.
- Omedelbar och effektiv skalning säkerställer att resurser etableras automatiskt eller på begäran oavsett vilken skala du behöver.
- Hög tillgänglighet och feltolerans.
- Mikrofakturering säkerställer att du bara faktureras för när koden körs.
- Koden körs i molnet som skrivs på det språk du väljer.

Använd följande tjänster för att aktivera serverlösa beräkningsfunktioner i dina mobilappar.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) är en händelsedriven beräkningsupplevelse som du kan använda för att köra din kod, skriven på det programmeringsspråk du väljer, utan att behöva oroa dig för servrar. Du behöver inte hantera programmet eller infrastrukturen för att köra det på. Funktioner skalas på begäran och du betalar bara för den tid koden körs. Azure-funktioner är ett bra sätt att implementera ett API för ett mobilt program. De är lätta att implementera och underhålla och är tillgängliga via HTTP.

**Huvudfunktioner**
- Händelsedriven och skalbar där du kan använda utlösare och bindningar för att definiera när en funktion anropas och vilka data den ansluter.
- Ta med egna beroenden eftersom Functions stöder NuGet och NPM, så att du kan använda dina favoritbibliotek.
- Integrerad säkerhet så att du kan skydda HTTP-utlösta funktioner med OAuth-leverantörer som Azure Active Directory, Facebook, Google, Twitter och Microsoft-konto.
- Förenklad integrering med olika [Azure-tjänster](/azure/azure-functions/functions-overview) och programvara som en tjänst (SaaS) erbjudanden.
- Flexibel utveckling så att du kan koda dina funktioner direkt i Azure-portalen eller konfigurera kontinuerlig integrering och distribuera din kod via GitHub, Azure DevOps Services och andra utvecklingsverktyg som stöds.
- Funktioner körtid är öppen källkod och tillgänglig på [GitHub](https://github.com/azure/azure-webjobs-sdk-script).
- Förbättrad utvecklingsupplevelse där du kan koda, testa och felsöka lokalt med hjälp av deras föredragna redigerare eller lättanvända webbgränssnitt med övervakning med integrerade verktyg och inbyggda DevOps-funktioner.
- Olika programmeringsspråk och värdalternativ för utveckling, till exempel C#, Node.js, Java, JavaScript eller Python.
- Pay-per-use prismodell innebär att du betalar bara för den tid du kör din kod.

**Referenser**
- [Azure-portal](https://portal.azure.com)
- [Azure Functions-dokumentation](/azure/azure-functions/)
- [Utvecklarguide för Azure Functions](/azure/azure-functions/functions-reference)
- [Snabbstarter](/azure/azure-functions/functions-create-first-function-vs-code)
- [Prover](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure Apptjänst
Med [Azure App Service](https://azure.microsoft.com/services/app-service/)kan du skapa och vara värd för webbappar och RESTful API:er på det programmeringsspråk du väljer utan att hantera infrastrukturen. Den erbjuder automatisk skalning och hög tillgänglighet, stöder både Windows och Linux och möjliggör automatiserade distributioner från GitHub, Azure DevOps eller valfri Git-repo.

**Huvudfunktioner**
- Stöd för flera språk och ramverk för ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP eller Python. Du kan också köra PowerShell och andra skript och körbara filer som bakgrundstjänster.
- DevOps optimering genom kontinuerlig integrering och distribution med Azure DevOps, GitHub, BitBucket, Docker Hub eller Azure Container Registry. Hantera dina appar i App Service med Azure PowerShell eller det plattformsoberoende kommandoradsgränssnittet (CLI).
- Global skala med hög tillgänglighet för att skala upp eller ut manuellt eller automatiskt.
- Anslutningar till SaaS-plattformar och lokala data för att välja mellan mer än 50 anslutningsappar för företagssystem som SAP, SaaS-tjänster som Salesforce och internettjänster som Facebook. Få åtkomst till lokala data med hjälp av hybridanslutningar och Virtuella Azure-nätverk.
- Azure App Service är ISO-, SOC- och PCI-kompatibel. Autentisera användare med Azure Active Directory eller med inloggning för sociala medier som Google, Facebook, Twitter och Microsoft. Skapa IP-adressbegränsningar och hantera tjänstidentiteter.
- Programmallar att välja från en omfattande lista över programmallar i Azure Marketplace, till exempel WordPress, Joomla och Drupal.
- Visual Studio-integrering med dedikerade verktyg i Visual Studio effektiviserar arbetet med att skapa, distribuera och felsöka.

**Referenser**
- [Azure-portal](https://portal.azure.com/)
- [Dokumentation för Azure App Service](/azure/app-service/)
- [Snabbstarter](/azure/app-service/app-service-web-get-started-dotnet)
- [Prover](/azure/app-service/samples-cli)
- [Självstudier](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) hanterar din värdbaserade Kubernetes-miljö. AKS gör det snabbt och enkelt att distribuera och hantera containerbaserade program utan kunskaper om orkestrering av containrar. Det eliminerar också bördan av löpande drift och underhåll. AKS-bestämmelser, uppgraderingar och skalor resurser på begäran, utan att ta dina program offline.

**Huvudfunktioner**
- Migrera enkelt befintliga program till behållare och kör inom AKS.
- Förenkla distributionen och hanteringen av mikrotjänstbaserade program.
- Säkra DevOps för AKS för att uppnå balans mellan hastighet och säkerhet och leverera kod snabbare i stor skala.
- Skala enkelt med hjälp av AKS- och Azure Container-instanser för att etablera poddar inuti behållarinstanser som startar på några sekunder.
- Distribuera och hantera IoT-enheter på begäran.
- Träna maskininlärningsmodeller med hjälp av verktyg som TensorFlow och KubeFlow.

**Referenser**
- [Azure-portal](https://portal.azure.com/)
- [Azure Kubernetes-tjänstdokumentation](/azure/aks/)
- [Snabbstarter](/azure/aks/kubernetes-walkthrough-portal)
- [Självstudier](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) är en bra lösning för alla scenarier som kan fungera i isolerade behållare, till exempel enkla program, automatisering av uppgifter och skapa jobb. Utveckla appar snabbt utan att hantera virtuella datorer.

**Huvudfunktioner**
- Snabba starttider som behållarinstanser kan starta behållare i Azure på några sekunder, utan att behöva etablera och hantera virtuella datorer.
- Offentlig IP-anslutning och anpassat DNS-namn.
- Hypervisor-nivå säkerhet som garanterar ditt program är så isolerade i en behållare som det skulle vara i en virtuell dator.
- Anpassade storlekar för optimal användning genom att tillåta exakta specifikationer för CPU-kärnor och minne. Du betalar för vad du behöver och faktureras per sekund, så att du kan finjustera dina utgifter utifrån dina faktiska behov.
- Beständig lagring för att hämta och bevara tillstånd. Behållarinstanser erbjuder direkt montering av Azure Files-resurser.
- Linux- och Windows-behållare som schemalagts med samma API.

**Referenser**
- [Azure-portal](https://portal.azure.com/)
- [Dokumentation av Azure Container Instances](/azure/container-instances/)
- [Snabbstarter](/azure/container-instances/container-instances-quickstart-portal)
- [Prover](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Självstudier](/azure/container-instances/container-instances-tutorial-prepare-app)