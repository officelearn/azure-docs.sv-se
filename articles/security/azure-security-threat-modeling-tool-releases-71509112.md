---
title: Threat Modeling Tool släpper - Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Dokumentera viktig information om threat modeling tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: bdf8b701567aaa3a0d9006333557bcec4f312723
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890924"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA-versionen 7.1.50911.2 - 9/12/2018

Vi är glada över att kunna presentera Microsoft Threat Modeling Tool är nu tillgänglig för nedladdning som en stöds allmänt tillgänglig (GA)-version. Den här versionen innehåller viktig sekretess och säkerhetsuppdateringar och felkorrigeringar, funktionsuppdateringar och stabilitet. Befintliga användare av förhandsversionen 2017 uppmanas att uppdatera till den senaste versionen via ClickOnce-tekniken vid öppning av klienten. För nya användare för verktyget [Klicka här för att ladda ned klienten](https://aka.ms/threatmodelingtool).

Den här versionen har vi avslutar stödet för förhandsversionen 2017 och rekommenderar att alla användare av uppdatering för förhandsversionen till GA-versionen. På eller efter den 15 oktober 2018, vi konfigurerar den minsta nödvändiga ClickOnce-versionen för Threat Modeling Tool och alla klienter i förhandsversion som krävs för att uppgradera.

I Microsoft Threat Modeling verktyget 2016, som är tillgänglig från den [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=49168), stöds förrän 1 oktober-2019 för kritiska säkerhetskorrigeringar endast fortfarande.

## <a name="feature-changes"></a>Funktionen ändringar

### <a name="azure-stencil-updates"></a>Azure stencil uppdateringar

Ytterligare Azure stenciler och deras associerade hot och åtgärder har lagts till stencil ange leverans med den här versionen. Betydande ändringar har gjorts i fokusområden av ”Azure App Services”, ”Azure-Databaserbjudanden” och ”Azure Storage”.

![Azure Stencil uppdateringar](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>OneDrive-integreringsfunktionen tas bort

”Spara till OneDrive”, ”öppna från OneDrive” och ”dela en länk” funktionerna i förhandsversionen har tagits bort. Användare av OneDrive uppmuntras att använda Microsofts [OneDrive för Windows](https://onedrive.live.com/about/en-us/download/) klienten att komma åt deras filer lagras på OneDrive via standardfil spara och öppna dialogrutor.

## <a name="notable-fixed-bugs-reported-by-customers"></a>Anmärkningsvärda felkorrigeringar som rapporteras av kunder

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>TMT förhandsversion kraschar verktyget när du använder mallen standard

- När en allmän stencil (till exempel ”allmän dataflöde”) har lagts till i ritläget ytan och genererar hot, kan verktyget krascha. Det här problemet har åtgärdats.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>TMT förhandsversion när jag sparar en rapport eller kopiera hot, är risknivåerna felaktiga

- Om en användare ändrar risknivån för specifika hot och sparar en rapport eller kopierar riskerna, kan risknivån återgå till ”hög”. Det här problemet har åtgärdats.

## <a name="known-issues-and-faq"></a>Kända problem och vanliga frågor och svar

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>Användare av högupplösta skärmar uppleva liten text i egenskaperna för hot

#### <a name="issue"></a>Problem

I vyn analys av verktyget om användaren har en högupplösta skärm som anges som standard till förstora för läsbarhet i Windows, visas avsnittet ”möjliga Mitigation(s)” i ett hot med liten text.

![Kända problem med högupplösta skärmar](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>Lösning

Användaren kan klickar du på minskning texten och använder standard Windows zoomkontrollen (CTRL-mus hjul upp) dit av avsnittet.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>Filer i avsnittet ”nyligen öppnade modeller” i huvudfönstret kanske inte kan öppna

#### <a name="issue"></a>Problem

Funktionen ”Öppna från OneDrive” i förhandsversionen har tagits bort. Användare med ”nyligen öppnade modeller” som har sparats till OneDrive får följande fel.

![OneDrive-funktionen har tagits bort](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>Lösning

Användare av OneDrive uppmuntras att använda Microsofts [OneDrive för Windows](https://onedrive.live.com/about/en-us/download/) klienten att komma åt deras filer lagras på OneDrive via standard- och ”öppna en modell” dialogrutan.

![OneDrive-funktionen har tagits bort](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>Min organisation använder 2016-versionen av verktyget, kan jag använda Azure stencil uppsättningen?

Ja, du kan! Den [Azure stencil är tillgängliga på github](https://github.com/Microsoft/threat-modeling-templates/), och kan inte läsas in i 2016-versionen av verktyget. Om du vill skapa en ny modell med Azure stencil, använder du dialogrutan ”mall för nya modeller” på skärmen huvudmenyn. TMT 2016 kan inte återges på länkarna som finns i fälten ”möjliga åtgärder” i Azure stencil uppsättningen, därför kan du se länkar som visas som HTML-taggar.

![Azure Stencil uppdateringar i 2016-klienten](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>Systemkrav

- Operativsystem som stöds
  - Microsoft Windows 10
- .NET-version som krävs
  - .NET 3.5.2
- Ytterligare krav
  - Internetanslutning krävs för att ta emot uppdateringar av verktyget samt mallar.

## <a name="documentation-and-feedback"></a>Dokumentation och feedback

- Dokumentation för Threat Modeling Tool finns på [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool), och innehåller information [om hur du använder verktyget](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Nästa steg

Ladda ned den senaste versionen av den [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
