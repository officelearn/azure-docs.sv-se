---
title: Läsa in data från Office 365 med hjälp av Azure Data Factory | Microsoft Docs
description: Använd Azure Data Factory för att kopiera data från Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: fe3a3b673f6512856f3640b3e103db8623570a88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60548288"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Läsa in data från Office 365 med hjälp av Azure Data Factory

Den här artikeln visar hur du använder Data Factory _läser in data från Office 365 i Azure Blob storage_. Du kan följa liknande steg för att kopiera data till Azure Data Lake Gen1 och Gen2. Referera till [Office 365 connector artikeln](connector-office-365.md) om hur du kopierar data från Office 365 i allmänhet.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På menyn till vänster väljer **skapa en resurs** > **Data och analys** > **Data Factory**: 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. I den **ny datafabrik** anger värden för fälten som visas i följande bild:
      
   ![Sida för ny datafabrik](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure data factory. Om du får felet ”datafabriksnamnet \"LoadFromOffice365Demo\" är inte tillgänglig”, ange ett annat namn för data factory. Du kan till exempel använda namnet  _**dittnamn**_**LoadFromOffice365Demo**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data factory. 
    * **Resursgrupp**: Välj en befintlig resursgrupp från den nedrullningsbara listan eller Välj den **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av data factory kan finnas på andra platser och regioner. Dessa datalager omfattar Azure Data Lake Store, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När datafabriken har skapats går du till din datafabrik. Du ser den **Data Factory** startsida, enligt följande bild:
   
   ![Datafabrikens startsida](./media/load-office-365-data/data-factory-home-page.png)

5. Välj den **författare och Övervakare** att starta Dataintegrationsprogrammet i en separat flik.

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. På sidan ”nu sätter vi igång” Välj **skapa pipeline**.
 
    ![Skapa pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. I den **fliken Allmänt** för pipelinen anger du ”CopyPipeline” för **namn** för pipelinen.

3. I verktygslådan aktiviteter > Flytta och transformera kategori > dra och släpp den **Kopieringsaktivitet** från verktygslådan till pipelinedesignerytan. Ange ”CopyFromOffice365ToBlob” som aktivitetsnamn.

### <a name="configure-source"></a>Konfigurera källan

1. Gå till pipelinen > **flik för datakälla**, klickar du på **+ ny** att skapa en källdatauppsättning. 

2. I fönstret ny datauppsättning väljer **Office 365**, och välj sedan **Slutför**.

    ![Ny Office 365-datauppsättning](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Du ser en ny flik öppnas för datauppsättningen för Office 365. På den **fliken Allmänt** längst ned i fönstret Egenskaper anger du ”SourceOffice365Dataset” för namn.

    ![Config Office 365-datauppsättning som är allmänt](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Gå till den **fliken anslutning** i fönstret Egenskaper. Bredvid textrutan länkad tjänst klickar du på **+ ny**.
 
    ![Datauppsättningsanslutning config Office 365](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. Ange ”Office365LinkedService” som namn på fönstret ny länkad tjänst, ange ID för tjänstens huvudnamn och nyckel för tjänstens huvudnamn och sedan välja Spara när du distribuerar den länkade tjänsten.

    ![Ny Office 365-länkad tjänst](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. När du har skapat den länkade tjänsten kommer du tillbaka till inställningarna för datauppsättningen. Välj nedåtpilen att expandera listan över tillgängliga Office 365-datauppsättningar bredvid ”tabell” och välj ”BasicDataSet_v0. Contact_v0 ”från den nedrullningsbara listan:

    ![Tabell för config Office 365-datauppsättning](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Gå till den **fliken Schema** egenskapsfönstret och välj **Importschema**.  Observera att värdena schemat och exempel kontakta datauppsättningen visas.

    ![Schemat för config Office 365-datauppsättningen](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. Gå nu tillbaka till pipelinen > källa fliken, kontrollerar du att SourceBlobDataset är valt.
 
### <a name="configure-sink"></a>Konfigurera kanalmottagare

1. Gå till pipelinen > **mottagare fliken**, och välj **+ ny** att skapa en datauppsättning för mottagare.
 
2. Observera att endast mål som stöds är markerade när du kopierar från Office 365 i fönstret ny datauppsättning. Välj **Azure Blob Storage**, och välj sedan **Slutför**.  I den här självstudien får kopiera du Office 365-data till Azure Blob Storage.

    ![Ny Blob-datauppsättning](./media/load-office-365-data/new-blob-dataset.png)

4. På den **fliken Allmänt** i fönstret Egenskaper skriver du ”OutputBlobDataset” i namnet.

5. Gå till den **fliken anslutning** i fönstret Egenskaper. Markera bredvid textrutan länkade tjänsten **+ ny**.

    ![Anslutning för konfigurations-Blob-datauppsättning](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. I fönstret ny länkad tjänst, ange ”AzureStorageLinkedService” som namn, Välj ”tjänstens huvudnamn” från den nedrullningsbara listan om du av autentiseringsmetoder, fyller du i tjänstens slutpunkt, ID för klient-tjänstens huvudnamn och Service principal key och välj Spara till distribuera den länkade tjänsten.  Se [här](connector-azure-blob-storage.md#service-principal-authentication) för hur du konfigurerar autentisering av tjänstens huvudnamn för Azure Blob Storage.

    ![Ny Blob länkad tjänst](./media/load-office-365-data/new-blob-linked-service.png)

7. När du har skapat den länkade tjänsten kommer du tillbaka till inställningarna för datauppsättningen. Markera bredvid sökväg, **Bläddra** att välja den utgående mappen där Office 365-data kommer att extraheras till.  Under ”Filformatinställningar”, bredvid filformat, väljer ”**JSON-format**”, och vid filmönster, väljer du ”**uppsättning objekt**”.

    ![Sökväg för konfigurations-Blob-datauppsättning och format](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. Gå tillbaka till pipelinen > fliken mottagare, kontrollerar du att OutputBlobDataset är valt.

## <a name="validate-the-pipeline"></a>Verifiera pipeline

Verifiera pipelinen genom att välja **Verifiera** i verktygsfältet.

Du kan också se JSON-koden som är associerad med pipelinen genom att klicka på kod i det övre högra hörnet.

## <a name="publish-the-pipeline"></a>Publicera pipelinen

I det översta verktygsfältet väljer **publicera alla**. Med den här åtgärden publicerar du enheter (datauppsättningar och pipelines) som du skapat i datafabriken.

![Publicera ändringar](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt

Välj **Utlös** i verktygsfältet och sedan **Trigger Now** (Utlös nu). På sidan körs pipelinen väljer **Slutför**. 

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

Gå till den **övervakningsfliken** till vänster. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i den **åtgärdskolumnen** att visa aktivitetsinformation och köra pipelinen på nytt.

![Övervaka pipeline](./media/load-office-365-data/pipeline-monitoring.png) 

Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer den **visa Aktivitetskörningar** länken i kolumnen åtgärder. Det finns bara en aktivitet i det här exemplet. Därför visas bara en post i listan. Mer information om kopieringsåtgärden väljer den **Informationslänk (glasögonikonen)** i kolumnen åtgärder.

![Övervakaraktivitet](./media/load-office-365-data/activity-monitoring.png) 

Om det här är första gången du begär data för den här kontexten (en kombination av vilka data som tabell håller på att åtkomst, vilken destination kontot är data som läses in i och vilka användaridentitet är att göra data åtkomstbegäran), ser du kopieringsaktiviteten status som ”**pågår**”, och endast när du klickar på länken ”information” under åtgärder ska du se statusen som ”**RequesetingConsent**”.  En medlem i gruppen data access godkännaren måste godkänna begäran i Privileged Access Management innan extrahering av data kan fortsätta.

_Status som begär tillstånd:_
![körning aktivitetsinformation – begäran om godkännande](./media/load-office-365-data/activity-details-request-consent.png) 

_Status som extraherar data:_

![Körningen av aktivitetsinformation – extrahera data](./media/load-office-365-data/activity-details-extract-data.png) 

När samtycke har angetts extrahering av data kommer att fortsätta och, om en stund, visar pipelinekörningen som slutförd.

![Övervaka pipeline - lyckades](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Gå till målet Azure Blob Storage nu och kontrollera att Office 365-data har hämtats i JSON-format.

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande artikel om du vill veta mer om Azure SQL Data Warehouse-stöd: 

> [!div class="nextstepaction"]
>[Office 365-anslutningsapp](connector-office-365.md)