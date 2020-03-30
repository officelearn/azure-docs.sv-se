---
title: Läsa in data från Office 365 med hjälp av Azure Data Factory
description: Använda Azure Data Factory för att kopiera data från Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: cd2b530375beb3e8fa1f79e004f4f1ac7fd4d0bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443915"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Läsa in data från Office 365 med hjälp av Azure Data Factory

Den här artikeln visar hur du använder data _factory-inläsningsdata från Office 365 i Azure Blob storage_. Du kan följa liknande steg för att kopiera data till Azure Data Lake Gen1 eller Gen2. I [artikeln office 365-anslutning](connector-office-365.md) om hur du kopierar data från Office 365 i allmänhet.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **Skapa en resurs** > **Analytics** > **Data Factory:** 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du värden för de fält som visas i följande bild:
      
   ![Sidan Ny datafabrik](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure-datafabrik. Om felet "Data fabriksnamn *LoadFromOffice365Demo* inte är tillgängligt" anger du ett annat namn för datafabriken. Du kan till exempel använda namnet _**ditt namn**_**LoadFromOffice365Demo**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa datafabriken. 
    * **Resursgrupp:** Välj en befintlig resursgrupp i listrutan eller välj alternativet **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av datafabriken kan finnas på andra platser och regioner. Dessa datalager inkluderar Azure Data Lake Store, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När skapandet är klart går du till din datafabrik. Startsidan för **Data Factory** visas i följande bild:
   
   ![Datafabrikens startsida](./media/load-office-365-data/data-factory-home-page.png)

5. Välj panelen **Författare och övervakare** för att starta dataintegreringsprogrammet på en separat flik.

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. På sidan "Nu kommer vi igång" väljer du **Skapa pipeline**.
 
    ![Skapa pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. På **fliken Allmänt** för pipelinen anger du "CopyPipeline" för **Namnet** på pipelinen.

3. I verktygsrutan Aktiviteter > Flytta & Omformningskategori > dra och släppa **aktiviteten Kopiera** från verktygsrutan till pipelinedesignerytan. Ange "CopyFromOffice365ToBlob" som aktivitetsnamn.

### <a name="configure-source"></a>Konfigurera källan

1. Gå till fliken > **källa**, klicka på **+ Ny** för att skapa en källdatauppsättning. 

2. Välj **Office 365**i fönstret Ny datauppsättning och välj sedan **Fortsätt**.
 
3. Du befinner dig nu på fliken konfiguration **Edit** av kopieringsaktivitet.

    ![Config Office 365-datauppsättning allmänt](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Du ser en ny flik som öppnats för Office 365-datauppsättning. På **fliken Allmänt** längst ned i fönstret Egenskaper anger du "SourceOffice365Dataset" för Namn.
 
5. Gå till **fliken Anslutning** i fönstret Egenskaper. Klicka på **+ Nytt**bredvid textrutan Länkad tjänst .

6. I fönstret Ny länkad tjänst anger du "Office365LinkedService" som namn, anger tjänstens huvud-ID och tjänstens huvudnyckel och testar sedan anslutningen och väljer **Skapa** för att distribuera den länkade tjänsten.

    ![Ny Office 365-länkad tjänst](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. När du har skapat den länkade tjänsten kommer du tillbaka till inställningarna för datauppsättningen. Bredvid **Tabell**väljer du nedpilen för att expandera listan över tillgängliga Office 365-datauppsättningar och väljer "BasicDataSet_v0. Message_v0" från listrutan:

    ![Config Office 365-datauppsättningstabell](./media/load-office-365-data/edit-dataset.png)

8. Gå nu tillbaka till fliken **Pipeline** > **Source** för att fortsätta konfigurera ytterligare egenskaper för Office 365-datautvinning.  Filter för användaromfattning och användaromfattning är valfria predikater som du kan definiera för att begränsa de data som du vill extrahera från Office 365. Se avsnittet egenskaper för [datauppsättning för Office 365](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) för hur du konfigurerar dessa inställningar.

9. Du måste välja ett av datumfiltren och ange värden för starttid och sluttid.

10. Klicka på fliken **Importera schema** om du vill importera schemat för meddelandedatauppsättning.

    ![Konfiguration av Office 365-datauppsättningsschema](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Konfigurera kanalmottagare

1. Gå till fliken pipeline > **Sink**och välj **+ Ny** om du vill skapa en sink-datauppsättning.
 
2. I fönstret Ny datauppsättning märker du att endast de mål som stöds är markerade när du kopierar från Office 365. Välj **Azure Blob Storage**, välj Binärt format och välj sedan **Fortsätt**.  I den här självstudien kopierar du Office 365-data till en Azure Blob Storage.

3. Klicka på **knappen Redigera** bredvid Azure Blob Storage-datauppsättningen för att fortsätta datakonfigurationen.

4. På **fliken Allmänt** i fönstret Egenskaper anger du "OutputBlobDataset" i Namn.

5. Gå till **fliken Anslutning** i fönstret Egenskaper. Vid textrutan Länkad tjänst väljer du **+ Nytt**.

6. I fönstret Ny länkad tjänst anger du "AzureStorageLinkedService" som namn, väljer "Tjänsthuvudnamn" i listrutan med autentiseringsmetoder, fyller i tjänstslutpunkten, klienten, tjänstens huvud-ID och Huvudnyckeln För Tjänsten och Välj sedan Spara till distribuera den länkade tjänsten.  Se [här](connector-azure-blob-storage.md#service-principal-authentication) för hur du konfigurerar tjänsthuvudnamnsautentisering för Azure Blob Storage.

    ![Ny Blob-länkad tjänst](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Verifiera pipeline

Verifiera pipelinen genom att välja **Verifiera** i verktygsfältet.

Du kan också se JSON-koden som är associerad med pipelinen genom att klicka på Kod längst upp till höger.

## <a name="publish-the-pipeline"></a>Publicera pipelinen

Välj **Publicera alla**i det övre verktygsfältet . Med den här åtgärden publicerar du enheter (datauppsättningar och pipelines) som du skapat i datafabriken.

![Publicera ändringar](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt

Välj **Lägg till utlösare** i verktygsfältet och välj sedan **Utlösare nu**. På sidan Pipeline Run (Pipelinekörning) väljer du **Slutför**. 

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

Gå till fliken **Övervaka** till vänster. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **Åtgärder** för att visa aktivitetsinformation och köra pipelinen igen.

![Övervaka pipeline](./media/load-office-365-data/pipeline-status.png) 

Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen Actions (Åtgärder). Det finns bara en aktivitet i det här exemplet. Därför visas bara en post i listan. Om du vill se mer information om kopieringsåtgärden väljer du länken för **detaljer** (glasögonikonen) i kolumnen Actions (Åtgärder).

![Övervaka aktivitet](./media/load-office-365-data/activity-status.png) 

Om det är första gången du begär data för den här kontexten (en kombination av vilken datatabell som används, vilket målkonto som är de data som läses in i och vilken användaridentitet som gör begäran om dataåtkomst), visas kopieringsaktivitetsstatusen som **Pågår**, och först när du klickar på länken "Detaljer" under Åtgärder ser du statusen som **RequesetingConsent**.  En medlem i gruppen för godkännande av dataåtkomst måste godkänna begäran i hantering av privilegierad åtkomst innan datautvinningen kan fortsätta.

_Status som begäran om samtycke:_
![Information om aktivitetskörning - begäran om samtycke](./media/load-office-365-data/activity-details-request-consent.png) 

_Status som extraherande data:_

![Information om aktivitetskörning - extrahera data](./media/load-office-365-data/activity-details-extract-data.png) 

När medgivandet har lämnats fortsätter datautvinningen och efter en viss tid visas pipelinekörningen som lyckades.

![Övervaka pipeline - lyckades](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Gå nu till målet Azure Blob Storage och kontrollera att Office 365-data har extraherats i binärt format.

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artikel om du vill veta mer om Azure SQL Data Warehouse-stöd: 

> [!div class="nextstepaction"]
>[Office 365-anslutningsapp](connector-office-365.md)