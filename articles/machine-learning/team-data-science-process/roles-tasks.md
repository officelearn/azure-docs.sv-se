---
title: Koppla datavetenskap Process roller och uppgifter - Azure | Microsoft Docs
description: "En översikt över viktiga komponenter, personal roller och tillhörande uppgifter för en datavetenskap team projekt."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: b502a586cdb7351d5b22c6b0db966723b8ebb7b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team datavetenskap Process roller och aktiviteter

Team av vetenskapliga data är ett ramverk som utvecklats av Microsoft som innehåller en strukturerad metod för att skapa förutsägelseanalyslösningar och intelligent program effektivt. Den här artikeln beskrivs rollerna personal och förknippade aktiviteter som hanteras av en datavetenskap team standardisera på den här processen. 

Den här introduktion länkar till självstudier som innehåller instruktioner om hur du ställer in TDSP miljön för hela vetenskap datagruppen, datavetenskap team och projekt. Vi ger detaljerad information med Visual Studio Team Services VSTS () i självstudierna vår kod värd plattform och verktyg för flexibel planering att hantera team uppgifter, styra åtkomst och hantera databaser. 

Du kommer även att kunna använda den här informationen för att implementera TDSP på din egen kod-värd och flexibel verktyg för planering. 

## <a name="structures-of-data-science-groups-and-teams"></a>Vetenskap grupper och grupper
Datavetenskap funktioner i företag kan ofta ordnas i följande hierarki:

1. ***Datavetenskap grupp/s***

2. ***Datavetenskap team/s inom gruppen/s***

Det kommer är grupp i sådan struktur och teamet leads. Vanligtvis ett projekt för datavetenskap görs genom ett datavetenskap team som kan bestå av projektet leads (för projektet styrning uppgifter och) och datavetare eller tekniker (enskilda deltagare / teknisk personal) som körs i datavetenskap data och tekniska delar av projektet. Innan den körs görs installation och styrning genom leads grupp, team eller projekt.

## <a name="definition-of-four-tdsp-roles"></a>Definition av fyra TDSP roller
Med ovan förutsätter angett vi fyra olika roller för vår team-personal:

1. ***Gruppansvarig***. Gruppansvarig är ansvarig för hela dataenheten vetenskap i ett företag. En dataenhet vetenskap kan ha flera team, som arbetar på flera datavetenskap projekt i olika företag lodräta annonser. En grupp Manager kan delegera sina uppgifter till ett surrogat, men de uppgifter som är associerade med rollen ändras inte.

2. ***Teamet Lead***. Ett team lead hanterar ett team i vetenskap dataenheten på ett företag. En grupp består av flera datavetare. För en dataenhet vetenskap med bara ett fåtal data forskare vara Group Manager och Team leda samma person.

3. ***Projektet Lead***. Ett projekt lead hanterar dagliga aktiviteter enskilda forskare med ett projekt för vetenskap specifika data.

4. ***Projektet enskilda deltagare***. Data forskare, affärsanalytiker, Data-tekniker, systemarkitekter och så vidare. Projektet enskilda deltagare kör en datavetenskap. 


**[AZURE.NOTE]**: En enskild person kan spela flera roller beroende på struktur i ett företag, eller det kan finnas mer än en person som arbetar på en roll. Det kan ofta vara fallet i små företag eller företag med ett litet antal personal i organisationen datavetenskap.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Aktiviteter som ska slutföras av fyra personal

Följande bild visar de översta uppgifterna för personal efter roll i att anta och genomföra Team vetenskap av data som conceptualized av Microsoft. 

![Översikt över roller och aktiviteter](./media/roles-tasks/overview-tdsp-top-level.png)

Det här schemat och följande, mer detaljerad beskrivning av uppgifter som är kopplade till varje roll i TDSP hjälper dig att välja rätt kursen baserat på dina ansvarsområden i organisationen.

>[AZURE.NOTE] I följande anvisningar visar vi hur du ställer in en TDSP miljö och utföra andra uppgifter vetenskap uppgifter i Visual Studio Team Services VSTS (). Anger vi hur du utför dessa uppgifter med VSTS eftersom det är vad vi använder för att implementera TDSP på Microsoft. VSTS underlättar samarbete genom att integrera hanteringen av arbetsobjekt som spårar aktiviteter och koden värdtjänsten används för att dela verktyg, ordna versioner och ge rollbaserad säkerhet. Du kan välja andra plattformar, om du föredrar att implementera de uppgifter som beskrivs av TDSP. Men beroende på din plattform vissa funktioner som vi använder från VSTS kanske inte tillgänglig. 
>
>Vi använder också den [Data vetenskap virtuell dator (DSVM)](http://aka.ms/dsvm) på Azure cloud som analytics skrivbordet med flera populära datavetenskap verktyg förkonfigurerade och integrerats med olika Microsoft-programvara och Azure-tjänster. Du kan använda DSVM eller andra utvecklingsmiljö för att implementera TDSP. 


## <a name="group-manager-tasks"></a>Gruppen Manager-uppgifter

Följande åtgärder har utförts av Gruppansvarig (eller avsedda TDSP systemadministratör) för att införa TDSP:

- Skapa en **gruppkontot** om en värd plattform (till exempel Github, Git, VSTS eller andra)
- Skapa en **projekt mallen databasen** på gruppkontot och seed från projektet mallen databasen har utvecklats av Microsoft TDSP team. TDSP projekt mallen databasen från Microsoft tillhandahåller en **standardiserade katalogstruktur** inklusive kataloger för data, kod och dokument och ger en uppsättning **standardiserade mallar** att hjälpa en effektiv datavetenskap process. 
- Skapa en **verktyget databasen**, och dirigera från Verktyg-databasen som utvecklats av Microsoft TDSP team. Verktyget TDSP databasen från Microsoft tillhandahåller en uppsättning användbara verktyg för effektiviserar arbetet med en data-forskare, inklusive verktyg för interaktiv datagranskning, analys och rapportering och för baslinje modellering och rapportering.
- Ställ in den **kontroll säkerhetsprincip** av de två databaserna på ditt gruppkonto.  

Detaljerade stegvisa anvisningar finns i [Gruppansvarig uppgifter för ett datavetenskap team](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Lead gruppuppgifter

Följande åtgärder har utförts av Team leda eller en avsedda team projekt-administratör att införa TDSP:

- Om VSTS väljs ska koden Värdplattformen för versionshantering och samarbete, skapar du en **grupprojekt** på gruppens VSTS server. I annat fall kan den här aktiviteten hoppas över.
- Skapa den **team projekt mallen databasen** under grupprojekt och seed det från grupp projekt mallen databasen har ställts in av grupp-manager eller en delegat av hanteraren. 
- Skapa den **team verktyget databasen**, och Lägg till team-specifika verktyg i databasen. 
- (Valfritt) Skapa  **[Azure file storage](https://azure.microsoft.com/services/storage/files/)**  som används för att lagra datatillgångar som kan vara användbara för hela gruppen. Andra gruppmedlemmar kan montera den här delade molnarkivet för filen på skrivbordet analytics.
- (Valfritt) Montera Azure file storage till den **datavetenskap virtuella** (DSVM) för teamet leda och Lägg till datatillgångar på den.
- Ställ in den **säkerhetskontroll** genom att lägga till teammedlemmar och konfigurera sina privilegier. 

Detaljerade stegvisa anvisningar finns i [Team leda uppgifter för ett datavetenskap team](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Project Lead-aktiviteter

Följande åtgärder har utförts av projektet leda till att införa TDSP:

- Skapa en **projekt databasen** under grupprojekt och seed från teamet projektet mall-databasen. 
- (Valfritt) Skapa **Azure file storage** som används för att lagra datatillgångar i projektet. 
- (Valfritt) Montera Azure file storage till den **datavetenskap virtuella** (DSVM) av projektet leda och lägga till projektet datatillgångar på den.
- Ställ in den **säkerhetskontroll** genom att lägga till projektmedlemmar och konfigurera sina privilegier. 

Detaljerade stegvisa anvisningar finns i [projekt leda uppgifter för ett datavetenskap team](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Projektet enskilda deltagare uppgifter

Följande åtgärder har utförts av projektet enskilda deltagare (vanligtvis en Data-forskare) att genomföra datavetenskap projektet med TDSP:

- Klona den **projekt databasen** ställa in genom projektet lead. 
- (Valfritt) Montera den delade **Azure file storage** team och projekt på sina **datavetenskap virtuella** (DSVM).
- Köra projektet. 

 
Stegvisa anvisningar för på-boarding till ett projekt, se [projekt enskilda deltagare för ett datavetenskap team](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Datavetenskap projektet körning
 
Genom att följa de relevanta uppsättningen instruktioner kan dataanalytiker, projekt lead och team leads skapa arbetsobjekt om du vill spåra alla aktiviteter och steg som krävs för ett projekt från början till dess slut. Med git också främjar samarbetet mellan datavetare och ser till att artefakter som skapas under körning av projektet är version kontrolleras och delas av alla projektmedlemmar.

Anvisningarna för projektet körningen har utvecklats baserat på antagandet att båda arbetsobjekt och projektet git databaser finns i VSTS. Om du använder VSTS för både kan du länka dina arbetsuppgifter med Git grenarna i ditt projekt-databaser. På så sätt kan spåra du enkelt vad som har gjorts för ett arbetsobjekt. 

Följande bild visar arbetsflödet för projektet körning med hjälp av TDSP.

![Vanliga vetenskap projektet körning](./media/roles-tasks/overview-project-execute.png)

Arbetsflödet innehåller steg som kan grupperas i tre aktiviteter:

- Skriv ut planera (projekt leda)
- Utveckla artefakter på git-grenar kan hantera arbetsobjekt (Data forskare)
- Granska koden och sammanslagning filialer med master filialer (projekt leda eller andra gruppmedlemmar)

Detaljerade stegvisa anvisningar i projektet körning av arbetsflödet finns [körning av datavetenskap projekt](project-execution.md).

## <a name="next-steps"></a>Nästa steg

Här är länkar till mer detaljerade beskrivningar av de roller och aktiviteter som definierats av Team av vetenskapliga data:

- [Gruppera Manager aktiviteter för en datavetenskap-teamet](group-manager-tasks.md)
- [Gruppuppgifter Lead för ett datavetenskap team](team-lead-tasks.md)
- [Projektet Lead uppgifter för ett datavetenskap team](project-lead-tasks.md)
- [Projektet enskilda deltagare för ett datavetenskap team](project-ic-tasks.md)