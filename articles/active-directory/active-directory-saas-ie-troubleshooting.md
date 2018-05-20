---
title: Felsöka Azure Access panelen-tillägg för Internet Explorer | Microsoft Docs
description: Hur du använder grupprinciper för att distribuera Internet Explorer-tillägget för Mina appar portalen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: f56b3230-26fd-42ec-9e3d-2c12daf15479
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4f1538cf598da8b5b9aa19def2d5f86ceaca0a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshooting-the-access-panel-extension-for-internet-explorer"></a>Felsökning av Access panelen-tillägg för Internet Explorer
Den här artikeln hjälper dig att felsöka följande problem:

* Du kan inte komma åt dina appar via portalen Mina appar när du använder Internet Explorer.
* Meddelandet ”installera programvara” även om du redan har installerat programvaran.

Om du är administratör kan se även: [hur du distribuerar Access panelen-tillägg för Internet Explorer med hjälp av Grupprincip](active-directory-saas-ie-group-policy.md)

## <a name="run-the-diagnostic-tool"></a>Kör verktyget diagnostik
Du kan diagnostisera problem med filnamnstillägget åtkomst panelen genom att hämta och kör diagnostikverktyg för åtkomstpanelen:

1. [Klicka här om du vill hämta diagnostikverktyget.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
2. Öppna filen, och tryck på **extrahera alla** knappen.
   
    ![Klicka på Extrahera alla](./media/active-directory-saas-ie-troubleshooting/extract1.png)
3. Tryck på den **extrahera** för att fortsätta.
   
    ![Klicka på Extrahera](./media/active-directory-saas-ie-troubleshooting/extract2.png)
4. Kör verktyget genom att högerklicka på filen med namnet **AccessPanelExtensionDiagnosticTool**och välj **öppna med > Microsoft Windows-baserad skriptmodul**.
   
    ![Öppna med > Microsoft Windows-baserade skriptvärden](./media/active-directory-saas-ie-troubleshooting/open_tool.png)
5. Sedan visas följande diagnostiska fönstret som beskriver vad kan vara fel med installationen.
   
    ![Ett exempel på fönstret diagnostik](./media/active-directory-saas-ie-troubleshooting/tool_preview.png)
6. Klicka på ”**Ja**” så att programmet åtgärda de problem som har hittats.
7. Stäng alla fönster i Internet Explorer om du vill spara ändringarna och öppna Internet Explorer igen.<br />Försök stegen nedan om du fortfarande inte kan komma åt dina appar.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Kontrollera att panelen-tillägget åtkomst är aktiverat
Kontrollera att tillägget för åtkomst-panelen är aktiverad i Internet Explorer:

1. I Internet Explorer klickar du på den **Kugghjulet ikonen** i det övre högra hörnet i fönstret. Välj sedan **Internetalternativ**.<br />(I tidigare versioner av Internet Explorer kan du hitta detta under **Verktyg > Internetalternativ**.
   
    ![Gå till Verktyg > Internet-alternativ](./media/active-directory-saas-ie-troubleshooting/internetoptions.png)
2. Klicka på den **program** och klicka sedan på den **Hantera tillägg** knappen.
   
    ![Klicka på Hantera tillägg](./media/active-directory-saas-ie-troubleshooting/internetoptions_programs.png)
3. I den här dialogrutan Välj **åtkomst panelen tillägget** och klicka sedan på den **aktivera** knappen.
   
    ![Klicka på Aktivera](./media/active-directory-saas-ie-troubleshooting/enableaddon.png)
4. Stäng alla fönster i Internet Explorer och öppna Internet Explorer igen om du vill spara ändringarna.

## <a name="enable-extensions-for-inprivate-browsing"></a>Aktivera tillägg för InPrivate-surfning
Om du använder InPrivate-surfning-läge:

1. I Internet Explorer klickar du på den **Kugghjulet ikonen** i det övre högra hörnet i fönstret. Välj sedan **Internetalternativ**.<br />(I tidigare versioner av Internet Explorer kan du hitta detta under **Verktyg > Internetalternativ**.
   
    ![Ett exempel på fönstret diagnostik](./media/active-directory-saas-ie-troubleshooting/inprivateoptions.png)
2. Gå till den **sekretess** sedan fliken **avmarkera** kryssrutan **inaktivera verktygsfält och tillägg när InPrivate-surfning startar**</p>
   
    ![Avmarkera inaktivera verktygsfält och tillägg när InPrivate-surfning startar](./media/active-directory-saas-ie-troubleshooting/enabletoolbars.png)
3. Stäng alla fönster i Internet Explorer och öppna Internet Explorer igen om du vill spara ändringarna.

## <a name="uninstall-the-access-panel-extension"></a>Avinstallera panelen-tillägget åtkomst
Så här avinstallerar åtkomstpanelen tillägget från datorn:

1. Tryck på på tangentbordet den **Windows-tangenten** att öppna Start-menyn. När menyn är öppen kan skriva du något om du vill göra en sökning. Skriv ”på Kontrollpanelen” och sedan öppna den **Kontrollpanelen** när den visas i sökresultaten.
   
    ![Sök efter Kontrollpanelen](./media/active-directory-saas-ie-troubleshooting/search_sm.png)
2. I det övre högra hörnet av Kontrollpanelen ändrar den **visa med** att **stora ikoner**. Hitta och klicka sedan på **program och funktioner** knappen.
   
    ![Ändra vyn för att visa stora ikoner](./media/active-directory-saas-ie-troubleshooting/control_panel.png)
3. I listan, väljer du **åtkomst panelen tillägget**, och klicka på den **avinstallera** knappen.
   
    ![Klicka på Avinstallera](./media/active-directory-saas-ie-troubleshooting/uninstall.png)
4. Du kan sedan försöker installera tillägget igen för att se om problemet är löst.

Om du får problem med tillägget avinstallerar du kan också ta bort den med hjälp av den [Microsoft åtgärda det](https://go.microsoft.com/?linkid=9779673) verktyget.

## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
* [Programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md)
* [Hur du distribuerar Access panelen-tillägg för Internet Explorer med hjälp av Grupprincip](active-directory-saas-ie-group-policy.md)

