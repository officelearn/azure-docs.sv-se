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
ms.openlocfilehash: 9458903378576a50db9be92b9377987829e1ba41
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200165"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Läsa in data från SAP Business Warehouse (BW) med hjälp av Azure Data Factory

Den här artikeln visar dig en genomgång om hur du använder Data Factory _läser in data från SAP Business Warehouse (BW) via öppna Hub i Azure Data Lake Storage Gen2_. Du kan följa liknande steg för att kopiera data till andra [datalager för mottagare som stöds](copy-activity-overview.md#supported-data-stores-and-formats). 

> [!TIP]
> Referera till [öppna hubben för SAP BW connector artikeln](connector-sap-business-warehouse-open-hub.md) om hur du kopierar data från SAP BW i allmänhet, inklusive introduktion av SAP BW Open Hub integrering och delta extrahering av flödet.

## <a name="prerequisites"></a>Förutsättningar

- **Azure Data Factory (ADF):** Om du inte har en data factory, följer du de ”[skapa en datafabrik](quickstart-create-data-factory-portal.md#create-a-data-factory)” avsnitt för att skapa en. 

- **SAP BW Open Hub mål (OHD) med måltypen som ”databastabell”.** Följ [konfigurationer för SAP BW Open Hub mål](#sap-bw-open-hub-destination-configurations) avsnittet att skapa en och för att bekräfta om din befintliga OHD är korrekt konfigurerad kan integreras med ADF.

- **SAP BW-användaren som används måste ha följande behörigheter:**

  - Auktorisering för RFC och SAP BW.
  - Behörigheter till den ”**köra**” aktivitet för auktorisering objektet ”**S_SDSAUTH**”.

- **[Lokal värd för Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) med SAP .NET connector 3.0 krävs**. Här följer detaljerade förberedelser som du kan göra:

  1. Installera och registrera lokal IR med version > = 3.13 (beskrivs i den här genomgången). 

  2. Ladda ned den [64-bitars SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) från SAP: s webbplats och installera den på den lokala IR-datorn.  När installationen i fönstret ”installationsanvisningarna” Kontrollera att du väljer den ”**installera sammansättningar GAC**” enligt i följande bild.

     ![Konfigurera SAP .NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>Fullständig kopia från SAP BW Open Hub

På Azure-portalen, gå till din data factory -> Välj **författare och Övervakare** att starta ADF UI på en separat flik. 

1. Sidan **Nu sätter vi igång** visas. Välj **Kopiera data** för att starta verktyget Kopiera data. 

2. På den **egenskaper** anger du ett namn för den **aktivitetsnamn** och markerar **nästa**.

3. På den **källdatalagret** klickar du på **+ Skapa ny anslutning** -> Välj **SAP BW Open Hub** från galleriet connector -> Välj **Fortsätt**. Du kan skriva ”SAP” i sökrutan för att filtrera kopplingarna.

4. På den **ange SAP BW Open hubbanslutning** sidan 

   ![Skapa SAP BW öppna hubben som är länkad tjänst](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Välj den **Anslut via Integration Runtime**: Klicka på listrutan för att välja en befintlig lokal IR eller skapa en om du inte har lokal IR ännu. 

      Om du vill skapa en ny, klickar du på **+ ny** i listrutan väljer typ -> **egenvärdbaserade** -> Ange en **namn** och klicka på **nästa** -> Välj **Expressinstallation** att installera på den aktuella datorn eller följ den **manuell installation** det steg.

      Som vi nämnde i [krav](#prerequisites), kontrollera att du också har den **SAP .NET connector 3.0** installerad på samma dator där lokal IR körs.

   2. Ange SAP BW **servernamn**, **systemnummer**, **klient-ID,** **språk** (om än EN) **användarnamn**, och **lösenord**.

   3. Klicka på **Testanslutning** för att verifiera inställningarna kan sedan välja **Slutför**.

   4. Du ser en ny anslutning skapas. Välj **Nästa**.

5. På den **Välj Öppna Hub mål** bläddrar den öppna Hub mål som är tillgängliga på SAP BW, och väljer du det alternativ som du vill kopiera data från och klicka sedan på **nästa**.

   ![Välj SAP BW Open Hub tabell](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Ange filtret om det behövs. Om öppen Hub mål endast innehåller data från Data Transfer processen (DTP) utförande med enskild begäran-ID, eller om du är säker på att din DTP har slutförts och vill kopiera alla data, avmarkera de **undanta senaste begäran**. Du kan lära dig mer om hur dessa inställningar är relaterade till SAP BW konfigurationen i [konfigurationer för SAP BW Open Hub mål](#sap-bw-open-hub-destination-configurations) avsnittet. Klicka på **verifiera** att kontrollera data returneras, välj sedan **nästa**.

   ![Konfigurera SAP BW Open Hub filter](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. I den **måldatalager** klickar du på **+ Skapa ny anslutning**, och välj sedan **Azure Data Lake Storage Gen2**, och välj **Fortsätt**.

8. I den **ange Azure Data Lake Storage anslutning** sidan 

   ![Skapa ADLS Gen2 länkad tjänst](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Välj Data Lake Storage Gen2 kan kontot från listrutan ”lagringskontonamn”.
   2. Välj **Slutför** att skapa anslutningen. Välj sedan **Nästa**.

9. I den **Välj utdatafil eller mapp** , anger du ”copyfromopenhub” som utdata mappnamnet och välj **nästa**.

   ![Välj utdatamapp](media/load-sap-bw-data/choose-output-folder.png)

10. I den **filformat inställningen** väljer **nästa** att använda standardinställningarna.

    ![Ange format för mottagare](media/load-sap-bw-data/specify-sink-format.png)

11. I den **inställningar** expanderar den **prestandainställningar**, och Ställ in **graden av parallellitet kopia** , till exempel 5 för att läsa in från SAP BW parallellt. Klicka på **Nästa**.

    ![Konfigurera inställningar](media/load-sap-bw-data/configure-copy-settings.png)

12. I den **sammanfattning** , granskar du inställningarna och välj **nästa**.

13. I den **distribution** väljer **övervakaren** att övervaka pipelinen.

    ![Distributionssida](media/load-sap-bw-data/deployment.png)

14. Observera att fliken **Övervaka** till vänster väljs automatiskt. Den **åtgärder** -kolumnen innehåller länkar för att visa information om aktivitetskörningar och köra pipelinen på nytt:

    ![Pipelineövervakning](media/load-sap-bw-data/pipeline-monitoring.png)

15. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningen, väljer den **visa Aktivitetskörningar** länken i den **åtgärder** kolumn. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Växla tillbaka till vyn med pipelinekörningar, Välj den **Pipelines** länken längst upp. Om du vill uppdatera listan väljer du **Uppdatera**.

    ![Aktivitetsövervakning](media/load-sap-bw-data/activity-monitoring.png)

16. För att övervaka körning-information för varje kopieringsaktiviteten, Välj den **information** länken (glasögonbilden) under **åtgärder** i övervakningsvyn-aktivitet. Du kan övervaka information som mängden data som kopieras från källan till mottagare, dataflöde, utförande med motsvarande tid och används konfigurationer:

    ![Aktivitetsövervakning information](media/load-sap-bw-data/activity-monitoring-details.png)

17. Granska den **högsta ID för begäran** som kopieras. Gå tillbaka till aktiviteten övervakningsvyn, klickar du på den **utdata** under **åtgärder**.

    ![Aktivitetsutdata](media/load-sap-bw-data/activity-output.png)

    ![Utdata för aktivitetsinformation](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Inkrementell kopia från SAP BW Open Hub

> [!TIP]
>
> Referera till [öppna hubben för SAP BW connector delta extrahering flow](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) mer information om hur ADF SAP BW Open Hub anslutningen fungerar för att kopiera inkrementella data från SAP BW och den här artikeln från första början för att förstå grunderna för anslutningstjänsten relaterade konfigurationer.

Nu ska vi fortsätta att konfigurera inkrementell kopia från SAP BW Open Hub. 

Den inkrementella kopian använder högvattenmärket mekanism utifrån **fråge-ID** skapas automatiskt i SAP BW Open Hub mål av DTP. Arbetsflödet för den här metoden illustreras i följande diagram:

![Inkrementell kopia arbetsflöde](media/load-sap-bw-data/incremental-copy-workflow.png)

På den ADF UI **nu sätter vi igång** väljer **skapa pipeline från mall** utnyttjar den inbyggda mallen. 

1. Sök efter ”SAP BW” för att hitta och välj mallen med namnet **stegvis kopiera från SAP BW till Azure Data Lake Storage Gen2**. Den här mallen kopierar data till ADLS Gen2 kan du följa senare liknande flöde för att kopiera till andra typer av mottagare.

2. På huvudsidan för mallen, Välj eller skapa följande tre anslutningar och välj sedan **Använd den här mallen** i nederkant högra hörnet.

   - **Azure Blob**: i den här genomgången ska vi använda Azure Blob och lagra högvattenmärket, vilket är max kopierade begärande-ID.
   - **SAP BW Open Hub**: kopiera data från källan. Referera till den tidigare genomgången fullständig kopia på detaljerade konfigurationer.
   - **ADLS Gen2**: mottagaren att kopiera data till. Referera till den tidigare genomgången fullständig kopia på detaljerade konfigurationer.

   ![Inkrementell kopia från SAP BW-mall](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Den här mallen skapas en pipeline med tre aktiviteter - **Lookup, kopieringsdata och webb-** – och gör dem har länkats till om de lyckas. Gå till pipelinen **parametrar** fliken du visa alla konfigurationer som du måste ange.

   ![Inkrementell kopiering från SAP BW-config](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Ange öppna tabell hubbnamnet för att kopiera data från.

   - **ADLSGen2SinkPath**: Ange den målsökväg ADLS Gen2 för att kopiera data till. Om sökvägen inte finns, skapas en vid körning av ADF kopiera aktivitet.

   - **HighWatermarkBlobPath**: Ange sökväg för att lagra värdet för högvattenmärket t.ex. `container/path`. 

   - **HighWatermarkBlobName**: Ange blobnamnet för att lagra värdet för högvattenmärket t.ex. `requestIdCache.txt`. I blob storage, motsvarande sökvägen för HighWatermarkBlobPath + HighWatermarkBlobName t.ex ”.*container/path/requestIdCache.txt*”, skapa en blob med innehåll 0. 

      ![Blobbinnehåll](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: i den här mallen använder vi webbaktivitet för att anropa Logic Apps för att ställa in värdet för högvattenmärket i Blob storage. Alternativt kan du också använda SQL-databas att lagra den och använda lagringsprocedur-aktivitet ska uppdatera värdet. 

      Här, måste du först skapa en Logikapp som följande och sedan kopiera den **HTTP post-URL** till det här fältet. 

      ![Logic App config](media/load-sap-bw-data/logic-app-config.png)

      1. Gå till Azure portal -> Ny en **Logikappar** tjänst -> Klicka på **+ tom Logikapp** att gå till **Logic Apps Designer**.

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

      3. Lägg till en åtgärd av **skapa blob**. ”Sökväg” och ”Blob-name” kan du använda samma värde i ovanstående HighWatermarkBlobPath och HighWatermarkBlobName.

      4. Klicka på **spara**, och kopiera sedan värdet för **HTTP post-URL** ska användas i ADF pipeline.

4. När du anger alla värden för ADF pipeline-parametrar måste du klicka på **felsöka** -> **Slutför** att anropa en körning för att verifiera konfigurationen. Du kan också välja **publicera alla** för att publicera alla ändringar, klicka sedan på **utlösaren** att köra en körning.

## <a name="sap-bw-open-hub-destination-configurations"></a>Konfigurationer för SAP BW Open Hub mål

Det här avsnittet introducerar den nödvändiga konfigurationen på SAP BW-sida för att kunna använda anslutningstjänsten SAP BW Open Hub i ADF för att kopiera data.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurera extrahering av delta i SAP BW

Om du behöver både historiska kopiera och inkrementell kopia eller bara en inkrementell kopia kan du konfigurera extrahering av delta i SAP BW.

1. Skapa öppna Hub målet (OHD)

   Du kan skapa OHD i SAP transaktion RSA1 som automatiskt skapar nödvändiga omvandling och Data Transfer processen (DTP). Använd följande inställningar:

   - Objekttypen kan vara något. Här använder vi InfoCube som ett exempel.
   - **Typ av mål:** *Databastabell*
   - **Nyckeln för tabellen:** *Teknisk nyckel*
   - **Extrahering:** *Behåll Data och infoga poster i tabellen*

   ![Skapa SAP BW OHD delta extrahering](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Du kan öka antalet parallella som kör SAP arbetsprocesser för DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Schemalägga DTP i processen går att härleda

   En Delta-DTP för en kub fungerar bara när det behövs rader inte har komprimerats ännu. Därför måste du se till att BW-kub komprimering inte är igång innan DTP till tabellen öppna Hub. Det enklaste sättet för detta integrerar den här DTP i din befintliga processen kedjor. I exemplet nedan DTP (för att OHD) infogas i processkedjan mellan steget justera (sammanställd sammanslagning) och Dölj (kuben komprimering).

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurera fullständig extrahering i SAP BW

Förutom delta-utvinning, kanske du vill ha en fullständig extrahering av samma InfoProvider. Det gäller vanligtvis om du vill gör full kopia utan inkrementella behöver eller vill du [synkroniserar delta extrahering](#re-sync-delta-extraction).

Du får inte ha fler än en DTP för samma OHD. Därför måste du skapa ytterligare OHD och sedan delta extraheringen är klar.

![create-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Välj olika alternativ än extrahering av delta för en fullständig inläsning OHD:

- I OHD: Ställ in alternativet ”extrahering” som ”*ta bort Data och infoga poster*”. Annars skulle data extraheras många gånger när Upprepa DTP i en kedja för BW-processen.

- I DTP: Ange ”extrahering läget” som ”*fullständig*”. Du måste ändra den automatiskt skapade DTP av Delta till fullständig bara när du har skapat OHD:

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- I ADF SAP BW Open Hub connector: inaktivera alternativet ”*exkludera senaste begäran*”. Annars skulle ingenting extraheras. 

Du vanligtvis köra fullständig DTP manuellt. Eller du kan också skapa en process-kedja för fullständig DTP – vanligtvis skulle det vara en separat process kedja som är oberoende av din befintliga processen kedjor. I båda fallen måste du **se till att DTP har slutförts innan du startar extraheringen med ADF kopiering**, annars partiella data ska kopieras.

### <a name="run-delta-extraction-the-first-time"></a>Kör delta extrahering första gången

Första Delta extraheringen är tekniskt sett en **fullständig extrahering**. Obs av standard ADF SAP BW Open Hub anslutningen utesluter den senaste begäran när du kopierar data. När det gäller extrahering av delta för första gången i ADF kopieringsaktiviteten, inga data kommer att extraheras förrän har efterföljande DTP genererar delta-data i tabellen med separata begärande-ID. Det finns två möjliga sätt att undvika det här scenariot:

1. Inaktivera alternativet ”exkludera senaste begäran” för den första Delta extrahering i det här fallet måste du se till att den första Delta DTP är klar innan du startar Delta extraheringen första gången
2. Stegen för omsynkronisering Delta extraheringen enligt beskrivningen nedan.

### <a name="re-sync-delta-extraction"></a>Synkronisera delta extrahering

Det finns några scenarier som ändrar data i SAP BW kuber men inte används av Delta DTP:

- SAP BW selektivt ta bort (raderna med hjälp av valfri filtervillkoret)
- SAP BW-begäran tas bort (felaktig begäran)

Ett SAP-öppna Hub mål är inte ett datamål för data mart-osättning (i alla SAP BW supportpaket sedan året 2015). Det är därför möjligt att ta bort data från en kub utan att ändra data i OHD. Du måste i så fall igen synkroniserar data i kuben med data i ADF genom att utföra följande steg:

1. Kör en fullständig extraktion i ADF (med hjälp av en fullständig DTP i SAP)
2. Ta bort alla rader i tabellen öppna hubben för Delta DTP
3. Ange status för Delta DTP till hämtats

Därefter kan fungerar alla efterföljande Delta DTPs och ADF Delta extraheringarna som förväntat.

Du kan ange status för Delta DTP till hämtats genom att köra Delta-DTP manuellt med hjälp av följande alternativ: ”*Ingen dataöverföring; Delta Status i källan: Hämtade*”.

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande artikel om du vill veta mer om SAP BW Open Hub anslutningsprogram stöd: 

> [!div class="nextstepaction"]
>[SAP Business Warehouse öppna Hub-anslutningsappen](connector-sap-business-warehouse-open-hub.md)
