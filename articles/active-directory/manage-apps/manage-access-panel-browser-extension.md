---
title: Felsökning Azure Access Panel-tillägg för Internet Explorer | Microsoft Docs
description: Hur du använder grupprinciper för att distribuera Internet Explorer-tillägget för Mina appar-portalen.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace2482e51454458977452f0aa610dd43a94e8a7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211197"
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Felsöka Access Panel-tillägg för Internet Explorer
Den här artikeln hjälper dig att felsöka följande problem:

* Du kan inte komma åt dina appar via portalen Mina appar när du använder Internet Explorer.
* Du ser meddelandet ”installera programvara” även om du redan har installerat programvaran.

Om du är administratör kan se även: [Så här distribuerar du Access Panel-tillägg för Internet Explorer med hjälp av en Grupprincip](deploy-access-panel-browser-extension.md)

## <a name="run-the-diagnostic-tool"></a>Kör diagnostikverktyget
Du kan diagnostisera problem med installationen med tillägget Access Panel genom att hämta och köra diagnostikverktyg för åtkomstpanelen:

1. [Klicka här om du vill hämta diagnostikverktyget.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Öppna filen, och tryck på **extrahera alla** knappen.
   
    ![Tryck på Extrahera alla](./media/manage-access-panel-browser-extension/extract1.png)
3. Tryck på den **extrahera** för att fortsätta.
   
    ![Tryck på Extract](./media/manage-access-panel-browser-extension/extract2.png)
4. Kör verktyget genom att högerklicka på filen med namnet **AccessPanelExtensionDiagnosticTool**och välj sedan **öppna med > Microsoft Windows-baserad skriptmodul**.
   
    ![Öppna med > Microsoft Windows-baserade skriptvärden](./media/manage-access-panel-browser-extension/open_tool.png)
5. Därefter visas följande diagnostiska fönster som beskriver vad som kan gå fel med installationen.
   
    ![Ett sampel från fönstret diagnostik](./media/manage-access-panel-browser-extension/tool_preview.png)
6. Klicka på ”**Ja**” att låta programmet åtgärda de problem som har hittats.
7. För att spara ändringarna, Stäng alla fönster i Internet Explorer och öppna Internet Explorer igen.<br />Om du fortfarande inte komma åt dina appar följer du instruktionerna nedan.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Kontrollera att tillägget för panelen åtkomst är aktiverat
För att verifiera att panelen-tillägget åtkomst är aktiverat i Internet Explorer:

1. I Internet Explorer klickar du på den **kugghjulsikonen** i det övre högra hörnet i fönstret. Välj sedan **Internetalternativ**.<br />(I tidigare versioner av Internet Explorer kan du hitta under **Verktyg > Internetalternativ**.
   
    ![Gå till Verktyg > Internet-alternativ](./media/manage-access-panel-browser-extension/internetoptions.png)
2. Klicka på den **program** och klicka sedan på den **Hantera tillägg** knappen.
   
    ![Klicka på Hantera tillägg](./media/manage-access-panel-browser-extension/internetoptions_programs.png)
3. I den här dialogrutan Välj **Access Panel-tillägg** och klicka sedan på den **aktivera** knappen.
   
    ![Klicka på Aktivera](./media/manage-access-panel-browser-extension/enableaddon.png)
4. För att spara ändringarna, Stäng alla fönster i Internet Explorer och öppna Internet Explorer igen.

## <a name="enable-extensions-for-inprivate-browsing"></a>Aktivera tillägg för InPrivate-surfning
Om du använder läget InPrivate-surfning:

1. I Internet Explorer klickar du på den **kugghjulsikonen** i det övre högra hörnet i fönstret. Välj sedan **Internetalternativ**.<br />(I tidigare versioner av Internet Explorer kan du hitta under **Verktyg > Internetalternativ**.
   
    ![Ett sampel från fönstret diagnostik](./media/manage-access-panel-browser-extension/inprivateoptions.png)
2. Gå till den **sekretess** fliken **avmarkera** kryssrutan **inaktivera verktygsfält och tillägg när InPrivate-surfning startar**</p>
   
    ![Avmarkera inaktivera verktygsfält och tillägg när InPrivate-surfning startar](./media/manage-access-panel-browser-extension/enabletoolbars.png)
3. För att spara ändringarna, Stäng alla fönster i Internet Explorer och öppna Internet Explorer igen.

## <a name="uninstall-the-access-panel-extension"></a>Avinstallera Access Panel-tillägg
Avinstallera tillägget åtkomstpanelen från datorn:

1. På tangentbordet, trycker du på den **Windows nyckeln** att öppna Start-menyn. När menyn är öppen kan du skriva något för att göra en sökning. Skriv ”Kontrollpanelen” och öppna sedan den **Kontrollpanelen** när den visas i sökresultaten.
   
    ![Sök efter Kontrollpanelen](./media/manage-access-panel-browser-extension/search_sm.png)
2. I det övre högra hörnet av Kontrollpanelen ändrar den **visa genom att** alternativet att **stora ikoner**. Hitta och klicka sedan på **program och funktioner** knappen.
   
    ![Ändra vyn för att visa stora ikoner](./media/manage-access-panel-browser-extension/control_panel.png)
3. I listan, väljer **Access Panel-tillägg**, och klicka på den **avinstallera** knappen.
   
    ![Klicka på Avinstallera](./media/manage-access-panel-browser-extension/uninstall.png)
4. Du kan sedan försöka att installera tillägget igen för att se om problemet är löst.

Om du får problem med avinstallera tillägget, du kan också ta bort den med hjälp av den [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) verktyget.

## <a name="related-articles"></a>Relaterade artiklar
* [Programåtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Så här distribuerar du Access Panel-tillägg för Internet Explorer med hjälp av en Grupprincip](deploy-access-panel-browser-extension.md)

