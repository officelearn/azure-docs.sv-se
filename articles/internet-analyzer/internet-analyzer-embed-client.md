---
title: Bädda in Internet Analyzer Client | Microsoft-dokument
description: I den här artikeln får du lära dig hur du bäddar in JavaScript-klienten för Internet Analyzer i programmet.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74896372"
---
# <a name="embed-the-internet-analyzer-client"></a>Bädda in Internet Analyzer-klienten

Den här artikeln visar hur du bäddar in JavaScript-klienten i ditt program. Installation av den här klienten är nödvändig för att köra tester och ta emot styrkortsanalys. **Den profilspecifika JavaScript-klienten tillhandahålls efter att det första testet har konfigurerats.** Därifrån kan du fortsätta att lägga till eller ta bort tester i den profilen utan att behöva bädda in ett nytt skript. Mer information om Internet Analyzer finns i [översikten](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Innan du börjar

Internet Analyzer kräver åtkomst till Azure och andra Microsoft-tjänster för att fungera korrekt. Tillåt nätverksåtkomst `fpc.msedge.net` till och eventuella förkonfigurerade slutpunktsadresser (synliga via [CLI)](internet-analyzer-cli.md)innan du bäddar in klienten.

## <a name="find-the-client-script-url"></a>Hitta klientskript-URL:en

Skript-URL:en finns antingen via Azure-portalen eller Azure CLI efter att ett test har konfigurerats. Mer information finns i [Skapa en Internet Analyzer-resurs](internet-analyzer-create-test-portal.md).

Alternativ 1. I Azure-portalen använder du den [här länken](https://aka.ms/InternetAnalyzerPreviewPortal) för att öppna förhandsgranskningsportalsidan för Azure Internet Analyzer. Navigera till din Internet Analyzer-profil för att se skript-URL:en genom att gå till **Inställningar > Konfiguration**.

Alternativ 2. Kontrollera egenskapen `scriptFileUri` med Hjälp av Azure CLI.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Information om klienten

Skriptet genereras specifikt för din profil och dina tester. När skriptet har lästs in körs det på en fördröjning på 2 sekunder. Först kontaktar den Internet Analyzer-tjänsten för att hämta listan över slutpunkter som konfigurerats i dina tester. Den kör sedan mätningarna och överför de tidsinftade resultaten tillbaka till Internet Analyzer-tjänsten.

## <a name="client-examples"></a>Exempel på klient

De här exemplen visar några grundläggande metoder för att bädda in klienten JavaScript på din webbsida eller ditt program. Vi `0bfcb32638b44927935b9df86dcfe397` använder som ett exempel profil-ID för skript-URL.

### <a name="run-on-page-load"></a>Kör på sidan belastning
Den enklaste metoden är att använda skripttaggen inuti metataggblocket. Den här taggen kör skriptet en gång per sidinläsning.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor om Internet Analyzer](internet-analyzer-faq.md)
