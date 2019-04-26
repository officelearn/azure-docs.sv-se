---
title: Felsöka Azure Access Panel-tillägg för Internet Explorer | Microsoft Docs
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
ms.date: 04/11/2019
ms.author: celested
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63dfece713657df4450f18b8a7ce212ce2c41687
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291574"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Felsöka Access Panel-tillägg för Internet Explorer

Den här artikeln hjälper dig att felsöka följande problem:

* Du kan inte komma åt dina appar via portalen Mina appar när du använder Internet Explorer.
* Du ser meddelandet ”installera programvara” även om du redan har installerat programvaran.

Om du är administratör, se [hur du distribuerar Access Panel-tillägg för Internet Explorer med hjälp av en Grupprincip](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Kör diagnostikverktyget

Du kan diagnostisera problem med installationen med tillägget Access Panel genom att hämta och köra diagnostikverktyg för åtkomstpanelen. 

Ladda ned och installera diagnostikverktyget:

1. [Välj den här länken för att hämta diagnostikverktyget.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)

2. Öppna filen och extrahera innehållet till datorn.
   
3. Kör verktyget genom att högerklicka på filen med namnet *AccessPanelExtensionDiagnosticTool.js* och välj **öppna med** > **Microsoft Windows-baserad skriptmodul** .
   
    ![Öppna med > Microsoft Windows-baserade skriptvärden](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

4. Granska diagnostiska resultaten som visas och välj **Ja** att åtgärda problem. Den **kontrollera resultaten** dialogruta med information om vad du gör om filnamnstillägget inte fungerar.  

5. Läs meddelandet och välj **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Kontrollera att tillägget för panelen åtkomst är aktiverat

För att verifiera att du har aktiverat Access Panel-tillägg i Internet Explorer:

1. I Internet Explorer, Välj den **kugghjulsikonen** i det övre högra hörnet i fönstret och välj **Internetalternativ**.
   
2. Gå till den **program** fliken och markera **Hantera tillägg**.
   
3. Välj **Access Panel-tillägg** i den **Microsoft Corporation** och väljer **aktivera**.
   
4. Om du vill spara ändringarna, kan du stänga alla webbläsarfönster Internet Explorer har öppen. Ändringen börjar gälla nästa gång du öppnar Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Aktivera tillägg för InPrivate-surfning

Så här aktiverar tillägg för InPrivate-surfning:

1. I Internet Explorer, Välj den **kugghjulsikonen** i det övre högra hörnet i fönstret och välj **Internetalternativ**.
   
2. Gå till den **sekretess** fliken och kontrollera att den **inaktivera verktygsfält och tillägg när InPrivate-surfning startar** kryssrutan är avmarkerad.
   
3.  Om du vill spara ändringarna, kan du stänga alla webbläsarfönster Internet Explorer har öppen. Ändringen börjar gälla nästa gång du öppnar Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Avinstallera Access Panel-tillägg

Så här avinstallerar Access Panel-tillägg från datorn:

1. Sök efter i Kontrollpanelen, *avinstallera*. 

2. I sökresultaten väljer **avinstallera ett program**.
   
    ![Sök efter avinstallera program.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

3. I listan, väljer **Access Panel-tillägg** och välj **avinstallera**.

    ![Avinstallera tillägget för åtkomst-panelen.](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)
   
4. Du kan sedan försöka att installera tillägget igen för att se om problemet är löst.

Om du stöter på problem med avinstallera tillägget, du kan också ta bort den med hjälp av den [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) verktyget.

## <a name="related-articles"></a>Relaterade artiklar
* [Programåtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Så här distribuerar du Access Panel-tillägg för Internet Explorer med hjälp av en Grupprincip](deploy-access-panel-browser-extension.md)

