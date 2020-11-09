---
title: Använda instrument panelen för kontroll av efterlevnad i Azure Security Center
description: Lär dig hur du lägger till och tar bort reglerande standarder från instrument panelen för kontroll av efterlevnad i Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2020
ms.author: memildin
ms.openlocfilehash: e7e1567a487dc6cadc94a42f02c597ff0e02665b
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372769"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Anpassa uppsättningen standarder i din instrument panel för regelefterlevnad

Azure Security Center jämför kontinuerligt konfigurationen av dina resurser med krav i bransch standarder, regler och benchmarks. **Instrument panelen** för övervakning av efterlevnad ger insikter om position utifrån hur du uppfyller särskilda kompatibilitetsinställningar och krav.


## <a name="overview-of-compliance-packages"></a>Översikt över efterlevnadspaket

Bransch standarder, reglerande standarder och benchmarks visas i Security Center som *efterlevnadsprinciper*.  Varje paket är ett initiativ som definieras i Azure Policy. Om du vill visa efterföljande data som har mappats som utvärderingar på din instrument panel lägger du till ett kompatibilitetstillstånd i hanterings gruppen eller prenumerationen från sidan **säkerhets princip** . (Läs mer om Azure Policy och initiativ i [arbeta med säkerhets principer](tutorial-security-policy.md).)

När du har registrerat en standard eller benchmark för ditt valda omfång tilldelar den initiativet till omfånget och standarden visas i instrument panelen för kontroll av efterlevnad med alla associerade efterlevnadsprinciper som har mappats som utvärderingar. Du kan också hämta sammanfattnings rapporter för alla standarder som har publicerats.

Microsoft spårar också gällande regelverk och förbättrar automatiskt täckningen i några av paketen över tid. När Microsoft släpper ut nytt innehåll för initiativet (nya principer som mappar till fler kontroller i standarden) visas det ytterligare innehållet automatiskt på din instrument panel.

> [!TIP]
> En standard som ökar med tiden när Microsoft lanserar nytt innehåll är **Azure CIS-1.1.0 (ny)** (mer formellt, [CIS Microsoft Azure grunderna benchmark-version 1.1.0](https://www.cisecurity.org/benchmark/azure/)). Du måste lägga till detta på din instrument panel tillsammans med "Azure CIS-1.1.0", en representation av Azure CIS som är konfigurerad som standard i varje Security Centers miljö. Paketet förlitar sig på en statisk uppsättning regler. Det nyare paketet innehåller fler principer och uppdateras automatiskt med tiden. Uppdatera till det nya dynamiska paketet enligt beskrivningen nedan.


## <a name="available-packages"></a>Tillgängliga paket

Du kan lägga till standarder som NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, Storbritannien officiella och Storbritannien NHS, Kanadas federala PBMM och Azure CIS 1.1.0 (ny) – en mer fullständig representation av Azure CIS 1.1.0. 

Dessutom kan du lägga till **Azure Security benchmark** , de Microsoft-baserade, Azure-/regionsspecifika rikt linjerna för säkerhet och efterlevnad av praxis som baseras på vanliga ramverk för efterlevnad. ([Läs mer om Azure Security benchmark](../security/benchmarks/introduction.md).)

Ytterligare standarder kommer att stödjas på instrument panelen när de blir tillgängliga. 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Lägg till en regel standard på din instrument panel

Följande steg beskriver hur du lägger till ett paket för att övervaka efterlevnaden av en av de regler som stöds.

> [!NOTE]
> Endast användare som är ägare eller princip deltagare har de behörigheter som krävs för att lägga till efterlevnads standarder. 

1. Från Security Centerens marginal List väljer du regelefterlevnad för att öppna instrument **panelen för kontroll** av efterlevnad. Här kan du se de efterlevnads standarder som för närvarande är tilldelade till de valda prenumerationerna.   

1. Överst på sidan väljer du **Hantera efterlevnadsprinciper**. Sidan princip hantering visas.

1. Välj den prenumeration eller hanterings grupp som du vill hantera position för regelefterlevnad för. 

    > [!TIP]
    > Vi rekommenderar att du väljer det högsta omfånget som standarden gäller för, så att efterlevnadsprinciper sammanställs och spåras för alla kapslade resurser. 

1. Om du vill lägga till de standarder som är relevanta för din organisation klickar du på **Lägg till fler standarder**. 

1. På sidan **Lägg till normer för regelefterlevnad** kan du söka efter paket för alla tillgängliga standarder. Några av de tillgängliga standard alternativen är:

    - **Benchmark för Azure-säkerhet**
    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP-CSCF – v2020**
    - **UKO och Storbritannien NHS**
    - **Kanada-PBMM**
    
    ![Lägga till reglerings paket i Azure Security Center kontroll panelen för regelefterlevnad](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Välj **Lägg till** och ange all nödvändig information för det aktuella initiativet, till exempel omfång, parametrar och reparation.

1. Från Security Centerens marginal List väljer du **regler för efterlevnad** igen för att gå tillbaka till instrument panelen för kontroll av efterlevnad.
    * Din nya standard visas i din lista över bransch & reglerande standarder. 
    * Om du har lagt till **Azure CIS-1.1.0 (ny)** , kommer den ursprungliga *statiska* vyn av din Azure CIS-1.1.0 även att finnas kvar tillsammans. Den kan tas bort automatiskt i framtiden.

    > [!NOTE]
    > Det kan ta några timmar innan en nyligen tillagd standard visas på instrument panelen för efterlevnad.

    [![Instrument panelen för kontroll av efterlevnad visar gamla och nya Azure CIS](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>Ta bort en standard från din instrument panel

Om någon av de angivna regel standarderna inte är relevanta för din organisation, är det enkelt att ta bort dem från användar gränssnittet. På så sätt kan du ytterligare anpassa instrument panelen för kontroll av efterlevnad och bara fokusera på de standarder som gäller för dig.

Så här tar du bort en standard:

1. Från Security Center menyn väljer du **säkerhets princip**.

1. Välj den relevanta prenumeration som du vill ta bort en standard från.

    > [!NOTE]
    > Du kan ta bort en standard från en prenumeration, men inte från en hanterings grupp. 

    Sidan säkerhets princip öppnas. För den valda prenumerationen visas standard principen, branscherna och regel standarderna och eventuella anpassade initiativ som du har skapat.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Ta bort en reglerande standard från instrument panelen för regler för efterlevnad i Azure Security Center":::

1. Välj **inaktivera** för den standard som du vill ta bort. Ett bekräftelse fönster visas.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Bekräfta att du verkligen vill ta bort den gällande lagen som du har valt":::

1. Välj **Ja**. Standard kommer att tas bort. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du **lägger till efterlevnadsprinciper** för att övervaka efterlevnaden av ytterligare standarder. 

Information om annat relaterat material finns i följande artiklar: 

- [Benchmark för Azure-säkerhet](../security/benchmarks/introduction.md)
- [Instrument panel för övervakning av gällande säkerhets Center](security-center-compliance-dashboard.md)
- [Arbeta med säkerhetspolicyer](tutorial-security-policy.md)