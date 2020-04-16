---
title: Felsöka Azure Synapse Studio (förhandsversion)
description: Felsöka Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431324"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Felsökning av Azure Synapse Studio (förhandsversion)

Den här felsökningsguiden innehåller instruktioner om vilken information som ska tillhandahållas när du öppnar en supportbiljett om problem med nätverksanslutningen. Med rätt information kan vi eventuellt lösa problemet snabbare.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>Anslutningsproblem för SQL on-demand (förhandsversion)

### <a name="symptom-1"></a>Symptom 1

Alternativet "SQL on-demand" är nedtonat i listrutan "Anslut till".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Symptom 2

Om du kör frågan med "SQL on-demand" visas felmeddelandet "Det gick inte att upprätta anslutningen till servern".

![symptom2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

> [!NOTE] 
>    Följande felsökningssteg gäller Krom Edge och Chrome. Du kan använda andra webbläsare (till exempel Firefox) med samma felsökningssteg, men fönstret "Utvecklarverktyg" kan ha en annan layout än skärmbilderna i den här TSG:n. Använd om möjligt INTE klassisk Edge för felsökning, eftersom det kan visa felaktig information i vissa situationer.

Öppna panelen "Diagnostikinformation" och välj knappen "Ladda ned diagnostik". Behåll den nedladdade informationen för felrapportering. Du kan i stället kopiera "Sessions-ID" och bifoga det när du öppnar supportbiljetten.

![diagnostik-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Försök med felsökning igen genom att försöka med åtgärden som du utförde i Azure Synapse Studio.

- För symptom 1, välj "Uppdatera"-knappen till höger om rullgardinsmenyn "Använd databas" på fliken "SQL script" och kontrollera om du kan se "SQL on-demand".
- Om du vill ha symptom 2 kan du försöka köra frågan igen för att se om den körs.

Om problemet kvarstår trycker du på F12 i webbläsaren för att öppna "Utvecklarverktyg" (DevTools).

Växla till panelen "Nätverk" i fönstret "Utvecklarverktyg". Välj knappen "Rensa" i verktygsfältet på panelen "Nätverk" om det behövs.
Kontrollera att "Inaktivera cache" på panelen "Nätverk" är markerat.

Försök igen med åtgärden som du utförde i Azure Synapse Studio. Nya objekt kan visas i listan "Nätverk" i "Utvecklarverktyg". Observera din nuvarande systemtid att tillhandahålla i supportbiljetten.

![nätverkspanel](media/troubleshooting-synapse-studio/network-panel.png)

Leta reda på objektet vars url-kolumn matchar följande mönster:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Där *[A*] är ditt arbetsytenamn, och "-ondemand" kan vara "-sqlod" och där [*B*] ska vara ett databasnamn, till exempel "master". Det bör finnas högst två objekt med samma URL-värde men olika metodvärden. ALTERNATIV OCH POST. Kontrollera om dessa två objekt har "200" eller "20x" under statuskolumnen, där "x" kan vara en siffra.

Om någon av dem har något annat än "20x" och:

- status börjar med "(misslyckades)", antingen vidga "Status" kolumnen eller hovra pekaren över statustexten för att se hela texten. Inkludera texten och/eller skärmbilden när supporten öppnas.

    ![status-text](media/troubleshooting-synapse-studio/status-text.png)

    - Om du ser ERR_NAME_NOT_RESOLVED och du har skapat arbetsytan inom 10 minuter väntar du i 10 minuter och försöker igen för att se om problemet fortfarande finns.
    - Om ERR_INTERNET_DISCONNECTED eller ERR_NETWORK_CHANGED visas kan det tyda på att nätverksanslutningen för datorn har problem. Kontrollera nätverksanslutningen och försök igen.
    - Om du ser ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR eller andra felkoder som innehåller "SSL", kan det tyda på att din lokala SSL-konfiguration har problem eller så har nätverksadministratören blockerat åtkomsten till SQL-servern på begäran. Öppna en supportbiljett och bifoga felkoden i beskrivningen.
    - Om du ser ERR_NETWORK_ACCESS_DENIED kan du behöva kontrollera med administratören om din lokala brandväggsprincip har blockerat åtkomsten till antingen *.database.windows.net domän eller fjärrport 1443.
    - Du kan också prova samma åtgärd direkt på en annan dator och/eller nätverksmiljö för att utesluta ett problem med nätverkskonfigurationen på datorn.

- status är "40x", "50x", eller andra nummer, välj på objektet (s) för att se detaljerna. Du bör se objektinformationen till höger. Hitta avsnittet "Svarshuvud". Kontrollera sedan om det finns ett objekt med namnet "access-control-allow-origin". Om så är fallet kontrollerar du om det har något av följande värden:

    - `*`(enkel asterisk)
    - https://web.azuresynapse.net/(eller annat värde som texten i webbläsarens adressfält börjar med)

Om svarshuvudet innehåller ett av ovanstående värden betyder det att vi redan borde ha samlat in felinformationen. Du kan öppna en supportbiljett om det behövs och bifoga skärmbilden av artikelinformationen.

Om du inte kan se sidhuvudet, eller om huvudet inte har något av värdena ovan, bifogar du en skärmbild av artikelinformationen när du öppnar biljetten.

![artikelinformation](media/troubleshooting-synapse-studio/item-details.png)

Om stegen ovan inte löser problemet kan du behöva öppna en supportbiljett. När du skickar in supportbiljetten ska du inkludera "Sessions-ID" eller "Diagnostikinformation" som hämtats i början av den här guiden.

När du anmäler problemet kan du eventuellt ta en skärmbild av fliken "Konsol" i "Utvecklarverktyg" och bifoga den till supportbiljetten. Bläddra innehållet och ta mer än en skärmdump om det behövs för att fånga hela meddelandet.

![utvecklare-verktygskonsol](media/troubleshooting-synapse-studio/developer-tool-console.png)

Om du bifogar skärmdumpar, ange tid (eller ett uppskattat tidsintervall) för när du tog skärmdumpar. Det kommer att hjälpa oss när vi undersöker problemet.

Vissa webbläsare har stöd för att visa tidsstämplar på fliken "Konsol". För Krom Edge/Chrome öppnar du dialogrutan "Inställningar" i "Utvecklarverktyg" och kontrollerar "Visa tidsstämplar" på fliken "Inställningar".

![inställningar för utvecklare-verktygskonsol](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![show-time-stämpel](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Nästa steg
Om de föregående stegen hjälper inte till att lösa problemet [Skapa en supportbiljett](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
