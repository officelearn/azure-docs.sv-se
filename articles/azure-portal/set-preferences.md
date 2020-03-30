---
title: Ange inställningar för Azure-portalen | Microsoft-dokument
description: Du kan ändra standardinställningarna för Azure-portalen så att de uppfyller dina egna inställningar. Inställningarna inkluderar tidsgränsen för inaktiv session, standardvy, menyläge, kontrast, tema, meddelanden och språk- och regionalformat
services: azure-portal
keywords: inställningar, timeout, språk, regional
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310687"
---
# <a name="set-your-azure-portal-preferences"></a>Ange Azure-portalinställningar

Du kan ändra standardinställningarna för Azure-portalen så att de uppfyller dina egna inställningar. Var och en av inställningarna nedan kan ändras:

* [Time-out för inaktiv session](#change-the-idle-duration-for-inactive-sign-out)
* [Standardvy](#choose-your-default-view)
* [Menyläge för Portal](#choose-a-portal-menu-mode)
* [Temat Färg och hög kontrast](#choose-a-theme)
* [Popup-meddelanden](#enable-or-disable-pop-up-notifications)
* [Språk och regionsformat](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Ändra allmänna portalinställningar

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Inställningar** i det globala sidhuvudet.

    ![Skärmbild som visar globala sidrubrikikoner med markerade inställningar](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Ändra inaktiv varaktighet för inaktiv ut signering

Timeout-inställningen för inaktivitet hjälper till att skydda resurser från obehörig åtkomst om du glömmer att skydda din arbetsstation. När du har varit inaktiv ett tag loggas du automatiskt ut från din Azure-portalsession.

Markera listrutan under **Logga ut när du är inaktiv**. Välj hur länge din Azure-portalsession är utloggade om du är inaktiv.

   ![Skärmbild som visar portalinställningar med inaktiva timeout-inställningar markerade](./media/set-preferences/inactive-signout-user.png)

Ändringen sparas automatiskt. Om du är inaktiv loggas din Azure-portalsession ut efter den tid du anger.

Den här inställningen kan också göras av en administratör på katalognivå för att framtvinga en maximal inaktiv tid. Om en administratör har angett en timeout på katalognivå kan du fortfarande ange din egen inaktiva utcheckningstid. Välj en tidsinställning som är mindre än vad som anges på katalognivå.

Om administratören har aktiverat en timeout-princip för inaktivitet markerar du kryssrutan **Åsidosätt tidsgränsen för kataloginteaktivitet.** Ange ett tidsintervall som är mindre än principinställningen.

   ![Skärmbild som visar portalinställningar med åsidosättning av tidsgränsen för kataloginformation markerad](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Om du är administratör och vill tillämpa en inaktiv timeout-inställning för alla användare av Azure-portalen läser [du Ange tidsgränsen för inaktivitet på katalognivå för användare av Azure-portalen](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Välj standardvy 

Du kan ändra sidan som öppnas som standard när din inloggning till Azure-portalen.

   ![Skärmbild som visar Azure-portalinställningar med standardvyn markerad](./media/set-preferences/default-view.png)

Standardvyinställningen styr vilken Azure portalvy som visas när du loggar in. Du kan välja att öppna Azure Home som standard eller instrumentpanelsvyn.

* **Hemmet** kan inte anpassas.  Den visar genvägar till populära Azure-tjänster och visar de resurser du har använt senast. Vi ger dig också användbara länkar till resurser som Microsoft Learn och Azure-färdplanen.
* Instrumentpaneler kan anpassas för att skapa en arbetsyta som är utformad just för dig. Du kan till exempel skapa en instrumentpanel som är projekt-, aktivitets- eller rollfokuserad. Om du väljer **Instrumentpanel**går standardvyn till den senast använda instrumentpanelen.

### <a name="choose-a-portal-menu-mode"></a>Välj ett portalmenyläge

Standardläget för portalmenyn styr hur mycket utrymme portalmenyn tar upp på sidan.

* När portalmenyn är i **utfällbart** läge döljs den tills du behöver den. Välj menyikonen för att öppna eller stänga menyn.
* Om du väljer **dockat** läge för portalmenyn är det alltid synligt. Du kan komprimera menyn för att ge mer arbetsutrymme. 

### <a name="choose-a-theme"></a>Välj ett tema

Det tema du väljer påverkar bakgrunds- och teckensnittsfärgerna som visas i Azure-portalen. Du kan välja bland ett av fyra förinställda färgteman. Välj varje miniatyr för att hitta det tema som passar dig bäst.

   ![Skärmbild som visar Azure-portalinställningar med teman markerade](./media/set-preferences/theme.png)

Du kan välja ett av de högkontrastteman i stället. Inställningarna med hög kontrast gör Azure-portalen lättare att läsa för synskadade användare och åsidosätter alla andra temaval. Mer information finns i [Aktivera hög kontrast eller ändra tema](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Aktivera eller inaktivera popup-meddelanden

Aviseringar är systemmeddelanden som är relaterade till din aktuella session. De ger information som ditt nuvarande kreditsaldo, när resurser som du just har skapat blir tillgängliga eller bekräftar din senaste åtgärd, till exempel. När popup-meddelanden är aktiverade visas meddelandena en kort stund i det övre hörnet på skärmen. 

Om du vill aktivera eller inaktivera popup-meddelanden markerar eller avmarkerar du kryssrutan **Aktivera popup-meddelanden.**

   ![Skärmbild som visar Azure-portalinställningar med popup-meddelanden markerade](./media/set-preferences/popup-notifications.png)

Om du vill läsa alla meddelanden som tas emot under den aktuella sessionen väljer du **Meddelanden** i det globala huvudet.

   ![Skärmbild som visar global sidhuvud i Azure-portalen med markerade meddelanden](./media/set-preferences/read-notifications.png)

Om du vill läsa aviseringar från tidigare sessioner letar du efter händelser i aktivitetsloggen. Mer information finns i [Visa och hämta Azure Activity-logghändelser](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Inställningar under användbara länkar

Om du har gjort ändringar i Azure-portalinställningarna och vill ignorera dem väljer du **Återställ standardinställningar .** Alla ändringar som du har gjort i portalinställningarna går förlorade. Det här alternativet påverkar inte anpassningar av instrumentpanelen.

Mer information om **Exportera alla inställningar** eller Ta bort alla inställningar och privata **instrumentpaneler**finns i [Exportera eller ta bort användarinställningar](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Ändra språk och nationella inställningar

Det finns två inställningar som styr hur texten i Azure-portalen visas. **Språkinställningen** styr det språk du ser för text i Azure-portalen. **Regionalt format** styr hur datum, tid, siffror och valuta visas.

Om du vill ändra det språk som används i Azure-portalen använder du listrutan för att välja från listan över tillgängliga språk.

Valet av regionalt format ändras för att visa nationella alternativ för endast det språk du har valt. Om du vill ändra den automatiska markeringen använder du listrutan för att välja önskat regionalt format.

Om du till exempel väljer engelska som språk och sedan väljer USA som regionalt format visas valutan i US-dollar. Om du väljer engelska som språk och sedan väljer Europa som regionalt format visas valutan i euro.

Välj **Använd** för att uppdatera dina inställningar för språk och regionalt format.

   ![Skärmbild som visar inställningar för språk och regionalt format](./media/set-preferences/language.png)

>[!NOTE]
>Dessa språkinställningar och nationella inställningar påverkar bara Azure-portalen. Dokumentationslänkar som öppnas på en ny flik eller ett nytt fönster använder webbläsarens språkinställningar för att bestämma vilket språk som ska visas.
>

## <a name="next-steps"></a>Nästa steg

* [Skapa och dela anpassade instrumentpaneler](azure-portal-dashboards.md)
* [Instruktionsvideoserie om Azure-portalen](azure-portal-video-series.md)
