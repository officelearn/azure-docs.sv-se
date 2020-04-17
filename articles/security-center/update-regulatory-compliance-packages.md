---
title: Så här uppdaterar du till dynamisk efterlevnadsövervakning i instrumentpanelen för regelefterlevnad i Azure Security Center | Microsoft-dokument
description: Uppdatera dina regelefterlevnadspaket
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537788"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Uppdatera till dynamiska efterlevnadspaket på instrumentpanelen för regelefterlevnad

Azure Security Center jämför kontinuerligt konfigurationen av dina resurser med krav i branschstandarder, föreskrifter och riktmärken. **Instrumentpanelen för regelefterlevnad** ger insikter om din efterlevnadsposition baserat på hur du uppfyller specifika efterlevnadskontroller och krav.

En standard som du kan spåra din efterlevnadsposition för är [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (mer formellt, "CIS Microsoft Azure Foundations Benchmark version 1.1.0"). 

Representationen av Azure CIS som ursprungligen visas i instrumentpanelen för efterlevnad är beroende av en statisk uppsättning regler som ingår i Security Center.

Med funktionen **för dynamiska efterlevnadspaket** förbättrar Security Center automatiskt sin täckning av branschstandarder över tid. Efterlevnadspaket är i huvudsak initiativ som definieras i Azure Policy. De kan tilldelas till ditt valda scope (prenumeration, hanteringsgrupp och så vidare). Om du vill se efterlevnadsdata mappade som utvärderingar på instrumentpanelen lägger du till ett efterlevnadspaket i din hanteringsgrupp eller prenumeration från säkerhetspolicyn. Om du lägger till ett efterlevnadspaket tilldelas initiativet för regelefterlevnad effektivt ditt valda scope. På så sätt kan du spåra nyligen publicerade lagstiftningsinitiativ som efterlevnadsstandarder i instrumentpanelen. När Microsoft släpper nytt innehåll för initiativet (nya principer som mappar till fler kontroller i standarden) visas det ytterligare innehållet automatiskt på instrumentpanelen.

Det dynamiska efterlevnadspaketet för Azure CIS-riktmärket, **Azure CIS 1.1.0 (ny),** förbättrar den ursprungliga *statiska* versionen genom att:

* Inklusive fler policyer
* Uppdatera automatiskt med ny täckning när den läggs till 

Uppdatera till det nya dynamiska paketet enligt beskrivningen nedan.

## <a name="adding-a-dynamic-compliance-package"></a>Lägga till ett dynamiskt efterlevnadspaket

I följande steg beskrivs hur du lägger till det dynamiska paketet för att övervaka din efterlevnad av Azure CIS-riktmärket v1.1.0.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Uppdatera till Azure CIS 1.1.0 -paketet för dynamisk kompatibilitet 

1. Öppna sidan **Säkerhetsprincip.** På den här sidan visas antalet hanteringsgrupper, prenumerationer, arbetsytor och din hanteringsgruppsstruktur.

1. Välj den prenumeration eller hanteringsgrupp som du vill hantera regelefterlevnadspositionen för. Vi rekommenderar att du väljer det högsta scope som standarden är tillämplig på så att efterlevnadsdata aggregeras och spåras för alla kapslade resurser. 

1. I avsnittet Bransch & regulatoriska standarder ser du att Azure CIS 1.1.0 kan uppdateras för nytt innehåll. Klicka på **Uppdatera nu**. 

1. Du kan också klicka på **Lägg till fler standarder** för att öppna sidan Lägg till efterlevnadsstandarder för **regelefterlevnad.** Där kan du söka manuellt efter **Azure CIS 1.1.0 (Nya)** och dynamiska paket för andra efterlevnadsstandarder som **NIST SP 800-53 R4**, **SWIFT CSP CSCF-v2020,** **UKO och UK NHS**och Canada **PBMM**.
    
    > [!TIP]
    > Endast användare som är ägare eller principdeltagare har de behörigheter som krävs för att lägga till efterlevnadsstandarder. 

    ![Lägga till regelpaket i Azure Security Centers instrumentpanel för regelefterlevnad](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. I Säkerhetscenters sidofält väljer du **Regelefterlevnad** för att öppna instrumentpanelen för regelefterlevnad. 
    * Azure CIS 1.1.0 (Ny) visas nu i din lista över bransch & lagstadgade standarder. 
    * Den ursprungliga *statiska* vyn för din Azure CIS 1.1.0-efterlevnad förblir också vid sidan av den. Det kan tas bort automatiskt i framtiden.

    > [!NOTE]
    > Det kan ta några timmar innan en nyligen tillagd standard visas på instrumentpanelen för efterlevnad.


    [![Instrumentpanel för regelefterlevnad som visar gamla och nya Azure CIS](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig:

* Så här **uppgraderar du standarderna** som visas i instrumentpanelen för regelefterlevnad till de nya *dynamiska* paketen
* Så här lägger du till **efterlevnadspaket** för att övervaka att du följer ytterligare standarder. 

För annat relaterat material, se följande artiklar: 

- [Instrumentpanel för regelefterlevnad i säkerhetscenter](security-center-compliance-dashboard.md)
- [Arbeta med säkerhetsprinciper](tutorial-security-policy.md)
- [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur du använder rekommendationer i Azure Security Center för att skydda dina Azure-resurser.