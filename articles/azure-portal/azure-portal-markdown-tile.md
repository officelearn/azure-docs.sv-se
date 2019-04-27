---
title: Använda en anpassad markdown-panel på Azure-instrumentpaneler
description: Lär dig hur du lägger till en markdown-panel i en Azure-instrumentpanelen att visa statiskt innehåll
services: azure-portal
keywords: ''
author: kfollis
ms.author: v-biyu
origin.date: 01/25/2019
ms.date: 03/04/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ec8cbddda4137656a53fd4968c451cd413959274
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551606"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Använd en markdown-panel på Azure-instrumentpaneler för att visa anpassat innehåll

Du kan lägga till en markdown-panel till din Azure-instrumentpaneler anpassade, statiskt innehåll. Du kan till exempel visa grundläggande anvisningar, en bild eller en uppsättning hyperlänkar med en markdown-panel.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Lägg till en markdown-panel på instrumentpanelen

1. Välj **instrumentpanelen** på Azure portal sidopanelen. Om du har skapat några anpassade instrumentpaneler i instrumentpanelsvyn kan använda listrutan för att välja instrumentpanelen där anpassade markdown-panel ska visas. Välj redigeringsikonen för att öppna den **Panelgalleriet**.

   ![Skärmbild som visar redigera instrumentpanelsvy](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. I den **Panelgalleriet**, leta upp panelen kallas **Markdown** och klicka på **Lägg till**. Panelen har lagts till i instrumentpanelen och **redigera Markdown** öppnas fönstret.

3. Redigera den **rubrik**, **underrubrik**, och **innehåll** fält att anpassa panelen. I det här exemplet, har markdown-panel redigerats om du vill visa information för supportavdelningen för hjälp.

   ![Skärmbild som visar redigeringsvyn för markdown-panel](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Välj **klar** att stänga den **redigera Markdown** fönstret. Innehållet visas på panelen Markdown, som sedan kan ändras genom att dra i det nedre högra hörnet.

   ![Skärmbild som visar anpassade markdown-panel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Markdown innehåll funktioner och begränsningar

Du kan använda valfri kombination av oformaterad text, syntax för Markdown och HTML-innehåll i markdown-panel. Azure-portalen använder ett bibliotek för öppen källkod som heter _markerats_ att omvandla ditt innehåll till HTML som visas på panelen. HTML som produceras av _markerats_ bearbetas före av portalen innan den återges. Det här steget kan du kontrollera att anpassningen inte kommer att påverka säkerhets- eller layouten för portalen. Under den förbearbetning tas någon del av HTML som utgör ett potentiellt hot bort. Följande typer av innehåll tillåts inte av portalen:

* JavaScript – `<script>` taggar och infogade JavaScript-utvärderingar tas bort.
* IFrames - `<iframe>` taggar kommer att tas bort.
* Style - `<style>` taggar kommer att tas bort. Infogad attribut på HTML-element stöds inte officiellt. Du kanske att vissa infogad stil element fungerar för dig, men om de störa layouten för portalen, kan de slutar fungera när som helst. Markdown-panel är avsedd för basic, statiskt innehåll som använder standardformat för portalen.

## <a name="next-steps"></a>Nästa steg

* Om du vill skapa en anpassad instrumentpanel [skapa och dela instrumentpaneler i Azure portal](../azure-portal/azure-portal-dashboards.md)
