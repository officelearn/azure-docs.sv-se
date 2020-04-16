---
title: Läsa in data från SAP Business Warehouse
description: Använda Azure Data Factory för att kopiera data från SAP Business Warehouse (BW)
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 96b23696164514ad2f16de72f0f76aa237ffce2e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415840"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopiera data från SAP Business Warehouse med hjälp av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln visar hur du använder Azure Data Factory för att kopiera data från SAP Business Warehouse (BW) via Open Hub till Azure Data Lake Storage Gen2. Du kan använda en liknande process för att kopiera data till andra [sink-datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Allmän information om hur du kopierar data från SAP BW, inklusive SAP BW Open Hub-integrering och deltaextraheringsflöde, finns i [Kopiera data från SAP Business Warehouse via Open Hub med hjälp av Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Krav

- **Azure Data Factory**: Om du inte har någon, följ stegen för att [skapa en datafabrik](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Open Hub Destination (OHD) med måltypen "Databastabell":** Information om hur du skapar en OHD eller kontrollerar att DIN OHD är korrekt konfigurerad för Integrering av Data Factory finns i avsnittet [SAP BW Open Hub Destination configurations](#sap-bw-open-hub-destination-configurations) i den här artikeln.

- **SAP BW-användaren behöver följande behörigheter:**

  - Auktorisering för fjärrfunktionsanrop (RFC) och SAP BW.
  - Behörigheter till aktiviteten "Kör" **för S_SDSAUTH** auktoriseringsobjektet.

- **En [självvärd integration runtime (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) med SAP .NET-anslutning 3.0**. Följ dessa inställningssteg:

  1. Installera och registrera den självvärderade integrationskörningen, version 3.13 eller senare. (Den här processen beskrivs senare i den här artikeln.)

  2. Hämta [64-bitars SAP Connector för Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) från SAP:s webbplats och installera den på samma dator som den självvärdbaserade IR:n. Under installationen kontrollerar du att du väljer **Installera sammansättningar till GAC** i dialogrutan **Valfria installationssteg,** som följande bild visar:

     ![Konfigurera dialogrutan SAP .NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Gör en fullständig kopia från SAP BW Open Hub

Gå till datafabriken i Azure-portalen. Välj **Författare & Övervakare** om du vill öppna datafabriksgränssnittet på en separat flik.

1. På sidan **Låt oss komma igång** väljer du Kopiera **data** för att öppna verktyget Kopiera data.

2. På sidan **Egenskaper** anger du ett **aktivitetsnamn**och väljer sedan **Nästa**.

3. På sidan **Källdatalager** väljer du **+Skapa ny anslutning**. Välj **SAP BW Open Hub** i kopplingsgalleriet och välj sedan **Fortsätt**. Om du vill filtrera kopplingarna kan du skriva **SAP** i sökrutan.

4. På **sidan Ange SAP BW Open Hub-anslutning** följer du dessa steg för att skapa en ny anslutning.

   ![Skapa tjänstsida för SAP BW Open Hub](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Välj en befintlig självvärd iR i listan **Anslut via integrationskörning.** Eller välj att skapa en om du inte har en ännu.

      Om du vill skapa en ny självvärd iR väljer du **+Nytt**och väljer sedan **Självvärd.** Ange ett **namn**och välj sedan **Nästa**. Välj **Express-installation** som ska installeras på den aktuella datorn eller följ de **manuella installationsstegen** som anges.

      Som [nämnts](#prerequisites)i Krav kontrollerar du att du har SAP Connector för Microsoft .NET 3.0 installerat på samma dator där den självvärdbaserade IR körs.

   2. Fyll i SAP **BW-servernamn**, **Systemnummer,** **Klient-ID,** **Språk** (om annat än **EN),** **Användarnamn**och **Lösenord**.

   3. Välj **Testa anslutning** för att validera inställningarna och välj sedan **Slutför**.

   4. En ny anslutning skapas. Välj **Nästa**.

5. På sidan **Välj öppna hubbmål** bläddrar du bland de öppna hubbdestinationer som är tillgängliga i din SAP BW. Välj ohd att kopiera data från och välj sedan **Nästa**.

   ![Välj tabell för öppna hubb-mål för SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Ange ett filter om du behöver ett. Om din OHD bara innehåller data från en enda dataöverföringsprocess (DTP) körning med ett enda begärande-ID, eller om du är säker på att din DTP är klar och du vill kopiera data, avmarkerar du kryssrutan **Uteslut senaste begäran.**

   Läs mer om de här inställningarna i avsnittet [SAP BW Open Hub Destination configurations](#sap-bw-open-hub-destination-configurations) i den här artikeln. Välj **Validera** om du vill dubbelkolla vilka data som ska returneras. Välj sedan **Nästa**.

   ![Konfigurera SAP BW Open Hub-filter](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. På sidan **Måldatalager** väljer du **+Skapa ny anslutning** > **Azure Data Lake Storage Gen2** > **Continue**.

8. På **sidan Ange Azure Data Lake Storage-anslutning** följer du dessa steg för att skapa en anslutning.

   ![Skapa en ADLS Gen2-länkad tjänstsida](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Välj ditt DataSjölagringsgenm2-kompatibla konto i listrutan **Namn.**
   2. Välj **Slutför** för att skapa anslutningen. Välj sedan **Nästa**.

9. På sidan **Välj utdatafil eller mapp** anger du **copyfromopenhub** som utdatamappnamn. Välj sedan **Nästa**.

   ![Välj sidan utdatamapp](media/load-sap-bw-data/choose-output-folder.png)

10. På **inställningssidan för Filformat** väljer du **Nästa** om du vill använda standardinställningarna.

    ![Ange sida för handflänsformat](media/load-sap-bw-data/specify-sink-format.png)

11. Expandera **Prestandainställningar**på sidan **Inställningar** . Ange ett värde för **Grad av kopia parallellitet** som 5 att ladda från SAP BW parallellt. Välj sedan **Nästa**.

    ![Konfigurera kopieringsinställningar](media/load-sap-bw-data/configure-copy-settings.png)

12. Granska inställningarna på sidan **Sammanfattning** . Välj sedan **Nästa**.

13. På **sidan Distribution** väljer du **Övervaka** för att övervaka pipelinen.

    ![Distributionssida](media/load-sap-bw-data/deployment.png)

14. Observera att fliken **Bildskärm** till vänster på sidan väljs automatiskt. Kolumnen **Åtgärder** innehåller länkar för att visa information om aktivitetskörning och för att köra pipelinen igen.

    ![Vyn Övervakning av pipeline](media/load-sap-bw-data/pipeline-monitoring.png)

15. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningen väljer du **Visa aktivitetskörningar** i kolumnen **Åtgärder.** Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till vyn pipeline-körningar väljer du länken **Pipelines** högst upp. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).

    ![Skärm för aktivitetsövervakning](media/load-sap-bw-data/activity-monitoring.png)

16. Om du vill övervaka körningsinformationen för varje kopieringsaktivitet väljer du länken **Information,** som är ikonen Glasögon under **Åtgärder** i aktivitetsövervakningsvyn. Tillgänglig information inkluderar datavolymen som kopieras från källan till diskhon, datadataflödet, körningssteg och varaktighet samt konfigurationer som används.

    ![Information om aktivitetsövervakning](media/load-sap-bw-data/activity-monitoring-details.png)

17. Om du vill visa **det maximala begärande-ID:et**går du tillbaka till aktivitetsövervakningsvyn och väljer **Utdata** under **Åtgärder**.

    ![Skärmen Aktivitetsutdata](media/load-sap-bw-data/activity-output.png)

    ![Informationsvy för aktivitetsutdata](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Inkrementell kopia från SAP BW Open Hub

> [!TIP]
> Se [SAP BW Open Hub-deltaextraheringsflödet](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) för SAP BW Open Hub i Data Factory kopierar inkrementella data från SAP BW. Den här artikeln kan också hjälpa dig att förstå grundläggande anslutningskonfiguration.

Nu ska vi fortsätta att konfigurera inkrementell kopia från SAP BW Open Hub.

Inkrementell kopia använder en "högvattenstämpel"-mekanism som baseras på **begärande-ID.** Det ID:t genereras automatiskt i SAP BW Open Hub Destination av DTP. I följande diagram visas det här arbetsflödet:

![Flödesschema för inkrementellt arbetsflöde för kopia](media/load-sap-bw-data/incremental-copy-workflow.png)

På sidan **Datafabrik Låt oss komma igång** väljer du Skapa pipeline från **mall** för att använda den inbyggda mallen.

1. Sök efter **SAP BW** för att hitta och välja den **inkrementella kopian från SAP BW till Azure Data Lake Storage** Gen2-mallen. Den här mallen kopierar data till Azure Data Lake Storage Gen2. Du kan använda ett liknande arbetsflöde för att kopiera till andra handfatstyper.

2. På mallens huvudsida markerar eller skapar du följande tre anslutningar och väljer sedan Använd den **här mallen** i fönstrets nedre högra hörn.

   - **Azure Blob storage**: I den här genomgången använder vi Azure Blob-lagring för att lagra den höga vattenstämpeln, vilket är *max kopierade begärande-ID*.
   - **SAP BW Open Hub:** Detta är källan att kopiera data från. Se den tidigare genomgången för full kopia för detaljerad konfiguration.
   - **Azure Data Lake Storage Gen2**: Det här är diskhon att kopiera data till. Se den tidigare genomgången för full kopia för detaljerad konfiguration.

   ![Inkrementell kopia från SAP BW-mall](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Den här mallen genererar en pipeline med följande tre aktiviteter och gör dem fastkedjade efter lyckad: *Uppslag,* *Kopiera data*och *webb*.

   Gå till fliken **PipelineParametrar.** Du ser alla konfigurationer som du behöver tillhandahålla.

   ![Inkrementell kopia från SAP BW-konfiguration](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Ange tabellnamnet Öppna hubben som data ska kopieras från.

   - **Data_Destination_Container**: Ange behållaren för mål azure datasjölagring gen2 att kopiera data till. Om behållaren inte finns skapar datafabrikens kopieringsaktivitet en under körningen.
  
   - **Data_Destination_Directory:** Ange mappsökvägen under Azure Data Lake Storage Gen2-behållaren att kopiera data till. Om sökvägen inte finns skapar datafabrikskopieringsaktiviteten en sökväg under körningen.
  
   - **HighWatermarkBlobContainer**: Ange behållaren för att lagra högvattenstämpelvärdet.

   - **HighWatermarkBlobDirectory**: Ange mappsökvägen under behållaren för att lagra värdet med hög vattenstämpel.

   - **HighWatermarkBlobName**: Ange blobnamnet för att lagra det `requestIdCache.txt`höga vattenstämpelvärdet, till exempel . I Blob-lagring går du till motsvarande sökväg för HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName, till exempel *container/path/requestIdCache.txt*. Skapa en blob med innehåll 0.

      ![Blobinnehåll](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: I den här mallen använder vi WebActivity för att anropa Azure Logic Apps för att ange värdet med hög vattenstämpel i Blob-lagring. Eller så kan du använda Azure SQL Database för att lagra den. Använd en lagrad proceduraktivitet för att uppdatera värdet.

      Du måste först skapa en logikapp, som följande bild visar. Klistra sedan in HTTP **POST-URL:en**.

      ![Konfiguration av Logic App](media/load-sap-bw-data/logic-app-config.png)

      1. Gå till Azure Portal. Välj en ny **Logic Apps-tjänst.** Välj **+Tom Logic App** för att gå till Logic Apps **Designer**.

      2. Skapa en utlösare av **När en HTTP-begäran tas emot**. Ange http-begärandetexten på följande sätt:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Lägg till en **skapa blob-åtgärd.** För **mappsökväg** och **Blob-namn**använder du samma värden som du har konfigurerat tidigare i *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* och *HighWatermarkBlobName*.

      4. Välj **Spara**. Kopiera sedan värdet för **HTTP POST-URL:en** som ska användas i datafabrikspipelinen.

4. När du har ange pipelineparametrarna för datafabriken väljer du > **Felsökningsslut** om du vill anropa en körning för att validera konfigurationen. **Debug** Du kan också välja **Publicera** om du vill publicera alla ändringar och sedan **välja Lägg till utlösare** för att köra en körning.

## <a name="sap-bw-open-hub-destination-configurations"></a>Konfigurationer för SAP BW Open Hub-mål

Det här avsnittet introducerar konfiguration av SAP BW-sidan för att använda SAP BW Open Hub-anslutningen i Data Factory för att kopiera data.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurera deltaextraktion i SAP BW

Om du behöver både historisk kopia och inkrementell kopia eller bara inkrementell kopia konfigurerar du deltaextrahering i SAP BW.

1. Skapa målet för öppna nav. Du kan skapa OHD i SAP Transaction RSA1, som automatiskt skapar den nödvändiga omvandlingen och dataöverföringsprocessen. Använd följande inställningar:

   - **ObjectType**: Du kan använda vilken objekttyp som helst. Här använder vi **InfoCube** som exempel.
   - **Måltyp:** Välj **databastabell**.
   - **Nyckeln i tabellen**: Välj **teknisk nyckel**.
   - **Extrahering:** Välj **Förvara data och Infoga poster i tabellen**.

   ![Dialogrutan Skapa SAP BW OHD-deltaextrahering](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Dialogrutan Skapa SAP BW OHD delta2-extrahering](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Du kan öka antalet sap-arbetsprocesser som körs parallellt för DTP:

   ![skapa-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Schemalägg DTP i processkedjor.

   En delta DTP för en kub fungerar bara om de nödvändiga raderna inte har komprimerats. Kontrollera att BW-kubkomprimering inte körs före DTP till open hub-tabellen. Det enklaste sättet att göra detta är att integrera DTP i dina befintliga processkedjor. I följande exempel infogas DTP (till OHD) i processkedjan mellan stegen *Justera* (aggregerad sammanslagning) och *Komprimera* (kubkomprimering).

   ![Skapa flödesschema för SAP BW-processklämning](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurera fullständig extrahering i SAP BW

Förutom delta extrahering, kanske du vill ha en fullständig extrahering av samma SAP BW InfoProvider. Detta gäller vanligtvis om du vill göra fullständig kopia men inte inkrementell, eller om du vill [synkronisera delta extrahering](#resync-delta-extraction).

Du kan inte ha mer än en DTP för samma OHD. Så måste du skapa ytterligare en OHD innan delta extrahering.

![Skapa SAP BW OHD full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

För en full belastning OHD, välj andra alternativ än för delta extraktion:

- I OHD: Ange alternativet **Extrahering** för att **ta bort data och infoga poster**. Annars extraheras data många gånger när du upprepar DTP i en BW-processkedja.

- I DTP: Ställ in **extraheringsläge** till **full**. Du måste ändra den automatiskt skapade DTP från **Delta** till **Full** omedelbart efter att OHD har skapats, eftersom den här bilden visar:

   ![Skapa dialogrutan SAP BW OHD som konfigurerats för "Fullständig" extrahering](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- I BW Open Hub-kopplingen för Data Factory: Stäng av **Uteslut senaste begäran**. Annars kommer ingenting att utvinnas.

Du kör vanligtvis hela DTP manuellt. Du kan också skapa en processkedja för hela DTP. Det är vanligtvis en separat kedja som är oberoende av dina befintliga processkedjor. I båda fallen *kontrollerar du att DTP:et är klart innan du påbörjar extraheringen med hjälp av Data Factory-kopian*. Annars kopieras endast partiella data.

### <a name="run-delta-extraction-the-first-time"></a>Kör deltaextraktion första gången

Den första delta utvinning är tekniskt en *fullständig extraktion*. Som standard utesluter SAP BW Open Hub-kopplingen den senaste begäran när den kopierar data. För den första deltaextraherningen extraheras inga data av datafabrikens kopieringsaktivitet förrän en efterföljande DTP genererar deltadata i tabellen med ett separat begärande-ID. Det finns två sätt att undvika det här scenariot:

- Inaktivera alternativet **Uteslut senaste begäran** för den första deltautextraheringen. Kontrollera att den första delta-DTP är klar innan du startar deltautextraktionen första gången.
-  Använd proceduren för att synkronisera om deltaextraktionen enligt beskrivningen i nästa avsnitt.

### <a name="resync-delta-extraction"></a>Resync delta extraktion

Följande scenarier ändrar data i SAP BW-kuber men beaktas inte av delta-DTP:

- SAP BW selektiv borttagning (av rader med hjälp av något filtervillkor)
- SAP BW-begäran radering (av felaktiga begäranden)

En SAP Open Hub-destination är inte ett data-mart-kontrollerat datamål (i alla SAP BW-supportpaket sedan 2015). Så kan du ta bort data från en kub utan att ändra data i OHD. Du måste sedan synkronisera om kubens data med Data Factory:

1. Kör en fullständig extrahering i Data Factory (med hjälp av en fullständig DTP i SAP).
2. Ta bort alla rader i tabellen Öppna hubb för delta-DTP.
3. Ange status för delta-DTP till **Hämtade**.

Efter detta fungerar alla efterföljande delta-DTPs och Data Factory delta extraheringar som förväntat.

Om du vill ange status för delta-DTP till **Hämtad**kan du använda följande alternativ för att köra delta-DTP manuellt:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Nästa steg

Lär dig mer om stöd för SAP BW Open Hub-anslutning:

> [!div class="nextstepaction"]
>[Sap Business Warehouse Open Hub-anslutning](connector-sap-business-warehouse-open-hub.md)
