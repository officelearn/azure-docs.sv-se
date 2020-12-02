---
title: Förstå proaktiv logg insamling på Azure Stack Edge Pro-enhet
description: Beskriver hur proaktiv logg insamling görs på en Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: alkohli
ms.openlocfilehash: f79de47ec0ffad11f650054b581dbbaae030edbf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466963"
---
# <a name="proactive-log-collection-on-your-azure-stack-edge-device"></a>Proaktiv logg insamling på din Azure Stack Edge-enhet

Du kan aktivera proaktiv logg insamling på din Azure Stack Edge-enhet baserat på systemets hälso indikatorer för att effektivt felsöka eventuella problem med enheten. Den här artikeln beskriver vad som är proaktiv logg samling, hur du aktiverar det och hur data hanteras när proaktiv logg insamling är aktiverat.
   
Informationen i den här artikeln gäller Azure Stack Edge Pro GPU, Azure Stack Edge Pro R och Azure Stack Edge Mini R-enheter.

## <a name="about-proactive-log-collection"></a>Om proaktiv logg insamling

Microsofts kund support-och teknik team använder system loggar från Azure Stack gräns enheten för att effektivt identifiera och åtgärda problem som kan uppstå under driften. Proaktiv logg insamling är en metod som varnar Microsoft om ett ärende/en händelse (se avsnittet proaktiva information om logg insamlings indikatorer för händelser som spåras) har identifierats av kundens Azure Stack Edge-installation. Support loggar som rör problemet laddas automatiskt upp till ett Azure Storage konto som hanteras och styrs av Microsoft. Microsoft Support och Microsoft-tekniker granskar dessa support loggar för att fastställa bästa möjliga åtgärd för att lösa problemet med kunden.    

> [!NOTE]
> De här loggarna används bara för fel söknings syfte och ger support till kunderna i händelse av problem. 


## <a name="enabling-proactive-log-collection"></a>Aktiverar proaktiv logg insamling

Du kan aktivera den proaktiva logg insamlingen när du försöker aktivera enheten via det lokala användar gränssnittet. 

1. Gå till sidan **Kom igång** i enhetens lokala webb gränssnitt.
2. På **aktiverings** panelen väljer du **Aktivera**. 

    ![Lokal webb gränssnitt "moln information" sida 1](./media/azure-stack-edge-pro-r-deploy-activate/activate-1.png)
    
3. I fönstret **Aktivera** :
    1. Ange aktiverings **nyckeln** som du fick i [Hämta aktiverings nyckeln för Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Du kan aktivera proaktiv logg insamling för att låta Microsoft samla in loggar baserat på enhetens hälso status. Loggarna som samlas in på det här sättet överförs till ett Azure Storage-konto.
    
    1. Välj **Använd**.

    ![Lokal webb gränssnitt "moln information" sida 2](./media/azure-stack-edge-pro-r-deploy-activate/activate-2.png)



## <a name="proactive-log-collection-indicators"></a>Proaktiva logg insamlings indikatorer

När den proaktiv logg samlingen har Aktiver ATS laddas loggarna upp automatiskt när någon av följande händelser identifieras på enheten:  


|Varning/fel/villkor  |Description  |
|---------|---------|
|AcsUnhealthyCondition     |Azures konsekventa tjänster är inte felfria.         |
|IOTEdgeAgentNotRunningCondition      |IoT Edge Agent körs inte.         |
|UpdateInstallFailedEvent | Det gick inte att installera uppdateringen.        |

 
Microsoft kommer att fortsätta att lägga till nya händelser i föregående lista. Inget ytterligare medgivande krävs för nya händelser. På den här sidan kan du läsa om de senaste indikatorerna för inaktiva proaktiva loggar.    
 

## <a name="other-log-collection-methods"></a>Andra logg insamlings metoder

Förutom proaktiv logg insamling, som samlar in vissa loggar som rör ett särskilt problem, finns det andra logg samlingar som kan ge mycket djupare förståelse för systemets hälsa och beteende. De här andra logg samlingarna kan vanligt vis utföras under en support förfrågan eller utlösas av Microsoft baserat på telemetridata som enheten tillhandahåller.  

## <a name="handling-data"></a>Hantera data

Om en kund aktiverar proaktiv logg insamling, samtycker de till Microsoft att samla in loggar från Azure Stack Edge-enheten enligt beskrivningen här. Kunden bekräftar och samverkar också med uppladdningen och lagringen av dessa loggar i ett Azure Storage konto som hanteras och styrs av Microsoft.

Microsoft använder endast data för att felsöka systemets hälsa och problem. Data används inte för marknadsföring, annonsering eller andra kommersiella ändamål utan kund medgivande. 

De data som Microsoft samlar in hanteras enligt våra standard sekretess metoder. Om en kund bestämmer sig för att återkalla sitt medgivande för proaktiv logg insamling kommer alla data som samlats in med medgivande innan återkallning inte att påverkas.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [samlar in ett support paket](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).