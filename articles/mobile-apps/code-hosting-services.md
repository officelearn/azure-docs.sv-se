---
title: Vara värd för ditt mobil programs käll kod i molnet med GitHub och Azure DevOps
description: Läs om tjänsterna som är värdar för din mobil program kod i molnet med Microsoft-tjänster.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8662b9f77614339ff514fa4fcf97dc1ee8fc7417
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454489"
---
# <a name="cloud-hosted-source-code-management-services"></a>Molnbaserade käll kods hanterings tjänster
Om du har utvecklings grupper med flera team medlemmar som arbetar på samma kodbas måste du hitta en lämplig plats som värd för din kod. Genom att lagra data i molnet och med en central lagrings plats kan alla ladda upp, redigera och hantera-filerna. De kan också samverka med andra utvecklare i projekt. Koden kan vara lättillgänglig oavsett var du befinner dig, så länge du har en Internet anslutning.

Moln tjänster är mycket enklare än lokala alternativ. Den kräver mindre maskin varu konfiguration och gör det möjligt för organisationer att slutföra implementerings processen på ett mer flexibelt sätt.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Fördelar med att vara värd för käll koden i molnet
- **Centraliserad moln lagring:** Visa och hantera dina data var som helst.
- **Bättre samarbets-och rengörings kod:** Håll koll på kod i team och hantera projekt för att säkerställa kontinuerlig leverans av utmärkt program vara.
- **Enklare att komma igång:** Bidra enkelt till dina projekt.
- **Snabbare versions cykel:** Arbeta snabbare i dina team och bidra enkelt till dina projekt.
- **Minska kostnaden:** Bry dig inte om att underhålla din egen maskin vara, servrar, VPN och så vidare.

Använd följande tjänster som värd för dina program data i molnet.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) är en lagrings värd tjänst med öppen källkod som är värd för käll kods projekt i olika programmeringsspråk. GitHub håller reda på de olika ändringar som görs i varje iteration.

**Huvudfunktioner**
- Använd kod värd för att behålla all kod på ett och samma ställe. Privata, offentliga och öppna käll databaser är utrustade med verktyg som hjälper dig att vara värd, version och versions kod.
- Granska koden och Använd de inbyggda gransknings verktygen för att göra kod granskningen till en viktig del av alla Teams processer:
    - Skydda grenar, föreslå ändringar och granskning av begäran.
    - Olikheter, kommentarer i sammanhanget och få tydliga synpunkter.
- Koordinera tidigt, håll jämna och få mer information med GitHub för projekt hantering:
    - Se projektets stora bild.
    - Använd uppgifts tavlor som är direkt intill din kod inuti GitHub.
    - Dra kort för att tilldela ärenden eller pull-begäranden till grupp medlemmar.
    - Ange mil stolpar för att ordna och spåra förloppet.
    - Skriv kommentarer för att samla in idéer som kan vara användbara, men som inte tillhör ett visst ärende eller en pull-begäran.
- Identifiera och välj rätt verktyg för bättre kommunikation och automatisering av arbete genom att köpa program från [GitHub Marketplace](https://github.com/marketplace).
- Hantera och utveckla team genom att använda: 
    - Användar roller som hjälper dig att organisera grupper och åtkomst behörigheter.
    - Diskussions tråds verktyg för att hålla konversationer om ämne och lag fokuserade.
    - Community-rikt linjerna för att snabbt skapa nya grupp medlemmar med ett konto.
- Bläddra bland och stjärnor populära projekt för att följa dem.
- Nätverk och lär dig av andra i branschen.

**Reference**
- [GitHub](https://github.com/)
- [GitHub-handböcker](https://guides.github.com/)
- [GitHub community-forum](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) har stöd för [Azure databaser](https://azure.microsoft.com/services/devops/repos/) och [TFVC (Team Foundation Version Control)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) som alternativ för käll kontroll. Den har obegränsade privata lagrings platser med kod granskningar, avancerad fil hantering, kod sökning och förgrenings principer för att säkerställa hög kvalitets kod. Azure databaser är perfekt för små projekt och stora organisationer som behöver stöd för interna Azure Active Directory och avancerade principer.
    
**Huvudfunktioner**
- Använd en obegränsad plats för värdbaserade värdbaserade värdbaserade git-källor för din offentliga och privata databaser:
    - Få stöd för alla git-klienter.
    - Använda Webhooks och API-integrering.
- Starta nästa version från en lagrings platsen pull-begäran:
    - Samar beta för att bygga bättre kod med hjälp av trådad diskussion och kontinuerlig integrering för varje ändring.
    - Konfigurera kontinuerlig integrering/kontinuerlig leverans (CI/CD) för att automatiskt utlösa versioner, tester och distributioner med varje slutförd pull-begäran. Du kan använda Azure-pipeliner eller dina verktyg.
    - Skydda din kod kvalitet med förgrenings principer.
- Upprätthåll centraliserad versions kontroll med Team Foundation versions kontroll, som innehåller kod granskning.
- Anslut till din kod med Xcode, Sol förmörkelse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code med mera.
- Använd kraftfull semantisk kod sökning.
- Få fördelar med företags färdiga funktioner. Azure DevOps har inbyggd integrering med Azure Active Directory, vilket fören klar processen att hantera åtkomst till dina kod databaser.
- Se till att kod kvaliteten med gren principer, till exempel ett minsta antal kod granskningar, krav för lyckade versioner och verk ställandet av Git-sammanfognings strategier.
- Anslut din favorit utvecklings miljö för att få åtkomst till databaser och hantera arbete.

**Reference**
- [Kom igång med Azure databaser](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentation om Azure databaser](/azure/devops/repos/?view=azure-devops)