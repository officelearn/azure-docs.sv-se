---
title: Styrning i Azure DevTest Labs-infrastruktur
description: Den här artikeln innehåller riktlinjer för styrning i Azure DevTest Labs-infrastruktur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 47f3e8ab14ecd50e958c57ba4c8f9f098fd5bb7b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868369"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Styrning i Azure DevTest Labs infrastruktur – hantera kostnader och ägarskap
Kostnad och ägarskap är primär frågor när du överväga att skapa din utveckling och testmiljöer. I det här avsnittet finns information som hjälper dig att optimera kostnad och justera ägarskap över din miljö.

## <a name="optimize-for-cost"></a>Optimera för kostnaden

### <a name="question"></a>Fråga
Hur kan jag Optimera för kostnaden i min labb-miljö?

### <a name="answer"></a>Svar
Det finns ett antal inbyggda funktioner för DevTest Labs som hjälper dig att optimera för kostnaden. Se [kostnadshantering, tröskelvärden](devtest-lab-configure-cost-management.md) [, principer och](devtest-lab-set-lab-policy.md) artiklar för att begränsa aktiviteter för dina användare. 

När du använder labb för utveckling och test-arbetsbelastningar, kan du använda den [Enterprise Dev/Test-Prenumerationsförmån](https://azure.microsoft.com/offers/ms-azr-0148p/), som en del av ditt Enterprise Agreement. Du kan också om du är en betala-kund kan du kanske vill den [betala-som-du gå DevTest erbjudandet](https://azure.microsoft.com/offers/ms-azr-0023p/).

Den här metoden ger dig flera fördelar:

- Särskilda lägre Dev/Test-priser för Windows-datorer, molntjänster, HDInsight, App Service och Logic Apps
- Bra priser för Enterprise Agreement (EA) för andra Azure-tjänster
- Tillgång till exklusiva avbildningar för utveckling/testning i galleriet, inklusive Windows 8.1 och Windows 10
 
Endast kan aktiva Visual Studio-prenumeranter (standardprenumerationer, årliga molnprenumerationer och månatliga molnprenumerationer) använda Azure-resurser som körs i en enterprise Dev/Test-prenumeration. Slutanvändare kan dock åtkomst till programmet att lämna kommentarer eller utföra acceptanstester. Användning av resurser inom denna prenumeration är begränsad till utvecklings- och testningsprogram, och ingen garanti vad gäller drifttid erbjuds.

Om du vill använda DevTest-erbjudande, Observera att den här förmånen är exklusivt för utveckling och testning av dina program. Användning inom prenumerationen ingår inget serviceavtal med ekonomisk UPPBACKNING, förutom användning av Azure DevOps och HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definiera en rollbaserad åtkomst i hela organisationen
### <a name="question"></a>Fråga
Hur definierar jag rollbaserad åtkomstkontroll för Mina DevTest Labs miljöer för att säkerställa att IT-avdelningen kan styra när utvecklare och testning kan utföra sitt arbete? 

### <a name="answer"></a>Svar
Det finns ett brett mönster, men du detaljnivån beror på din organisation.

Central IT bör äger bara vad som krävs och aktivera teamen för projekt och program ha nödvändiga nivå av kontroll. Det innebär normalt den centrala IT äger prenumerationen och hanterar viktiga IT-funktioner, till exempel nätverkskonfigurationer. Uppsättningen **ägare** för en prenumeration ska vara små. Följande ägare kan utse ytterligare ägare när det finns ett behov eller tillämpa prenumerationsnivå principer, till exempel ”ingen offentlig IP-adress”.

Det kan finnas en delmängd av användare som behöver åtkomst i hela prenumerationen, t.ex nivå 1 eller nivå 2-stöd. I det här fallet rekommenderar vi att du ger användarna den **deltagare** så att de inte kan hantera resurser, men ge användaråtkomst eller justera principer.

DevTest Labs-resursen ska ägas av ägare som ligger nära projekt/program-teamet. Det beror på att de förstår sina krav vad gäller datorer och programvara som krävs. I de flesta organisationer är ägare till den här DevTest Labs-resurs ofta projekt/utveckling lead. Den här ägaren kan hantera användare och principer i laboratoriemiljön och kan hantera alla virtuella datorer i labb-miljö.

Gruppmedlemmarna projekt/program ska läggas till rollen DevTest Labs-användare. Dessa användare kan skapa virtuella datorer (infogade med lab och prenumerationsnivå principer). De kan också hantera sina egna virtuella datorer. De kan inte hantera virtuella datorer som tillhör andra användare.

Mer information finns i [Azure enterprise kodskelett – förebyggande Prenumerationsåtgärder](/azure/architecture/cloud-adoption/appendix/azure-scaffold) dokumentation.


## <a name="next-steps"></a>Nästa steg
Se [företagets principer och efterlevnad](devtest-lab-guidance-governance-policy-compliance.md).
