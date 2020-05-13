---
title: Viktig information för Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210901"
---
# <a name="whats-new-in-azure-security-center"></a>Vad är nytt i Azure Security Center?

Azure-säkerhet ligger i aktiv utveckling och tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen ger den här sidan dig information om:

- Nya funktioner
- Felkorrigeringar
- Föråldrade funktioner

Den här sidan uppdateras regelbundet, så du kan uppdatera den ofta. Om du söker efter objekt som är äldre än sex månader hittar du dem i [arkivet för vad som är nytt i Azure Security Center](release-notes-archive.md).


## <a name="may-2020"></a>Maj 2020

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Ändringar av åtkomst till virtuell dator (VM) för just-in-Time (JIT)

Security Center innehåller en valfri funktion för att skydda de virtuella datorernas hanterings portar. Detta ger ett försvar mot den vanligaste formen av brute force-attacker.

Den här uppdateringen ger följande ändringar i den här funktionen:

- Rekommendationen som aviserar dig om att aktivera JIT på en virtuell dator har bytt namn. Tidigare bör "just-in-Time-Network Access Control" tillämpas på virtuella datorer "det är nu:" hanterings portar för virtuella datorer bör skyddas med just-in-Time Network Access Control ".

- Rekommendationen har endast Aktiver ATS om det finns öppna hanterings portar.

[Lär dig mer om funktionen för JIT-åtkomst](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Anpassade rekommendationer har flyttats till en separat säkerhets kontroll

En av de säkerhets kontroller som introducerades med den förbättrade säkra poängen var "implementera säkerhets metod tips". Alla anpassade rekommendationer som skapats för dina prenumerationer placerades automatiskt i denna kontroll. 

För att göra det lättare att hitta dina anpassade rekommendationer har vi flyttat dem till en särskild säkerhets kontroll, "anpassade rekommendationer". Den här kontrollen påverkar inte dina säkra poäng.

Läs mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version) i Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Växla tillagd för att Visa rekommendationer i kontroller eller som en platt lista

Säkerhets kontroller är logiska grupper av relaterade säkerhets rekommendationer. De återspeglar dina sårbara angrepps ytor. En kontroll är en uppsättning säkerhets rekommendationer med anvisningar som hjälper dig att implementera dessa rekommendationer.

Om du vill se hur väl din organisation skyddar varje enskild attack yta granskar du poängen för varje säkerhets kontroll.

Som standard visas dina rekommendationer i säkerhets kontrollerna. Från den här uppdateringen kan du också visa dem som en lista. Om du vill visa dem som en enkel lista som sorteras efter hälso status för de berörda resurserna använder du den nya växla-gruppen efter-kontroller. Växlingen är ovanför listan i portalen.

Säkerhets kontrollerna – och den här växlingen är en del av den nya säkra poängen. Kom ihåg att skicka oss din feedback från portalen.

Läs mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version) i Azure Security Center](secure-score-security-controls.md).


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>Säkerhets rekommendationer för konto som har flyttats till säkerhets kontrollen "säkerhet rekommenderas"

En av de säkerhets kontroller som introduceras med förbättrade säkra poäng är "rekommenderade säkerhets metoder". När en rekommendation är i den här kontrollen påverkar den inte de säkra poängen. 

Med den här uppdateringen har tre rekommendationer flyttats ut från de kontroller som de ursprungligen placerades i och i den här kontrollen med bästa praxis. Vi har vidtagit det här steget eftersom vi har fastställt att risken för dessa tre rekommendationer är lägre än vad som ursprungligen troddes.

Rekommendationerna är:

- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration (ursprungligen i "Aktivera MFA"-kontrollen)
- Externa konton med Läs behörighet bör tas bort från din prenumeration (från början i kontrollen hantera åtkomst och behörigheter)
- Högst 3 ägare bör anges för din prenumeration (ursprungligen i "hantera åtkomst och behörigheter"-kontrollen)

Läs mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version) i Azure Security Center](secure-score-security-controls.md).


### <a name="custom-policies-with-custom-metadata-generally-available"></a>Anpassade principer med anpassade metadata är allmänt tillgängliga

Anpassade principer är nu en del av Security Center rekommendationer, säkra poäng och kontroll panelen för regler för efterlevnad. Den här funktionen är nu allmänt tillgänglig och gör att du kan utöka din organisations täckning för säkerhets utvärdering i Security Center. 

Skapa ett anpassat initiativ i Azure policy, Lägg till principer till den och publicera det i Azure Security Center och visualisera det som rekommendationer.

Nu har vi även lagt till alternativet för att redigera metadata för anpassade rekommendationer. Alternativen för metadata omfattar allvarlighets grad, reparations steg, Hot information och mycket annat.  

[Lär dig mer om att förbättra dina anpassade rekommendationer med detaljerad information](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).


## <a name="april-2020"></a>April 2020

### <a name="dynamic-compliance-packages-now-generally-available"></a>Dynamiska Compliance-paket är nu allmänt tillgängliga

Instrument panelen för Azure Security Center reglering av efterlevnad innehåller nu **dynamiska paket för efterlevnad** (nu allmänt tillgängliga) för att spåra ytterligare bransch-och reglerings standarder.

Du kan lägga till dynamiska efterlevnadsprinciper i din prenumeration eller hanterings grupp på sidan Security Center säkerhets princip. När du har registrerat en standard eller benchmark visas standarden på instrument panelen för kontroll av efterlevnad med alla associerade efterlevnadsprinciper som har mappats som utvärderingar. En sammanfattnings rapport för någon av de standarder som har registrerats är tillgänglig för hämtning.

Nu kan du lägga till standarder som till exempel:

- **NIST SP 800-53 R4**
- **SWIFT CSP-CSCF – v2020**
- **Storbritannien, officiella och UK NHS**
- **Canada Federal PBMM**
- **Azure CIS-1.1.0 (ny)** (som är en mer fullständig representation av Azure CIS-1.1.0)

Dessutom har vi nyligen lagt till **Azures säkerhets benchmark**, de Microsoft-baserade Azure-/regionsspecifika rikt linjerna för säkerhet och efterlevnad av praxis som baseras på vanliga ramverk för efterlevnad. Ytterligare standarder kommer att stödjas på instrument panelen när de blir tillgängliga.  
 
[Läs mer om hur du anpassar uppsättningen med standarder på instrument panelen för](update-regulatory-compliance-packages.md)regelefterlevnad.


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Identitets rekommendationer ingår nu i Azure Security Center kostnads fri nivå

Säkerhets rekommendationer för identitet och åtkomst på den Azure Security Center kostnads fria nivån är nu allmänt tillgängliga. Detta är en del av arbetet för att göra CSPM-funktionerna (Cloud Security position Management) kostnads fritt. Fram till nu var de här rekommendationerna bara tillgängliga på standard pris nivån.

Exempel på identitets-och åtkomst rekommendationer är:

- "Multifaktorautentisering måste vara aktiverat på konton med ägar behörigheter för din prenumeration."
- "Högst tre ägare bör anges för din prenumeration."
- "Föråldrade konton bör tas bort från din prenumeration."

Om du har prenumerationer på den kostnads fria pris nivån kommer deras säkra resultat att påverkas av den här ändringen eftersom de aldrig utvärderades för identitets-och åtkomst säkerhet.

[Lär dig mer om identitets-och åtkomst rekommendationer](recommendations-reference.md#recs-identity).
[Läs mer om övervakning av identitet och åtkomst](security-center-identity-access.md).


## <a name="march-2020"></a>Mars 2020

### <a name="workflow-automation-is-now-generally-available"></a>Arbets flödes automatisering är nu allmänt tillgänglig

Funktionen för automatisering av arbets flöden i Azure Security Center är nu allmänt tillgänglig. Använd den för att automatiskt utlösa Logic Apps om säkerhets aviseringar och rekommendationer. Dessutom är manuella utlösare tillgängliga för aviseringar och alla rekommendationer som har alternativet snabb korrigering tillgängligt.

Varje säkerhets program innehåller flera arbets flöden för incident svar. De här processerna kan omfatta att meddela relevanta intressenter, starta en ändrings hanterings process och tillämpa vissa åtgärder för reparation. Säkerhets experter rekommenderar att du automatiserar så många steg som beskrivs i de här procedurerna. Automation minskar kostnaderna och kan förbättra säkerheten genom att se till att process stegen utförs snabbt, konsekvent och enligt dina fördefinierade krav.

Mer information om automatiska och manuella Security Center-funktioner för att köra arbets flöden finns i [arbets flödes automatisering](workflow-automation.md).

Information om hur du skapar Logic Apps finns i [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integrering av Azure Security Center med administrations Center för Windows

Nu kan du flytta dina lokala Windows-servrar från Windows administrations Center direkt till Azure Security Center. Security Center blir sedan den enda rutan med glas för att Visa säkerhets information för alla dina Windows administrations Center-resurser, inklusive lokala servrar, virtuella datorer och ytterligare PaaS arbets belastningar.

När du har flyttat en server från Windows administrations Center till Azure Security Center kan du:

- Visa säkerhets aviseringar och rekommendationer i Security Center tillägget för Windows administrations Center.
- Visa säkerhets position och hämta ytterligare detaljerad information om dina hanterade Windows administrations Center-servrar i Security Center inom Azure Portal (eller via ett API).

Läs mer om hur du [integrerar Azure Security Center med administrations Center för Windows](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Skydd för Azure Kubernetes-tjänsten

Azure Security Center utökar dess behållar säkerhetsfunktioner för att skydda Azure Kubernetes service (AKS).

Den populära plattforms Kubernetes med öppen källkod har antagits så mycket att den nu är bransch standard för behållar dirigering. Trots den här omfattande implementeringen finns det fortfarande brist på förståelse för hur du skyddar en Kubernetes-miljö. Att skydda angrepps ytorna i ett behållar program kräver expert kunskaper för att säkerställa att infrastrukturen konfigureras säkert och ständigt övervakas för potentiella hot.

Security Center försvaret omfattar:

- **Identifiering och synlighet** – kontinuerlig identifiering av hanterade AKS-instanser i prenumerationer som registrerats på Security Center.
- **Säkerhets rekommendationer** – användbara rekommendationer som hjälper dig att följa säkerhets metoder för AKS. Dessa rekommendationer ingår i dina säkra Poäng för att se till att de visas som en del av din organisations säkerhets position. Ett exempel på en AKS rekommendation som du kan se är "rollbaserad åtkomst kontroll ska användas för att begränsa åtkomsten till ett Kubernetes service-kluster".
- **Hot skydd** – genom kontinuerlig analys av din AKS-distribution, Security Center varnar dig om hot och skadlig aktivitet som har identifierats på värd-och AKS kluster nivå.

[Läs mer om Azure Kubernetes Services-integrering med Security Center](azure-kubernetes-service-integration.md).
[Läs mer om behållar säkerhetsfunktionerna i Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Förbättrad just-in-Time-upplevelse

Funktionerna, åtgärden och gränssnittet för Azure Security Center just-in-Time-verktyg som skyddar hanterings portarna har förbättrats på följande sätt: 

- **Motiverings fält** – när du begär åtkomst till en virtuell dator (VM) via sidan just-in-time i Azure Portal, är ett nytt valfritt fält tillgängligt för att ange en motivering för begäran. Information som anges i det här fältet kan spåras i aktivitets loggen. 
- **Automatisk rensning av redundanta regler för just-in-Time (JIT)** – när du uppdaterar en JIT-princip körs automatiskt ett rensnings verktyg för att kontrol lera giltigheten för hela ruleset. Verktyget söker efter avvikelser mellan regler i principen och reglerna i NSG. Om ett matchnings fel upptäcks i rensnings verktyget, bestäms orsaken och när det är säkert att göra det, tar bort inbyggda regler som inte behövs längre. Rengöringen tar aldrig bort regler som du har skapat. 

[Lär dig mer om funktionen för JIT-åtkomst](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Två säkerhets rekommendationer för webb program som är föråldrade

Två säkerhets rekommendationer relaterade till webb program är föråldrade: 

- Reglerna för webb program på IaaS NSG: er bör vara härdade.
    (Relaterad princip: NSG: er-reglerna för webb program på IaaS bör vara härdade)

- Åtkomst till App Services bör vara begränsad.
    (Relaterad princip: åtkomst till App Services ska vara begränsad [för hands version])

Dessa rekommendationer visas inte längre i Security Center listan över rekommendationer. De relaterade principerna kommer inte längre att ingå i initiativet med namnet "Security Center default".

[Läs mer om säkerhets rekommendationer](recommendations-reference.md).

## <a name="february-2020"></a>Februari 2020

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>Filbaserad attack identifiering för Linux är nu i för hands version

När angripare ökar angreppet med stealthier-metoder för att undvika identifiering kan Azure Security Center utöka identifieringen av fil lös attacker för Linux, förutom Windows. Filbaserade attacker utnyttjar sårbarheter i program vara, injicera skadliga nytto laster i ofarliga system processer och Dölj i minnet. Dessa tekniker:

- minimera eller eliminera spår av skadlig kod på disk
- minska risken för identifiering med diskbaserade genomsöknings lösningar med skadlig kod

För att räkna ut det här hotet Azure Security Center lanserad fil lös attack identifiering för Windows i oktober 2018 och har nu utökad avkänning av fil lös attacker på Linux också. 


## <a name="january-2020"></a>Januari 2020

### <a name="enhanced-secure-score"></a>Förbättrade säkra Poäng

Nu finns en förbättrad version av den säkra poängen-funktionen i Azure Security Center tillgänglig i för hands versionen. I den här versionen är flera rekommendationer grupperade i säkerhets kontroller som bättre återspeglar dina sårbara attack ytor (till exempel begränsa åtkomst till hanterings portar).

Bekanta dig med de säkra Poäng ändringarna under förhands gransknings fasen och bestäm andra åtgärder som kan hjälpa dig att skydda din miljö ytterligare.

Läs mer i [förbättrade säkra poäng (för hands version) i Azure Security Center](secure-score-security-controls.md).