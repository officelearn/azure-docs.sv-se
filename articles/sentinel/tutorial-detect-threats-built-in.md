---
title: Undersök aviseringar med Azure Sentinel | Microsoft Docs
description: Använd den här självstudien för att lära dig att undersöka aviseringar med Azure Sentinel.
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77585211"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Självstudie: identifiera hot färdiga


> [!IMPORTANT]
> Varningen från en box är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

När du har [anslutit dina data källor](quickstart-onboard.md) till Azure Sentinel vill du bli meddelad när något misstänkt inträffar. För att du ska kunna göra detta ger Azure Sentinel dig med inbyggda mallar som är färdiga. De här mallarna utformades av Microsofts team med säkerhetsexperter och analytiker baserat på kända hot, vanliga angrepps vektorer och misstänkta aktivitets eskalerings kedjor. När du har aktiverat de här mallarna söker de automatiskt efter aktiviteter som ser misstänkt ut i din miljö. Många av mallarna kan anpassas för att söka efter eller filtrera ut aktiviteter utifrån dina behov. De aviseringar som genereras av de här mallarna skapar incidenter som du kan tilldela och undersöka i din miljö.

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel:

> [!div class="checklist"]
> * Använd välkomst identifieringar
> * Automatisera hot svar

## <a name="about-out-of-the-box-detections"></a>Om fördefinierade identifieringar

Om du vill visa alla färdiga identifieringar går du till **analyser** och sedan **regel mallar**. Den här fliken innehåller alla inbyggda regler för Azure-kontroll.

   ![Använd inbyggda identifieringar för att hitta hot med Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Följande typer av mallar är tillgängliga:

- **Microsoft Security** – Microsoft Security templates skapar automatiskt Azure Sentinel-incidenter från aviseringarna som genereras i andra Microsoft-säkerhetslösningar i real tid. Du kan använda Microsofts säkerhets regler som mall för att skapa nya regler med liknande logik. Mer information om säkerhets regler finns i [skapa incidenter automatiskt från Microsofts säkerhets aviseringar](create-incidents-from-alerts.md).
- **Fusion** -baserad på fusions teknik, avancerad attack identifiering i flera steg i Azure Sentinel använder skalbara algoritmer för maskin inlärning som kan korrelera många aviseringar och händelser med låg kvalitet på flera produkter i hög åter givning och åtgärds bara incidenter. Fusion är aktiverat som standard. Eftersom logiken är dold kan du inte använda den som en mall för att skapa mer än en regel.
- **Beteende analys för maskin inlärning** – dessa mallar baseras på tillverkarspecifika Microsoft Machine Learning-algoritmer, så du kan inte se den interna logiken för hur de fungerar och när de körs. Eftersom logiken är dold kan du inte använda den som en mall för att skapa mer än en regel.
-   **Schemalagda** – schemalagda analys regler är schemalagda frågor som skrivits av Microsofts säkerhets experter. Du kan se fråge logiken och göra ändringar i den. Du kan använda schemalagda regler som mall för att skapa nya regler med liknande logik.

## <a name="use-out-of-the-box-detections"></a>Använd välkomst identifieringar

1. Om du vill använda en inbyggd mall klickar du på **Skapa regel** för att skapa en ny aktiv regel baserat på mallen. Varje post innehåller en lista över nödvändiga data källor som kontrol leras automatiskt och det kan resultera i att **skapa regler** inaktive ras.
  
   ![Använd inbyggda identifieringar för att hitta hot med Azure Sentinel](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Guiden Skapa regel öppnas, baserat på den valda mallen. All information fylls i, och för **schemalagda regler** eller **säkerhets regler för Microsoft**, kan du anpassa logiken så att den passar din organisation bättre, eller skapa ytterligare regler baserat på den inbyggda mallen. När du har gått igenom stegen i guiden Skapa regel och slutfört skapandet av en regel som baseras på mallen, visas den nya regeln på fliken **aktiva regler** .

Mer information om fälten i guiden finns i [Självstudier: skapa anpassade analys regler för att identifiera misstänkta hot](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du kommer igång med att identifiera hot med Azure Sentinel. 

Om du vill lära dig hur du automatiserar dina svar på hot [ställer du in automatiserade hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).

