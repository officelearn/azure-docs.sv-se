---
title: Hantera kostnader och ägande i Azure DevTest Labs
description: Den här artikeln innehåller information som hjälper dig att optimera för kostnad och anpassa ägarskap i hela din miljö.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561663"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Styrning av Azure DevTest Labs-infrastruktur – Hantera kostnader och ägande
Kostnad och ägande är primära problem när du överväger att bygga din utveckling och testmiljöer. I det här avsnittet hittar du information som hjälper dig att optimera för kostnad och anpassa ägarskapet i hela din miljö.

## <a name="optimize-for-cost"></a>Optimera för kostnad

### <a name="question"></a>Fråga
Hur kan jag optimera för kostnader i min DevTest Labs-miljö?

### <a name="answer"></a>Svar
Det finns ett antal inbyggda funktioner i DevTest Labs som hjälper dig att optimera för kostnad. Se [kostnadshantering, tröskelvärden](devtest-lab-configure-cost-management.md) [och policyartiklar](devtest-lab-set-lab-policy.md) för att begränsa användarnas aktiviteter. 

När du använder DevTest Labs för en utvecklings- och testarbetsbelastning kan du överväga att använda [Enterprise Dev/Test Subscription Benefit](https://azure.microsoft.com/offers/ms-azr-0148p/)som en del av ditt Enterprise-avtal. Alternativt, om du är en Pay as you Go kund, kanske du vill överväga [Pay-as-you go DevTest erbjudande](https://azure.microsoft.com/offers/ms-azr-0023p/).

Detta tillvägagångssätt ger dig många fördelar:

- Särskilda lägre utvecklings-/testpriser på virtuella Datorer i Windows, molntjänster, HDInsight, App Service och Logic Apps
- Ea-priser (Great Enterprise Agreement) för andra Azure-tjänster
- Tillgång till exklusiva utvecklings-/testbilder i galleriet, inklusive Windows 8.1 och Windows 10
 
Endast aktiva Visual Studio-prenumeranter (standardprenumerationer, årliga molnprenumerationer och månatliga molnprenumerationer) kan använda Azure-resurser som körs inom en företagsförsak/testprenumeration. Slutanvändare kan dock komma åt programmet för att ge feedback eller utföra acceptanstester. Användningen av resurser inom den här prenumerationen är begränsad till att utveckla och testa program, och ingen drifttidsgaranti erbjuds.

Om du väljer att använda DevTest-erbjudandet bör du tänka på att den här förmånen uteslutande är avsedd för utveckling och testning av dina program. Användning inom prenumerationen medför inte ett ekonomiskt stödda serviceavtal, förutom användning av Azure DevOps och HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definiera en rollbaserad åtkomst i hela organisationen
### <a name="question"></a>Fråga
Hur definierar jag rollbaserad åtkomstkontroll för mina DevTest Labs-miljöer för att säkerställa att IT-tjänster kan styra medan utvecklare/test kan utföra sitt arbete? 

### <a name="answer"></a>Svar
Det finns ett brett mönster, men detaljerna beror på din organisation.

Central IT bör endast äga det som är nödvändigt och göra det möjligt för projekt- och applikationsteamen att ha den kontrollnivå som krävs. Vanligtvis innebär det att central IT äger prenumerationen och hanterar centrala IT-funktioner som nätverkskonfigurationer. Ägaruppsättningen **för** en prenumeration ska vara liten. Dessa ägare kan nominera ytterligare ägare när det finns behov, eller tillämpa principer på prenumerationsnivå, till exempel "Ingen offentlig IP".

Det kan finnas en delmängd användare som kräver åtkomst över en prenumeration, till exempel Nivå 1- eller Nivå 2-stöd. I det här fallet rekommenderar vi att du ger dessa användare åtkomst **till deltagaren** så att de kan hantera resurserna, men inte ge användaråtkomst eller justera principer.

DevTest Labs-resursen ska ägas av ägare som ligger nära projekt-/programteamet. Det beror på att de förstår sina krav när det gäller maskiner, och krävs programvara. I de flesta organisationer är ägaren till den här DevTest Labs-resursen ofta projektledare för projekt/utveckling. Den här ägaren kan hantera användare och principer i labbmiljön och hantera alla virtuella datorer i DevTest Labs-miljön.

Projekt-/programgruppsmedlemmarna ska läggas till i rollen DevTest Labs-användare. Dessa användare kan skapa virtuella datorer (i linje med policyerna för labb- och prenumerationsnivå). De kan också hantera sina egna virtuella datorer. De kan inte hantera virtuella datorer som tillhör andra användare.

Mer information finns i [Azure enterprise scaffold – prescriptive subscription governance](/azure/architecture/cloud-adoption/appendix/azure-scaffold) documentation.


## <a name="next-steps"></a>Nästa steg
Se [Företagspolicy och efterlevnad](devtest-lab-guidance-governance-policy-compliance.md).
