---
title: Automatisera distributionen och publiceringen av dina mobil program med Visual Studio App Center-och Azure-tjänster
description: Läs om de tjänster som App Center som hjälper dig att skapa en pipeline för kontinuerliga leveranser för dina mobila program.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795606"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatisera distributionen och publiceringen av dina mobil program med kontinuerliga leverans tjänster

Som utvecklare skriver du kod och checkar in den i kod databasen, men incheckningarna som checkas in i lagrings platsen kanske inte alltid är konsekventa. Med flera utvecklare som arbetar med samma projekt kan problem uppstå med integreringen och teamet skulle kunna köras i situationer där saker inte fungerar, buggar fortsätter Piling och projekt utveckling blir fördröjd. Utvecklare måste vänta tills hela program varu koden har skapats och testats för att söka efter fel och det gör processen långsam och mindre iterativ.

Med **kontinuerlig leverans**automatiserar du distributionen och lanseringen av dina mobil program, oavsett om du distribuerar programmet till en grupp testare eller företags anställda (för beta testning) eller App Store (för produktion). Det gör distributioner mindre riskfyllda, uppmuntrar snabba iterationer och låter dig frigöra nya ändringar i kunderna på ett kontinuerligt sätt.

## <a name="distribute-application-binaries-to-beta-testers"></a>Distribuera binärfiler för program till beta testare
Beta testningen av ditt mobil program är ett av de viktigaste stegen under program utvecklings processen. Det hjälper dig att hitta buggar och problem i programmet tidigt och feedback som förbättrar din program kvalitet och förbereder den för produktions användning.

Använd följande tjänster för att aktivera pipeline för kontinuerlig leverans i dina mobilappar.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center distribuera](/appcenter/distribution/) är ett verktyg som utvecklare kan använda för att snabbt frigöra versioner till slutanvändarens enheter. Med en fullständig installations Portal får du inte bara en kraftfull lösning för Beta app-distribution utan även ett bekvämt alternativ till distribution via offentliga app-butiker. Utvecklare kan automatisera sina distributions arbets flöden ytterligare med App Center bygga och offentliga program lagrings integrationer.

**Viktiga funktioner**
- **Distribuera din app till beta testare och användare** och se till att alla testare finns på den senaste versionen av programmet.
- **Meddela tester av nya versioner** utan testare genom nedladdnings flödet igen.
- **Hantera distributions grupper** för olika versioner av programmet.
- **Distribution till butiker** 
    - [Apples](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **Plattforms stöd** – iOS, Android, MacOS, tvOS, Xamarin, reakta Native, Unity, Cordova.
- Registrera iOS-enheter automatiskt i etablerings profilen.

**Reference**
- [Registrera dig med App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Kom igång med App Center distribuera](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure-pipelines

[Azure-pipelines](https://azure.microsoft.com/services/devops/pipelines/) är en helt aktuell tjänst för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) som fungerar med din prioriterade git-Provider och kan distribuera till de flesta större moln tjänster, inklusive Azure-tjänster. Du kan börja med din kod på GitHub, GitHub Enterprise Server, GitLab, BitBucket Cloud eller Azure databaser. Sedan kan du automatisera bygge, testning och distribution av koden till Microsoft Azure, Google Cloud Platform eller Amazon Web Services.

**Viktiga funktioner**
- Förenklad uppgifts baserad upplevelse för att konfigurera en CI-Server för både **inbyggda (Android-, iOS-och Windows-) och plattforms oberoende (Xamarin-, Cordova-och reagerar inbyggda) mobila program**.
- **Alla språk, plattformar och moln** -build, test och distribution av Node. js-, python-, Java-, php-, ruby-,C++go C#-, C/,-, Android-och iOS-appar. Kör parallellt på Linux, macOS och Windows. Distribuera till moln leverantörer som Azure, AWS och GCP. Distribuera mobila program via beta kanaler och app-butiker.
- **Stöd för intern behållare** – skapa nya behållare enkelt och skicka dem till alla register. Distribuera behållare till oberoende värdar eller Kubernetes.
- **Avancerade arbets flöden och funktioner** – enkel länkning och skapande av flera faser. Stöd för YAML, test integration, versions portar, rapportering med mera.
- **Utöknings bar** – Använd en mängd olika bygg-, test-och distributions uppgifter som skapats av communityn – hundratals tillägg från slack till SonarCloud. Du kan även distribuera från andra CI-system, t. ex. Jenkins. Webhooks och REST-API: er hjälper dig att integrera
- **Kostnads fria, värdbaserade molnbaserade versioner** för offentliga och privata lagrings platser.
- **Stöder distribution till andra moln leverantörer** som AWS, GCP osv.

**Reference**
- [Kom igång med Azures guide för pipeline](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Kom igång med Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribuera ditt program direkt till app-butiker
När ditt program är klart för produktion och du vill att det ska användas offentligt måste det skickas till app-butiker där det kan hämtas av kunderna. Det finns flera sätt att distribuera ditt program direkt till app-butiker. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Med [App Center distribuerar](/appcenter/distribution/stores/) tjänsten kan du publicera dina mobil program direkt till app-butiker. När programmet är redo att laddas ned av slutanvändarna kan du publicera dina Programbinärfiler direkt från App Center-portalen.  

Du kan distribuera direkt till:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Butik](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
App Store som har utvecklats och underhålls av Apple gör det möjligt för användare att söka efter och ladda ned program som utvecklats för iOS, MacOS, watche och tvOS-enheter. Utvecklare måste skicka in sina iOS-appar till Apple Store för offentligt bruk.

### <a name="google-play"></a>Google Play

Google Play är den officiella App Store för Android OS, så att användarna kan söka efter och ladda ned program som utvecklats för Android-enheter och publicera via Google.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) är en molnbaserad tjänst i hanterings utrymmet för Enterprise Mobility (EMM) som hjälper personalen att vara produktiv och samtidigt skydda företagets data. Med Intune kan du 
- Hantera de mobila enheter och datorer som din personal använder för att få åtkomst till företagets data.
- Hantera de mobila program som din personal använder.
- Skydda företagets information genom att kontrol lera hur personalen får åtkomst till och delar den.
- Se till att enheter och program är kompatibla med företagets säkerhets krav.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Distribuera uppdateringar direkt till användarnas enheter

### <a name="codepush"></a>CodePush
[CodePush](/appcenter/distribution/codepush/) -tjänsten i App Center gör det möjligt för Apache Cordova och reagera på inbyggda utvecklare att distribuera uppdateringar av mobila program direkt till användarnas enheter. Den fungerar som en central lagrings plats som utvecklare kan publicera vissa uppdateringar till (till exempel JS-, HTML-, CSS-och bild ändringar). Sedan kan program fråga efter uppdateringar från lagrings platsen med hjälp av de tillhandahållna klient-SDK: erna. På så sätt kan du ha en mer deterministisk och direkt engagemang modell för dina slutanvändare, samtidigt som du får hjälp med att adressera buggar eller lägga till små funktioner utan att du behöver återskapa en binär eller omdistribuera den via alla offentliga app-butiker.

**Viktiga funktioner**
- Tillåter Cordova och reagerar inbyggda utvecklare för att distribuera uppdateringar av mobila program direkt till användarnas enheter utan att släppas på en butik.
- Användbart för att åtgärda buggar eller lägga till/ta bort små funktioner som inte kräver att återskapa binärfiler och omdistributioner via respektive butiker.

**Reference**
- [Registrera dig med App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Kom igång med kod-push i App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)