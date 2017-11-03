---
title: 'Steg 3: Skapa ett nytt experiment i Machine Learning | Microsoft Docs'
description: "Steg 3 i utveckla en förutsägelselösning genomgång: skapa ett nytt utbildning experiment i Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: a8f1764204740a8f5ef757e5e2ad63cfd43af150
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Genomgång steg 3: Skapa ett nytt Azure Machine Learning-experiment
Detta är det tredje steget i den här genomgången [utveckla en förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. **Skapa ett nytt experiment**
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
Nästa steg i den här genomgången är att skapa ett experiment i Machine Learning Studio som använder datauppsättningen som vi har överförts.  

1. Klicka på Studio **+ ny** längst ned i fönstret.
2. Välj **EXPERIMENT**, och välj sedan ”tomt Experiment”. 

    ![Skapa ett nytt experiment][0]

2. Välj experiment för standardnamnet överst på arbetsytan och Byt till ett beskrivande.

    ![Byt namn på experimentet][5]
   
   > [!TIP]
   > Är det en bra idé att fylla i **sammanfattning** och **beskrivning** för experiment i den **egenskaper** fönstret. De här egenskaperna ger dig möjlighet att dokumentera experimentet så att alla som tittar på det senare kan förstå dina mål och metoder.
   > 
   > ![Experiment egenskaper][6]
   > 
3. Expandera i modulpaletten till vänster om arbetsytan för experimentet **sparade datauppsättningar**.
4. Hitta datamängden som du skapade under **Mina datauppsättningar** och drar den till arbetsytan. Du kan också hitta datauppsättningen genom att ange namnet i den **Sök** rutan ovanför paletten.  

    ![Lägg till dataset i experimentet][7]

## <a name="prepare-the-data"></a>Förbered data
Du kan visa de första 100 dataraderna och del statistisk information för hela datauppsättningen: Klicka på utdataporten för datauppsättningen (liten cirkel längst ned) och välj **visualisera**.  

Eftersom filen inte levererades med kolumnrubriker Studio tillhandahåller allmänna rubriker (Kol1, Col2, *etc.*). Bra rubriker är inte nödvändigt att skapa en modell, men de gör det enklare att arbeta med data i experimentet. Dessutom när vi publicerar slutligen den här modellen i en webbtjänst, så att rubrikerna identifiera kolumner till användaren i tjänsten.  

Vi kan lägga till kolumnrubriker använder de [redigera Metadata] [ edit-metadata] modul.
Du använder den [redigera Metadata] [ edit-metadata] modul för att ändra metadata som associeras med en datamängd. I det här fallet använda vi den för att ge mer vänliga namn för kolumnrubrikerna. 

Att använda [redigera Metadata][edit-metadata], du först ange vilka kolumner som ska ändras (i det här fallet för alla.) Därefter måste ange du åtgärden som ska utföras på dessa kolumner (i det här fallet, ändra kolumnrubrikerna.)

1. På modulpaletten, skriver du ”metadata” i den **Sök** rutan. Den [redigera Metadata] [ edit-metadata] visas i modullistan.

2. Klicka och dra den [redigera Metadata] [ edit-metadata] modulen till arbetsytan och släpp nedan datauppsättningen som vi har lagt till tidigare.

3. Dataset för att ansluta den [redigera Metadata][edit-metadata]: Klicka på utdataporten för datauppsättningen (liten cirkel längst ned i datauppsättningen) genom att dra till indataport av [redigera Metadata] [ edit-metadata] (små cirkeln längst upp i modulen), släpper musknappen. Modulen och dataset vara ansluten även om du flyttar antingen på arbetsytan.
   
   Experimentet bör nu se ut ungefär så här:  
   
   ![Lägga till redigera Metadata][1]
   
   Rött utropstecken visar att vi inte har ställts in egenskaperna för den här modulen ännu. Vi ska göra nästa avsnitt.
   
   > [!TIP]
   > Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. I det här fallet dubbelklickar du på den [redigera Metadata] [ edit-metadata] modulen och Skriv kommentaren ”Lägg till kolumnrubrikerna”. Klicka på någon annanstans på arbetsytan för att stänga dialogrutan. Om du vill visa kommentaren klickar du på nedpilen i modulen.
   > 
   > ![Redigera Metadatamodul med kommentar har lagts till][8]
   > 
4. Välj [redigera Metadata][edit-metadata], och i den **egenskaper** till höger om arbetsytan klickar du på **starta kolumnväljaren**.

5. I den **Markera kolumner** dialogrutan Välj alla rader i **tillgängliga kolumner** och klicka på > att flytta dem till **valda kolumner**.
   Dialogrutan bör se ut så här:

   ![Kolumnväljaren med alla kolumner har valts][2]

6. Klicka på den **OK** är markerat.

7. I den **egenskaper** rutan Sök efter den **nya kolumnnamn** parameter. Ange en lista över namn för de 21 kolumnerna i datauppsättning, avgränsade med kommatecken och kolumnordning i det här fältet. Du kan hämta kolumner namnen i dataset-dokumentationen på webbplatsen UCI eller av praktiska skäl kan du kopiera och klistra in i följande lista:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   Egenskapsrutan ser ut så här:
   
   ![Egenskaper för redigera-Metadata][3]

> [!TIP]
> Om du vill kontrollera kolumnrubrikerna kör experimentet (klicka på **kör** under arbetsytan för experimentet). När körningen (en grön bock på [redigera Metadata][edit-metadata]), klicka på utdataporten för den [redigera Metadata] [ edit-metadata] modulen och välj **visualisera**. Du kan visa resultatet av alla moduler på samma sätt att visa förloppet för data via experimentet.
> 
> 

## <a name="create-training-and-test-datasets"></a>Skapa utbildning och testa datauppsättningar
Vi måste vissa data att träna modellen och vissa att testa den.
Så i nästa steg i experimentet vi dela datauppsättningen i två separata datauppsättningar: en för vår modell och en för att testa den.

Detta gör vi använder den [dela Data] [ split] modul.  

1. Hitta de [dela Data] [ split] , drar den till arbetsytan, och koppla den till den [redigera Metadata] [ edit-metadata] modul.

2. Som standard är delade förhållandet mellan 0,5 och **Randomized dela** parameter har angetts. Det innebär att en slumpmässig halvan av data utdata via en port för den [dela Data] [ split] modulen och hälften via den andra. Du kan justera parametrarna, samt de **slumptal** parameter, ändra delningen av träning och testning av data. I det här exemplet vi lämna dem som-är.
   
   > [!TIP]
   > Egenskapen **andel av rader i den första utdatauppsättningen** avgör hur mycket data som utdata med den *vänstra* utgående port. Till exempel om du anger förhållandet till 0,7 är 70% av data utdata genom vänstra porten och 30% via rätt port.  
   > 
   > 

3. Dubbelklicka på den [dela Data] [ split] modulen och Skriv kommentaren, ”utbildning/testning data dela 50%”. 

Vi kan använda utdata för den [dela Data] [ split] modulen men vi vill, men vi väljer att använda vänstra utdata som träningsdata och rätt utdata som tester.  

Som anges i den [föregående steg](walkthrough-2-upload-data.md), kostnaden för misclassifying en hög kreditrisk som låg är fem gånger högre än kostnaden för misclassifying en låg kreditrisk som hög. Kontot för den här vi för att generera en ny datamängd som visar den här kostnaden-funktionen. I den nya datamängden replikeras varje hög risk exempel fem gånger medan varje låg risk exempel inte replikeras.   

Vi kan göra replikeringen med hjälp av R-koden:  

1. Leta upp och dra den [köra R-skriptet] [ execute-r-script] modul på arbetsytan för experimentet. 

2. Anslut den vänstra utdataporten för den [dela Data] [ split] modul till den första indataporten (”Dataset1”) för den [köra R-skriptet] [ execute-r-script] modul.

3. Dubbelklicka på den [köra R-skriptet] [ execute-r-script] modulen och Skriv kommentaren ”ange kostnaden justering”.

4. I den **egenskaper** fönstret Ta bort standardtexten i den **R-skriptet** parametern och ange det här skriptet:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R-skriptet i modulen köra R-skriptet][9]

Vi behöver göra den här samma replikeringsåtgärden för varje utdata från den [dela Data] [ split] modulen så att data utbildning och tester har samma kostnadsjustering. Det enklaste sättet att göra detta är genom att duplicera den [köra R-skriptet] [ execute-r-script] modul som vi har gjort och ansluta det till ett annat utgående porten för den [dela Data] [ split] modul.

1. Högerklicka på den [köra R-skriptet] [ execute-r-script] modulen och välj **kopiera**.

2. Högerklicka på arbetsytan för experimentet och välj **klistra in**.

3. Dra modulen nya på plats och ansluter sedan den högra utdataporten för den [dela Data] [ split] modul till den första indataporten på detta nya [köra R-skriptet] [ execute-r-script] modul. 

4. Längst ned på arbetsytan klickar du på **kör**. 

> [!TIP]
> Kopia av modulen köra R-skriptet innehåller samma skript som den ursprungliga modulen. När du kopierar och klistrar in en modul på arbetsytan behåller alla egenskaper för ursprungligt kopian.  
> 
> 

Vårt experiment nu ser ut ungefär så här:

![Lägga till delade modulen och R-skript][4]

Mer information om hur du använder R-skript i experimenten finns [Utöka ditt experiment med R](extend-your-experiment-with-r.md).

**Nästa: [tåg och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
