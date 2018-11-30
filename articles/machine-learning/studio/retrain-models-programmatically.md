---
title: Träna om Machine Learning Studio-modeller via programmering – Azure | Microsoft Docs
description: Lär dig hur du tränar en modell och uppdatera webbtjänsten för att använda den nyligen tränade modellen i Azure Machine Learning programmässigt.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: (previous ms.author=yahajiza, author=YasinMSFT)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.openlocfilehash: b38143fe6f1f1cf3e65a2989e1b0a71c28530b2a
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313393"
---
# <a name="retrain-machine-learning-studio-models-programmatically"></a>Träna om Machine Learning Studio-modeller via programmering
I den här genomgången får du lära dig hur du programmässigt omtrimning av en Azure Machine Learning Studio web service med hjälp av C# och Machine Learning Batch Execution service.

När du har modellkomponenten modellen visar följande genomgångar hur du uppdaterar modellen i din förutsägbar webbtjänst:

* Om du har distribuerat en klassisk webbtjänst i Machine Learning Web Services-portalen finns i [Omtrimma en klassisk webbtjänst](retrain-a-classic-web-service.md). 
* Om du har distribuerat en ny webbtjänst finns i [omtrimning av en ny webbtjänst med hjälp av Machine Learning Management-cmdletar](retrain-new-web-service-using-powershell.md).

En översikt över omtränings processen, se [träna en Maskininlärningsmodell](retrain-machine-learning-model.md).

Om du vill börja med din befintliga nya Azure Resource Manager-baserade webbtjänst finns i [tränar en förutsägbar webbtjänst](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Skapa ett träningsexperiment
I det här exemplet ska du använda ”exempel 5: träna, testa, utvärdera för binär klassificering: vuxna datauppsättningen” från Microsoft Azure Machine Learning-exempel. 

Skapa experimentet:

1. Logga in på Microsoft Azure Machine Learning Studio. 
2. I nederkant högra hörnet av instrumentpanelen, klickar du på **New**.
3. Välj exempel 5 från Microsoft-Samples.
4. Om du vill byta namn på experimentet överst i arbetsytan för experimentet väljer du namnet på experiment ”exempel 5: träna, testa, utvärdera för binär klassificering: vuxna datauppsättningen”.
5. Typ av inventering modell.
6. Längst ned på arbetsytan för experimentet klickar du på **kör**.
7. Klicka på **konfigurera web service** och välj **Retraining webbtjänsten**. 

Nedan visas första försöket.
   
   ![Inledande experiment.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Skapa ett förutsägbart experiment och publicera som en webbtjänst
Därefter skapar du ett Predicative Experiment.

1. Längst ned på arbetsytan för experimentet klickar du på **konfigurera Web Service** och välj **förutsägande webbtjänsten**. Detta sparar modellen som en Träningsmodell och lägger till web service indata och utdata moduler. 
2. Klicka på **Run** (Kör). 
3. När experimentet har körts, klickar du på **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [nyhet]**.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräcklig behörighet i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Distribuera träningsexperimentet som en webbtjänst för utbildning
Du måste distribuera träningsexperimentet som du har skapat som en webbtjänst för Retraining för att träna om den tränade modellen. Den här webbtjänsten måste en *Web Service utdata* moduler som är anslutna till den *[Träningsmodell] [ train-model]* modulen för att kunna skapa nya tränas modeller.

1. Om du vill gå tillbaka till träningsexperimentet, klickar du på ikonen experiment i den vänstra rutan och sedan på experimentet med namnet insamlade modellen.  
2. I sökrutan Experiment sökobjekt skriver du webbtjänsten. 
3. Dra en *Webbtjänstindata* modulen till experimentet och koppla dess utdata till den *Rensa Data som saknas* modulen.  Detta säkerställer att dina omtränings data bearbetas på samma sätt som den ursprungliga informationen för utbildning.
4. Dra två *webbtjänsten utdata* moduler till experimentets arbetsyta. Anslut utdataporten för den *Träningsmodell* modul till en och utdata från den *utvärdera modell* modul till en annan. Web service-utdata för **Träningsmodell** ger oss den nya tränade modellen. Utdata som är kopplade till **utvärdera modell** returnerar modulen utdatafiler, vilket är prestandaresultat.
5. Klicka på **Run** (Kör). 

Därefter måste du distribuera träningsexperimentet som en webbtjänst som producerar en tränad modell och utvärderingsresultat av modellen. För att åstadkomma detta är ditt nästa uppsättning åtgärder beroende på om du arbetar med en klassisk webbtjänst eller en ny webbtjänst.  

**Klassisk webbtjänst**

Längst ned på arbetsytan för experimentet klickar du på **konfigurera Web Service** och välj **distribuera webbtjänsten [klassisk]**. Webbtjänsten **instrumentpanelen** visas med API-nyckeln och API-hjälpsidan för batchkörning. Endast Batch Execution-metoden kan användas för att skapa anpassade modeller.

**Ny webbtjänst**

Längst ned på arbetsytan för experimentet klickar du på **konfigurera Web Service** och välj **distribuera webbtjänsten [nyhet]**. Web Service Azure Machine Learning Web Services-portalen öppnar sidan distribuera tjänsten. Skriv ett namn för din webbtjänst och väljer en betalningsplan och sedan på **distribuera**. Batch Execution metoden kan användas för att skapa anpassade modeller

I båda fallen när experimentet har slutförts, resulterande arbetsflödet ska se ut så här:

![Resulterande arbetsflöde efter att köra.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Träna modellen med nya data med hjälp av BES
I det här exemplet använder du C# att skapa omtränings-program. Du kan också använda exempelkoden Python eller R för att åstadkomma detta.

Att anropa API: erna Retraining:

1. Skapa ett C#-konsolprogram i Visual Studio: **New** > **projekt** > **Visual C#** > **Windows Classic Desktop** > **konsolprogram (.NET Framework)**.
2. Logga in på Machine Learning-webbtjänst-portalen.
3. Om du arbetar med en klassisk webbtjänst, klickar du på **klassiska webbtjänster**.
   1. Klicka på den webbtjänst som du arbetar med.
   2. Klicka på standardslutpunkt.
   3. Klicka på **förbrukar**.
   4. Längst ned på den **förbruka** sidan den **exempelkoden** klickar du på **Batch**.
   5. Fortsätt till steg 5 i den här proceduren.
4. Om du arbetar med en ny webbtjänst, klickar du på **webbtjänster**.
   1. Klicka på den webbtjänst som du arbetar med.
   2. Klicka på **förbrukar**.
   3. AT längst ned på förbruka sidan den **exempelkoden** klickar du på **Batch**.
5. Kopiera exempel C# Platskod för batchkörning och klistra in den i filen Program.cs Se namnområdet förblir intakta.

Lägg till Nuget-paketet system.NET.http.Formatting som anges i kommentarerna. Om du vill lägga till referensen till Microsoft.WindowsAzure.Storage.dll, kan du först behöva installera klientbiblioteket för Microsoft Azure storage-tjänster. Mer information finns i [Windows lagringstjänster](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Uppdatera apikey deklaration
Leta upp den **apikey** deklaration.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

I den **grundläggande förbrukning info** delen av den **förbruka** sidan letar du upp den primära nyckeln och kopiera den till den **apikey** deklaration.

### <a name="update-the-azure-storage-information"></a>Uppdatera informationen om Azure Storage
Exempelkoden BES laddar upp en fil från en lokal enhet (till exempel ”C:\temp\CensusIpnput.csv”) till Azure Storage, bearbetar dessa och skriver resultatet tillbaka till Azure Storage.  

För att åstadkomma detta måste du hämta Storage namn, nyckel och behållare kontoinformationen för ditt lagringskonto från den klassiska Azure-portalen och uppdatera motsvarande värden i koden. 

1. Logga in på den klassiska Azure-portalen.
2. I den vänstra kolumnen klickar du på **Storage**.
3. Välj en för att lagra retrained modellen från listan över lagringskonton.
4. Längst ned på sidan klickar du på **hantera åtkomstnycklar**.
5. Kopiera och spara den **primära åtkomstnyckel** och Stäng dialogrutan. 
6. Överst på sidan klickar du på **behållare**.
7. Välj en befintlig behållare eller skapa en ny och spara namnet.

Leta upp den *StorageAccountName*, *StorageAccountKey*, och *StorageContainerName* deklarationer och uppdatera de värden du sparade från Azure-portalen.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Du måste också kontrollera indatafilen är tillgänglig på den plats du anger i koden. 

### <a name="specify-the-output-location"></a>Ange platsen för utdata
När du anger platsen i nyttolasten för begäran, tillägg till filen som anges i *RelativeLocation* måste anges som ilearner. 

Se följande exempel:

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you would like to use for your output file, and valid file extension (usually .csv for scoring results, or .ilearner for trained models)*/
            }
        },

> [!NOTE]
> Namnen på dina utdata-platser kan skilja sig från de i den här genomgången baserat på den ordning som du har lagt till web service utdata moduler. Eftersom du har konfigurerat den här träningsexperiment med två utdata kan inkludera resultatet storage platsinformation i båda.  
> 
> 

![Omtränings utdata][6]

Diagram 4: Träna utdata.

## <a name="evaluate-the-retraining-results"></a>Utvärdera Omtränings resultaten
När du kör programmet innehåller utdata URL: en och SAS-token behövs för att komma åt utvärderingar.

Du kan se prestandaresultat retrained modellen genom att kombinera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från utdataresultat som för *output2* (som visas i den föregående bilden för omtränings utdata) och klistra in den fullständiga URL: en i webbläsarens adressfält.  

Granska resultaten för att avgöra om den nyligen tränade modellen utför tillräckligt bra för att ersätta den befintliga.

Kopiera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från utdataresultat som, ska du använda dem under omtränings.

## <a name="next-steps"></a>Nästa steg
Om du har distribuerat förutsägbar webbtjänst genom att klicka på **distribuera webbtjänsten [klassisk]**, se [Omtrimma en klassisk webbtjänst](retrain-a-classic-web-service.md).

Om du har distribuerat förutsägbar webbtjänst genom att klicka på **distribuera webbtjänsten [nyhet]**, se [omtrimning av en ny webbtjänst med hjälp av Machine Learning Management-cmdletar](retrain-new-web-service-using-powershell.md).

<!-- Retrain a New web service using the Machine Learning Management REST API -->


[1]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
