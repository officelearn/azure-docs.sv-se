---
title: Automatisera livscykeln för dina appar med Visual Studio App Center och Azure-tjänster
description: Lär dig mer om tjänster som App Center som hjälper till att konfigurera kontinuerlig uppbyggnad och integrering för dina mobila applikationer.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240928"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatisera livscykeln för dina appar med kontinuerlig uppbyggnad och integrering

Som utvecklare skriver du kod och checkar in den i koddatabasen, men de åtaganden som kontrolleras i repoerna kanske inte alltid är konsekventa. När flera utvecklare arbetar med samma projekt kan problem komma med integration. Team kan stöta på situationer där saker inte fungerar, buggar hopar sig och projektutveckling försenas. Utvecklare måste vänta tills hela programvarukoden är byggd och testad för att söka efter fel, vilket gör processen långsam och mindre iterativ. 

Med kontinuerlig uppbyggnad och integration kan utvecklare förenkla byggen och testa sin kod genom att genomföra sina ändringar i källkodsarkivet och sätta tester och verifieringar i byggmiljön. På detta sätt är de alltid kör tester mot sin kod. Alla ändringar som görs i källkoden byggs kontinuerligt när det görs ett åtagande i databasen. Vid varje incheckning validerar och kör ci-servern (Continuous Integration) alla tester som utvecklaren har skapat. Om testerna inte går igenom skickas koden tillbaka för ytterligare ändringar. På så sätt bryter utvecklarna inte de versioner som skapas. De behöver inte heller köra alla tester lokalt på sina datorer, vilket ökar utvecklarens produktivitet. 

## <a name="key-benefits"></a>Viktiga fördelar
- Automatisera dina versioner, tester och distributioner för pipelines.
- Identifiera buggar och åtgärda problem tidigt för att säkerställa snabbare utgivningshastigheter.
- Genomför kod oftare och bygg program snabbt.
- Få flexibilitet att ändra kod snabbt utan problem.
- Få snabbare time-to-market så att endast god kvalitet kod gör det hela vägen igenom.
- Gör små kodändringar mer effektivt eftersom små koddelar är integrerade samtidigt.
- Öka teamets transparens och ansvarsskyldighet så att du får kontinuerlig feedback från dina kunder och ditt team.

Använd följande tjänster för att aktivera en pipeline för kontinuerlig integrering i dina mobilappar.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build](/appcenter/build/) hjälper dig att skapa inbyggda program och plattformsoberoende program som ditt team arbetar med med hjälp av en säker molninfrastruktur. Du kan enkelt ansluta din repo i Visual Studio App Center och börja bygga din app i molnet på varje åtagande. Du behöver inte oroa dig för att konfigurera byggservrar lokalt, komplicerade konfigurationer och kod som bygger på en medarbetares dator men inte din.

Med den extra kraften i Visual Studio App Center-tjänsterna kan du automatisera arbetsflödet ytterligare. Du kan automatiskt släppa byggen till testare och offentliga appbutiker med App Center Distribute. Du kan också köra automatiserade gränssnittstester på tusentals riktiga enhets- och OS-konfigurationer i molnet med Test för App Center.

**Huvudfunktioner**
- Konfigurera kontinuerlig integrering på några minuter och skapa program oftare och snabbare.
- Integrera med GitHub, BitBucket, Azure DevOps och GitLab.
- Skapa snabba och säkra byggen på hanterade datorer med molnbaserade värden.
- Aktivera dina versioner för att starta test och kontrollera om appen bygger på verkliga iOS- och Android-enheter.
- Få stöd för inbyggt och plattformsoberoende för iOS, Android, macOS, Windows, Xamarin och React Native.
- Anpassa dina versioner genom att lägga till skript efter klon, förbyggning och efter build.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Komma igång med App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), en tjänst i Azure DevOps, är en komplett fungerande cd-tjänst för kontinuerlig integrering och kontinuerlig leverans (CD) som fungerar med din önskade Git-provider. Den kan distribuera till de flesta större molntjänster, vilket inkluderar Azure. Du kan börja med din kod på GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud eller Azure Repos. Sedan kan du automatisera version, testning och distribution av din kod till Microsoft Azure, Google Cloud Platform eller Amazon Web Services (AWS).

**Huvudfunktioner**
- **Förenklad uppgiftsbaserad upplevelse för att konfigurera en CI-server:** Konfigurera en CI-server för mobila både inbyggda (Android, iOS och Windows) och plattformsoberoende (Xamarin, Cordova och React Native), förutom Microsoft och icke-Microsoft-baserade servertekniker (Node.js, Java).
- **Alla språk, plattformar och moln:** Skapa, testa och distribuera Node.js-, Python-, Java-, PHP-, Ruby-, Go-, C/C++-, C#-, Android- och iOS-program. Kör parallellt på Linux, macOS och Windows. Distribuera till molnleverantörer som Azure, AWS och Google Cloud Platform. Distribuera mobilappar via betakanaler och appbutiker.
- **Stöd för inbyggd behållare:** Skapa nya behållare med lätthet och skicka dem till alla register. Distribuera behållare till oberoende värdar eller Kubernetes.
- **Avancerade arbetsflöden:** Skapa enkelt byggkedjor och flerfasade byggen. Få stöd för YAML, testintegration, släppgrindar, rapportering med mera.
- **Utökningsbar:** Använd en rad bygg-, test- och distributionsuppgifter som skapats av communityn, som innehåller hundratals tillägg från Slack till SonarCloud. Du kan även distribuera från andra CI-system, som Jenkins. Webbkrokar och REST API:er kan hjälpa dig att integrera.
- **Kostnadsfria molnbaserade versioner:** Dessa versioner är tillgängliga för offentliga och privata databaser.
- **Stöd för distribution till andra molnleverantörer:** Bland leverantörerna finns AWS och Google Cloud Platform.

**Referenser**
- [Komma igång med Azure Pipelines-guiden](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Kom igång med Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Snabbstarter](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Information om hur du kan välja rätt tjänst för dina programversioner finns i artikeln som jämför [App Center Build jämfört med Azure Pipelines](/appcenter/build/choose-between-services).
