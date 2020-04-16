---
title: Övrar i Azure Data Lake Storage Gen2 i Azure Synapse Analytics
description: Lär dig hur du får information om Azure Data Lake Storage Gen2 i Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430583"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Inta data i Azure Data Lake Storage Gen2 

I den här artikeln får du lära dig hur du äter data från en plats till en annan i ett Azure Data Lake Gen 2-lagringskonto (Azure Data Lake Gen 2) med Azure Synapse Analytics.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration:** Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto:** Du använder Azure Data Lake Gen 2 som ett *källdatalager.* Om du inte har ett lagringskonto läser du [Skapa ett Azure Storage-konto](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för steg för att skapa ett.

## <a name="create-linked-services"></a>Skapa länkade tjänster

I Azure Synapse Analytics är en länkad tjänst där du definierar din anslutningsinformation till andra tjänster. I det här avsnittet lägger du till Azure Synapse Analytics och Azure Data Lake Gen 2 som länkade tjänster.

1. Öppna Azure Synapse Analytics UX och gå till fliken **Hantera.**
1. Under **Externa anslutningar**väljer du Länkade **tjänster**.
1. Om du vill lägga till en länkad tjänst klickar du på **Ny**.
1. Välj panelen Azure Data Lake Storage Gen2 i listan och klicka på **Fortsätt**.
1. Ange dina autentiseringsuppgifter. Kontonyckel, tjänsthuvudnamn och hanterad identitet stöds för närvarande autentiseringstyper. Klicka på testanslutningen för att kontrollera att dina autentiseringsuppgifter är korrekta. 
1. Klicka på **Skapa** när du är klar.

## <a name="create-pipeline"></a>Skapa pipeline

En pipeline innehåller det logiska flödet för en körning av en uppsättning aktiviteter. I det här avsnittet ska du skapa en pipeline som innehåller en kopieringsaktivitet som äter data från Azure Data Lake Gen 2 i en SQL-pool.

1. Gå till fliken **Orchestrate.** Klicka på plusikonen bredvid pipeline-huvudet och välj **Pipeline**.
1. Dra **Kopiera data** till pipeline-arbetsytan under **Flytta och omforma** i aktivitetsfönstret.
1. Klicka på kopieringsaktiviteten och gå **New** till fliken **Källa.**
1. Välj Azure Data Lake Storage Gen2 när datalagringen lagras och klicka på Fortsätt.
1. Välj Avgränsadtext som format och klicka på Fortsätt.
1. I fönstret Ange egenskaper väljer du den ADLS-länkade tjänst som du skapade. Ange källdatas filsökväg och ange om den första raden har ett huvud. Du kan importera schemat från filarkivet eller en exempelfil. Klicka på OK när du är klar.
1. Gå till fliken **Sink.** Klicka på **Ny** om du vill skapa en ny sink-datauppsättning.
1. Välj Azure Data Lake Storage gen2 som datalager och klicka på Fortsätt.
1. Välj Avgränsadtext som format och klicka på Fortsätt.
1. I fönstret Ange egenskaper väljer du den ADLS-länkade tjänst som du skapade. Ange sökvägen till mappen där du vill skriva data. Klicka på OK när du är klar.

## <a name="debug-and-publish-pipeline"></a>Felsöka och publicera pipeline

När du är klar med konfigurationen av pipelinen kan du köra en felsökningskörning innan du publicerar artefakterna för att kontrollera att allt är korrekt.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 
1. När pipelinen kan köras väljer du **Publicera alla**i det övre verktygsfältet . Den här åtgärden publicerar entiteter (datauppsättningar och pipelines) som du har skapat till Tjänsten Synapse Analytics.
1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill se meddelandemeddelanden klickar du på klockknappen längst upp till höger. 


## <a name="trigger-and-monitor-the-pipeline"></a>Utlösa och övervaka pipelinen

I det här steget utlöser du pipelinen som publicerades i föregående steg manuellt. 

1. Välj **Lägg till utlösare** i verktygsfältet och välj sedan **Utlösare nu**. På sidan **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.  
1. Gå till fliken **Bildskärm** i det vänstra sidofältet. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **Åtgärder** för att visa aktivitetsinformation och köra pipelinen igen.
1. Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). I det här exemplet finns det bara en aktivitet, så du ser bara en post i listan. Om du vill se mer information om kopieringsåtgärden väljer du länken för **detaljer** (glasögonikonen) i kolumnen **Actions** (Åtgärder). Välj **Pipeline Runs** högst upp för att gå tillbaka till vyn Pipeline Runs. Välj **Uppdatera** för att uppdatera vyn.
1. Kontrollera att dina data är korrekt skrivna i SQL-poolen.


## <a name="next-steps"></a>Nästa steg

Mer information om dataintegrering för Synapse Analytics finns [i inmatningsdata i en SQL-poolartikel.](data-integration-sql-pool.md)
