---
title: Visualisera dina data med hjälp av instrumentpaneler baserade på Azure Monitor-arbetsböcker i Azure Sentinel | Microsoft-dokument
description: Använd den här självstudien om du vill lära dig hur du visualiserar dina data med hjälp av instrumentpaneler baserat på arbetsböcker i Azure Sentinel.
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
ms.date: 01/01/2020
ms.author: yelevin
ms.openlocfilehash: b4461ac43e9356536914b345ef28f5de62fc9f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585228"
---
# <a name="tutorial-visualize-and-monitor-your-data"></a>Självstudiekurs: Visualisera och övervaka dina data



När du [har anslutit dina datakällor](quickstart-onboard.md) till Azure Sentinel kan du visualisera och övervaka data med azure sentinel-antagandet av Azure Monitor-arbetsböcker, vilket ger mångsidighet när du skapar anpassade instrumentpaneler. Arbetsböckerna visas på olika sätt i Azure Sentinel, men det kan vara användbart för dig att se hur du [skapar interaktiva rapporter med Azure Monitor-arbetsböcker](../azure-monitor/app/usage-workbooks.md). Med Azure Sentinel kan du skapa anpassade arbetsböcker över dina data och även levereras med inbyggda arbetsboksmallar så att du snabbt kan få insikter om dina data så fort du ansluter en datakälla.


Den här självstudien hjälper dig att visualisera dina data i Azure Sentinel.
> [!div class="checklist"]
> * Använda inbyggda arbetsböcker
> * Skapa nya arbetsböcker

## <a name="prerequisites"></a>Krav

- Du måste ha minst behörighet för arbetsboksläsare eller arbetsboksdeltagare i resursgruppen på Azure Sentinel-arbetsytan.

> [!NOTE]
> Arbetsböckerna som du kan se i Azure Sentinel sparas i azure sentinel-arbetsytans resursgrupp och taggas av arbetsytan där de skapades.

## <a name="use-built-in-workbooks"></a>Använda inbyggda arbetsböcker

1. Gå till **Arbetsböcker** och välj sedan **Mallar** för att se hela listan över azure sentinel-inbyggda arbetsböcker. Om du vill se vilka som är relevanta för de datatyper som du har anslutit visas **datatypen obligatorisk** i varje arbetsbok bredvid en grön bock om du redan strömmar relevanta data till Azure Sentinel.
  ![gå till arbetsböcker](./media/tutorial-monitor-data/access-workbooks.png)
1. Klicka på **Visa arbetsbok** om du vill visa mallen ifylld med dina data.
  
1. Om du vill redigera arbetsboken väljer du **Spara**och väljer sedan den plats där du vill spara json-filen för mallen. 

   > [!NOTE]
   > Detta skapar en Azure-resurs baserat på relevant mall och sparar själva mallen Json-filen och inte data.


1. Välj **Visa arbetsbok**. Klicka sedan på knappen **Redigera** högst upp. Nu kan du redigera arbetsboken och anpassa den efter dina behov. Mer information om hur du anpassar arbetsboken finns i [skapa interaktiva rapporter med Azure Monitor-arbetsböcker](../azure-monitor/app/usage-workbooks.md).
![visa arbetsböcker](./media/tutorial-monitor-data/workbook-graph.png)
1. När du har gjort ändringarna kan du spara arbetsboken. 

1. Du kan också klona arbetsboken: Välj **Redigera** och sedan **Spara som**, se till att spara den med ett annat namn, under samma prenumeration och resursgrupp. Dessa arbetsböcker visas under fliken **Mina arbetsböcker.**


## <a name="create-new-workbook"></a>Skapa ny arbetsbok

1. Gå till **Arbetsböcker** och välj sedan **Lägg till arbetsbok** om du vill skapa en ny arbetsbok från grunden.
  ![gå till arbetsböcker](./media/tutorial-monitor-data/create-workbook.png)

1. Om du vill redigera arbetsboken markerar du **Redigera**och lägger sedan till text, frågor och parametrar efter behov. Mer information om hur du anpassar arbetsboken finns i [skapa interaktiva rapporter med Azure Monitor-arbetsböcker](../azure-monitor/app/usage-workbooks.md). 

1. När du skapar en fråga anger du att **datakällan** är inställd **på Loggar**, **resurstypen** är inställd **på Logganalys** och väljer sedan relevanta arbetsytor. 

1. När du har skapat arbetsboken sparar du arbetsboken och ser till att du sparar den under prenumerations- och resursgruppen för din Azure Sentinel-arbetsyta.

1. Om du vill att andra i organisationen ska använda arbetsboken väljer du **Delade rapporter**under **Spara** . Om du vill att arbetsboken bara ska vara tillgänglig för dig väljer du **Mina rapporter**.

1. Om du vill växla mellan arbetsböcker på arbetsytan kan](./media/tutorial-monitor-data/switch.png)du välja **Öppna** ![Växla arbetsböcker i den övre rutan i en arbetsbok. Växla mellan arbetsböcker i fönstret som öppnas till höger.

   ![Växla arbetsböcker](./media/tutorial-monitor-data/switch-workbooks.png)


## <a name="how-to-delete-workbooks"></a>Ta bort arbetsböcker

Du kan ta bort arbetsböcker som har skapats från en Azure Sentinel-mall. 

Om du vill ta bort en anpassad arbetsbok markerar du den sparade arbetsbok som du vill ta bort på sidan Arbetsböcker och väljer **Ta bort**. Då tas den sparade arbetsboken bort.

> [!NOTE]
> Då tas resursen bort och alla ändringar som du har gjort i mallen. Den ursprungliga mallen förblir tillgänglig.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du visar dina data i Azure Sentinel.

Mer information om hur du automatiserar dina svar på hot finns [i Konfigurera automatiska hotsvar i Azure Sentinel](tutorial-respond-threats-playbook.md).
