---
title: Skapa och dela instrumentpaneler med Azure portal | Microsoft Docs
description: Den här artikeln förklarar hur du skapar och redigerar instrumentpaneler i Azure-portalen.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: doubeby
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: kfollis
ms.openlocfilehash: 4a2dee4ad90fb269268ec7d3e86def58f2444e3a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884736"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Skapa och dela instrumentpaneler i Azure portal
Du kan skapa flera instrumentpaneler och dela dem med andra som har åtkomst till dina Azure-prenumerationer.  Den här artikeln går igenom grunderna i att skapa, redigera, publicera och hantera åtkomst till instrumentpaneler.

## <a name="create-a-dashboard"></a>Skapa en instrumentpanel
Om du vill skapa en instrumentpanel väljer du knappen **Ny instrumentpanel** bredvid namnet på den aktuella instrumentpanelen.  

![Skapa instrumentpanel](./media/azure-portal-dashboards/new-dashboard.png)

Detta skapar en ny, tom, privat instrumentpanel och du hamnar i anpassningsläge. Där kan du ge instrumentpanelen ett namn och lägga till eller ordna om paneler.  I detta läge har komprimerbart panelgalleriet framför den vänstra navigeringsmenyn.  Panelgalleriet kan du söka efter paneler för dina Azure-resurser på olika sätt: du kan söka genom [resursgrupp](../azure-resource-manager/resource-group-overview.md#resource-groups), genom resurstyp, av [taggen](../azure-resource-manager/resource-group-using-tags.md), eller genom att söka efter din resurs med namnet.  

![Anpassa instrumentpanelen](./media/azure-portal-dashboards/customize-dashboard.png)

Lägga till paneler genom att dra och släppa dem i instrumentpanelen för surface var du vill.

Det finns en ny kategori som heter **Allmänt** för paneler som inte är associerade med en viss resurs.  I det här exemplet Fäst vi Markdown-panel.  Du kan använda den här panelen för att lägga till eget innehåll på din instrumentpanel.  Panelen stöder oformaterad text, [markdownsyntax](https://daringfireball.net/projects/markdown/syntax), och en begränsad uppsättning HTML.  (För säkerhet, du kan inte göra saker som att mata in `<script>` taggar eller använda vissa stil-elementet i CSS som kan störa portalen.) 

![Lägg till markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Redigera en instrumentpanel
Du kan fästa paneler från panelgalleriet eller sida vid sida-representation av bladen när du har skapat din instrumentpanel. Nu ska vi fästa en representation av resursgrupp. Du kan antingen PIN-kod när du bläddrar efter objektet, eller från bladet för resursgruppen. Båda metoderna resultera i att fästa en panel-representation av resursgruppen.

![Fäst på instrumentpanel](./media/azure-portal-dashboards/pin-to-dashboard.png)

Efter att fästa objektet, visas den på instrumentpanelen.

![Visa instrumentpanel](./media/azure-portal-dashboards/view-dashboard.png)

Nu när vi har en Markdown-panel och en resursgrupp fäst på instrumentpanelen, kan vi ändra storlek på och ordna om paneler i en lämplig layout.

Du kan se alla sammanhangsberoende kommandon för panelen genom att hovra och markera ”...” eller att högerklicka på en panel. Som standard finns det två objekt:

1. **Ta bort från instrumentpanelen** – tar bort panelen på instrumentpanelen
2. **Anpassa** – anger anpassningsläge

![Anpassa panel](./media/azure-portal-dashboards/customize-tile.png)

Genom att välja Anpassa kan du ändra storlek på och ordna om paneler. Om du vill ändra storlek på en panel, Välj den nya storleken från snabbmenyn som visas i följande bild.

![Ändra storlek på panelen](./media/azure-portal-dashboards/resize-tile.png)

Eller, om panelen har stöd för alla storlekar, kan du dra det nedre högra hörnet till önskad storlek.

![Ändra storlek på panelen](./media/azure-portal-dashboards/resize-corner.png)

När du ändrar storlek på paneler, visa instrumentpanelen.

![Visa panel](./media/azure-portal-dashboards/view-tile.png)

När du har angett en instrumentpanel, markerar du den **anpassningen är klar** avsluta anpassningsläge eller högerklicka och välj **anpassningen är klar** på snabbmenyn.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Publicera en instrumentpanel och hantera åtkomstkontroll
Instrumentpaneler är privata som standard när de skapas, vilket innebär att du är den enda som kan se dem.  Använd knappen **Dela** som visas tillsammans med andra kommandon i instrumentpanelen för att visa den för andra.

![dela instrumentpanelen](./media/azure-portal-dashboards/share-dashboard.png)

Du uppmanas att välja en prenumeration och resursgrupp som din instrumentpanel ska publiceras till. Om du vill integrera fullständigt instrumentpaneler i ekosystemet, har vi implementerat delade instrumentpaneler som Azure-resurser (så att du inte kan dela genom att skriva en e-postadress).  Åtkomst till den information som visas i de flesta paneler på portalen regleras av [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md). Ur en access control skiljer delade instrumentpaneler sig inte från en virtuell dator eller ett lagringskonto.  

Anta att du har en Azure-prenumeration och gruppmedlemmarna har tilldelats rollerna för **ägare**, **deltagare**, eller **läsare** för prenumerationen.  Användare som är ägare eller deltagare kan visa, visa, skapa, ändra eller ta bort instrumentpaneler i den prenumerationen.  Användare som är läsare kan inte kan listan och visa instrumentpaneler, men ändra eller ta bort dem.  Användare med läsåtkomst kan göra lokala ändringar i en delad instrumentpanel, men det går inte att publicera ändringarna på servern.  De kan dock göra en privat kopia av instrumentpanelen för eget bruk.  Som alltid kan genomdriva enskilda paneler på instrumentpanelen för sina egna regler för åtkomstkontroll utifrån de resurser som de motsvarar.  

För att underlätta för dig guidar portalen dig genom ett mönster där du placerar dina instrumentpaneler i en resursgrupp som kallas **instrumentpaneler**.  

![Publicera instrumentpanel](./media/azure-portal-dashboards/publish-dashboard.png)

Du kan också välja att publicera en instrumentpanel till en viss resursgrupp.  Åtkomstkontroll för instrumentpanelen matchar åtkomstkontroll för resursgruppen.  Användare som kan hantera resurser i resursgruppen har också åtkomst till instrumentpaneler.

![publicera instrumentpanelen till resursgruppen](./media/azure-portal-dashboards/publish-to-resource-group.png)

När instrumentpanelen har publicerats kan den **åtkomst + dela** Kontrollpanelen kommer att uppdatera och visa information om publicerade instrumentpanelen, inklusive en länk för att hantera användarnas åtkomst till instrumentpanelen.  Den här länken öppnar bladet standard rollbaserad åtkomstkontroll används för att hantera åtkomsten för alla Azure-resurser.  Du kan alltid återgå till den här vyn genom att välja **resursen**.

![Hantera åtkomstkontroll](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>Nästa steg
* För att hantera resurser, se [hantera Azure-resurser via portalen](../azure-resource-manager/resource-group-portal.md).
* För att distribuera resurser, se [distribuera resurser med Resource Manager-mallar och Azure-portalen](../azure-resource-manager/resource-group-template-deploy-portal.md).

