---
title: Skapa och dela instrumentpaneler med Azure portal | Microsoft Docs
description: Den här artikeln beskriver hur du skapar, anpassa, publicera och dela instrumentpaneler i Azure-portalen.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537423"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Skapa och dela instrumentpaneler i Azure portal

Instrumentpaneler gör det möjligt för dig att skapa vyn fokuserade och välorganiserad i Azure-portalen om dina molnresurser. Använda instrumentpaneler som en arbetsyta där du kan snabbt starta uppgifter för dagliga uppgifter och övervaka resurser.  Skapa anpassade instrumentpaneler baserat på projekt, uppgifter och användarroller, till exempel.  Azure-portalen ger en standardinstrumentpanel som utgångspunkt. Du kan redigera standardinstrumentpanelen, skapa och anpassa ytterligare instrumentpaneler och publicera och dela instrumentpaneler för att göra dem tillgängliga för andra användare. Den här artikeln beskriver hur du skapar en ny instrumentpanel, anpassa gränssnittet, och publicera och dela instrumentpaneler.

## <a name="create-a-new-dashboard"></a>Skapa en ny instrumentpanel

I det här exemplet vi skapa en ny, privat instrumentpanel och tilldela ett namn. Följ dessa steg för att komma igång:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Välj **instrumentpanelen** från den övre delen av den vänstra sidorutan. Din standardvy kan redan vara inställt på instrumentpanelen.
1. Välj **+ ny instrumentpanel**.

    ![Skärmbild av standardinstrumentpanelen](./media/azure-portal-dashboards/dashboard-new.png)

4. Den här åtgärden öppnar den **Panelgalleriet**, där du ska välja paneler och ett tomt rutnät där du ska placera panelerna.

    ![Skärmbild av panelgalleriet och tomt rutnät](./media/azure-portal-dashboards/dashboard-name.png)

5. Välj den **min instrumentpanel** text i instrumentpanelen etikett och ange ett namn som hjälper dig att identifiera anpassad instrumentpanel.
1. Välj **anpassningen är klar** i sidhuvudet för att avsluta redigeringsläget.

Tom instrumentpanelen visas nu i instrumentpanelsvyn. Välj i listrutan bredvid instrumentpanelens namn att se instrumentpaneler som är tillgängliga för dig – listan kan innehålla instrumentpaneler som andra användare har skapat och delat.

## <a name="edit-a-dashboard"></a>Redigera en instrumentpanel

Nu ska vi redigera instrumentpanelen som du vill lägga till, ändra storlek på och ordna paneler som representerar dina Azure-resurser.

### <a name="add-tiles"></a>Lägg till paneler

Följ dessa steg för att lägga till paneler på en instrumentpanel:
1. Välj ![redigeringsikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **redigera** från sidhuvudet.

    ![Skärmbild av instrumentpanelen markering redigera](./media/azure-portal-dashboards/dashboard-edit.png)

2. Bläddra i **Panelgalleriet** eller Använd sökfältet för att hitta den panel som du vill.
1. Välj **Lägg till** att automatiskt lägga till panelen på instrumentpanelen med en standardstorlek och plats. Eller dra panelen till rutnätet och placera den där du vill.

Många resource sidorna (även kallat ”blad”) innehåller en kartnålsikon i kommandofältet. Om du väljer ikonen för är en panel som representerar källsidan fäst på instrumentpanelen som för närvarande är aktiv. Den här metoden är ett annat sätt att lägga till paneler på instrumentpanelen.

![Skärmbild av sidan kommandofältet med Fäst-ikonen](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Om du arbetar med mer än en organisation kan lägga till den **organisationsidentitet** panel på instrumentpanelen att tydliggöra vilken organisationens resurser som tillhör.
>
>
### <a name="resize-or-rearrange-tiles"></a>Ändra storlek på eller omarrangera paneler
Ändra storlek på en panel eller ordna om paneler på en instrumentpanel, Följ dessa steg:

1. Välj ![redigeringsikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **redigera** från sidhuvudet.
1. Välj snabbmenyn i det övre högra hörnet av en panel. Välj en storlek på panelen. Paneler som har stöd för alla storlekar även innehålla en ”referens” i det nedre högra hörnet som låter dig Dra panelen till önskad storlek.

   ![Skärmbild av instrumentpanelen med storleken panelmeny öppna](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Välj en panel och drar den till en ny plats i rutnätet ordna din instrumentpanel.

### <a name="additional-tile-configuration"></a>Ytterligare sida vid sida-konfigurationen

Vissa paneler kan behöva ytterligare konfiguration för att visa den information som du vill. Till exempel den **måttdiagram** panel måste ställas in för att visa ett mått från **Azure Monitor**. Du kan också anpassa paneldata om du vill åsidosätta instrumentpanelens tid standardinställningarna.

Valfri panel som behöver ställas in visar en **konfigurera panel** banderoll tills du anpassa panelen. Välj den banderollen och göra de nödvändiga inställningarna.

![Skärmbild av panelen som kräver konfiguration](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> En markdown-panel kan du visa anpassade, statiskt innehåll på din instrumentpanel. Detta kan vara grundläggande anvisningar, en bild, en uppsättning hyperlänkar eller även kontaktinformation. Läs mer om hur du använder en markdown-panel [använder en anpassad markdown-panel](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Anpassa paneldata

Data på instrumentpanelen visar automatiskt aktiviteten för de senaste 24 timmarna. Följ dessa steg om du vill visa ett annat tidsintervall för just den här panelen:

1. Välj den ![filterikonen](./media/azure-portal-dashboards/dashboard-filter.png) filter-ikonen från det övre vänstra hörnet av panelen eller välj **anpassa paneldata** på snabbmenyn.

   ![Skärmbild av panelen snabbmenyn](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Markera kryssrutan för att **Åsidosätt instrumentpanelens tidsinställningar på ikonnivå**.

   ![Skärmbild av dialogrutan Konfigurera panel tidsinställningar](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Välj tidsintervallet som ska visas för den här panelen. Du kan välja bland de senaste 30 minuterna till de senaste 30 dagarna eller definiera ett anpassat intervall.
1. Välj tidskornighet ska visas. Du kan visa var som helst från en minut steg till en månad.
1. Välj **Använd**.

## <a name="delete-a-tile"></a>Ta bort en panel

Följ dessa steg om du vill ta bort en panel från en instrumentpanel:

* Välj snabbmenyn i det övre högra hörnet av panelen och välj sedan **ta bort från instrumentpanel**. Eller:
* Välj ![redigeringsikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **redigera** ange anpassningsläge. Håll muspekaren i det övre högra hörnet av panelen och välj sedan den ![borttagningsikon](./media/azure-portal-dashboards/dashboard-delete-icon.png) borttagningsikon om du vill ta bort panelen på instrumentpanelen.

   ![Skärmbild som visar hur du tar bort en panel från instrumentpanelen](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klona en instrumentpanel

Om du vill använda en befintlig instrumentpanel som en mall för en ny instrumentpanel, Följ dessa steg:

1. Kontrollera att instrumentpanelsvyn för visar instrumentpanelen som du vill kopiera.
1. I sidhuvudet, Välj ![klona ikonen](./media/azure-portal-dashboards/dashboard-clone.png) **klona**.
1. En kopia av instrumentpanelen med namnet ”klona av *instrumentpanelens namn*” öppnats i redigeringsläge. Använd de föregående stegen i den här artikeln för att byta namn på och anpassa instrumentpanelen.

## <a name="publish-and-share-a-dashboard"></a>Publicera och dela en instrumentpanel

När du skapar en instrumentpanel är privata som standard, vilket innebär att du är den enda som kan se den. Om du vill göra instrumentpaneler som är tillgänglig för andra, kan du dela dem med andra användare. Du måste först publicera instrumentpanel som en Azure-resurs. Följ dessa steg för att publicera och dela en anpassad instrumentpanel:

1. Välj ![delningsikon](./media/azure-portal-dashboards/dashboard-share-icon.png) **dela** från sidhuvudet. Den **delning och åtkomstkontroll** formulär visas.
1. Kontrollera att rätt instrumentpanelens namn visas.
1. Välj en **prenumerationsnamn**. Användare med åtkomst till prenumerationen kan använda den delade instrumentpanelen. Åtkomst till resurser som representeras av enskilda paneler bestäms av rollbaserad åtkomstkontroll i Azure.
1. Markera kryssrutan för att publicera den här instrumentpanelen till resursgruppen ”instrumentpaneler” för den valda prenumerationen. Eller, avmarkera kryssrutan och välja att publicera till en befintlig resursgrupp i stället.
1. Välj en plats för instrumentpanelen för resursen. Vi rekommenderar att du leta upp instrumentpanelen med andra resurser. Obs: Om du väljer från befintliga resursgrupper, instrumentpanelen finns automatiskt med resursgruppen.
1. Välj **Publicera**.

   ![Skärmbild av instrumentpanelen publiceringsdialogrutan](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Ange åtkomstkontroll på en delad instrumentpanel

När instrumentpanelen har publicerats kan du hantera vem som har åtkomst till instrumentpanelen genom att följa dessa steg:

1. I den **delning och åtkomstkontroll** väljer **hantera användare**.

   ![Skärmbild av instrumentpanelen delning och styra dialogrutan](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. Den **Access Control** öppnas. På den här sidan kan du granska åtkomstnivån för en person eller lägga till en ny rolltilldelning. När du lägger till en rolltilldelning beviljar du behörigheter till instrumentpanelen.

> [!NOTE]
> Paneler är representativt vyer av resurser i din organisation. Åtkomst till resurser som hanteras via rollbaserad åtkomstkontroll tilldelning och behörigheter ärvs från prenumerationen till resursen. Ge åtkomst till en instrumentpanel tilldela inte automatiskt behörigheter till resurserna som visas på instrumentpanelen. Läs mer om behörigheter till delade instrumentpaneler och rollbaserad åtkomstkontroll för resurser, [dela instrumentpaneler med rollbaserad åtkomstkontroll](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Öppna en delad instrumentpanel

Följ dessa steg för att hitta och öppna en delad instrumentpanel:

1. Välj i listrutan bredvid instrumentpanelens namn.
1. Välj ett värde från listan över instrumentpaneler eller **Bläddra bland alla instrumentpaneler** om du vill öppna instrumentpanelen visas inte i listan.

   ![Skärmbild av instrumentpanelen valmenyn](./media/azure-portal-dashboards/dashboard-browse.png)

3. I den **typ** väljer **delade instrumentpaneler**.
1. Välj en eller flera prenumerationer. Du kan också ange text för att filtrera instrumentpaneler efter namn.
1. Välj en instrumentpanel från listan över delade instrumentpaneler.

## <a name="delete-a-dashboard"></a>Ta bort en instrumentpanel

Följ dessa steg om du vill ta bort en privat eller delade instrumentpanel permanent:

1. Välj den instrumentpanel som du vill ta bort från listrutan bredvid instrumentpanelens namn.
1. Välj ![borttagningsikon](./media/azure-portal-dashboards/dashboard-delete-icon.png) **ta bort** från sidhuvudet.
1. För en privat instrumentpanel, väljer **OK** i bekräftelsedialogrutan för att ta bort instrumentpanelen. För en delad instrumentpanel i bekräftelsedialogen, väljer du kryssrutan för att bekräfta att den publicerade instrumentpanelen kommer inte längre att ses av andra. Välj **OK**.

   ![Skärmbild av Bekräfta borttagning](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Nästa steg

* [Dela instrumentpaneler med rollbaserad åtkomstkontroll](azure-portal-dashboard-share-access.md)
* [Skapa Azure-instrumentpaneler programmässigt](azure-portal-dashboards-create-programmatically.md)
