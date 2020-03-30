---
title: Microsoft Threat Modeling Tool release 2018-09-12
titleSuffix: Azure
description: Dokumentera viktig information för verktyget för hotmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: a921310f14f0d48922165a46d750366e170ee374
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269918"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Hot modelleringsverktyg GA release 7.1.50911.2 - 2018-09-12

Vi är glada att kunna meddela att Microsoft Threat Modeling Tool nu finns att ladda ner som en allmänt tillgänglig (GA) som stöds. Den här versionen innehåller viktiga sekretess- och säkerhetsuppdateringar samt buggfixar, funktionsuppdateringar och stabilitetsförbättringar. Befintliga användare av 2017 års förhandsversion uppmanas att uppdatera till den senaste versionen via ClickOnce-tekniken när klienten öppnas. För nya användare av verktyget kan du [hämta klienten](https://aka.ms/threatmodelingtool).

Med den här versionen avslutar vi stödet för 2017 års förhandsversion och rekommenderar alla användare av förhandsversionen till GA-versionen. Den 15 oktober 2018 eller senare ställer vi in den minsta clickonce-versionen för threat modeling tool, och alla förhandsversionsklienter måste uppgraderas.

Microsoft Threat Modeling Tool 2016, som är tillgängligt från [Microsoft Download Center,](https://www.microsoft.com/en-us/download/details.aspx?id=49168)stöds fortfarande fram till den 1 oktober 2019 endast för kritiska säkerhetskorrigeringar.

## <a name="feature-changes"></a>Funktionsändringar

### <a name="azure-stencil-updates"></a>Azure-stenciluppdateringar

Ytterligare Azure-stenciler och tillhörande hot och mildrande åtgärder har lagts till i stenciluppsättningen som levereras med den här versionen. Betydande ändringar har gjorts i fokusområdena "Azure App Services", "Azure Database Offerings" och "Azure Storage".

![Azure Stencil Uppdateringar](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>OneDrive-integrationsfunktionen har tagits bort

Funktionerna "Spara på OneDrive", "Öppna från OneDrive" och "Dela en länk" i förhandsversionen har tagits bort. Användare av OneDrive uppmanas att använda Microsofts [OneDrive för Windows-klient](https://onedrive.live.com/about/en-us/download/) för att komma åt sina filer som lagras på OneDrive via standarddialogerna för filsparing och öppna.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Anmärkningsvärda fasta fel som rapporterats av kunder

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>I förhandsversionen av TMT kraschar verktyget när standardmallen används

- När en generisk stencil (till exempel "Allmänt dataflöde") läggs till på ritningsytan och genererar hot kan verktyget krascha. Det här problemet har åtgärdats.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>I förhandsversionen av TMT, när jag sparar en rapport eller kopierar hoten, är risknivåerna felaktiga

- Om en användare ändrar risknivån för specifika hot och sedan sparar en rapport eller kopierar riskerna, kan risknivån återgå till "Hög". Det här problemet har åtgärdats.

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Användare av högupplösta skärmar kan uppleva liten text i hotegenskaperna

#### <a name="issue"></a>Problem

Om användaren har en skärm med hög upplösning som som standard är inställd för att förstora för läsbarhet i Windows i analysvyn kan avsnittet "Möjlig begränsning" i ett hot visas med liten text.

![Kända problem med högupplösta skärmar](./media/threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Lösning

Användaren kan klicka på begränsningstexten och använda den vanliga Windows zoomkontrollen (Crtl-Mouse Wheel Up) för att öka förstoringen av det avsnittet.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Filer i avsnittet "Nyligen öppnade modeller" i huvudfönstret kan inte öppna

#### <a name="issue"></a>Problem

Funktionen "Öppna från OneDrive" i förhandsversionen har tagits bort. Användare med "Nyligen öppnade modeller" som sparades på OneDrive får följande felmeddelande.

![OneDrive-funktionen har tagits bort](./media/threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Lösning

Användare av OneDrive uppmanas att använda Microsofts [OneDrive för Windows-klient](https://onedrive.live.com/about/en-us/download/) för att komma åt sina filer som lagras på OneDrive via standard- och "Öppna en modell"-dialogruta.

![OneDrive-funktionen har tagits bort](./media/threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Min organisation använder 2016-versionen av verktyget, kan jag använda Azure-stenciluppsättningen?

Ja, det kan du! [Azure-stenciluppsättningen är tillgänglig på GitHub](https://github.com/Microsoft/threat-modeling-templates/)och kan läsas in i 2016-versionen av verktyget. Om du vill skapa en ny modell med Azure-stenciluppsättningen använder du dialogrutan "Mall för nya modeller" på huvudmenyskärmen. TMT 2016 kan inte återge länkarna som finns i fälten "Möjliga mildranden" i Azure-stenciluppsättningen, därför kan du se länkar som visas som HTML-taggar.

![Azure Stencil Uppdateringar i 2016-klienten](./media/threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - Microsoft Windows 10
- .NET-version krävs
  - .NET 3.5.2
- Ytterligare krav
  - En Internetanslutning krävs för att ta emot uppdateringar av verktyget och mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentation för hotmodelleringsverktyget finns på [docs.microsoft.com](threat-modeling-tool.md)och innehåller information [om hur du använder verktyget](threat-modeling-tool-getting-started.md).

## <a name="next-steps"></a>Nästa steg

Hämta den senaste versionen av [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
