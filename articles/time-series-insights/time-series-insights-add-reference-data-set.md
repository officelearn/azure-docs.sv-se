---
title: Så här lägger du till referensdatauppsättningar i din miljö – Azure Time Series Insights | Microsoft-dokument
description: I den här artikeln beskrivs hur du lägger till en referensdatauppsättning för att öka data i azure time series insights-miljön.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087259"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Skapa en referensdatauppsättning för time series insights-miljön med hjälp av Azure-portalen

I den här artikeln beskrivs hur du lägger till en referensdatauppsättning i azure time series insights-miljön. Referensdata är användbara för att ansluta till källdata för att öka värdena.

En referensdatauppsättning är en samling objekt som utökar händelserna från händelsekällan. Time Series Insights ingress-motor ansluter till varje händelse från händelsekällan med motsvarande datarad i referensdatauppsättningen. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på kolumnerna primärnyckel som definierats i referensdatauppsättningen.

Referensdata är inte sammanfogade retroaktivt. Således matchas endast aktuella och framtida ingressdata och sammanfogas till referensdatumuppsättningen, när den har konfigurerats och överförts.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Läs mer om Time Series Insight referensdatamodell.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Lägga till en referensdatauppsättning

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Hitta din befintliga Azure Time Series Insights-miljö. Välj **Alla resurser** på menyn till vänster i Azure-portalen. Välj Time Series Insights-miljö.

1. Välj sidan **Översikt.** Expandera avsnittet **Essentials** högst upp på sidan för att hitta url:en för Utforskaren för **Tidsseriestatistik** och öppna länken.  

   [![Avsnittet Expandera väsentligheter](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Visa utforskaren för din Time Series Insights-miljö.

1. Expandera miljöväljaren i Utforskaren för Time Series Insights. Välj den aktiva miljön. Välj referensdataikonen längst upp till höger på utforskarsidan.

   [![Lägga till referensdata](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Välj knappen **+ Lägg till en datauppsättning** om du vill börja lägga till en ny datauppsättning.

   [![Lägga till datauppsättning](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. På sidan **Ny referensdatauppsättning** väljer du formatet för data:

   - Välj **CSV** för kommaavgränsade data. Den första raden behandlas som en rubrikrad.
   - Välj **JSON Array** för JavaScript-objekt notation (JSON) formaterade data.

   [![Välj dataformat.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Ange data med en av de två metoderna:

   - Klistra in data i textredigeraren. Välj sedan **knappen Parsa referensdata.**
   - Välj knappen **Välj fil** om du vill lägga till data från en lokal textfil.

   Klistra till exempel in CSV-data: [ ![Inklist CSV-data](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Klistra till exempel in JSON-matrisdata: Klistra [ ![in JSON-data](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Om det finns ett fel med att tolka datavärdena visas felet i `CSV parsing error, no rows extracted`rött längst ned på sidan, till exempel .

1. När data har tolkats visas ett datarutnät som visar kolumnerna och raderna som representerar data. Granska datarutnätet för att säkerställa korrekthet.

   [![Granska referensdata](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Granska varje kolumn för att förstå vilken datatyp som antas och ändra datatypen om det behövs.  Välj datatypssymbolen i **#** kolumnrubriken: för dubbla (numeriska data), **T| F** för boolesk, eller **Abc** för sträng.

   [![Välj datatyper på kolumnrubrikerna.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Byt namn på kolumnrubrikerna om det behövs. Nyckelkolumnnamnet är nödvändigt för att ansluta till motsvarande egenskap i händelsekällan. 

   > [!IMPORTANT]
   > Kontrollera att referensdatanyckelkolumnnamnen matchar exakt händelsenamnet med inkommande data, inklusive skiftlägeskänslighet. Kolumnnamnen som inte är nyckeltal används för att utöka inkommande data med motsvarande referensdatavärden.

1. Skriv ett värde i fältet **Filtrera raderna...** om du vill granska specifika rader efter behov. Filtret är användbart för att granska data, men används inte när data överförs.

1. Namnge datauppsättningen genom att fylla i **namnfältet Datauppsättning** ovanför datarutnätet.

    [![Ge datauppsättningen ett namn.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Ange kolumnen **Primärnyckel** i datauppsättningen genom att välja listrutan ovanför datarutnätet.

    [![Markera nyckelkolumnerna.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Valfritt)** Välj **+** knappen om du vill lägga till en sekundär nyckelkolumn, som en sammansatt primärnyckel. Om du behöver ångra markeringen väljer du det tomma värdet i listrutan för att ta bort den sekundära nyckeln.

1. Om du vill ladda upp data väljer du knappen **Ladda upp rader.**

    [![Ladda upp rader och bekräfta data.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Sidan bekräftar den slutförda överföringen och visar meddelandet **Datauppsättningen med datauppsättningen**.

    > [!WARNING]
    > Kolumner eller egenskaper som delas mellan referensdatauppsättningar visar ett fel för uppladdning **av dubblettgenskapsnamn.** Felet förhindrar inte en lyckad överföring av referensdatauppsättningarna. Den kan tas bort genom att kombinera rader som delar det duplicerade egenskapsnamnet.

1. Välj **Lägg till en rad,** **Massimportrader**eller **Lägg till en kolumn** om du vill lägga till fler referensdatavärden efter behov.

    [![Lägg till en rad, Massimportrader eller Lägg till en kolumn.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Varje rad som delar en unik nyckel med en annan rad kommer att ha sina kolumner åsidosatta av den sista raden till som delar den unika nyckeln.

   > [!NOTE]
   > Tillagda rader behöver **inte** vara *rektangulära* – de kan ha färre, större eller varierande kolumner från de andra posterna i referensdatauppsättningen.

## <a name="next-steps"></a>Nästa steg

* [Hantera referensdata](time-series-insights-manage-reference-data-csharp.md) programmässigt.

* För den fullständiga API-referensen finns [i API-dokumentet för referensdata.](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)
