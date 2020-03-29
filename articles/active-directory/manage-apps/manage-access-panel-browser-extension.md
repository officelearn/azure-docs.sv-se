---
title: Felsöka Azure Access Panel-tillägget för IE | Microsoft-dokument
description: Så här använder du grupprincipen för att distribuera Internet Explorer-tillägget för portalen Mina appar.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723918"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Felsöka tillägg till åtkomstpanelen för Internet Explorer

Den här artikeln hjälper dig att felsöka följande problem:

* Du kan inte komma åt dina appar via Portalen Mina appar när du använder Internet Explorer.
* Meddelandet "Installera programvara" visas även om du redan har installerat programvaran.

Om du är administratör läser du [Så här distribuerar du tillägget till åtkomstpanelen för Internet Explorer med grupprincip](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Kör diagnostikverktyget

Du kan diagnostisera installationsproblem med tillägg till åtkomstpanelen genom att hämta och köra diagnostikverktyget för åtkomstpanelen. 

Så här hämtar och installerar du diagnostikverktyget:

1. [Välj den här länken om du vill hämta diagnostikverktyget.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Öppna filen och extrahera innehållet till datorn.
1. Om du vill köra verktyget högerklickar du på filen *AccessPanelExtensionDiagnosticTool.js* och väljer **Öppna med** > **Microsoft Windows-baserad skriptvärd**.

    ![Öppna med > Microsoft Windows-baserad skriptvärd](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Granska de diagnostiska resultat som visas och välj **Ja** för att åtgärda problemen. Dialogrutan **Kontrollera resultat** visas med information om vad du ska göra om tillägget inte fungerar.  
1. Läs meddelandet och välj **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Kontrollera att tillägg till åtkomstpanelen är aktiverat

Så här kontrollerar du att du har aktiverat tillägg till åtkomstpanelen i Internet Explorer:

1. I Internet Explorer väljer du **ikonen Redskap** längst upp till höger i fönstret och väljer **Internet-alternativ**.
1. Gå till fliken **Program** och välj **Hantera tillägg**.
1. Välj **Tillägg för åtkomstpanelen** i avsnittet **Microsoft Corporation** och välj **Aktivera**.
1. Om du vill spara ändringarna stänger du alla webbläsarfönster i Internet Explorer som du har öppna. Ändringen träder i kraft nästa gång du öppnar Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Aktivera tillägg för InPrivate-surfning

Så här aktiverar du tillägg för InPrivate-surfning:

1. I Internet Explorer väljer du **ikonen Redskap** längst upp till höger i fönstret och väljer **Internet-alternativ**.
1. Gå till fliken **Sekretess** och kontrollera att kryssrutan **Inaktivera verktygsfält och tillägg när InPrivate-surfning startar** är avmarkerat.
1. Om du vill spara ändringarna stänger du alla webbläsarfönster i Internet Explorer som du har öppna. Ändringen träder i kraft nästa gång du öppnar Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Avinstallera tillägg till åtkomstpanelen

Så här avinstallerar du tillägg till åtkomstpanelen från datorn:

1. Sök efter *avinstallation*på Kontrollpanelen .
1. I sökresultaten väljer du **Avinstallera ett program**.

    ![Välj alternativet Avinstallera ett program på Kontrollpanelen](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Välj Tillägg för **Åtkomstpanelen** i listan och välj **Avinstallera**.

    ![Avinstallera tillägg till åtkomstpanelen](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Du kan sedan försöka installera tillägget igen för att se om problemet har lösts.

Om du stöter på problem med att avinstallera tillägget kan du också ta bort det med verktyget [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Relaterade artiklar

* [Programåtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Distribuera tillägg till åtkomstpanelen för Internet Explorer med grupprincip](deploy-access-panel-browser-extension.md)
