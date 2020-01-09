---
title: Använd en anpassad markdown-panel på Azure-instrumentpaneler
description: Lär dig hur du lägger till en markdown-panel på en Azure-instrumentpanel för att Visa statiskt innehåll
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3d2e6d2d0bde76a35a18373fabf64ce36c6c320e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640150"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Använd en markdown-panel på Azure-instrumentpaneler för att visa anpassat innehåll

Du kan lägga till en markdown-panel till dina Azure-instrumentpaneler för att visa anpassat, statiskt innehåll. Du kan till exempel Visa Basic-instruktioner, en bild eller en uppsättning hyperlänkar med en markdown-panel.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Lägg till en markdown-panel på din instrument panel

1. Välj **instrument panel** från Azure Portal sid panelen. Om du har skapat anpassade instrument paneler, i vyn instrument panel, använder du List rutan för att välja den instrument panel där den anpassade markdown-panelen ska visas. Välj redigerings ikonen för att öppna **panel galleriet**.

   ![Skärm bild som visar Redigeraren för instrument panelen](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Leta upp panelen med namnet **markdown** i **panel galleriet**och klicka på **Lägg till**. Panelen läggs till i instrument panelen och fönstret **Redigera markdown** öppnas.

1. Redigera fälten **rubrik**, **under rubrik**och **innehåll** för att anpassa panelen. I det exempel som visas här har panelen markdown redigerats för att Visa anpassad information om supportavdelningen.

   ![Skärm bild som visar markdown panels redigerings vy](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Välj **Done** för att stänga fönstret **Redigera markdown** . Ditt innehåll visas på markdown-panelen, som sedan kan ändra storlek genom att dra i handtaget i det nedre högra hörnet.

   ![Skärm bild som visar en anpassad markdown-panel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funktioner och begränsningar för markdown-innehåll

Du kan använda valfri kombination av oformaterad text, markdown-syntax och HTML-innehåll på markdown-panelen. I Azure Portal används ett bibliotek med öppen källkod som heter _markerad_ för att transformera ditt innehåll till HTML som visas på panelen. Den HTML-kod som tillverkas av har _marker ATS_ i förväg av portalen innan den renderas. Det här steget ser till att anpassningen inte påverkar portalens säkerhet eller layout. Under den för bearbetningen tas alla delar av HTML-koden som utgör ett potentiellt hot bort. Följande typer av innehåll tillåts inte av portalen:

* Java Script – `<script>` Taggar och infogade JavaScript-utvärderingar tas bort.
* iframes – `<iframe>` taggar tas bort.
* Style-`<style>` taggar tas bort. Inline Style-attribut för HTML-element stöds inte officiellt. Det kan hända att vissa infogade format element fungerar för dig, men om de stör portalens layout kan de sluta fungera när som helst. Markdown-panelen är avsedd för grundläggande, statiskt innehåll som använder portalens standardformat.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar en anpassad instrument panel finns [i skapa och dela instrument paneler i Azure Portal](../azure-portal/azure-portal-dashboards.md)
