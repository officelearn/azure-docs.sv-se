---
title: Skapa anpassade analys regler för att identifiera misstänkta hot med Azure Sentinel | Microsoft Docs
description: Använd den här självstudien för att lära dig hur du skapar anpassade analys regler för att identifiera misstänkta hot med Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 18c11198f6b81e72e371b3ab06ed3a7330078c52
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023781"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>Självstudier: Skapa anpassade analys regler för att identifiera misstänkta hot

När du har [anslutit dina data källor](quickstart-onboard.md) To Azure Sentinel kan du skapa anpassade regler som kan söka efter vissa kriterier i din miljö och generera incidenter när villkoren matchas så att du kan undersöka dem. Den här självstudien hjälper dig att skapa anpassade regler för att identifiera hot med Azure Sentinel.

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel.
> [!div class="checklist"]
> * Skapa analys regler
> * Automatisera hot svar

## <a name="create-custom-analytic-rules"></a>Skapa anpassade analys regler

Du kan skapa anpassade analys regler som hjälper dig att söka efter de typer av hot och felaktigheter som är misstänkta i din miljö. Regeln säkerställer att du meddelas direkt, så att du kan prioritering, undersöka och åtgärda hoten.

1. I Azure Portal under Azure Sentinel väljer du **analys**.

1. I den översta meny raden väljer du **+ skapa** och väljer **schemalagd frågeregel**. Då öppnas **guiden Skapa anpassad regel**.

    ![Skapa schemalagd fråga](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. Ange ett beskrivande namn och en beskrivning på fliken **Allmänt** . Ange **allvarlighets grad för aviseringen** vid behov. När du skapar regeln kan du aktivera den, vilket gör att den körs omedelbart när du har skapat den. Om du skapar den som inaktive rad läggs regeln till på fliken **aktiva regler** och du kan aktivera den därifrån när du behöver den.

    ![Börja skapa en anpassad analys regel](media/tutorial-detect-threats-custom/general-tab.png)

1. På fliken **Inställningar** kan du antingen skriva en fråga direkt eller skapa frågan i Log Analytics och sedan klistra in den i fältet **Sök fråga** . När du ändrar och konfigurerar din fråga simulerar Azure Sentinel frågeresultaten i fönstret för **förhands granskning** till höger. På så sätt kan du få information om hur mycket data som genereras under ett angivet tidsintervall för den avisering som du skapar. Mängden beror på vad du anger för **Kör fråga varje** och **Sök efter data från den sista**. Om du ser att aviseringen i genomsnitt skulle utlösa aviseringar ofta kan du ange antalet resultat högre, så att det är högre än den genomsnittliga bas linjen.

   ![Skapa fråga i Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   Här är en exempel fråga som varnar dig när ett avvikande antal resurser skapas i Azure Activity.

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > Frågans längd måste vara mellan 1 och 1 0000 tecken och får inte innehålla "search \*" eller "union \*".

    1. Under **schemaläggning av frågor**anger du följande parametrar:

        1.  Ställ in **Kör fråga var** för att ange **frekvensen** för hur ofta frågan ska köras – så ofta som var femte minut eller som sällan som en gång om dagen.

        1.  Ange **Sök data från den sista** för att kontrol lera tidsfönstret för hur mycket data som frågan körs på, till exempel, den kan köras varje timme, i över 60 minuter med data.

        1. Du kan ställa in Azure Sentinel för att **sluta köra frågan när aviseringen har genererats** om du bara vill få en avisering när den inträffar. Du måste ange det fönster under vilket frågan ska sluta köras, upp till 24 timmar.

    1. Definiera villkor för aviserings Utlös under **aviserings utlösare**. Under **enhets mappning**kan du mappa kolumnerna i din fråga till entitetsfält som identifieras av Azure Sentinel. För varje fält mappar du relevant kolumn i frågan som du skapade i Log Analytics till lämpligt entitetsfält. Varje entitet innehåller flera fält, t. ex. SID och GUID. Du kan mappa entiteten enligt alla fält, inte bara entiteten på den översta nivån.

1.  På fliken **automatisera svar** väljer du de spel böcker som du vill köra automatiskt när en avisering genereras av den anpassade regeln. Mer information om hur du skapar och automatiserar spel böcker finns i [svara på hot](tutorial-respond-threats-playbook.md).

    ![Automatisera svar på hot i Azure Sentinel](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. Välj **Granska** för att granska alla inställningar för den nya varnings regeln och välj sedan **skapa för att initiera aviserings regeln**.

   ![Granska din anpassade fråga](media/tutorial-detect-threats-custom/review-tab.png)

1.  När aviseringen har skapats läggs en anpassad regel till i tabellen under **aktiva regler**. I den här listan kan du aktivera, inaktivera eller ta bort varje regel.

1.  Om du vill visa resultaten av de aviserings regler som du skapar går du till sidan **incidenter** där du kan prioritering, [undersöka incidenter](tutorial-investigate-cases.md)och åtgärda hoten.


> [!NOTE]
> Aviseringar som genereras i Azure Sentinel är tillgängliga via [Microsoft Graph säkerhet](https://aka.ms/securitygraphdocs). Mer information finns i dokumentationen för [Microsoft Graph säkerhets aviseringar](https://aka.ms/graphsecurityreferencebetadocs).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du kommer igång med att identifiera hot med Azure Sentinel.

Om du vill lära dig hur du automatiserar dina svar på hot [ställer du in automatiserade hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).

