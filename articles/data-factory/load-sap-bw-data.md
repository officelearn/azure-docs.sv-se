---
title: Läsa in data från SAP Business Warehouse med hjälp av Azure Data Factory | Microsoft Docs
description: Använd Azure Data Factory för att kopiera data från SAP Business Warehouse (BW)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9a123ed45b5857aa40fc9853a95c528833ba8aa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549244"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse med hjälp av Azure Data Factory

Den här artikeln visar hur du använder Azure Data Factory för att kopiera data från SAP Business Warehouse (BW) via öppna hubben till Azure Data Lake Storage Gen2. Du kan använda en liknande process för att kopiera data till andra [datalager för mottagare som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Allmän information om hur du kopierar data från SAP BW, inklusive SAP BW Open Hub-integrering och delta extrahering av flow finns i [kopiera data från SAP Business Warehouse via öppna hubben med hjälp av Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

- **Azure Data Factory**: Om du inte har något, följer du stegen för att [skapa en datafabrik](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Open Hub mål (OHD) med måltypen ”databastabell”**: Skapa en OHD eller för att kontrollera att din OHD är korrekt konfigurerad för Data Factory-integrering finns i den [konfigurationer för SAP BW Open Hub mål](#sap-bw-open-hub-destination-configurations) i den här artikeln.

- **SAP BW-användaren måste ha följande behörigheter**:

  - Auktorisering för Fjärrfunktionsanrop (RFC) och SAP BW.
  - Behörigheter till ”kör” aktivitet av den **S_SDSAUTH** auktorisering objekt.

- **En [integration runtime (IR) med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime) med SAP .NET connector 3.0**. Följ anvisningarna för installation:

  1. Installera och registrera den lokala integreringskörningen, version 3,13 eller senare. (Den här processen beskrivs senare i den här artikeln.)

  2. Ladda ned den [64-bitars SAP-Anslutningsappen för Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) från SAP: s webbplats och installera den på samma dator som lokal IR. Under installationen kan du kontrollera att du väljer **installera sammansättningar GAC** i den **installationsanvisningarna** dialogrutan som visas i följande bild:

     ![Konfigurera SAP .NET Connector-dialogrutan](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Göra en fullständig kopia från SAP BW Open Hub

Gå till din datafabrik i Azure-portalen. Välj **författare och Övervakare** att öppna Användargränssnittet för Data Factory på en separat flik.

1. På den **nu sätter vi igång** väljer **kopieringsdata** att öppna verktyget kopieringsdata.

2. På den **egenskaper** anger en **aktivitetsnamn**, och välj sedan **nästa**.

3. På den **källdatalagret** väljer **+ Skapa ny anslutning**. Välj **SAP BW Open Hub** connector-galleriet och välj sedan **Fortsätt**. Om du vill filtrera anslutningsapparna du kan skriva **SAP** i sökrutan.

4. På den **ange SAP BW Open hubbanslutning** utför dessa steg för att skapa en ny anslutning.

   ![Skapa SAP BW Open Hub-länkad tjänst, sida](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Från den **Anslut via integration runtime** väljer du en befintlig lokal IR. Eller välj att skapa en om du inte har något ännu.

      Om du vill skapa en ny lokal IR, Välj **+ ny**, och välj sedan **egenvärdbaserade**. Ange en **namn**, och välj sedan **nästa**. Välj **Expressinstallation** att installera på den aktuella datorn, eller följa den **manuell installation** instruktionerna.

      Som vi nämnde i [krav](#prerequisites), se till att du har SAP-Anslutningsappen för Microsoft .NET 3.0 installerat på samma dator där den lokala Integreringskörningen körs.

   2. Fyll i SAP BW **servernamn**, **systemnummer**, **klient-ID,** **språk** (om än **EN**) , **Användarnamn**, och **lösenord**.

   3. Välj **Testanslutning** att verifiera inställningarna och välj sedan **Slutför**.

   4. En ny anslutning skapas. Välj **Nästa**.

5. På den **Välj Öppna Hub mål** sidan, bläddra öppna Hub-mål som är tillgängliga i din SAP BW. Välj OHD att kopiera data från och välj sedan **nästa**.

   ![Välj SAP BW Open Hub måltabell](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Ange ett filter om du behöver en. Om din OHD innehåller endast data från körningen av en enda dataöverföring processen (DTP) med en enskild begäran-ID, eller om du vet att din DTP är klar och du vill kopiera data, avmarkera de **undanta senaste begäran** markerar du kryssrutan.

   Mer information om de här inställningarna i den [konfigurationer för SAP BW Open Hub mål](#sap-bw-open-hub-destination-configurations) i den här artikeln. Välj **verifiera** att kontrollera vilka data som ska returneras. Välj sedan **Nästa**.

   ![Konfigurera SAP BW Open Hub filter](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. På den **måldatalager** väljer **+ Skapa ny anslutning** > **Azure Data Lake Storage Gen2**  >   **Fortsätta**.

8. På den **ange Azure Data Lake Storage anslutning** utför dessa steg för att skapa en anslutning.

   ![Skapa en sida för den länkade tjänsten av ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Välj ditt Data Lake Storage Gen2-kompatibla konto från den **namn** listrutan.
   2. Välj **Slutför** att skapa anslutningen. Välj sedan **Nästa**.

9. På den **Välj utdatafil eller mapp** anger **copyfromopenhub** som mappnamn utdata. Välj sedan **Nästa**.

   ![Välj sidan installationsmapp för utdata](media/load-sap-bw-data/choose-output-folder.png)

10. På den **filformat inställningen** väljer **nästa** att använda standardinställningarna.

    ![Ange mottagare sidan](media/load-sap-bw-data/specify-sink-format.png)

11. På den **inställningar** expanderar **prestandainställningar**. Ange ett värde för **graden av parallellitet kopia** , till exempel 5 för att läsa in från SAP BW parallellt. Välj sedan **Nästa**.

    ![Konfigurera inställningar](media/load-sap-bw-data/configure-copy-settings.png)

12. På den **sammanfattning** granskar du inställningarna. Välj sedan **Nästa**.

13. På den **distribution** väljer **övervakaren** att övervaka pipelinen.

    ![Distributionssida](media/load-sap-bw-data/deployment.png)

14. Observera att den **övervakaren** fliken till vänster på sidan väljs automatiskt. Den **åtgärder** -kolumnen innehåller länkar för att visa information om aktivitet och kör och köra pipelinen på nytt.

    ![Pipeline-övervakningsvyn](media/load-sap-bw-data/pipeline-monitoring.png)

15. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningen, väljer **visa Aktivitetskörningar** i den **åtgärder** kolumn. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till vyn pipeline-körningar, Välj den **Pipelines** länken längst upp. Om du vill uppdatera listan väljer du **Uppdatera**.

    ![Aktivitetsövervakning skärmen](media/load-sap-bw-data/activity-monitoring.png)

16. För att övervaka körning-information för varje kopieringsaktiviteten, Välj den **information** länken, vilket är en nedan glasögonikonen **åtgärder** i vyn övervakning av aktivitet. Tillgänglig information är datavolym som kopieras från källan till mottagare, dataflöde, utförande och varaktighet och konfigurationer som används.

    ![Aktivitetsövervakning information](media/load-sap-bw-data/activity-monitoring-details.png)

17. Visa den **högsta ID för begäran**går du tillbaka till aktivitetsövervakning vyn och välj **utdata** under **åtgärder**.

    ![Skärmen för utdata av aktivitet](media/load-sap-bw-data/activity-output.png)

    ![Detaljer för aktiviteten utdata](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>Göra en inkrementell kopia från SAP BW Open Hub

> [!TIP]
> Se [öppna hubben för SAP BW connector delta extrahering flow](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) att lära dig hur anslutningstjänsten SAP BW Open Hub i Data Factory kopierar inkrementella data från SAP BW. Den här artikeln kan också hjälpa dig att förstå grundläggande kopplingskonfiguration.

Nu ska vi fortsätta att konfigurera inkrementell kopia från SAP BW Open Hub.

Inkrementell kopia använder en ”hög vattenstämpel” som baseras på den **fråge-ID**. Detta ID genereras automatiskt i SAP BW Open Hub mål av DTP. I följande diagram visas det här arbetsflödet:

![Flödesschema för inkrementell kopia arbetsflöde](media/load-sap-bw-data/incremental-copy-workflow.png)

Om data factory **nu sätter vi igång** väljer **skapa pipeline från mall** att använda den inbyggda mallen.

1. Sök efter **SAP BW** att hitta och välj den **stegvis kopiera från SAP BW till Azure Data Lake Storage Gen2** mall. Den här mallen kopierar data till Azure Data Lake Storage Gen2. Du kan använda ett liknande arbetsflöde för att kopiera till andra typer av mottagare.

2. På huvudsidan för mallens väljer eller skapar följande tre anslutningar och välj sedan **Använd den här mallen** i det nedre högra hörnet i fönstret.

   - **Azure Blob storage**: I den här genomgången ska vi använda Azure Blob storage för att lagra högvattenmärket, vilket är den *max kopieras ID för förfrågan*.
   - **SAP BW Open Hub**: Det här är källan för att kopiera data från. Referera till den tidigare genomgången fullständig kopia för detaljerad konfiguration.
   - **Azure Data Lake Storage Gen2**: Det här är mottagare att kopiera data till. Referera till den tidigare genomgången fullständig kopia för detaljerad konfiguration.

   ![Inkrementell kopia från SAP BW-mall](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Den här mallen skapar en pipeline med följande tre aktiviteter och gör dem har länkats till om de lyckas: *Lookup*, *kopiera Data*, och *Web*.

   Gå till pipelinen **parametrar** fliken. Du ser alla konfigurationer som du måste ange.

   ![Inkrementell kopia från SAP BW-konfiguration](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Ange öppna tabell hubbnamnet för att kopiera data från.

   - **ADLSGen2SinkPath**: Ange sökvägen för att kopiera data till Azure Data Lake Storage Gen2. Om sökvägen inte finns skapas kopieringsaktiviteten Data Factory en sökväg under körning.

   - **HighWatermarkBlobPath**: Ange sökvägen för att lagra värdet för hög-vattenstämpeln som `container/path`.

   - **HighWatermarkBlobName**: Ange blobnamnet för att lagra värdet för högvattenmärket som `requestIdCache.txt`. I Blob storage, gå till motsvarande sökvägen till HighWatermarkBlobPath + HighWatermarkBlobName, till exempel *container/path/requestIdCache.txt*. Skapa en blob med innehåll 0.

      ![Blobbinnehåll](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: I den här mallen använder vi WebActivity för att anropa Azure Logic Apps för att ange storleksgränsen för hög i Blob storage. Eller du kan använda Azure SQL Database för att lagra den. Använda en lagrad procedur-aktivitet ska uppdatera värdet.

      Du måste först skapa en logikapp, så som visas i följande bild. Klistra sedan in den **HTTP post-URL**.

      ![Logic App-konfiguration](media/load-sap-bw-data/logic-app-config.png)

      1. Gå till Azure Portal. Välj en ny **Logic Apps** service. Välj **+ tom Logikapp** att gå till **Logic Apps Designer**.

      2. Skapa en utlösare för **när en HTTP-begäran tas emot**. Ange HTTP-begärandetexten enligt följande:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. Lägg till en **skapa blob** åtgärd. För **mappsökväg** och **blobnamnet**, använder du samma värden som du konfigurerade tidigare i **HighWatermarkBlobPath** och **HighWatermarkBlobName**.

      4. Välj **Spara**. Kopiera sedan värdet för **HTTP post-URL** ska användas i Data Factory-pipeline.

4. När du har angett parametrarna för Data Factory-pipeline, Välj **felsöka** > **Slutför** att anropa en körning för att verifiera konfigurationen. Eller välj **publicera alla** att publicera ändringar och välj sedan **utlösaren** att köra en körning.

## <a name="sap-bw-open-hub-destination-configurations"></a>Konfigurationer för SAP BW Open Hub mål

Det här avsnittet introducerar konfigurationen av SAP BW-sida för att använda anslutningstjänsten SAP BW Open Hub i Data Factory för att kopiera data.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurera extrahering av delta i SAP BW

Om du behöver både historiska kopiera och inkrementell kopia eller bara en inkrementell kopia kan du konfigurera extrahering av delta i SAP BW.

1. Skapa öppna Hub-mål. Du kan skapa OHD i SAP transaktion RSA1, vilket automatiskt skapar de nödvändiga omvandling och överföring av replikeringsdata bearbetar. Använd följande inställningar:

   - **ObjectType**: Du kan använda någon objekttyp. Här kan vi använda **InfoCube** som exempel.
   - **Måltypen**: Välj **databastabellen**.
   - **Viktiga tabellens**: Välj **teknisk nyckeln**.
   - **Extrahering**: Välj **behålla Data och infoga poster i tabellen**.

   ![SAP BW OHD delta extrahering av dialogrutan Skapa](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![SAP BW OHD delta2 extrahering av dialogrutan Skapa](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Du kan öka antalet parallella som kör SAP arbetsprocesser för DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Schemalägga DTP i processen kedjor.

   Delta DTP för en kub fungerar bara om de rader som behövs inte har komprimerats. Kontrollera att BW-kub komprimering inte är körs innan DTP till tabellen öppna Hub. Det enklaste sättet att göra detta är att integrera din befintliga processen går att härleda DTP. I följande exempel DTP (för att OHD) infogas i processkedjan mellan den *justera* (sammanställd sammanslagning) och *Dölj* steg (kuben komprimering).

   ![Skapa flödesschema för SAP BW processen kedja](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurera fullständig extrahering i SAP BW

Förutom att delta extrahering du en fullständig extrahering av samma SAP BW InfoProvider. Detta gäller vanligtvis om du vill fullständig kopia men inte är inkrementell, eller om du vill [synkronisera om delta extrahering](#resync-delta-extraction).

Du kan inte ha fler än en DTP för samma OHD. Därför måste du skapa en ytterligare OHD innan delta extrahering.

![Skapa SAP BW OHD fullständig](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Välj olika alternativ än för extrahering av delta för en fullständig inläsning OHD:

- In OHD: Ange den **extrahering** alternativet att **ta bort Data och infoga poster**. I annat fall extraheras data många gånger när du upprepa DTP i en kedja för BW-processen.

- I DTP: Ange **extrahering läge** till **fullständig**. Du måste ändra den automatiskt skapade DTP från **Delta** till **fullständig** omedelbart efter att OHD skapas, så som visas i den här bilden:

   ![Skapa SAP BW OHD dialogrutan som konfigurerats för ”Full” extrahering](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- I Data Factory BW Open Hub-anslutningsprogram: Inaktivera **exkludera senaste begäran**. I annat fall kommer ingenting att extraheras.

Du vanligtvis köra fullständig DTP manuellt. Du kan också skapa en process-kedja för fullständig DTP. Det är vanligtvis en separat kedja som är oberoende av din befintliga processen kedjor. I båda fallen *se till att DTP är klar innan du börjar extraheringen med hjälp av Data Factory-kopia*. I annat fall kopieras endast partiella data.

### <a name="run-delta-extraction-the-first-time"></a>Kör delta extrahering första gången

Första delta extraheringen är tekniskt sett en *fullständig extrahering*. Som standard omfattar anslutningstjänsten SAP BW Open Hub inte den senaste begäran när data kopieras. För den första delta-utvinning, ska inga data extraheras av Data Factory kopieringsaktiviteten tills en efterföljande DTP genererar delta-data i tabellen med en separat begärande-ID. Det finns två sätt att undvika det här scenariot:

- Stäng av den **exkludera senaste begäran** alternativet för första delta-utvinning. Kontrollera att den första listan DTP är klar innan du börjar delta extraheringen första gången.
-  Stegen nedan för omsynkroniseras delta-utvinning, enligt beskrivningen i nästa avsnitt.

### <a name="resync-delta-extraction"></a>Synkronisera om delta extrahering

Följande scenarier ändra data i SAP BW-kuber men används inte med delta DTP:

- SAP BW selektivt ta bort (raderna med hjälp av valfri filtervillkoret)
- Borttagning av SAP BW-begäran (av felaktiga begäranden)

Ett SAP-öppna Hub mål är inte ett datamål för data mart-osättning (i alla paket på grund av SAP BW-stöd eftersom 2015). Därför kan du ta bort data från en kub utan att ändra data i OHD. Du måste sedan synkronisera om data i kuben med Data Factory:

1. Köra en fullständig extraktion i Data Factory (med hjälp av en fullständig DTP i SAP).
2. Ta bort alla rader i tabellen öppna hubben för delta DTP.
3. Ange status för delta DTP till **hämtats**.

Därefter kan fungerar alla efterföljande delta DTPs och Data Factory delta extraheringarna som förväntat.

Ange status för delta DTP till **hämtats**, du kan använda följande alternativ för att köra delta DTP manuellt:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Nästa steg

Läs mer om SAP BW Open Hub anslutningsprogram stöd:

> [!div class="nextstepaction"]
>[SAP Business Warehouse öppna Hub-anslutningsappen](connector-sap-business-warehouse-open-hub.md)
