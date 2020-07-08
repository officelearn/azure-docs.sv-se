---
title: Felsöka Azure Access panel-tillägget för IE | Microsoft Docs
description: Så här använder du grup princip för att distribuera Internet Explorer-tillägget för My Apps-portalen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763269"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Felsöka åtkomst panel tillägget för Internet Explorer

Den här artikeln hjälper dig att felsöka följande problem:

* Du kan inte komma åt dina appar via portalen Mina appar när du använder Internet Explorer.
* Du ser meddelandet "installera program vara" även om du redan har installerat program varan.

Om du är administratör kan du läsa om [hur du distribuerar åtkomst panels tillägget för Internet Explorer med Grupprincip](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Kör Diagnostic-verktyget

Du kan diagnostisera installations problem med åtkomst panels tillägget genom att ladda ned och köra diagnostikverktyget för åtkomst panelen. 

Så här hämtar och installerar du diagnostikverktyget:

1. [Välj den här länken för att ladda ned diagnostikverktyget.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Öppna filen och extrahera innehållet till datorn.
1. Om du vill köra verktyget högerklickar du på filen med namnet *AccessPanelExtensionDiagnosticTool.js* och väljer **Öppna med**  >  **Microsoft Windows-baserad skript värd**.

    ![Öppna med > Microsoft Windows-baserad skript värd](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Granska de diagnostiska resultat som visas och välj **Ja** för att åtgärda problemen. Dialog rutan **kontrol lera resultat** visas med information om vad du kan göra om tillägget inte fungerar.  
1. Läs meddelandet och välj **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Kontrol lera att åtkomst panels tillägget är aktiverat

Kontrol lera att du har aktiverat åtkomst panel tillägget i Internet Explorer:

1. I Internet Explorer väljer du **kugg hjuls ikonen** i det övre högra hörnet i fönstret och väljer **Internet alternativ**.
1. Gå till fliken **program** och välj **Hantera tillägg**.
1. Välj **åtkomst panels tillägg** i avsnittet **Microsoft Corporation** och välj **Aktivera**.
1. Om du vill spara ändringarna stänger du alla öppna Internet Explorer-fönster. Ändringen börjar gälla nästa gången du öppnar Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Aktivera tillägg för InPrivate-surfning

Så här aktiverar du tillägg för InPrivate-surfning:

1. I Internet Explorer väljer du **kugg hjuls ikonen** i det övre högra hörnet i fönstret och väljer **Internet alternativ**.
1. Gå till fliken **Sekretess** och kontrol lera att kryss rutan **Inaktivera verktygsfält och tillägg när InPrivate-surfning börjar** är avmarkerad.
1. Om du vill spara ändringarna stänger du alla öppna Internet Explorer-fönster. Ändringen börjar gälla nästa gången du öppnar Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Avinstallera åtkomst panel tillägget

Så här avinstallerar du åtkomst panels tillägget från datorn:

1. I kontroll panelen söker du efter *Avinstallera*.
1. I Sök resultaten väljer du **Avinstallera ett program**.

    ![Välj alternativet Avinstallera ett program på kontroll panelen](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Välj **åtkomst panels tillägg** i listan och välj **Avinstallera**.

    ![Avinstallera åtkomst panel tillägget](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Du kan sedan försöka installera tillägget igen för att se om problemet har lösts.

Om du stöter på problem med att avinstallera tillägget kan du också ta bort det med hjälp av [Microsoft Fix it](https://go.microsoft.com/?linkid=9779673) -verktyget.

## <a name="related-articles"></a>Relaterade artiklar

* [Program åtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Så här distribuerar du åtkomst panels tillägget för Internet Explorer med grupprincip](deploy-access-panel-browser-extension.md)
