---
title: Automatisera apparnas livs cykel med Visual Studio App Center-och Azure-tjänster
description: Lär dig mer om tjänsterna som App Center som hjälper dig att konfigurera kontinuerlig build och integrering för dina mobila program.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: 86f75e393f54827a58627e3c41b19563c3983ac9
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2020
ms.locfileid: "84482844"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatisera apparnas livs cykel med kontinuerlig version och integrering

Som utvecklare skriver du kod och markerar den i kod databasen, men incheckningarna som checkas in i lagrings platsen kanske inte alltid är konsekventa. När flera utvecklare arbetar med samma projekt kan problem uppstå med integreringen. Team kan köras i situationer där saker inte fungerar, buggar och projekt utveckling blir fördröjda. Utvecklare måste vänta tills hela program varu koden har skapats och testats för att söka efter fel, vilket gör processen långsam och mindre iterativ. 

Med kontinuerlig build och integrering kan utvecklare förenkla build och testa sin kod genom att spara sina ändringar i käll kods lagrings platsen och lägga till tester och verifieringar i build-miljön. På så sätt körs de alltid för att testa koden. Alla ändringar som görs i käll koden skapas kontinuerligt när det görs en incheckning av lagrings platsen. Med varje incheckning validerar och kör servern för kontinuerlig integrering (CI) alla test som skapas av utvecklaren. Om testerna inte skickas skickas koden tillbaka för ytterligare ändringar. På så sätt bryter utvecklarna inte de versioner som skapas. De behöver inte heller köra alla tester lokalt på sina datorer, vilket ökar produktiviteten för utvecklare. 

## <a name="key-benefits"></a>Viktiga fördelar
- Automatisera dina versioner, tester och distributioner för pipeliner.
- Identifiera buggar och åtgärda problem tidigt för att säkerställa snabbare versions hastigheter.
- Genomför kod oftare och bygg program snabbt.
- Få flexibilitet att ändra kod snabbt utan problem.
- Få snabbare tid till marknad så att endast bra kvalitets kod gör den till det hela vägen.
- Gör små kod ändringar mer effektivt eftersom små delar av kod är integrerade på en och samma tidpunkt.
- Öka teamets öppenhet och ansvar så att du får kontinuerlig feedback från dina kunder och ditt team.

Använd följande tjänster för att aktivera en pipeline för kontinuerlig integrering i dina mobila appar.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center build](/appcenter/build/) hjälper dig att bygga inbyggda program och plattforms oberoende program som ditt team arbetar med med hjälp av en säker moln infrastruktur. Du kan enkelt ansluta din lagrings platsen i Visual Studio App Center och börja skapa din app i molnet vid varje genomförande. Du behöver inte bekymra dig om att konfigurera Bygg servrar lokalt, komplicerade konfigurationer och kod som bygger på en kollegas dator, men inte din.

Med den extra kraften hos Visual Studio App Center Services kan du automatisera arbets flödet ytterligare. Du kan automatiskt frigöra versioner till tester och offentliga app-butiker med App Center distribuera. Du kan också köra automatiserade UI-tester på tusentals verkliga enheter och OS-konfigurationer i molnet med App Center-test.

**Huvudfunktioner**
- Konfigurera kontinuerlig integrering på några minuter och skapa program oftare och snabbare.
- Integrera med GitHub, BitBucket, Azure DevOps och GitLab.
- Skapa snabba och säkra versioner på hanterade, molnbaserade datorer.
- Aktivera dina versioner för att starta test och kontrol lera om appen bygger på verkliga iOS-och Android-enheter.
- Få inbyggt och plattforms oberoende stöd för iOS, Android, macOS, Windows, Xamarin och reagera internt.
- Anpassa dina versioner genom att lägga till skript för efter kloning, före kompilering och efter kompilering.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Kom igång med App Center build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure-pipeliner](https://azure.microsoft.com/services/devops/pipelines/), en tjänst i Azure DevOps, är en komplett kontinuerlig integrerings-och kontinuerlig leverans tjänst (CD) som fungerar med din prioriterade git-Provider. Den kan distribuera till de flesta större moln tjänster, som innehåller Azure. Du kan börja med din kod på GitHub, GitHub Enterprise Server, GitLab, BitBucket Cloud eller Azure databaser. Sedan kan du automatisera bygge, testning och distribution av koden till Microsoft Azure, Google Cloud Platform eller Amazon Web Services (AWS).

**Huvudfunktioner**
- **Förenklad uppgifts baserad upplevelse för att konfigurera en CI-Server:** Konfigurera en CI-Server för både inbyggda (Android-, iOS-och Windows-) och plattforms oberoende (Xamarin-, Cordova-och reagerar inbyggda) mobila program, förutom Microsoft och icke-Microsoft (Node. js, Java)-baserade server tekniker.
- **Valfritt språk, plattform och moln:** Bygg, testa och distribuera Node. js-, python-, Java-, PHP-, ruby-, go-, C/C++-, C#-, Android-och iOS-program. Kör parallellt på Linux, macOS och Windows. Distribuera till moln leverantörer som Azure, AWS och Google Cloud Platform. Distribuera mobila program via beta kanaler och app-butiker.
- **Stöd för inbyggd behållare:** Skapa nya behållare utan problem och skicka dem till alla register. Distribuera behållare till oberoende värdar eller Kubernetes.
- **Avancerade arbets flöden:** Skapa enkelt Bygg kedjor och versioner med fler faser. Få support för YAML, test integration, versions portar, rapportering med mera.
- **Utöknings bar:** Använd en mängd bygg-, test-och distributions uppgifter som skapats av communityn, som innehåller hundratals tillägg från slack till SonarCloud. Du kan även distribuera från andra CI-system, t. ex. Jenkins. Webhooks och REST-API: er kan hjälpa dig att integrera.
- **Kostnads fria, värdbaserade molnbaserade versioner:** Dessa versioner är tillgängliga för offentliga och privata lagrings platser.
- **Stöd för distribution till andra moln leverantörer:** Leverantörer inkluderar AWS och Google Cloud Platform.

**Referenser**
- [Kom igång med Azures guide för pipeline](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Kom igång med Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Snabbstarter](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

För att hjälpa dig att välja rätt tjänst för dina program versioner, se artikeln som jämför [App Center build vs. Azure-pipeliner](/appcenter/build/choose-between-services).
