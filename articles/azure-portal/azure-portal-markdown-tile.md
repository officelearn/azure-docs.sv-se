---
title: Använda en anpassad markdown-panel på Azure-instrumentpaneler
description: Lär dig hur du lägger till en markdown-panel på en Azure-instrumentpanel för att visa statiskt innehåll
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/08/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 74102423461a56bb6fc19c2eb9874f96a76e34e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310721"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Använda en markdown-panel på Azure-instrumentpaneler för att visa anpassat innehåll

Du kan lägga till en markdown-panel på dina Azure-instrumentpaneler för att visa anpassat, statiskt innehåll. Du kan till exempel visa grundläggande instruktioner, en bild eller en uppsättning hyperlänkar på en markdown-panel.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Lägga till en markdown-panel på instrumentpanelen

1. Välj **Instrumentpanel** i sidofältet i Azure-portalen.

   ![Skärmbild som visar portal sidofältet](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Om du har skapat några anpassade instrumentpaneler använder du listrutan i instrumentpanelsvyn för att välja instrumentpanelen där den anpassade markdown-panelen ska visas. Välj redigeringsikonen för att öppna **panelgalleriet**.

   ![Skärmbild som visar redigeringsvyn för instrumentpanelen](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Leta reda på den panel som kallas **Markdown** i **panelgalleriet**och välj **Lägg till**. Panelen läggs till på instrumentpanelen och fönstret **Redigera markeringsfönster** öppnas.

1. Ange värden för **Rubrik** och **Underrubrik**, som visas på panelen när du har flyttat till ett annat fält.

   ![Skärmbild som visar resultatet av att ange titel och underrubrik](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Välj ett av alternativen för att inkludera markeringsinnehåll: **Infoga redigering** eller Infoga innehåll **med URL**.

   - Välj **Infogad redigering** om du vill ange markdown direkt.

      ![Skärmbild som visar infogat innehåll](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Välj **Infoga innehåll med URL** om du vill använda befintligt markdown-innehåll som finns online.

      ![Skärmbild som visar att ange URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > För ökad säkerhet kan du skapa en markdown-fil och lagra den i en [Azure storage account-blob där kryptering är aktiverad](../storage/common/storage-service-encryption.md)och sedan peka på filen med hjälp av URL-alternativet. Markdown-innehållet krypteras via lagringskontots krypteringsalternativ. Endast användare med behörighet till filen kan se markdown-innehållet på instrumentpanelen.

1. Välj **Klar** om du vill stänga fönstret **Redigera markeringsmarkering.** Innehållet visas på Markdown-panelen, som du kan ändra storlek på genom att dra handtaget i det nedre högra hörnet.

   ![Skärmbild som visar anpassad nedmärkningspanel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funktioner och begränsningar för Markdown-innehåll

Du kan använda valfri kombination av oformaterad text, markans syntax och HTML-innehåll på markdown-panelen. Azure-portalen använder ett bibliotek med öppen källkod som kallas _markerat_ för att omvandla ditt innehåll till HTML som visas på panelen. HTML-koden som produceras av _markerad_ bearbetas i förväg av portalen innan den återges. Det här steget hjälper dig att se till att anpassningen inte påverkar portalens säkerhet eller layout. Under förbehandlingen tas alla delar av HTML-koden som utgör ett potentiellt hot bort. Följande typer av innehåll tillåts inte av portalen:

* JavaScript `<script>` – taggar och inline JavaScript-utvärderingar tas bort.
* iframes `<iframe>` - taggar kommer att tas bort.
* Stil `<style>` - taggar kommer att tas bort. Infogade formatattribut för HTML-element stöds inte officiellt. Du kanske upptäcker att vissa infogade formatelement fungerar för dig, men om de stör portalens layout kan de sluta fungera när som helst. Markdown-panelen är avsedd för grundläggande, statiskt innehåll som använder portalens standardformat.

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar en anpassad instrumentpanel finns [i Skapa och dela instrumentpaneler i Azure-portalen](../azure-portal/azure-portal-dashboards.md)
