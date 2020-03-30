---
title: Skapa och dela instrumentpaneler i Azure-portalen
description: I den här artikeln beskrivs hur du skapar, anpassar, publicerar och delar instrumentpaneler i Azure-portalen.
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
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4c01321662b302103cdedfb5b78dadf89860fb8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132075"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Skapa och dela instrumentpaneler i Azure-portalen

Instrumentpaneler är en fokuserad och organiserad vy över dina molnresurser i Azure-portalen. Använd instrumentpanelerna som en arbetsyta där du snabbt kan starta uppgifter för dagliga åtgärder och övervaka resurser. Du kan exempelvis skapa anpassade instrumentpaneler som baseras på projekt, uppgifter eller användarroller.

Azure-portalen tillhandahåller en standardinstrumentpanel som utgångspunkt. Du kan redigera standardinstrumentpanelen. Skapa och anpassa ytterligare instrumentpaneler och publicera och dela instrumentpaneler för att göra dem tillgängliga för andra användare. I den här artikeln beskrivs hur du skapar en ny instrumentpanel, anpassar gränssnittet och publicerar och delar instrumentpaneler.

## <a name="create-a-new-dashboard"></a>Skapa en ny instrumentpanel

I det här exemplet skapar vi en ny, privat instrumentpanel och tilldelar ett namn. Kom igång genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Instrumentpanel**på Azure-portalmenyn . Standardvyn kanske redan är inställd på instrumentpanelen.

    ![Öppna instrumentpanelen](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Välj **Ny instrumentpanel**.

    ![Skärmbild av den nya instrumentpanelen](./media/azure-portal-dashboards/create-new-dashboard.png)

    Den här åtgärden öppnar **panelgalleriet**, där du väljer paneler och ett tomt rutnät där du ordnar panelerna.

    ![Skärmbild av panelgalleri och tomt rutnät](./media/azure-portal-dashboards/dashboard-name.png)

1. Välj texten **Min instrumentpanel** i instrumentpanelsetiketten och ange ett namn som hjälper dig att enkelt identifiera den anpassade instrumentpanelen.

1. Välj **Klar anpassa** i sidhuvudet för att avsluta redigeringsläge.

Instrumentpanelsvyn visar nu den nya instrumentpanelen. Markera pilen bredvid instrumentpanelsnamnet om du vill visa instrumentpaneler som är tillgängliga för dig. Listan kan innehålla instrumentpaneler som andra användare har skapat och delat.

## <a name="edit-a-dashboard"></a>Redigera en instrumentpanel

Nu ska vi redigera instrumentpanelen för att lägga till, ändra storlek på och ordna paneler som representerar dina Azure-resurser.

### <a name="add-tiles-from-the-dashboard"></a>Lägga till paneler från instrumentpanelen

Så här lägger du till paneler på en instrumentpanel:

1. Välj ![redigeringsikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Redigera** i sidhuvudet.

    ![Skärmbild av redigering av instrumentpanelsmarkering](./media/azure-portal-dashboards/dashboard-edit.png)

1. Bläddra i **panelgalleriet** eller använd sökfältet för att hitta den panel du vill ha.

1. Välj **Lägg till** om du vill lägga till panelen på instrumentpanelen med standardstorlek och plats. Du kan också dra panelen till rutnätet och placera den där du vill.

> [!TIP]
> Om du arbetar med mer än en organisation lägger du till panelen **Organisationsidentitet** på instrumentpanelen för att tydligt visa vilken organisation resurserna tillhör.

### <a name="add-tiles-from-a-resource-page"></a>Lägga till paneler från en resurssida

Det finns ett alternativt sätt att lägga till paneler på instrumentpanelen. Många resurssidor innehåller en pushpin-ikon i kommandofältet. Om du väljer ikonen fästs en panel som representerar källsidan på instrumentpanelen som för närvarande är aktiv. 

![Skärmbild av sidkommandofältet med pin-ikonen](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Ändra storlek på eller ordna om paneler

Så här ändrar du storleken på en panel eller ordnar om panelerna på en instrumentpanel:

1. Välj ![redigeringsikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Redigera** i sidhuvudet.

1. Markera snabbmenyn i det övre högra hörnet på en panel. Välj sedan en panelstorlek. Paneler som stöder alla storlekar innehåller också ett "handtag" i det nedre högra hörnet som låter dig dra panelen till den storlek du vill ha.

    ![Skärmbild av instrumentpanelen med menyn panelstorlek öppen](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Markera en panel och dra den till en ny plats i rutnätet för att ordna instrumentpanelen.

### <a name="additional-tile-configuration"></a>Ytterligare panelkonfiguration

Vissa paneler kan kräva mer konfiguration för att visa den information du vill ha. Diagrampanelen **Mått** måste till exempel ställas in för att visa ett mått från **Azure Monitor**. Du kan också anpassa paneldata för att åsidosätta instrumentpanelens standardtidsinställningar.

Alla paneler som måste konfigureras visar en **konfigurera panelbanderoll** tills du anpassar panelen. Så här anpassar du panelen:

1. Välj **Klar anpassa** i sidhuvudet för att avsluta redigeringsläge.

1. Välj banderollen och gör sedan den nödvändiga konfigurationen.

    ![Skärmbild av panel som kräver konfiguration](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Med en nedrullningspanel kan du visa anpassat, statiskt innehåll på instrumentpanelen. Det kan vara grundläggande instruktioner, en bild, en uppsättning hyperlänkar eller till och med kontaktinformation. Mer information om hur du använder en markdown-panel finns i [Använda en markdown-panel på Azure-instrumentpaneler för att visa anpassat innehåll](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Anpassa paneldata

Data på instrumentpanelen visar automatiskt aktivitet under de senaste 24 timmarna. Så här visar du ett annat tidsintervall för just den här panelen:

1. Välj **Anpassa paneldata** från snabbmenyn eller ![filterikonfiltret](./media/azure-portal-dashboards/dashboard-filter.png) i det övre vänstra hörnet på panelen.

    ![Skärmbild av snabbmenyn för paneler](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Markera kryssrutan om du vill **åsidosätta instrumentpanelens tidsinställningar på panelnivå**.

    ![Skärmbild av dialogrutan för att konfigurera inställningar för paneltid](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Välj det tidsintervall som ska visas för den här panelen. Du kan välja mellan de senaste 30 minuterna till de senaste 30 dagarna eller definiera ett anpassat intervall.

1. Välj den tidsgranularitet som ska visas. Du kan visa allt från en minuts steg till en månad.

1. Välj **Använd**.

## <a name="delete-a-tile"></a>Ta bort en panel

Så här tar du bort en panel från en instrumentpanel:

* Markera snabbmenyn i det övre högra hörnet på panelen och välj sedan **Ta bort från instrumentpanelen**. Eller:

* Välj ![redigeringsikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Redigera** om du vill ange anpassningsläge. Hovra i det övre högra hörnet ![av](./media/azure-portal-dashboards/dashboard-delete-icon.png) panelen och välj sedan ikonen för borttagning av borttagning av borttagning av borttagning av borttagning för att ta bort panelen från instrumentpanelen.

   ![Skärmbild som visar hur du tar bort panel från instrumentpanelen](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klona en instrumentpanel

Så här använder du en befintlig instrumentpanel som mall för en ny instrumentpanel:

1. Kontrollera att instrumentpanelsvyn visar den instrumentpanel som du vill kopiera.

1. Välj ![klonikonen](./media/azure-portal-dashboards/dashboard-clone.png) **Klona**i sidhuvudet .

1. En kopia av instrumentpanelen med namnet **Klon av** *instrumentpanelsnamnet* öppnas i redigeringsläge. Använd föregående steg i den här artikeln för att byta namn på och anpassa instrumentpanelen.

## <a name="publish-and-share-a-dashboard"></a>Publicera och dela en instrumentpanel

När du skapar en instrumentpanel är den privat som standard, vilket innebär att du är den enda som kan se den. Om du vill göra instrumentpaneler tillgängliga för andra kan du publicera och dela dem. Mer information finns i [Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Öppna en delad instrumentpanel

Så här hittar du och öppnar en delad instrumentpanel:

1. Markera pilen bredvid instrumentpanelsnamnet.

1. Välj i listan över instrumentpaneler som visas. Om instrumentpanelen som du vill öppna inte finns med i listan:

    1. välj **Bläddra bland alla instrumentpaneler**.

        ![Skärmbild av menyn för val av instrumentpanel](./media/azure-portal-dashboards/dashboard-browse.png)

    1. Välj **Delade instrumentpaneler**i fältet **Typ** .

        ![Skärmbild av alla instrumentpaneler urvalsmenyn](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Välj en eller flera prenumerationer. Du kan också ange text för att filtrera instrumentpaneler efter namn.

    1. Välj en instrumentpanel i listan över delade instrumentpaneler.

## <a name="delete-a-dashboard"></a>Ta bort en instrumentpanel

Så här tar du bort en privat eller delad instrumentpanel permanent:

1. Välj den instrumentpanel som du vill ta bort från listan bredvid instrumentpanelsnamnet.

1. Välj ![](./media/azure-portal-dashboards/dashboard-delete-icon.png) **borttagningsikon Ta bort** från sidhuvudet.

1. Om du vill ha en privat instrumentpanel väljer du **OK** i bekräftelsedialogrutan för att ta bort instrumentpanelen. Om du vill ha en delad instrumentpanel markerar du kryssrutan i bekräftelsedialogrutan för att bekräfta att den publicerade instrumentpanelen inte längre kan visas av andra. Välj **OK**.

    ![Skärmbild av borttagningsbekräftelse](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Nästa steg

* [Dela Azure-instrumentpaneler med hjälp av rollbaserad åtkomstkontroll](azure-portal-dashboard-share-access.md)
* [Skapa Azure-instrumentpaneler på ett programmatiskt sätt](azure-portal-dashboards-create-programmatically.md)
