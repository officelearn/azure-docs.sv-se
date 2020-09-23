---
title: Felsöka Synapse Studio (för hands version)
description: Felsöka Azure Synapse Studio
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f859700be32bda5d8245429076c2359d1adf9d5a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988069"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Fel sökning för Azure Synapse Studio (för hands version)

Den här fel söknings guiden innehåller instruktioner om vilken information som ska tillhandahållas när du öppnar ett support ärende om problem med nätverks anslutningen. Med rätt information kan vi eventuellt lösa problemet snabbare.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>Problem med tjänst anslutning för SQL på begäran (för hands version)

### <a name="symptom-1"></a>Symptom 1

Alternativet "SQL on-demand" är nedtonat i list rutan "Anslut till".

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Symptom 2

Genom att köra frågan med "SQL på begäran" får du fel meddelandet "Det gick inte att upprätta anslutningen till servern".

![Skärm bilden visar att det inte gick att upprätta en anslutning till Server meddelandet.](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

> [!NOTE] 
>    Följande fel söknings steg är till för krom Edge och Chrome. Du kan använda andra webbläsare (till exempel FireFox) med samma fel söknings steg, men fönstret "utvecklarverktyg" kan ha en annan layout än skärm bilderna i den här TSG. Om möjligt ska du inte använda klassisk gräns för fel sökning, eftersom det kan visa felaktig information i vissa situationer.

Öppna panelen diagnostisk information och välj knappen "Ladda ned diagnostik". Behåll den nedladdade informationen för fel rapportering. Du kan i stället kopiera "sessions-ID" och koppla det när du öppnar support ärendet.

![diagnostik-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Starta fel sökningen genom att försöka utföra åtgärden igen i Azure Synapse Studio.

- För symptom 1 väljer du knappen Uppdatera till höger om List rutan "Använd databas" på fliken "SQL-skript" och kontrollerar om du kan se "SQL på begäran".
- Prova att köra frågan igen för att se om den körs korrekt, för symptom 2.

Om problemet fortfarande finns trycker du på F12 i webbläsaren för att öppna "Utvecklarverktyg" (DevTools).

I fönstret Utvecklarverktyg växlar du till panelen nätverk. Välj knappen "Rensa" i verktygsfältet på panelen "nätverk" om det behövs.
Kontrol lera att "inaktivera cache" i "Network"-panelen är markerat.

Försök utföra åtgärden igen i Azure Synapse Studio. Du kan se nya objekt som visas i listan "nätverk" i "Utvecklarverktyg". Notera din aktuella system tid för att tillhandahålla support ärendet.

![Skärm bild som visar DevTools-fönster med nätverk och inaktivera cacheminne online har valts.](media/troubleshooting-synapse-studio/network-panel.png)

Hitta objektet vars URL-kolumn matchar följande mönster:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Där [*A*] är namnet på din arbets yta och "-OnDemand" kan vara "-sqlod" och var [*B*] ska vara ett databas namn, till exempel "Master". Det får finnas högst två objekt med samma URL-värde men olika metod värden. ALTERNATIV och POST. Kontrol lera om dessa två objekt har "200" eller "20x" under kolumnen status, där "x" kan vara valfri siffra.

Om någon av dem har något annat än "20x" och:

- status börjar med "(misslyckad)", antingen utvidgar kolumnen "status" eller hovra över status texten för att se hela texten. Ta med texten och/eller skärm bilden när du öppnar support ärendet.

    ![Skärm bilden visar resultatet, inklusive ett felaktigt värde i kolumnen Status.](media/troubleshooting-synapse-studio/status-text.png)

    - Om du ser ERR_NAME_NOT_RESOLVED och du har skapat din arbets yta inom 10 minuter, vänta i 10 minuter och försök att se om problemet fortfarande finns.
    - Om du ser ERR_INTERNET_DISCONNECTED eller ERR_NETWORK_CHANGED kan det bero på att din DATORs nätverks anslutning har problem. Kontrol lera nätverks anslutningen och försök igen.
    - Om du ser ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR eller andra felkoder som innehåller "SSL", kan det tyda på att den lokala SSL-konfigurationen har problem eller att nätverks administratören har blockerat åtkomst till SQL-servern på begäran. Öppna ett support ärende och bifoga felkoden i beskrivningen.
    - Om du ser ERR_NETWORK_ACCESS_DENIED kan du behöva kontrol lera med administratören om den lokala brand Väggs principen har blockerat åtkomst till antingen *. database.windows.net-domän eller Fjärrport 1443.
    - Du kan också prova samma åtgärd direkt på en annan dator och/eller nätverks miljö för att utesluta ett problem med nätverks konfigurationen på din dator.

- status är "40x", "50 gånger" eller andra siffror, Välj objekt för att se information. Du bör se objekt informationen till höger. Hitta avsnittet "svars huvud". kontrol lera sedan om ett objekt med namnet "åtkomst kontroll-Allow-Origin" finns. I så fall, kontrol lera om det har något av följande värden:

    - `*` (enkel asterisk)
    - https://web.azuresynapse.net/ (eller annat värde som texten i webbläsarens Adress fält börjar med)

Om svars huvudet innehåller ett av ovanstående värden, innebär det att vi redan borde ha samlat in information om problemet. Du kan öppna ett support ärende om det behövs och eventuellt bifoga skärm bilden med objekt detaljerna.

Om du inte kan se rubriken eller om rubriken inte har något av värdena som anges ovan, bifoga en skärm bild av objekt detaljerna när du öppnar biljetten.

![Skärm bild som visar DevTools-fönstret med U R L markerat i svarshuvuden.](media/troubleshooting-synapse-studio/item-details.png)

Om stegen ovan inte löser problemet kan du behöva öppna ett support ärende. När du skickar in ditt support ärende ska du ta med "sessions-ID" eller "diagnostikinformation" som hämtades i början av den här guiden.

När du rapporterar problemet kan du eventuellt ta en skärm bild av din "konsol"-flik i "Utvecklarverktyg" och koppla den till support ärendet. Rulla innehållet och ta mer än en skärm bild om det behövs för att avbilda hela meddelandet.

![Skärm bild som visar DevTools-fönstret, storlek för att visa hela meddelandet för en möjlig skärm bild.](media/troubleshooting-synapse-studio/developer-tool-console.png)

Om du kopplar skärm bilder anger du tiden (eller ett uppskattat tidsintervall) för när du tog skärm bilderna. Det hjälper oss när du tittar på problemet.

Vissa webbläsare har stöd för att Visa tidsstämplar på fliken "konsol". För krom Edge/Chrome öppnar du dialog rutan "Inställningar" i "Utvecklarverktyg" och kontrollerar "Visa tidsstämplar" på fliken "Preferences".

![Skärm bild som visar DevTools-fönstret med inställningar som valts i en snabb meny.](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![Skärm bild som visar DevTools fönster inställningar med Visa valda tidsstämplar.](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Nästa steg
Om föregående steg inte hjälper till att lösa problemet [skapar du ett support ärende](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
