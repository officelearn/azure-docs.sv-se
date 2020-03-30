---
title: Automatisera distributionen och versionen av dina mobila program med Visual Studio App Center och Azure-tjänster
description: Lär dig mer om tjänster som App Center som hjälper till att konfigurera kontinuerlig leveranspipeline för dina mobilappar.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235342"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatisera distributionen och lanseringen av dina mobilappar med kontinuerliga leveranstjänster

Som utvecklare skriver du kod och checkar in den i koddatabasen, men de åtaganden som kontrolleras i repoerna kanske inte alltid är konsekventa. När flera utvecklare arbetar med samma projekt kan problem komma med integration. Team kan stöta på situationer där saker inte fungerar, buggar hopar sig och projektutveckling försenas. Utvecklare måste vänta tills hela programvarukoden är byggd och testad för att söka efter fel, vilket gör processen långsam och mindre iterativ.

Med kontinuerlig leverans automatiserar du distributionen och lanseringen av dina mobila program. Det spelar ingen roll om du distribuerar programmet till en grupp testare eller företagsanställda (för betatestning) eller till en App Store (för produktion). Kontinuerlig leverans gör distributioner mindre riskfyllda och uppmuntrar snabba iterationer. Du kan också släppa nya ändringar till dina kunder på ett kontinuerligt sätt.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuera programbinärer till betatestare
Betatestning av din mobilapplikation är ett av de kritiska stegen under programutvecklingsprocessen. Det hjälper till att hitta buggar och problem i ditt program tidigt. Feedbacken förbättrar programkvaliteten när du gör den klar för produktionsanvändning.

Använd följande tjänster för att aktivera en kontinuerlig leveranspipeline i dina mobilappar.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Distribute](/appcenter/distribution/) är ett verktyg för utvecklare att snabbt släppa byggen till enheter. Med en komplett installationsportalupplevelse är App Center Distribute en kraftfull lösning för distribution av testare av betaappar. Det är också ett bekvämt alternativ till distribution via offentliga appbutiker. Utvecklare kan automatisera sitt distributionsarbetsflöde ytterligare med App Center Build och offentliga programlagringsintegreringar.

**Huvudfunktioner**
- Distribuera din app till betatestare och användare och se till att alla dina testare är på den senaste versionen av ditt program.
- Meddela testare av nya utgåvor utan att testare går igenom nedladdningsflödet igen.
- Hantera distributionsgrupper för olika versioner av ditt program.
- Distribuera till butiker: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Få plattformsstöd för iOS, Android, macOS, tvOS, Xamarin, React Native, Unity och Cordova.
- Registrera automatiskt iOS-enheter till din etableringsprofil.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Komma igång med App Center Distribute](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) är en fullfjädrad tjänst för kontinuerlig integrering (CI) och CD-tjänst (continuous delivery) som fungerar med din önskade Git-leverantör. Azure Pipelines kan distribuera till de flesta större molntjänster, till exempel Azure-tjänster. Du kan börja med din kod på GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud eller Azure Repos. Sedan kan du automatisera version, testning och distribution av din kod till Microsoft Azure, Google Cloud Platform eller Amazon Web Services (AWS).

**Huvudfunktioner**
- **Förenklad uppgiftsbaserad upplevelse för att konfigurera en CI-server:** Konfigurera en CI-server för mobila applikationer (både Android, iOS och Windows) och plattformsoberoende (Xamarin, Cordova och React Native).
- **Alla språk, plattformar och moln:** Skapa, testa och distribuera Node.js-, Python-, Java-, PHP-, Ruby-, Go-, C/C++-, C-appar#, Android och iOS. Kör parallellt på Linux, macOS och Windows. Distribuera till molnleverantörer som Azure, AWS och Google Cloud Platform. Distribuera mobilappar via betakanaler och appbutiker.
- **Stöd för inbyggd behållare:** Skapa nya behållare med lätthet och skicka dem till alla register. Distribuera behållare till oberoende värdar eller Kubernetes.
- **Avancerade arbetsflöden och funktioner:** Skapa enkelt byggkedjor och flerfasade byggen. Få stöd för YAML, testintegration, släppgrindar, rapportering med mera.
- **Utökningsbar:** Använd en rad bygg-, test- och distributionsuppgifter som skapats av communityn, som innehåller hundratals tillägg från Slack till SonarCloud. Du kan även distribuera från andra CI-system, som Jenkins. Webbkrokar och REST API:er kan hjälpa dig att integrera.
- **Kostnadsfria molnbaserade versioner:** Dessa versioner är tillgängliga för offentliga och privata databaser.
- **Stöd för distribution till andra molnleverantörer:** Bland leverantörerna finns AWS och Google Cloud Platform.

**Referenser**
- [Komma igång med Azure Pipelines-guiden](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Kom igång med Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuera ditt program direkt till App Stores
När ditt program är klart för produktionsanvändning och du vill att det ska användas offentligt måste det skickas till appbutiker där det kan laddas ner av kunder. Det finns flera sätt att distribuera ditt program direkt till appbutiker. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Med [App Center Distribute](/appcenter/distribution/stores/)kan du publicera dina mobilappar direkt i appbutiker. När programmet är klart att laddas ned av användare kan du publicera dina programbinärer direkt från Visual Studio App Center-portalen. 

Du kan distribuera direkt till:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
I App Store som utvecklats och underhålls av Apple kan användare bläddra bland och ladda ner program som utvecklats för iOS-, MacOS-, WatchOS- och tvOS-enheter. Utvecklare måste skicka in sina iOS-appar till Apple App Store för allmänt bruk.

### <a name="google-play"></a>Google Play

Google Play är den officiella App Store för Android OS, där användare kan bläddra och ladda ner program som utvecklats för Android-enheter som publiceras via Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) är en molnbaserad tjänst i företagets mobilitetshanteringsutrymme som gör det möjligt för din personal att vara produktiv samtidigt som företagets data skyddas. Med Intune kan du:
- Hantera mobila enheter och datorer som de anställda använder för att komma åt företagets data.
- Hantera de mobila applikationer som personalen använder.
- Skydda din företagsinformation genom att kontrollera hur din personal får åtkomst till och delar den.
- Se till att enheter och program är kompatibla med företagets säkerhetskrav.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Distribuera uppdateringar direkt till användarnas enheter

### <a name="codepush"></a>KodPush (kupong)
Med [CodePush](/appcenter/distribution/codepush/) i App Center kan Apache Cordova och React Native-utvecklare distribuera uppdateringar av mobilappar direkt till sina användares enheter. Den fungerar som en central databas som utvecklare kan publicera vissa uppdateringar till, till exempel JavaScript, HTML, CSS och bildändringar. Sedan kan program fråga efter uppdateringar från databasen med hjälp av de angivna klient-SDK:erna. På så sätt kan du ha en mer deterministisk och direkt engagemangsmodell med användarna medan du åtgärdar buggar eller lägger till små funktioner. Du behöver inte återskapa en binär eller distribuera den via offentliga appbutiker.

**Huvudfunktioner**
- Cordova- och React Native-utvecklare kan distribuera uppdateringar av mobilappar direkt till användarnas enheter utan att släppa på en butik.
- Användbart för att åtgärda buggar eller lägga till och ta bort små funktioner som inte kräver att du återskapar binära och distribuerar den via respektive butiker.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Komma igång med CodePush i App Center](/appcenter/distribution/codepush/)
- [KodPush CLI](/appcenter/distribution/codepush/cli)