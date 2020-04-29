---
title: Så här uppdaterar du övervakning av dynamisk övervakning av efterlevnad på din Azure Security Center kontroll panel för efterlevnad | Microsoft Docs
description: Uppdatera dina regler för regelefterlevnad
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fa5027ed285456247891c84e559b74a14237f553
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537788"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Uppdatera till dynamiska Compliance-paket på instrument panelen för regelefterlevnad

Azure Security Center jämför kontinuerligt konfigurationen av dina resurser med krav i bransch standarder, regler och benchmarks. **Instrument panelen** för övervakning av efterlevnad ger insikter om position utifrån hur du uppfyller särskilda kompatibilitetsinställningar och krav.

En standard som du kan använda för att spåra position för efterlevnad är [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (mer formellt, "CIS Microsoft Azure grunderna benchmark-1.1.0"). 

Representationen av Azure CIS som inlednings vis visas i instrument panelen för efterlevnad förlitar sig på en statisk uppsättning regler som ingår i Security Center.

Med funktionen för **dynamiska paket** kan Security Center automatiskt förbättra sin täckning av bransch standarder över tid. Efterlevnadsprinciper är i huvudsak initiativ definierade i Azure Policy. De kan tilldelas till ditt valda omfång (prenumeration, hanterings grupp och så vidare). Om du vill visa de kompatibilitets data som har mappats som utvärderingar på din instrument panel lägger du till ett kompatibilitetstillstånd i hanterings gruppen eller prenumerationen inifrån säkerhets principen. Genom att lägga till ett kompatibilitetstillstånd tilldelas du en kontroll av regelefterlevnad i det valda omfånget. På så sätt kan du spåra nyligen publicerade regel initiativ som efterlevnadsprinciper på din instrument panel. När Microsoft släpper ut nytt innehåll för initiativet (nya principer som mappar till fler kontroller i standarden) visas det ytterligare innehållet automatiskt på din instrument panel.

Det dynamiska Compliance-paketet för Azure CIS-benchmark, **Azure CIS-1.1.0 (ny)**, förbättrar den ursprungliga *statiska* versionen genom att:

* Inklusive fler principer
* Uppdatera automatiskt med ny täckning när den läggs till 

Uppdatera till det nya dynamiska paketet enligt beskrivningen nedan.

## <a name="adding-a-dynamic-compliance-package"></a>Lägga till ett dynamiskt Compliance-paket

Följande steg beskriver hur du lägger till det dynamiska paketet för att övervaka din efterlevnad med Azure CIS benchmark v-1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Uppdatera till Azure CIS-1.1.0 (nytt) dynamiskt Compliance-paket 

1. Öppna sidan **säkerhets princip** . På den här sidan visas antalet hanterings grupper, prenumerationer, arbets ytor och din hanterings grupps struktur.

1. Välj den prenumeration eller hanterings grupp som du vill hantera position för regelefterlevnad för. Vi rekommenderar att du väljer det högsta omfånget som standarden gäller för, så att efterlevnadsprinciper sammanställs och spåras för alla kapslade resurser. 

1. I avsnittet bransch & reglerande standarder ser du att Azure CIS-1.1.0 kan uppdateras för nytt innehåll. Klicka på **Uppdatera nu**. 

1. Du kan också klicka på **Lägg till fler standarder** för att öppna sidan **Lägg till normer för regelefterlevnad** . Där kan du söka manuellt efter **Azure CIS-1.1.0 (nya)** och dynamiska paket för andra efterlevnadsprinciper som **NIST SP 800-53 R4**, **Swift CSP CSCF-V2020**, **UKO och Storbritannien NHS**och **Canada PBMM**.
    
    > [!TIP]
    > Endast användare som är ägare eller princip deltagare har de behörigheter som krävs för att lägga till efterlevnads standarder. 

    ![Lägga till reglerings paket i Azure Security Center kontroll panelen för regelefterlevnad](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. Från Security Centerens marginal List väljer du regelefterlevnad för att öppna instrument **panelen för kontroll** av efterlevnad. 
    * Azure CIS-1.1.0 (ny) visas nu i din lista över bransch & reglerande standarder. 
    * Den ursprungliga *statiska* vyn av din Azure CIS 1.1.0-kompatibilitet kommer också att finnas kvar tillsammans. Den kan tas bort automatiskt i framtiden.

    > [!NOTE]
    > Det kan ta några timmar innan en nyligen tillagd standard visas på instrument panelen för efterlevnad.


    [![Instrument panelen för kontroll av efterlevnad visar gamla och nya Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig:

* Så här **uppgraderar du de standarder** som visas på instrument panelen för regelefterlevnad till nya *dynamiska* paket
* Hur du **lägger till efterlevnadsprinciper** för att övervaka efterlevnaden av ytterligare standarder. 

Information om annat relaterat material finns i följande artiklar: 

- [Instrument panel för övervakning av gällande säkerhets Center](security-center-compliance-dashboard.md)
- [Arbeta med säkerhetsprinciper](tutorial-security-policy.md)
- [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md) – lär dig hur du använder rekommendationer i Azure Security Center för att skydda dina Azure-resurser.