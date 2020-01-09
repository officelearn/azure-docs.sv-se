---
title: Ange Azure Portal inställningar | Microsoft Docs
description: Du kan ändra Azure Portal standardinställningarna så att de passar dina egna inställningar. Inställningarna omfattar tids gräns för inaktiv session, standardvy, Meny läge, kontrast, tema, meddelanden och språk och nationella format
services: azure-portal
keywords: inställningar, tids gräns, språk, regional
author: mblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 0c18ccbf5d9e4884af46e088f1a4ead67f50c3f5
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75641374"
---
# <a name="set-your-azure-portal-preferences"></a>Ange Azure Portal inställningar

Du kan ändra standardinställningarna för Azure Portal så att de passar dina egna inställningar. Var och en av inställningarna i listan nedan kan ändras:

* [Tids gräns för inaktiv session](#change-the-idle-duration-for-inactive-sign-out)
* [Standardvy](#choose-your-default-view)
* [Meny läge för Portal](#choose-a-portal-menu-mode)
* [Tema för färg och hög kontrast](#choose-a-theme)
* [Popup-meddelanden](#enable-or-disable-pop-up-notifications)
* [Språk och regionalt format](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Ändra allmänna Portal inställningar

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Inställningar** från det globala sidhuvudet.

    ![Skärm bild som visar globala sidhuvuds ikoner med markerade inställningar](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Ändra varaktighet för inaktivitet för inaktiv utloggning

Inställningen tids gräns för inaktivitet hjälper till att skydda resurser från obehörig åtkomst om du glömmer att skydda din arbets Station. När du har varit inaktiv under en stund loggas du in automatiskt från Azure Portal-sessionen.

Välj List rutan under **Logga ut mig när du är inaktiv**. Välj hur länge din Azure Portal-session ska loggas ut om du är inaktiv.

   ![Skärm bild som visar Portal inställningar med inaktiva timeout-inställningar markerade](./media/set-preferences/inactive-signout-user.png)

Ändringen sparas automatiskt. Om du är inaktiv kommer din Azure Portal-session att logga ut efter den varaktighet som du har angett.

Den här inställningen kan också göras av en administratör på katalog nivå för att genomdriva en maximal inaktiv tid. Om en administratör har gjort en timeout-inställning på katalog nivå kan du fortfarande ange en egen inaktiv inloggnings tid. Välj en tids inställning som är mindre än vad som anges på katalog nivå.

Om din administratör har aktiverat en timeout-princip för inaktivitet markerar du kryss rutan **Åsidosätt timeout-princip för katalog inaktivitet** . Ange ett tidsintervall som är mindre än princip inställningen.

   ![Skärm bild som visar Portal inställningar som åsidosätter inställningen för timeout för katalog inaktivitet](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Om du är administratör och vill framtvinga en inaktiv timeout-inställning för alla användare av Azure Portal, se [Ange tids gräns för inaktivitet i katalog nivå för användare av Azure Portal](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Välj standardvy 

Du kan ändra sidan som öppnas som standard när du loggar in på Azure Portal.

   ![Skärm bild som visar Azure Portal inställningar med standard visning markerat](./media/set-preferences/default-view.png)

Inställningen standardvy styr vilken Azure Portal vy som visas när du loggar in. Du kan välja att öppna Azure start som standard eller vyn instrument panel.

* Det går inte att anpassa **Start sidan** .  Den visar genvägar till populära Azure-tjänster och listar de resurser som du har använt senast. Vi ger dig också användbara länkar till resurser som Microsoft Learn och Azure-översikten.
* Instrument paneler kan anpassas för att skapa en arbets yta som har utformats precis för dig. Du kan till exempel bygga en instrument panel som är projekt, uppgift eller roll fokuserad. Om du väljer **instrument panel**, kommer din standardvy att gå till den senast använda instrument panelen.

### <a name="choose-a-portal-menu-mode"></a>Välj ett meny läge för portalen

Standard läget för Portal-menyn styr hur mycket utrymme som Portal menyn tar upp på sidan.

* När Portal menyn är i **utfällt** läge är den dold tills du behöver den. Välj Meny ikonen för att öppna eller stänga menyn.
* Om du väljer **dockat** läge för Portal-menyn är det alltid synligt. Du kan komprimera menyn för att få mer arbets yta. 

### <a name="choose-a-theme"></a>Välj ett tema

Det tema som du väljer påverkar bakgrunds-och teckensnitts färgerna som visas i Azure Portal. Du kan välja någon av fyra förinställda färg teman. Välj varje miniatyr bild för att hitta det tema som passar dig bäst.

   ![Skärm bild som visar Azure Portal inställningar med teman markerade](./media/set-preferences/theme.png)

Du kan välja ett tema med hög kontrast i stället. Med inställningarna för hög kontrast blir Azure Portal lättare att läsa för synskadade användare och åsidosätta alla andra tema val. Mer information finns i [Aktivera hög kontrast eller ändra tema](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Aktivera eller inaktivera popup-meddelanden

Meddelanden är system meddelanden relaterade till din aktuella session. De tillhandahåller information som ditt aktuella kredit saldo när resurser som du just har skapat blir tillgängliga eller bekräfta din senaste åtgärd, till exempel. När popup-meddelanden är aktiverade visas meddelandena kortfattat i det övre hörnet på skärmen. 

Markera eller avmarkera kryss rutan **Aktivera popup-meddelanden** om du vill aktivera eller inaktivera popup-meddelanden.

   ![Skärm bild som visar Azure Portal inställningar med popup-meddelanden markerade](./media/set-preferences/popup-notifications.png)

Om du vill läsa alla meddelanden som tagits emot under den aktuella sessionen väljer du **meddelanden** från den globala rubriken.

   ![Skärm bild som visar Azure Portal global rubrik med meddelanden markerade](./media/set-preferences/read-notifications.png)

Om du vill läsa meddelanden från tidigare sessioner söker du efter händelser i aktivitets loggen. Läs mer i läsa [och hämta Azure aktivitets logg händelser](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Inställningar under användbara länkar

Om du har ändrat Azure Portal inställningarna och vill ta bort dem väljer du **Återställ standardinställningar**. Eventuella ändringar som du har gjort i Portal inställningarna går förlorade. Det här alternativet påverkar inte anpassningar av instrument paneler.

Mer information om att **Exportera alla inställningar** eller **ta bort alla inställningar och privata instrument paneler**finns i [Exportera eller ta bort användar inställningar](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Ändra språk och nationella inställningar

Det finns två inställningar som styr hur texten i Azure Portal visas. **Språk** inställningen styr språket som visas för text i Azure Portal. **Regions formatet** styr hur datum, tid, siffror och valuta visas.

Om du vill ändra vilket språk som används i Azure Portal använder du List rutan för att välja i listan över tillgängliga språk.

Det regionala format valet ändras för att Visa regionala alternativ för endast det språk som du har valt. Om du vill ändra det automatiskt väljer du det regionala formatet du vill använda i list rutan.

Om du till exempel väljer engelska som språk och väljer USA som regions format, visas valutan i amerikanska dollar. Om du väljer engelska som språk och väljer sedan Europa som regions format visas valutan i euro.

Välj **tillämpa** för att uppdatera språk och nationella format inställningar.

   ![Skärm bild som visar inställningar för språk och nationella format](./media/set-preferences/language.png)

>[!NOTE]
>Dessa språk och nationella inställningar påverkar bara Azure Portal. I dokumentations länkar som öppnas i en ny flik eller i ett fönster används webbläsarens språk inställningar för att avgöra vilket språk som ska visas.
>

## <a name="next-steps"></a>Nästa steg

* [Skapa och dela anpassade instrument paneler](azure-portal-dashboards.md)
* [Azure Portal instruktions video serie](azure-portal-video-series.md)
