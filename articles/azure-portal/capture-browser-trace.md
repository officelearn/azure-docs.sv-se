---
title: Samla in en webbläsarspårning för felsökning | Microsoft-dokument
description: Samla in nätverksinformation från en webbläsarspårning för att felsöka problem med Azure-portalen.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310704"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Samla in en webbläsarspårning för felsökning

Om du felsöker ett problem med Azure-portalen och du behöver kontakta Microsoft-supporten rekommenderar vi att du först samlar in en webbläsarspårning och lite ytterligare information. Den information du samlar in kan ge viktig information om portalen när problemet uppstår. Följ stegen i den här artikeln för de utvecklarverktyg i webbläsaren du använder: Google Chrome eller Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) eller Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome och Microsoft Edge (Krom)

Google Chrome och Microsoft Edge (Krom) är båda baserade på [Chromium open source-projektet](https://www.chromium.org/Home). Följande steg visar hur du använder utvecklarverktygen, som är mycket lika i de två webbläsarna. Mer information finns i [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) och [Microsoft Edge (Chromium) Developer Tools](/microsoft-edge/devtools-guide-chromium).

1. Logga in på [Azure-portalen](https://portal.azure.com). Det är viktigt att du loggar in _innan_ du startar spårningen så att spårningen inte innehåller känslig information som är relaterad till din inloggning. 

1. Börja spela in de steg du tar i portalen med hjälp av [Steginspelare](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. I portalen navigerar du till steget precis innan problemet uppstår.

1. Tryck på F12 ![eller välj](media/capture-browser-trace/chromium-icon-settings.png) > Skärmbild av ikon för webbläsarinställningar**Fler verktyg** > **Utvecklarverktyg**.

1. Som standard behåller webbläsaren endast spårningsinformation för den sida som för närvarande läses in. Ange följande alternativ så att webbläsaren behåller all spårningsinformation, även om din förebråre kräver att du går till mer än en sida:

    1. Välj fliken **Nätverk** och välj sedan **Bevara loggen**.

          ![Skärmbild av "Bevara logg"](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Välj fliken **Konsol,** välj **Konsolinställningar**och välj sedan **Bevara logg**. Välj **Konsolinställningar** igen om du vill stänga inställningsfönstret.

          ![Skärmbild av "Bevara logg"](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Välj fliken **Nätverk** och välj sedan **Stoppa inspelning av nätverksloggen** och **Rensa**.

    ![Skärmbild av "Stoppa inspelningsnätverksloggen" och "Rensa"](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Välj **Spela in nätverkslogg**och återskapa problemet i portalen.

    ![Skärmbild av "Starta profileringssession"](media/capture-browser-trace/chromium-start-session.png)

    Sessionsutdata som liknar följande bild visas.

    ![Skärmbild av webbläsarspårningsresultat](media/capture-browser-trace/chromium-browser-trace-results.png)

1. När du har återskapat det oväntade portalbeteendet väljer du **Stoppa inspelning av nätverksloggen**och väljer sedan **Exportera HAR** och spara filen.

    ![Skärmbild av "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Stoppa steginspelaren och spara filen.

1. Tillbaka i fönstret för webbläsarutvecklare och välj fliken **Konsol.** Högerklicka, välj sedan **Spara som...** och spara konsolutdata i en textfil.

    ![Skärmbild av konsolutdata](media/capture-browser-trace/chromium-console-select.png)

1. Paketera HAR-filen, konsolutdata och skärminspelning i komprimerat format som .zip och dela den med Microsoft-stöd.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

Följande steg visar hur du använder utvecklarverktygen i Microsoft Edge (EdgeHTML). Mer information finns i [Microsoft Edge (EdgeHTML) Developer Tools](/microsoft-edge/devtools-guide).

1. Logga in på [Azure-portalen](https://portal.azure.com). Det är viktigt att du loggar in _innan_ du startar spårningen så att spårningen inte innehåller känslig information som är relaterad till din inloggning. 

1. Börja spela in de steg du tar i portalen med hjälp av [Steginspelare](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. I portalen navigerar du till steget precis innan problemet uppstår.

1. Tryck på F12 ![eller välj](media/capture-browser-trace/edge-icon-settings.png) > Skärmbild av ikon för webbläsarinställningar**Fler verktyg** > **Utvecklarverktyg**.

1. Som standard behåller webbläsaren endast spårningsinformation för den sida som för närvarande läses in. Ange följande alternativ så att webbläsaren behåller all spårningsinformation, även om din förebråre kräver att du går till mer än en sida:

    1. Välj fliken **Nätverk** och avmarkera sedan alternativet **Rensa poster på navigate**.

          ![Skärmbild av "Rensa poster vid navigering"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Välj fliken **Konsol** och välj sedan **Bevara logg**.

          ![Skärmbild av "Bevara logg"](media/capture-browser-trace/edge-console-preserve-log.png)

1. Välj fliken **Nätverk** och välj sedan **Sluta profilera session** och Rensa **session**.

    ![Skärmbild av "Sluta profilera session" och "Rensa session"](media/capture-browser-trace/edge-stop-clear-session.png)

1. Välj **Starta profileringssession**och återskapa problemet i portalen.

    ![Skärmbild av "Starta profileringssession"](media/capture-browser-trace/edge-start-session.png)

    Sessionsutdata som liknar följande bild visas.

    ![Skärmbild av webbläsarspårningsresultat](media/capture-browser-trace/edge-browser-trace-results.png)

1. När du har återskapat det oväntade portalbeteendet väljer du **Sluta profilera session**och väljer sedan **Exportera som har** och spara filen.

    ![Skärmbild av "Exportera som HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Stoppa steginspelaren och spara filen.

1. I fönstret Verktyg för webbläsarutvecklare väljer du fliken **Konsol** och expanderar fönstret. Placera markören i början av konsolens utdata och dra sedan och markera hela innehållet i utdata. Högerklicka och välj sedan **Kopiera**och spara konsolutdata i en textfil.

    ![Skärmbild av konsolutdata](media/capture-browser-trace/edge-console-select.png)

1. Paketera HAR-filen, konsolutdata och skärminspelning i komprimerat format som .zip och dela den med Microsoft-stöd.

## <a name="apple-safari"></a>Apple Safari

Följande steg visar hur du använder utvecklarverktygen i Apple Safari. Mer information finns i [översikt över Safari Developer Tools](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac).

1. Aktivera utvecklarverktygen i Apple Safari:

    1. Välj **Safari**och välj sedan **Inställningar**.

        ![Skärmbild av Safari-inställningar](media/capture-browser-trace/safari-preferences.png)

    1. Välj fliken **Avancerat** och välj sedan **Visa utveckla-menyn i menyraden**.

        ![Skärmbild av avancerade safariinställningar](media/capture-browser-trace/safari-show-develop-menu.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Det är viktigt att du loggar in _innan_ du startar spårningen så att spårningen inte innehåller känslig information som är relaterad till din inloggning. 

1. Börja spela in de steg du tar i portalen. Mer information finns i [Så här spelar du in skärmen på din Mac](https://support.apple.com/HT208721).

1. I portalen navigerar du till steget precis innan problemet uppstår.

1. Välj **Utveckla**och välj sedan **Visa webbkontroll**.

    ![Skärmbild av "Visa webbkontroll"](media/capture-browser-trace/safari-show-web-inspector.png)

1. Som standard behåller webbläsaren endast spårningsinformation för den sida som för närvarande läses in. Ange följande alternativ så att webbläsaren behåller all spårningsinformation, även om din förebråre kräver att du går till mer än en sida:

    1. Välj fliken **Nätverk** och välj sedan **Bevara logg .**

          ![Skärmbild av "Bevara logg"](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Välj fliken **Konsol** och välj sedan **Bevara logg**.

          ![Skärmbild av "Bevara logg"](media/capture-browser-trace/safari-console-preserve-log.png)

1. Välj fliken **Nätverk** och välj sedan **Rensa nätverksobjekt**.

    ![Skärmbild av "Rensa nätverksobjekt"](media/capture-browser-trace/safari-clear-session.png)

1. Återskapa problemet i portalen. Sessionsutdata som liknar följande bild visas.

    ![Skärmbild av webbläsarspårningsresultat](media/capture-browser-trace/safari-browser-trace-results.png)

1. När du har återskapat det oväntade portalbeteendet väljer du **Exportera** och sparar filen.

    ![Skärmbild av "Export"](media/capture-browser-trace/safari-network-export-har.png)

1. Stoppa skärminspelaren och spara filen.

1. I fönstret Verktyg för webbläsarutvecklare väljer du fliken **Konsol** och expanderar fönstret. Placera markören i början av konsolens utdata och dra sedan och markera hela innehållet i utdata. Använd Kommando-C för att kopiera utdata och spara den i en textfil.

    ![Skärmbild av konsolutdata](media/capture-browser-trace/safari-console-select.png)

1. Paketera HAR-filen, konsolutdata och skärminspelning i komprimerat format som .zip och dela den med Microsoft-stöd.

## <a name="next-steps"></a>Nästa steg

[Översikt över Azure Portal](azure-portal-overview.md)