---
title: Vara värd för ditt mobil programs käll kod i molnet med GitHub och Azure DevOps
description: Läs om tjänsterna som är värdar för din mobil program kod i molnet med Microsoft-tjänster.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795632"
---
# <a name="cloud-hosted-source-code-management-services"></a>Cloud Hosted Source Code Management Services
Om du har utvecklings grupper med flera team medlemmar som arbetar på samma kodbas måste du hitta en lämplig plats som värd för din kod. Genom att lagra data i molnet och med en central lagrings plats kan alla ladda upp, redigera och hantera-filerna, interagera med andra utvecklare i projekt och ger möjlighet till koden att vara lättillgänglig oavsett var du befinner dig, så länge som du ha en Internet anslutning.

Moln värdskap är mycket enklare än lokala alternativ eftersom det krävs mindre maskin varu konfiguration och gör det möjligt för organisationer att slutföra implementerings processen på ett mer flexibelt sätt.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Fördelar med att vara värd för käll koden i molnet
- **Centraliserad moln lagring** för att visa och hantera dina data var som helst.
- Ger **bättre samarbete och renare kod** inom team för att hålla koll på kod och hantera projekt för att säkerställa kontinuerlig leverans av utmärkt program vara.
- **Få en inblandning** och gör det **enkelt att bidra** till dina projekt.
- **Snabbare lanserings cykel** eftersom ditt team kan arbeta snabbare och **enkelt bidra** till dina projekt.
- **Minska kostnaderna** utan att behöva oroa dig för att underhålla din egen maskin vara, servrar, VPN och så vidare.

Använd följande tjänster som värd för dina program data i molnet.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) är en värd tjänst med öppen källkod som är värd för käll kods projekt i en mängd olika programmeringsspråk och håller koll på de olika ändringar som gjorts i varje iteration.

**Viktiga funktioner**
- **Kod som är värd** för all din kod på ett och samma ställe. Privat, offentlig eller öppen källkod är alla databaser utrustade med verktyg som hjälper dig att vara värd, version och versions kod.
- Sömlös **kod granskning** och inbyggda gransknings verktyg gör att kod granskar en viktig del av alla team processer.
    - Skydda grenar, föreslå ändringar och granskning av begäran.
    - Se skillnaden och kommentarerna i sammanhanget och få tydliga synpunkter.
- Koordinera tidigt, håll jämna och få mer information med GitHub för **projekt hantering**.
    - Se projektets stora bild.
    - **Uppgifts tavlor** som är direkt intill din kod inuti GitHub.
    - **Dra och släpp kort** så att du kan tilldela problem eller pull-begäranden till grupp medlemmar.
    - **Mil stolpar** för att ordna och spåra förloppet.
    - **Anteckningar** för att samla in idéer som kan vara användbara, men som inte tillhör ett visst ärende eller en pull-begäran.
- Identifiera och välj rätt verktyg för bättre kommunikation och automatisering av arbete genom att köpa program från **[GitHub Marketplace](https://github.com/marketplace)** .
- **Team hantering** som gör det enkelt att hantera och utveckla team, **användar roller** för att organisera grupper och åtkomst behörigheter, verktyg för **diskussions trådar** för att hålla konversationer om ämne och team fokuserade och **rikt linjer för communityn** för att snabbt ställa in nya team medlemmar med ett konto.
- **Bläddra bland** och **stjärnor** populära projekt för att följa dem.
- **Nätverk och lär dig** av andra i branschen.

**Reference**
- [GitHub](https://github.com/)
- [GitHub-handböcker](https://guides.github.com/)
- [GitHub community-forum](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) har stöd för [Azure databaser](https://azure.microsoft.com/services/devops/repos/) och [TFVC (Team Foundation Version Control)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) som alternativ för käll kontroll. Den har obegränsade privata lagrings platser med kod granskningar, avancerad fil hantering, kod sökning och förgrenings principer för att säkerställa hög kvalitets kod. Azure databaser är perfekt för små projekt och stora organisationer som behöver stöd för interna AAD-support och avancerade principer.
    
**Viktiga funktioner**
- **Obegränsad lagrings plats för värdbaserade git-källkod** för din offentliga och privata databaser
    - Stöd för alla git-klienter.
    - Webhooks och API-integrering.
- Ta **bort din nästa version** från en databaser pull-begäran
    - Samar beta för att bygga bättre kod med hjälp av trådad diskussion och kontinuerlig integrering för varje ändring.
    - Konfigurera kontinuerlig integrering/kontinuerlig leverans (CI/CD) för att automatiskt utlösa byggen, testningar och distributioner med varje slutförd pull-begäran med hjälp av Azure Pipelines eller dina verktyg.
    - Skydda din kod kvalitet med förgrenings principer.
- **Centraliserad versions kontroll med Team Foundation versions kontroll**, inklusive kod granskning.
- **Anslut till din kod** med Xcode, Sol förmörkelse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code med mera.
- **Kraftfull semantisk kod sökning**.
- **Företaget är redo** eftersom det har inbyggd integrering med Azure Active Directory (AD), vilket fören klar hanteringen av åtkomst till dina kod databaser.
- **Se till att kod kvaliteten** med gren principer, till exempel minsta antal kod granskningar, kräver lyckade byggen och att du tvingar git-sammanslagnings strategier.
- **Anslut din favorit utvecklings miljö** för att få åtkomst till databaser och hantera arbete.

**Reference**
- [Kom igång med Azure databaser](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentation om Azure databaser](/azure/devops/repos/?view=azure-devops)