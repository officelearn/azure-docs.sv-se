---
title: "Träna om Machine Learning-modeller via programmering | Microsoft Docs"
description: "Lär dig mer om att träna om en modell och uppdatera webbtjänsten för att använda den nya tränade modellen i Azure Machine Learning programmässigt."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: 7ae4f977-e6bf-4d04-9dde-28a66ce7b664
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: raymondl;garye;v-donglo
ms.openlocfilehash: c56ce659766536772d203d0366ef6b53e544a82b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-machine-learning-models-programmatically"></a>Omtrimning av Azure Machine Learning-modeller via programmering
I den här genomgången får lära du dig att träna programmässigt om en Azure Machine Learning-webbtjänst med hjälp av C# och Machine Learning Batch Execution service.

När du har retrained modellen, visar hur du uppdaterar modellen i förutsägande webbtjänsten följande genomgång:

* Om du har distribuerat en klassisk webbtjänst i Machine Learning-webbtjänster portal finns [träna om en klassisk webbtjänst](retrain-a-classic-web-service.md). 
* Om du har distribuerat en ny webbtjänst finns [träna om en ny webbtjänst med hjälp av Machine Learning Management-cmdlets](retrain-new-web-service-using-powershell.md).

En översikt över omtränings-processen, se [träna om en Maskininlärningsmodell](retrain-machine-learning-model.md).

Om du vill börja med din befintliga nya Azure Resource Manager baserad webbtjänst finns [träna om en befintlig förutsägande webbtjänst](retrain-existing-resource-manager-based-web-service.md).

## <a name="create-a-training-experiment"></a>Skapa ett experiment utbildning
För det här exemplet använder du ”exempel 5: tåg och Test, utvärdera för binär klassificering: vuxna Dataset” från Microsoft Azure Machine Learning-prover. 

Skapa experimentet:

1. Logga in till Microsoft Azure Maskininlärning Studio. 
2. Klicka på nedre högra hörnet av instrumentpanelen, **ny**.
3. Välj exempel 5 från Microsoft-Samples.
4. Byt namn på experimentet överst på arbetsytan för experimentet väljer du namnet på experiment ”exempel 5: tåg och Test, utvärdera för binär klassificering: vuxna Dataset”.
5. Typ av inventering modell.
6. Längst ned i arbetsytan för experimentet klickar du på **kör**.
7. Klicka på **Konfigurera webbtjänsten** och välj **Omtränings webbtjänsten**. 

Nedan visas första försöket.
   
   ![Första försöket.][2]


## <a name="create-a-predictive-experiment-and-publish-as-a-web-service"></a>Skapa en prediktivt experiment och publicera som en webbtjänst
Nu kan du skapa ett Predicative Experiment.

1. Längst ned i arbetsytan för experimentet klickar du på **konfigurera Web Service** och välj **förutsägande webbtjänsten**. Detta sparar modellen som en Tränad modell och lägger till web service ingående och utgående moduler. 
2. Klicka på **Run** (Kör). 
3. När experimentet har slutförts klickar du på **distribuera webbtjänsten [klassisk]** eller **distribuera webbtjänsten [ny]**.

> [!NOTE] 
> Om du vill distribuera en ny webbtjänst måste du ha tillräckliga behörigheter i prenumerationen som du distribuerar webbtjänsten. Mer information finns i [hantera en webbtjänst med hjälp av Azure Machine Learning-webbtjänster portal](manage-new-webservice.md). 

## <a name="deploy-the-training-experiment-as-a-training-web-service"></a>Distribuera utbildning experiment som en webbtjänst för utbildning
För att träna om den tränade modellen, måste du distribuera träningsexperiment som du har skapat som en webbtjänst för Retraining. Den här webbtjänsten måste en *Web Service utdata* modul som är ansluten till den  *[Träningsmodell] [ train-model]*  modulen för att kunna skapa nya tränats modeller.

1. För att återgå till utbildning experimentet klickar du på ikonen experiment i den vänstra rutan och klicka på experiment med namnet inventering modellen.  
2. Skriv i sökrutan Experiment sökobjekt webbtjänsten. 
3. Dra en *webbtjänst* till arbetsytan för experimentet och koppla dess utdata till den *Rensa Data som saknas* modul.  Detta säkerställer att omtränings data bearbetas på samma sätt som den ursprungliga informationen för utbildning.
4. Dra två *webbtjänsten utdata* moduler till arbetsytan för experimentet. Ansluta utdata från den *Träningsmodell* modul till en och utdata från den *utvärdera modell* modul till den andra. Web service-utdata för **Träningsmodell** ger oss den nya tränade modellen. Utdata som är kopplade till **utvärdera modell** returnerar att modulen utdata, vilket är prestandaresultat.
5. Klicka på **Run** (Kör). 

Därefter måste du distribuera utbildning experiment som en webbtjänst som producerar en tränad modell och utvärderingsresultat av modellen. För att åstadkomma detta kan din nästa uppsättning åtgärder, beroende på om du arbetar med en klassisk webbtjänst eller en ny webbtjänst.  

**Klassiska webbtjänst**

Längst ned i arbetsytan för experimentet klickar du på **konfigurera Web Service** och välj **distribuera webbtjänsten [klassisk]**. Webbtjänsten **instrumentpanelen** visas med API-nyckel och API-hjälpsidan för Batch-körningen. Batch Execution metoden kan användas för att skapa tränade modeller.

**Ny webbtjänst**

Längst ned i arbetsytan för experimentet klickar du på **konfigurera Web Service** och välj **distribuera webbtjänsten [ny]**. Web Service Azure Machine Learning-webbtjänster portal öppnar sidan distribuera tjänsten. Ange ett namn för webbtjänsten och välj en betalningsplan, och klicka sedan **distribuera**. Endast Batch Execution-metoden kan användas för att skapa tränade modeller

I båda fallen när experimentet har slutförts, resulterande arbetsflödet ska se ut så här:

![Resulterande arbetsflödet när du kör.][4]



## <a name="retrain-the-model-with-new-data-using-bes"></a>Träna om modellen med nya data med BES
I det här exemplet använder du C# skapa omtränings programmet. Du kan också använda exempelkoden Python eller R för att utföra den här uppgiften.

Att anropa Omtränings-API:

1. Skapa ett C#-konsolprogram i Visual Studio: **ny** > **projekt** > **Visual C#** > **Windows Klassiska Desktop** > **konsolen App (.NET Framework)**.
2. Logga in på portalen Machine Learning-webbtjänst.
3. Om du arbetar med en klassisk webbtjänst klickar du på **klassiska webbtjänster**.
   1. Klicka på den webbtjänst som du arbetar med.
   2. Klicka på standardslutpunkten.
   3. Klicka på **använda**.
   4. Längst ned i den **förbruka** sidan den **exempelkod** klickar du på **Batch**.
   5. Fortsätt till steg 5 i den här proceduren.
4. Om du arbetar med en ny webbtjänst klickar du på **Web Services**.
   1. Klicka på den webbtjänst som du arbetar med.
   2. Klicka på **använda**.
   3. AT längst ned i förbruka sidan den **exempelkod** klickar du på **Batch**.
5. Kopiera C# exempelkod för batchkörning och klistra in den i filen Program.cs, vilket gör att namnområdet intakt.

Lägg till Nuget-paketet Microsoft.AspNet.WebApi.Client som anges i kommentarerna. Om du vill lägga till en referens till Microsoft.WindowsAzure.Storage.dll, kan du först behöva installera klientbiblioteket för Microsoft Azure storage-tjänster. Mer information finns i [Windows lagringstjänster](https://www.nuget.org/packages/WindowsAzure.Storage).

### <a name="update-the-apikey-declaration"></a>Uppdatera apikey deklarationen
Leta upp den **apikey** deklaration.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

I den **grundläggande förbrukning info** avsnitt i den **förbruka** , leta upp den primära nyckeln och kopiera den till den **apikey** deklaration.

### <a name="update-the-azure-storage-information"></a>Uppdatera Azure Storage-informationen
Exempelkoden BES överför en fil från en lokal enhet (till exempel ”C:\temp\CensusIpnput.csv”) till Azure Storage, bearbetar den och skriver resultatet till Azure Storage.  

För att åstadkomma detta måste du hämta lagring namn och nyckel behållaren kontoinformationen för ditt lagringskonto från den klassiska Azure-portalen och uppdatera motsvarande värden i koden. 

1. Logga in på den klassiska Azure-portalen.
2. Klicka på den vänstra navigeringsfönstret i kolumnen **lagring**.
3. Välj en för att lagra retrained modellen från listan över storage-konton.
4. Längst ned på sidan klickar du på **hantera åtkomstnycklar**.
5. Kopiera och spara den **primära åtkomstnyckeln** och Stäng dialogrutan. 
6. Överst på sidan, klickar du på **behållare**.
7. Välj en befintlig behållare eller skapa en ny och spara namnet.

Leta upp den *StorageAccountName*, *StorageAccountKey*, och *StorageContainerName* deklarationer och uppdatera värden som du sparade från Azure-portalen.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure Storage Account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage Key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage Container name

Du måste också kontrollera indatafilen är tillgänglig på den plats du anger i koden. 

### <a name="specify-the-output-location"></a>Ange platsen för utdata
När du anger platsen i nyttolasten för begäran om tillägg av filen anges i *RelativeLocation* måste anges som ilearner. 

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
> Namnen på utdata-platser kan skilja sig från de som finns i den här genomgången utifrån den ordning som du har lagt till web service utdata moduler. Eftersom du ställt in den här träningsexperiment med två utdata, inkluderar resultaten platsinformation för lagring för båda.  
> 
> 

![Omtränings utdata][6]

Diagram över 4: Omtränings utdata.

## <a name="evaluate-the-retraining-results"></a>Utvärdera Omtränings resultaten
När du kör programmet innehåller URL: en och SAS-token krävs för att komma åt utvärderingsresultaten utdata.

Du kan se prestandaresultat retrained modellen genom att kombinera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* från resultatet för *output2* (som visas i föregående omtränings utdata avbildningen) och klistra in den fullständiga URL: en i webbläsarens adressfält.  

Granska resultaten för att avgöra om den nyligen tränade modellen utför bra för att ersätta den befintliga versionen.

Kopiera den *BaseLocation*, *RelativeLocation*, och *SasBlobToken* utdata-resultaten ska du använda dem under omtränings-processen.

## <a name="next-steps"></a>Nästa steg
Om du har distribuerat förutsägande webbtjänsten genom att klicka på **distribuera webbtjänsten [klassisk]**, se [träna om en klassisk webbtjänst](retrain-a-classic-web-service.md).

Om du har distribuerat förutsägande webbtjänsten genom att klicka på **distribuera webbtjänsten [ny]**, se [träna om en ny webbtjänst med hjälp av Machine Learning Management-cmdlets](retrain-new-web-service-using-powershell.md).

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
