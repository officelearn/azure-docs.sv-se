---
title: Azure Monitor-vydesigner till sammanfattning och åtkomst för arbetsböcker konvertering
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658854"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Visa sammanfattning och åtkomst för arbetsböcker i arbetsböcker
[View designer](view-designer.md) är en funktion i Azure Monitor som låter dig skapa anpassade vyer som hjälper dig att visualisera data i din Log Analytics arbetsyta, med diagram, listor och tidslinjer. De håller på att fasas ut och ersättas med arbetsböcker, som ger ytterligare funktioner. I den här artikeln beskrivs hur du kan skapa en översiktssammanfattning och behörigheter som krävs för att komma åt arbetsböcker.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Skapa din arbetsytas sammanfattning från Azure Dashboard
Visa designeranvändare kan känna till att ha en översiktspanel för att representera en uppsättning vyer. För att upprätthålla en visuell översikt som sammanfattningen av visningsdesignerns arbetsyta erbjuder arbetsböcker fästa steg som kan fästas på [instrumentpanelen för Azure-portalen](../../azure-portal/azure-portal-dashboards.md). Precis som översiktspanelerna i sammanfattningen av arbetsytan länkas fästa arbetsboksobjekt direkt till arbetsboksvyn.

Du kan dra nytta av de höga anpassningsfunktionerna som medföljer Azure-instrumentpaneler, som gör det möjligt att uppdatera automatiskt, flytta, dimensionera och ytterligare filtrering för dina fästa objekt och visualiseringar. 

![Instrumentpanel](media/view-designer-conversion-access/dashboard.png)

Skapa en ny Azure-instrumentpanel eller välj en befintlig instrumentpanel för att börja fästa arbetsböcker objekt.

Om du vill fästa enskilda objekt måste du aktivera pin-ikonen för ditt specifika steg. Om du vill göra det väljer du motsvarande **redigeringsknapp** för steget och väljer sedan kugghjulsikonen för att öppna **Avancerade inställningar**. Markera alternativet **Om du alltid vill visa pin-ikonen för det här steget**visas en pin-ikon i det övre högra hörnet av steget. Med den här fäststiftet kan du fästa specifika visualiseringar på instrumentpanelen, till exempel översiktspanelerna.

![Fäst steg](media/view-designer-conversion-access/pin-step.png)


Du kanske också vill fästa flera visualiseringar från arbetsboken eller hela arbetsbokens innehåll på en instrumentpanel. Om du vill fästa hela arbetsboken väljer du **Redigera** i det övre verktygsfältet för att växla **redigeringsläge**. En pin-ikon visas, så att du antingen kan fästa hela arbetsboksobjektet eller alla enskilda steg och visualiseringar i arbetsboken.

![Fäst alla](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Delnings- och visningsbehörigheter 
Arbetsböcker har fördelen av att antingen vara ett privat eller delat dokument. Som standard sparas sparade arbetsböcker under **Mina rapporter,** vilket innebär att endast skaparen kan visa den här arbetsboken.

Du kan dela dina arbetsböcker genom att välja ikonen **Dela** i det övre verktygsfältet i **redigeringsläge**. Du uppmanas att flytta arbetsboken till **Delade rapporter**, vilket genererar en länk som ger direkt åtkomst till arbetsboken.

För att en användare ska kunna visa en delad arbetsbok måste de ha åtkomst till både den prenumeration och resursgrupp som arbetsboken sparas under.

![Prenumerationsbaserad åtkomst](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Nästa steg

- [Vanliga uppgifter](view-designer-conversion-tasks.md)