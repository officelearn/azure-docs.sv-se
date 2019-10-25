---
title: Automatisera apparnas livs cykel med Visual Studio App Center-och Azure-tjänster
description: Lär dig mer om tjänsterna som App Center som hjälper dig att konfigurera kontinuerlig build och integrering för dina mobila program.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795554"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatisera apparnas livs cykel med kontinuerlig version och integrering

Som utvecklare skriver du kod och checkar in den i kod databasen, men incheckningarna som checkas in i lagrings platsen kanske inte alltid är konsekventa. Med flera utvecklare som arbetar med samma projekt kan problem uppstå med integreringen och teamet skulle kunna köras i situationer där saker inte fungerar, buggar fortsätter Piling och projekt utveckling blir fördröjd. Utvecklare måste vänta tills hela program varu koden har skapats och testats för att söka efter fel och det gör processen långsam och mindre iterativ. 

Med **kontinuerlig build och integrering**kan utvecklare förenkla skapandet och testningen av sin kod genom att inte bara spara sina ändringar i käll kods lagrings platsen utan även lägga till tester och verifieringar i build-miljön så att de alltid körs tester mot koden. Alla ändringar som görs i käll koden byggs kontinuerligt när det görs en incheckning av lagrings platsen. Med varje incheckning validerar CI-servern och kör alla tester som utvecklaren har skapat. Om testerna inte skickas skickas koden tillbaka för ytterligare ändringar. Detta gör det möjligt för utvecklaren att inte bryta de versioner som skapas och att inte köra alla tester lokalt på sin dator som i sin tur ökar produktiviteten för utvecklare. 

## <a name="key-benefits"></a>Viktiga fördelar
- **Automatiserad** utveckling, testning och distribution av pipeliner.
- **Identifiera buggar och åtgärda problem** tidigt för att säkerställa snabbare versions hastigheter.
- **Genomför kod** oftare och bygg program snabbt.
- **Flexibelt** att ändra kod snabbt utan problem.
- En **snabbare tid till marknad** garanterar att endast bra kvalitets kod gör det helt enkelt.
- **Små kod ändringar** så att du kan integrera små delar av kod i taget.
- **Förbättra teamets genomskinlighet och ansvars** områden gör att du kan få **kontinuerlig feedback** från dina kunder utan även ditt team.

Använd följande tjänster för att aktivera kontinuerlig integrering av pipelinen i dina mobila appar.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center build](/appcenter/build/) service hjälper dig att bygga interna och plattforms oberoende program som ditt team arbetar med med en säker moln infrastruktur. Du kan enkelt ansluta din lagrings platsen i App Center och börja skapa din app i molnet på varje genomförande utan att behöva oroa dig för att konfigurera Bygg servrar lokalt, komplicerade konfigurationer och kod som bygger på en kollegas dator, men inte din.

Med den extra kraften hos App Center Services kan du automatisera arbets flödet ytterligare. Släpp versioner automatiskt till tester och offentliga app-butiker med App Center distribuera, eller kör automatiserade UI-tester på tusentals verkliga enheter och OS-konfigurationer i molnet med App Center-test.

**Viktiga funktioner**
- **Konfigurera kontinuerlig integrering** på några minuter och skapa program oftare och snabbare.
- Integrera med **GitHub, BitBucket, Azure DevOps och GitLab**.
- **Snabba och säkra versioner** av hanterade, molnbaserade datorer.
- **Aktivera dina versioner för att "starta test"** och kontrol lera om appen bygger på verkliga iOS-och Android-enheter.
- **Inbyggt och plattforms oberoende support** – iOS, Android, MacOS, Windows, Xamarin, preagerat Native.
- **Anpassa dina versioner** genom att lägga till skript för efter kloning, före kompilering och efter kompilering.

**Reference**
- [Registrera dig med App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Kom igång med App Center build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure-pipelines
 [Azure-pipeliner](https://azure.microsoft.com/services/devops/pipelines/), en tjänst i Azure DevOps, är en tjänst för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) som fungerar med din prioriterade git-Provider och kan distribueras till de flesta större moln tjänster, inklusive Azure. Du kan börja med din kod på GitHub, GitHub Enterprise Server, GitLab, BitBucket Cloud eller Azure databaser. Sedan kan du automatisera bygge, testning och distribution av koden till Microsoft Azure, Google Cloud Platform eller Amazon Web Services.

**Viktiga funktioner**
- Förenklad uppgifts baserad upplevelse för att konfigurera en CI-Server för både inbyggda (Android-, iOS-och Windows-) och plattforms oberoende (Xamarin-, Cordova-och reagerar inbyggda) mobila program, förutom Microsoft och icke-Microsoft (Node. js, Java)-baserad server programmen.
- **Alla språk, plattformar och molnbaserade** , test-och distribution av Node. js-, python-, Java-, php-, ruby-,C++go C#-, C/,-, Android-och iOS-program. Kör parallellt på Linux, macOS och Windows. Distribuera till moln leverantörer som Azure, AWS och GCP. Distribuera mobila program via beta kanaler och app-butiker.
- **Stöd för intern behållare** – skapa nya behållare enkelt och skicka dem till alla register. Distribuera behållare till oberoende värdar eller Kubernetes.
- **Avancerade arbets flöden** – enkel länkning och skapande av flera faser. Stöd för YAML, test integration, versions portar, rapportering med mera.
- **Utöknings bar** – Använd en mängd olika bygg-, test-och distributions uppgifter som skapats av communityn – hundratals tillägg från slack till SonarCloud. Du kan även distribuera från andra CI-system, t. ex. Jenkins, och använda Webhooks och REST-API: er för att hjälpa dig att integrera
- **Kostnads fria, värdbaserade molnbaserade versioner** för offentliga och privata lagrings platser.
- **Har stöd för distribution till andra moln leverantörer** som Amazon Web Services, Google Cloud Platform osv.

**Reference**
- [Kom igång med Azures guide för pipeline](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Kom igång med Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Snabbstarter](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Om du vill välja rätt tjänst för dina program versioner följer du artikeln som jämför [App Center build vs. Azure-pipeliner](/appcenter/build/choose-between-services).
