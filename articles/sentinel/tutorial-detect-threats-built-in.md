---
title: Undersök aviseringar med Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder färdiga, inbyggda Azure Threat Detection-mallar som meddelar dig när något misstänkt inträffar.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d73337c25c812363b7a542bf42372ca3baa10e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605430"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Självstudie: Identifiera hot direkt


> [!IMPORTANT]
> Varningen från en box är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

När du har [anslutit dina data källor](quickstart-onboard.md)   till Azure Sentinel ska du bli meddelad när något misstänkt inträffar. Det är därför som Azure Sentinel tillhandahåller färdiga, inbyggda mallar som hjälper dig att skapa hot identifierings regler. De här mallarna utformades av Microsofts team med säkerhetsexperter och analytiker baserat på kända hot, vanliga angrepps vektorer och misstänkta aktivitets eskalerings kedjor. Regler som skapas från de här mallarna söker automatiskt igenom din miljö efter aktivitet som ser misstänkt ut. Många av mallarna kan anpassas för att söka efter aktiviteter eller filtrera dem efter dina behov. De aviseringar som genereras av dessa regler skapar incidenter som du kan tilldela och undersöka i din miljö.

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel:

> [!div class="checklist"]
> * Använd inaktuella hot identifieringar
> * Automatisera hot svar

## <a name="about-out-of-the-box-detections"></a>Om fördefinierade identifieringar

Om du vill visa alla färdiga identifieringar går du till **analyser** och sedan **regel mallar**. Den här fliken innehåller alla inbyggda regler för Azure-kontroll.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Använd inbyggda identifieringar för att hitta hot med Azure Sentinel":::

Följande typer av mallar är tillgängliga:

- **Microsoft-säkerhet**
   
   Microsoft Security templates skapar automatiskt Azure Sentinel-incidenter från aviseringarna som genereras i andra Microsoft-säkerhetslösningar i real tid. Du kan använda Microsofts säkerhets regler som mall för att skapa nya regler med liknande logik. Mer information om säkerhets regler finns i [skapa incidenter automatiskt från Microsofts säkerhets aviseringar](create-incidents-from-alerts.md).

- **Fusion** 

    Med utgångs punkt i fusions tekniken används skalbara algoritmer för maskin inlärning i Azure Sentinel för att korrelera många varningar och händelser över flera produkter till hög åter givning och åtgärds bara incidenter. Fusion är aktiverat som standard. Eftersom logiken är dold och därför inte kan anpassas, kan du bara skapa en regel med den här mallen.

- **Beteende analys av Machine Learning**

    De här mallarna baseras på tillverkarspecifika Microsoft Machine Learning-algoritmer, så du kan inte se den interna logiken för hur de fungerar och när de körs. Eftersom logiken är dold och därför inte kan anpassas, kan du bara skapa en regel med varje mall av den här typen.

- **Schemalagd**

    Regler för schemalagd analys baseras på inbyggda frågor som skrivits av Microsofts säkerhets experter. Du kan se fråge logiken och göra ändringar i den. Du kan använda mallen för schemalagda regler och anpassa inställningarna för fråge logik och schemaläggning för att skapa nya regler.

## <a name="use-out-of-the-box-detections"></a>Använd välkomst identifieringar

1. Om du vill använda en inbyggd mall klickar du på mallnamnet och sedan på knappen **Skapa regel** i informations fönstret för att skapa en ny aktiv regel baserat på mallen. Varje mall innehåller en lista över nödvändiga data källor. När du öppnar mallen kontrol leras data källorna automatiskt för tillgänglighet. Om det finns ett tillgänglighets problem kan knappen **Skapa regel** vara inaktive rad, eller så kan du se en varning om den här inställningen.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Använd inbyggda identifieringar för att hitta hot med Azure Sentinel":::
 
1. Om du klickar på knappen **Skapa regel** öppnas guiden Skapa regel som baseras på den valda mallen. All information fylls i och med de **schemalagda** eller **Microsoft** -säkerhetsmallarna kan du anpassa logiken och andra regel inställningar så att de passar dina behov bättre. Du kan upprepa den här processen för att skapa ytterligare regler baserade på den inbyggda mallen. När du har gått igenom stegen i guiden Skapa regel till slutet, kommer du att ha skapat en regel som baseras på mallen. De nya reglerna visas på fliken **aktiva regler** .

    Mer information om hur du anpassar reglerna i guiden Skapa regel finns i [Självstudier: skapa anpassade analys regler för att identifiera hot](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du kommer igång med att identifiera hot med Azure Sentinel. 

Om du vill lära dig hur du automatiserar dina svar på hot [ställer du in automatiserade hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).

