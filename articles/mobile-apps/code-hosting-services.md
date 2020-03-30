---
title: Värd för din källkod för mobilapplikationer i molnet med GitHub och Azure DevOps
description: Lär dig mer om vilka tjänster som ska vara värd för din mobilprogramkod i molnet med Microsoft-tjänster.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 66d8980ab53010af0703d789fbe791c60a32052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240969"
---
# <a name="cloud-hosted-source-code-management-services"></a>Molnbaserade källkodshanteringstjänster
Om du har utvecklingsteam med flera gruppmedlemmar som arbetar med samma kodbas måste du hitta en lämplig plats för att vara värd för din kod. Om du lagrar data i molnet och har en centraliserad databas kan alla ladda upp, redigera och hantera kodfilerna. De kan också interagera med andra utvecklare i projekt. Koden kan vara lättillgänglig oavsett var du är, så länge du har en internetuppkoppling.

Molnhosting är mycket enklare än lokala alternativ. Det kräver mindre maskinvarukonfiguration och gör det möjligt för organisationer att slutföra implementeringsprocessen på ett mer flexibelt sätt.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Fördelar med att vara värd för källkod i molnet
- **Centraliserad molnlagring:** Visa och hantera dina data var du än är.
- **Bättre samarbete och renare kod:** Håll koll på koden inom team och hantera projekt för att säkerställa kontinuerlig leverans av utmärkt programvara.
- **Lättare att engagera sig:** Enkelt bidra till dina projekt.
- **Snabbare utgivningscykel:** Arbeta snabbare i dina team och enkelt bidra till dina projekt.
- **Minska kostnaden:** Oroa dig inte för att underhålla din egen hårdvara, servrar, VPN och så vidare.

Använd följande tjänster för att vara värd för dina programdata i molnet.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) är en värdtjänst för lagringsplatsen med öppen källkod som är värd för källkodsprojekt på en mängd olika programmeringsspråk. GitHub håller reda på de olika ändringar som gjorts i varje iteration.

**Huvudfunktioner**
- Använd kodhosting för att hålla all din kod på ett ställe. Privata, offentliga och öppna källkodsarkiv är alla utrustade med verktyg som hjälper dig att vara värd, version och utgivningskod.
- Granska koden och använd de inbyggda granskningsverktygen för att göra kodgranskning till en viktig del av alla teams processer:
    - Skydda grenar, föreslå ändringar och begär recensioner.
    - Upptäck skillnader, kommentera i sitt sammanhang och få tydlig feedback.
- Samordna tidigt, håll dig i linje med och få mer gjort med GitHubs projekthanteringsverktyg:
    - Se projektets helhet.
    - Använd aktivitetstavlor som ligger precis bredvid koden i GitHub.
    - Dra kort för att tilldela problem eller hämta begäranden till gruppmedlemmar.
    - Ange milstolpar för att organisera och spåra förloppet.
    - Skriv anteckningar för att fånga idéer som kan vara användbara men som inte tillhör ett visst problem eller en viss pull-begäran.
- Upptäck enkelt och välj rätt verktyg för bättre kommunikation och automatisering av arbete genom att köpa program från [GitHub Marketplace.](https://github.com/marketplace)
- Hantera och utöka team med hjälp av: 
    - Användarroller som hjälper till att organisera team och komma åt behörigheter.
    - Diskussionstrådsverktyg för att hålla konversationer på ämne och team fokuserade.
    - Community-riktlinjer för att snabbt konfigurera nya gruppmedlemmar med ett konto.
- Bläddra och stjärn populära projekt för att följa dem.
- nätverka och lära av andra i branschen.

**Referenser**
- [GitHub](https://github.com/)
- [GitHub-guider](https://guides.github.com/)
- [GitHub-communityforum](https://github.community/)
- [GitHub-marknadsplats](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) stöder [Azure Repos](https://azure.microsoft.com/services/devops/repos/) och [Team Foundation Version Control (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) som alternativ för källkontroll. Den har obegränsade kostnadsfria privata databaser med samarbetskodgranskningar, avancerad filhantering, kodsökning och grensprinciper för att säkerställa kod av hög kvalitet. Azure Repos är bra för små projekt och stora organisationer som behöver inbyggt Azure Active Directory-stöd och avancerade principer.
    
**Huvudfunktioner**
- Använd en obegränsad Git-källkodsdatabas med molnbaserad för offentliga och privata repor:
    - Få support för alla Git-klienter.
    - Använd webbkrokar och API-integrering.
- Kick off din nästa bygga från en repo pull begäran:
    - Samarbeta för att skapa bättre kod med hjälp av trådad diskussion och kontinuerlig integrering för varje ändring.
    - Ställ in kontinuerlig integrering/kontinuerlig leverans (CI/CD) för att automatiskt utlösa byggen, tester och distributioner med varje slutförd pull-begäran. Du kan använda Azure Pipelines eller dina verktyg.
    - Skydda kodkvaliteten med grenprinciper.
- Underhåll centraliserad versionskontroll med Team Foundation Version Control, som inkluderar kodgranskning.
- Anslut till koden med hjälp av Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code med mera.
- Använd kraftfull semantisk kodsökning.
- Dra nytta av företagsklara funktioner. Azure DevOps har inbyggd integrering med Azure Active Directory, vilket förenklar processen för att hantera åtkomst till dina koddatabaser.
- Säkerställ kodkvalitet med grenprinciper, till exempel ett minsta antal kodgranskningar, krav för lyckade versioner och efterlevnad av Git-sammanfogningsstrategier.
- Anslut din favoritutvecklingsmiljö för att komma åt repor och hantera arbete.

**Referenser**
- [Komma igång med Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Azure Repos-dokumentation](/azure/devops/repos/?view=azure-devops)