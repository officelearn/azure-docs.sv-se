---
title: Undersök aviseringar med Azure Sentinel| Microsoft-dokument
description: Använd den här självstudien om du vill lära dig hur du undersöker aviseringar med Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585211"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Självstudiekurs: Identifiera hot som inte finns i rutan


> [!IMPORTANT]
> Hotidentifiering utanför boxen finns för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

När du [har anslutit dina datakällor](quickstart-onboard.md) till Azure Sentinel vill du få ett meddelande när något misstänkt händer. För att du ska kunna göra detta ger Azure Sentinel dig färdiga inbyggda mallar. Dessa mallar har utformats av Microsofts team av säkerhetsexperter och analytiker baserat på kända hot, vanliga attackvektorer och misstänkta aktivitetsupptrappningskedjor. När du har aktiverat dessa mallar söker de automatiskt efter alla aktiviteter som ser misstänkta ut i hela din miljö. Många av mallarna kan anpassas för att söka efter eller filtrera bort aktiviteter, enligt dina behov. Aviseringarna som genereras av dessa mallar skapar incidenter som du kan tilldela och undersöka i din miljö.

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel:

> [!div class="checklist"]
> * Använda out-of-the-box upptäckter
> * Automatisera hotsvar

## <a name="about-out-of-the-box-detections"></a>Om fördefinierade identifieringar

Om du vill visa alla färdiga identifieringar går du till **Analytics** och sedan **regelmallar**. Den här fliken innehåller alla azure sentinel-inbyggda regler.

   ![Använd inbyggda identifieringar för att hitta hot med Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Följande malltyper är tillgängliga:

- **Microsofts säkerhet** – Microsofts säkerhetsmallar skapar automatiskt Azure Sentinel-incidenter från aviseringar som genereras i andra Säkerhetslösningar från Microsoft i realtid. Du kan använda Microsofts säkerhetsregler som en mall för att skapa nya regler med liknande logik. Mer information om säkerhetsregler finns i [Skapa incidenter automatiskt från Microsofts säkerhetsvarningar](create-incidents-from-alerts.md).
- **Fusion** – Baserat på Fusion-teknik använder avancerad flerstegsattackidentifiering i Azure Sentinel skalbara maskininlärningsalgoritmer som kan korrelera många aviseringar och händelser med låg återgivning över flera produkter till högåtergivning och användbara incidenter. Fusion är aktiverat som standard. Eftersom logiken är dold kan du inte använda den som en mall för att skapa mer än en regel.
- **Datorinlärning beteendeanalys** - Dessa mallar är baserade på egenutvecklade Microsoft machine learning algoritmer, så du kan inte se den interna logiken i hur de fungerar och när de körs. Eftersom logiken är dold kan du inte använda den som en mall för att skapa mer än en regel.
-   **Schemalagda** analytiska regler är schemalagda frågor som skrivits av Microsofts säkerhetsexperter. Du kan se frågelogiken och göra ändringar i den. Du kan använda schemalagda regler som en mall för att skapa nya regler med liknande logik.

## <a name="use-out-of-the-box-detections"></a>Använda out-of-the-box upptäckter

1. Om du vill använda en inbyggd mall klickar du på **Skapa regel** för att skapa en ny aktiv regel baserat på mallen. Varje post har en lista över nödvändiga datakällor som kontrolleras automatiskt och detta kan resultera i **att skapa regel** inaktiveras.
  
   ![Använd inbyggda identifieringar för att hitta hot med Azure Sentinel](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. Då öppnas guiden Skapa regel, baserat på den valda mallen. All information fylls i automatiskt och för **schemalagda regler** eller **Microsofts säkerhetsregler**kan du anpassa logiken så att den passar din organisation bättre eller skapa ytterligare regler baserat på den inbyggda mallen. När du har följt stegen i guiden skapa regler och slutfört skapandet av en regel baserad på mallen visas den nya regeln på fliken **Aktiva regler.**

Mer information om fälten i guiden finns i [Självstudiekurs: Skapa anpassade analytiska regler för att upptäcka misstänkta hot](tutorial-detect-threats-custom.md).



## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig hur du kommer igång med att identifiera hot med Azure Sentinel. 

Om du vill veta hur du automatiserar dina svar på hot [konfigurerar du automatiska hotsvar i Azure Sentinel](tutorial-respond-threats-playbook.md).

