---
title: Automatisera distributionen och publiceringen av dina mobil program med Visual Studio App Center-och Azure-tjänster
description: Läs om de tjänster som App Center som hjälper dig att skapa en pipeline för kontinuerliga leveranser för dina mobila program.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 1fef01ed4db7252043a8adee478b22ef2417e21f
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450894"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatisera distributionen och publiceringen av dina mobil program med kontinuerliga leverans tjänster

Som utvecklare skriver du kod och markerar den i kod databasen, men incheckningarna som checkas in i lagrings platsen kanske inte alltid är konsekventa. När flera utvecklare arbetar med samma projekt kan problem uppstå med integreringen. Team kan köras i situationer där saker inte fungerar, buggar och projekt utveckling blir fördröjda. Utvecklare måste vänta tills hela program varu koden har skapats och testats för att söka efter fel, vilket gör processen långsam och mindre iterativ.

Med kontinuerlig leverans automatiserar du distributionen och publiceringen av dina mobila program. Det spelar ingen roll om du distribuerar programmet till en grupp testare eller anställda i företaget (för beta testning) eller till en App Store (för produktion). Kontinuerlig leverans gör distributioner mindre riskfyllda och uppmuntrar snabba iterationer. Du kan också publicera nya ändringar till dina kunder på ett kontinuerligt sätt.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuera binärfiler för program till beta testare
Beta testningen av ditt mobil program är ett av de viktigaste stegen under program utvecklings processen. Det hjälper dig att hitta buggar och problem i programmet tidigt. Feedbacken förbättrar din program kvalitet när du får den redo för produktions användning.

Använd följande tjänster för att aktivera en pipeline för kontinuerlig leverans i dina mobila appar.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center distribuera](/appcenter/distribution/) är ett verktyg som utvecklare kan komma åt för att snabbt frigöra versioner till enheter. Med en fullständig installations Portal får App Center distribuera en kraftfull lösning för Beta app-testdistribution. Det är också ett bekvämt alternativ till distribution via offentliga app-butiker. Utvecklare kan automatisera sina distributions arbets flöden ytterligare med App Center bygga och offentliga program lagrings integrationer.

**Huvudfunktioner**
- Distribuera din app till beta testare och användare och se till att alla tester finns på den senaste versionen av programmet.
- Meddela tester av nya versioner utan testare genom nedladdnings flödet igen.
- Hantera distributions grupper för olika versioner av programmet.
- Distribuera till butiker: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Få plattforms stöd för iOS, Android, macOS, tvOS, Xamarin, reagera inbyggd, Unity och Cordova.
- Registrera iOS-enheter automatiskt i etablerings profilen.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Kom igång med App Center distribuera](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure-pipelines](https://azure.microsoft.com/services/devops/pipelines/) är en helt aktuell tjänst för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) som fungerar med den primära git-providern. Azure-pipelines kan distribueras till de flesta större moln tjänster, till exempel Azure-tjänster. Du kan börja med din kod på GitHub, GitHub Enterprise Server, GitLab, BitBucket Cloud eller Azure databaser. Sedan kan du automatisera bygge, testning och distribution av koden till Microsoft Azure, Google Cloud Platform eller Amazon Web Services (AWS).

**Huvudfunktioner**
- **Förenklad uppgifts baserad upplevelse för att konfigurera en CI-Server:** Konfigurera en CI-Server för både intern (Android-, iOS-och Windows-) och plattforms oberoende (Xamarin-, Cordova-och reagerar inbyggda) mobila program.
- **Valfritt språk, plattform och moln:** Bygg, testa och distribuera Node. js-, python-, Java-, PHP-, ruby-, go-, C/C++-, C#-, Android-och iOS-appar. Kör parallellt på Linux, macOS och Windows. Distribuera till moln leverantörer som Azure, AWS och Google Cloud Platform. Distribuera mobila program via beta kanaler och app-butiker.
- **Stöd för inbyggd behållare:** Skapa nya behållare utan problem och skicka dem till alla register. Distribuera behållare till oberoende värdar eller Kubernetes.
- **Avancerade arbets flöden och funktioner:** Skapa enkelt Bygg kedjor och versioner med fler faser. Få support för YAML, test integration, versions portar, rapportering med mera.
- **Utöknings bar:** Använd en mängd bygg-, test-och distributions uppgifter som skapats av communityn, som innehåller hundratals tillägg från slack till SonarCloud. Du kan även distribuera från andra CI-system, t. ex. Jenkins. Webhooks och REST-API: er kan hjälpa dig att integrera.
- **Kostnads fria, värdbaserade molnbaserade versioner:** Dessa versioner är tillgängliga för offentliga och privata lagrings platser.
- **Stöd för distribution till andra moln leverantörer:** Leverantörer inkluderar AWS och Google Cloud Platform.

**Referenser**
- [Kom igång med Azures guide för pipeline](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Kom igång med Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuera ditt program direkt till app-butiker
När programmet är redo för produktion och du vill att det ska användas offentligt måste det skickas till app-butiker där det kan hämtas av kunderna. Det finns flera sätt att distribuera ditt program direkt till app-butiker. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Med [App Center distribuera](/appcenter/distribution/stores/)kan du publicera dina mobil program direkt till app-butiker. När programmet är redo att laddas ned av användare kan du publicera dina Programbinärfiler direkt från Visual Studio App Center-portalen. 

Du kan distribuera direkt till:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
I App Store som har utvecklats och underhålls av Apple kan användare söka efter och ladda ned program som utvecklats för iOS, MacOS, watche och tvOS-enheter. Utvecklare måste skicka in sina iOS-appar till Apple App Store för offentlig användning.

### <a name="google-play"></a>Google Play

Google Play är den officiella App Store för Android OS, där användare kan söka efter och ladda ned program som utvecklats för Android-enheter som publiceras via Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) är en molnbaserad tjänst i Enterprise Mobility Management-utrymmet som hjälper dig att göra det möjligt för din personal att vara produktiv och samtidigt skydda företagets data. Med Intune kan du:
- Hantera mobila enheter och datorer som de anställda använder för att komma åt företagets data.
- Hantera de mobila program som din personal använder.
- Skydda företagets information genom att kontrol lera hur personalen får åtkomst till och delar den.
- Se till att enheter och program är kompatibla med företagets säkerhets krav.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Distribuera uppdateringar direkt till användarnas enheter

### <a name="codepush"></a>CodePush
Med [CodePush](/appcenter/distribution/codepush/) i App Center kan Apache Cordova och reagera inbyggda utvecklare distribuera uppdateringar av mobila program direkt till användarnas enheter. Den fungerar som en central lagrings plats som utvecklare kan publicera vissa uppdateringar till, till exempel Java Script, HTML, CSS och avbildnings ändringar. Sedan kan program fråga efter uppdateringar från lagrings platsen med hjälp av de tillhandahållna klient-SDK: erna. På så sätt kan du ha en mer deterministisk och direkt engagemang modell med dina användare när du adresserar buggar eller lägger till små funktioner. Du behöver inte återskapa en binär eller distribuera om den via några offentliga app-butiker.

**Huvudfunktioner**
- Cordova och reagera inbyggda utvecklare kan distribuera uppdateringar av mobila program direkt till användarnas enheter utan att släppas på en butik.
- Användbart för att åtgärda buggar eller lägga till och ta bort små funktioner som inte kräver att du återskapar binärfilen och distribuerar om den via respektive butiker.

**Referenser**
- [Registrera dig med Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Kom igång med CodePush i App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)