---
title: Felsöka Azure Access Panel-tillägg för Internet Explorer | Microsoft Docs
description: Hur du använder grupprinciper för att distribuera Internet Explorer-tillägget för Mina appar-portalen.
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
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723918"
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
1. Öppna filen och extrahera innehållet till datorn.
1. Kör verktyget genom att högerklicka på filen med namnet *AccessPanelExtensionDiagnosticTool.js* och välj **öppna med** > **Microsoft Windows-baserad skriptmodul** .

    ![Öppna med > Microsoft Windows-baserade skriptvärden](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Granska diagnostiska resultaten som visas och välj **Ja** att åtgärda problem. Den **kontrollera resultaten** dialogruta med information om vad du gör om filnamnstillägget inte fungerar.  
1. Läs meddelandet och välj **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Kontrollera att tillägget för panelen åtkomst är aktiverat

För att verifiera att du har aktiverat Access Panel-tillägg i Internet Explorer:

1. I Internet Explorer, Välj den **kugghjulsikonen** i det övre högra hörnet i fönstret och välj **Internetalternativ**.
1. Gå till den **program** fliken och markera **Hantera tillägg**.
1. Välj **Access Panel-tillägg** i den **Microsoft Corporation** och väljer **aktivera**.
1. Om du vill spara ändringarna, kan du stänga alla webbläsarfönster Internet Explorer har öppen. Ändringen börjar gälla nästa gång du öppnar Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Aktivera tillägg för InPrivate-surfning

Så här aktiverar tillägg för InPrivate-surfning:

1. I Internet Explorer, Välj den **kugghjulsikonen** i det övre högra hörnet i fönstret och välj **Internetalternativ**.
1. Gå till den **sekretess** fliken och kontrollera att den **inaktivera verktygsfält och tillägg när InPrivate-surfning startar** kryssrutan är avmarkerad.
1. Om du vill spara ändringarna, kan du stänga alla webbläsarfönster Internet Explorer har öppen. Ändringen börjar gälla nästa gång du öppnar Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Avinstallera Access Panel-tillägg

Så här avinstallerar Access Panel-tillägg från datorn:

1. Sök efter i Kontrollpanelen, *avinstallera*.
1. I sökresultaten väljer **avinstallera ett program**.

    ![Välj avinstallera ett program-alternativ från Kontrollpanelen](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. I listan, väljer **Access Panel-tillägg** och välj **avinstallera**.

    ![Avinstallera Access Panel-tillägg](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Du kan sedan försöka att installera tillägget igen för att se om problemet är löst.

Om du stöter på problem med avinstallera tillägget, du kan också ta bort den med hjälp av den [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) verktyget.

## <a name="related-articles"></a>Relaterade artiklar

* [Programåtkomst och enkel inloggning med Azure Active Directory](what-is-single-sign-on.md)
* [Så här distribuerar du Access Panel-tillägg för Internet Explorer med hjälp av en Grupprincip](deploy-access-panel-browser-extension.md)
