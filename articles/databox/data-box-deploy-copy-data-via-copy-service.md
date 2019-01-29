---
title: Kopiera data till Microsoft Azure Data Box via SMB | Microsoft Docs
description: Lär dig hur du kopierar data till Azure Data Box via datakopieringstjänsten
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: a71635abd036bb89546dd3421af97cd9b88f4327
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440053"
---
# <a name="tutorial-use-data-copy-service-to-directly-ingest-data-into-azure-data-box-preview"></a>Självstudier: Använda datakopieringstjänsten för att direkt mata in data i Azure Data Box (förhandsversion)

I den här självstudien beskrivs hur du matar in data med hjälp av datakopieringstjänst utan behov av en mellanliggande värd. Datakopieringstjänsten körs lokalt på Data Box, ansluter till din NAS-enhet via SMB och kopierar data till Data Box.

Använd datakopieringstjänsten:

- I NAS-miljöer (nätverksansluten lagring) där de mellanliggande värdarna kanske inte är tillgängliga.
- Med små filer som kräver veckor för inmatning och uppladdning av data. Den här tjänsten förbättrar avsevärt tiden för inmatning och uppladdning av data.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kopiera data till Data Box
> * Förbered för att skicka Data Box.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

1. Du har slutfört självstudien [: Konfigurera Azure Data Box](data-box-deploy-set-up.md).
2. Du har fått din Data Box-enhet och orderstatusen i portalen är **Levererad**.
3. Du har autentiseringsuppgifterna för den NAS-källenhet som du ansluter till för datakopiering.
4. Du har anslutning till ett höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE anslutning inte är tillgänglig kan en 1 GbE datalänk användas, men kopieringshastigheten påverkas.

## <a name="copy-data-to-data-box"></a>Kopiera data till Data Box

När du har anslutning till NAS är nästa steg att kopiera data. Granska följande innan du kopierar data:

- När du kopierar data ser du till att datastorleken överensstämmer med storleksbegränsningarna som beskrivs i avsnittet om [Azure Storage- och Data Box-gränser](data-box-limits.md).
- Om data som laddas upp av Data Box samtidigt laddas upp av andra program utanför Data Box kan detta resultera i fel med uppladdningsjobb och skadade data.
- Om data omsätts när datakopieringstjänsten läser dem kan det uppstå fel eller skadade data.

För att kopiera data med hjälp av datakopieringstjänsten behöver du skapa ett jobb. Följ dessa steg för att skapa ett jobb för datakopiering.

1. I det lokala webbgränssnittet på din Data Box går du till **Hantera > Kopiera data**.
2. På sidan **Kopiera data** klickar du på **Skapa**.

    ![Klicka på **Skapa** på sidan **Kopiera data**](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. I dialogrutan **Configure and start** (Konfigurera och starta) anger du följande indata.
    
    |Fält                          |Värde    |
    |-------------------------------|---------|
    |Jobbnamn                       |Ett unikt namn med färre än 230 tecken för jobbet. Följande tecken tillåts inte i jobbnamnet: \<, \>, \|, \?, \*, \\, \:, \/ och \\\.         |
    |Källplats                |Ange SMB-sökvägen till datakällan i formatet: `\\<ServerIPAddress>\<ShareName>` eller `\\<ServerName>\<ShareName>`.        |
    |Användarnamn                       |Användarnamn för åtkomst till datakällan.        |
    |Lösenord                       |Lösenord för åtkomst till datakällan.           |
    |Mållagringskonto    |Välj mållagringskonto för uppladdning av data i den nedrullningsbara listan.         |
    |Mållagringstyp       |Välj mållagringstyp bland blockblob, sidblob eller Azure Files.        |
    |Målcontainer/-resurs    |Ange namnet på containern eller resursen för uppladdning av data till mållagringskontot. Namnet kan vara ett resursnamn eller ett containernamn. Exempel: `myshare` eller `mycontainer`. Du kan även ange i formatet `sharename\directory_name` eller `containername\virtual_directory_name` i molnet.        |
    |Matchningsmönster för filkopiering    | Ange matchningsmönster för filnamn på följande två sätt.<ul><li>**Använda uttryck med endast jokertecken** `*` och `?` stöds i uttryck med jokertecken. Till exempel matchar det här uttrycket `*.vhd` alla filer som har filnamnstillägget .vhd. På samma sätt matchar `*.dl?` alla filer vars filnamnstillägg är antingen `.dl` eller `.dll`. Dessutom matchar `*foo` alla filer vars namn slutar med `foo`.<br>Du kan ange uttryck med jokertecken direkt i fältet. Som standard behandlas det värde som anges i fältet som uttryck med jokertecken.</li><li>**Använda reguljära uttryck** – POSIX-baserade reguljära uttryck stöds. Till exempel matchar det reguljära uttrycket `.*\.vhd` alla filer som har filnamnstillägget `.vhd`. För reguljära uttryck anger du `<pattern>` direkt som `regex(<pattern>)`. <li>Mer information om reguljära uttryck finns i [snabbreferensen för reguljära uttryck](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |Filoptimering              |När det här är aktiverat packas filerna vid inmatningen. Detta förbättrar datakopieringen för små filer.        |
 
4. Klicka på **Starta**. Indata valideras, och om validering lyckas startas ett jobb. Det kan ta några minuter för jobbet att startas.

    ![Starta ett jobb från dialogrutan Konfigurera jobb och starta](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Ett jobb med de angivna inställningarna skapas. Markera kryssrutan så kan du pausa och återuppta, avbryta eller starta om ett jobb.

    ![Hantera ett jobb via sidan Kopiera data](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Du kan pausa det här jobbet om det påverkar NAS-resurserna under tider med hög belastning.

        ![Pausa ett jobb](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Du kan återuppta jobbet senare under tider med låg belastning.

        ![Återuppta ett jobb](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Du kan avbryta ett jobb när som helst.

        ![Avbryt ett jobb](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png) En bekräftelse krävs när du avbryter ett jobb.

        ![Bekräfta jobbavbrott](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Om du avbryter ett jobb tas data som redan har kopierats inte bort. Om du vill ta bort alla data som du har kopierat på din Data Box återställer du enheten.

        ![Återställa enhet](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Om du avbryter eller pausar ett jobb kan stora filer som kopieras förbli halvt kopierade. De här filerna laddas upp i samma tillstånd till Azure. När du försöker avbryta eller pausa bör du kontrollera att filerna kopieras korrekt. För att validera filerna tittar du på SMB-resurser eller laddar ned BOM-filen.

    - Du kan starta om ett jobb om det plötsligt misslyckades på grund av ett tillfälligt fel, till exempel ett glapp i nätverket. Du kan inte starta om ett jobb om den har nått ett avslutat tillstånd som har slutförts eller slutfördes med fel. Felen kan bero på problem med eller filnamngivning eller filstorlek. De här felen loggas, men jobbet kan inte startas om när det har slutförts.

        ![Starta om ett jobb som misslyckades](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Om det uppstår ett fel och jobbet inte kan startas om laddar du ned felloggarna och tittar på felet i loggfilerna. När du har åtgärdat problemet kan du skapa ett nytt jobb för att kopiera filerna. Du kan även [kopiera filerna över SMB](data-box-deploy-copy-data.md).
    
    - I den här versionen kan du inte ta bort ett jobb.
    
    - Du kan skapa obegränsade jobb men köra högst 10 jobb parallellt åt gången.
    - Om filoptimering är på packas de små filerna vid inmatningen för att förbättra prestanda för kopiering. I de här fallen ser du en packad fil (GUID som namn) såsom det visas i följande skärmbild.

        ![Exempel på en packad fil](media/data-box-deploy-copy-data-via-copy-service/packed-file-on-ingest.png)

6. När jobbet pågår på sidan **Kopiera data**:

    - I kolumnen **Status** kan du visa statusen för kopieringsjobbet. Statusen kan vara:
        - **Körs**
        - **Misslyckades**
        - **Lyckades**
        - **Pausar**
        - **Pausat**
        - **Avbryts**
        - **Avbrutet**
        - **Slutfört med fel**
    - I kolumnen **Filer** ser du antalet och storleken på det totala antal filer som kopieras.
    - I kolumnen **Bearbetade** ser du antalet och storleken på filer som bearbetas.
    - I kolumnen **Information** klickar du på **Visa** om du vill se jobbinformation.
    - Om det inträffar några fel under kopieringsprocessen enligt vad som visas i kolumnen **Antal fel** går du till kolumnen **Fellogg** och laddar ned felloggarna för felsökning.

Vänta tills kopieringsjobben är klara. Eftersom vissa fel endast loggas på sidan **Anslut och kopiera** kontrollerar du att kopieringsjobben har slutförts utan fel innan du går vidare till nästa steg.

![Inga fel på sidan **Anslut och kopiera**](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

För att säkerställa dataintegriteten beräknas kontrollsumman infogat när data kopieras. När kopieringen är klar kontrollerar du det använda utrymmet och det lediga utrymmet på enheten.
    
![Kontrollera ledigt och använt utrymme på instrumentpanelen](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

När kopieringsjobbet är klart kan du gå till **Förbered för att skicka**.

>[!NOTE]
> Förbered för att skicka kan inte köras medan kopieringsjobb pågår.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Kopiera data till Data Box
> * Förbereda för att skicka Data Box

Gå vidare till nästa självstudie och lär dig hur du skickar tillbaka din Data Box-enhet till Microsoft.

> [!div class="nextstepaction"]
> [Skicka din Azure Data Box till Microsoft](./data-box-deploy-picked-up.md)

