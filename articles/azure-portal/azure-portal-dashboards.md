---
title: Skapa och dela instrumentpaneler med Azure portal | Microsoft Docs
description: "Den här artikeln beskriver hur du skapar och redigerar instrumentpaneler i Azure-portalen."
services: azure-portal
documentationcenter: 
author: sewatson
manager: timlt
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: multiple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: sewatson
ms.openlocfilehash: 5429e68723448ff5db6ef0ed8da1b927e97e6dd9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Skapa och dela instrumentpaneler i Azure-portalen
Du kan skapa flera instrumentpaneler och dela dem med andra användare har åtkomst till dina Azure-prenumerationer.  Den här artikeln går igenom grunderna i att skapa, redigera, publicera och hantera åtkomst till instrumentpaneler.

## <a name="create-a-dashboard"></a>Skapa en instrumentpanel
Om du vill skapa en instrumentpanel, Välj den **ny instrumentpanel** knappen bredvid namnet på den aktuella instrumentpanelen.  

![Skapa instrumentpanel](./media/azure-portal-dashboards/new-dashboard.png)

Den här åtgärden skapar en ny, tom, privata instrumentpanel och placerar du i anpassning läge där du kan kalla instrumentpanelen och lägga till eller ordna om paneler.  I detta läge tar döljas panelen galleriet över den vänstra navigeringsmenyn.  Galleriet sida vid sida kan du hitta paneler för Azure-resurser på olika sätt: du kan bläddra genom [resursgruppen](../azure-resource-manager/resource-group-overview.md#resource-groups), av resurstyp, av [taggen](../azure-resource-manager/resource-group-using-tags.md), eller genom att söka efter resurs efter namn.  

![Anpassa instrumentpanel](./media/azure-portal-dashboards/customize-dashboard.png)

Lägg till paneler genom att dra och släppa dem i instrumentpanelen ytan var du vill.

Det finns en ny kategori som heter **allmänna** för brickor som inte är associerade med en viss resurs.  I det här exemplet Fäst vi panelen Markdown.  Du kan använda den här panelen för att lägga till anpassade innehåll på instrumentpanelen.  Panelen stöder oformaterad text [markdownsyntax](https://daringfireball.net/projects/markdown/syntax), och en begränsad uppsättning HTML.  (För säkerhet, kan du göra saker som mata in `<script>` taggar eller använda vissa formatmalls-element i CSS som kan störa portalen.) 

![Lägg till markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Redigera en instrumentpanel
Du kan fästa paneler i galleriet panelen eller sida vid sida-representation av blad när du har skapat din instrumentpanel. Vi Fäst representation av våra resursgruppen. Du kan antingen PIN-kod när du bläddrar objektet, eller från bladet för resursgruppen. Båda metoderna resultera i att fästa panelen representation av resursgruppen.

![Fäst på instrumentpanelen](./media/azure-portal-dashboards/pin-to-dashboard.png)

När filen har fästs objektet, visas det på instrumentpanelen.

![visa instrumentpanelen](./media/azure-portal-dashboards/view-dashboard.png)

Nu när vi har en Markdown-panelen och en resursgrupp fäst på instrumentpanelen kan vi ändra storlek på och flytta panelerna i en lämplig layout.

Du kan se alla kontextuella kommandon för brickan av hovra och välja ”...” eller högerklicka på en panel. Det finns två objekt som standard:

1. **Ta bort från instrumentpanelen** – tar bort panelen från instrumentpanelen
2. **Anpassa** – anger anpassa läge

![Anpassa sida vid sida](./media/azure-portal-dashboards/customize-tile.png)

Genom att välja Anpassa, kan du ändra storlek på och ordna om paneler. Om du vill ändra storlek på en panel, Välj den nya storleken i snabbmenyn som visas i följande bild.

![Ändra storlek på panelen](./media/azure-portal-dashboards/resize-tile.png)

Eller, om panelen stöder alla storlekar, kan du dra det nedre högra hörnet till önskad storlek.

![Ändra storlek på panelen](./media/azure-portal-dashboards/resize-corner.png)

När du ändrar storlek på panelerna, visa instrumentpanelen.

![vyn sida vid sida](./media/azure-portal-dashboards/view-tile.png)

När du är klar med att anpassa en instrumentpanel, markerar du bara den **klar anpassa** avsluta anpassa läge eller högerklicka och välj **klar anpassa** på snabbmenyn.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publicera en instrumentpanel och hantera åtkomstkontrollen
Den är privat som standard, vilket innebär att du är den enda som kan se den när du skapar en instrumentpanel.  Använd för att göra den synlig för andra i **resursen** som visas tillsammans med andra kommandon för instrumentpanelen.

![dela instrumentpanelen](./media/azure-portal-dashboards/share-dashboard.png)

Du uppmanas att välja en prenumeration och resursgrupp för din instrumentpanel som ska publiceras. Om du vill integrera sömlöst instrumentpaneler i ekosystemet, har vi implementerat delade instrumentpaneler som Azure-resurser (så att du inte kan dela genom att skriva en e-postadress).  Åtkomst till den information som visas i de flesta paneler i portalen regleras av [Azure rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md). Ur ett access control skiljer delade instrumentpaneler sig inte från en virtuell dator eller ett lagringskonto.  

Anta att du har en Azure-prenumeration och gruppmedlemmarna har tilldelats rollerna i **ägare**, **deltagare**, eller **reader** för prenumerationen.  Användare som är ägare eller deltagare kan visa, visa, skapa, ändra eller ta bort instrumentpaneler i den prenumerationen.  Användare som är läsare kan inte kan listan och visa instrumentpaneler, men ändra eller ta bort dem.  Användare med reader åtkomst kan göra lokala redigeringar i en delad instrumentpanel, men det går inte att publicera ändringarna på servern.  De kan dock göra en privat kopia av instrumentpanelen för eget bruk.  Enskilda paneler på instrumentpanelen framtvinga som alltid sina egna regler för åtkomstkontroll baserat på de resurser som de motsvarar.  

För enkelhetens skull portalen har publicering upplevelse guider du mot ett mönster som placerar du instrumentpaneler i en resursgrupp kallas **instrumentpaneler**.  

![publicera instrumentpanelen](./media/azure-portal-dashboards/publish-dashboard.png)

Du kan också välja att publicera en instrumentpanel till en viss resursgrupp.  Åtkomstkontroll för instrumentpanelen matchar åtkomstkontroll för resursgruppen.  Användare som kan hantera resurser i resursgruppen har också åtkomst till instrumentpanelerna.

![publicera instrumentpanelen till resursgruppen](./media/azure-portal-dashboards/publish-to-resource-group.png)

När instrumentpanelen har publicerats i **delning + åtkomst** kontrollen ska uppdatera och visa information om publicerade instrumentpanelen, inklusive en länk för att hantera användarnas åtkomst till instrumentpanelen.  Den här länken öppnar bladet standard rollbaserad åtkomstkontroll används för att hantera åtkomsten för Azure-resurser.  Du alltid kan komma tillbaka till den här vyn genom att välja **resursen**.

![Hantera åtkomstkontroll](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Nästa steg
* För att hantera resurser, se [hantera Azure-resurser via portalen](../azure-resource-manager/resource-group-portal.md).
* Om du vill distribuera resurser, se [distribuera resurser med Resource Manager-mallar och Azure-portalen](../azure-resource-manager/resource-group-template-deploy-portal.md).

