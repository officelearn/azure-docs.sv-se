---
title: 'Steg 3: Skapa ett nytt Machine Learning Studio-experiment'
titleSuffix: Azure Machine Learning Studio
description: 'Steg 3 av utveckla en förutsägelselösning genomgång: Skapa en ny träningsexperiment i Azure Machine Learning Studio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: garyericson
ms.author: garye
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/23/2017
ms.openlocfilehash: aee0370b4d1ea1bffe8e6723c14e6248ebbd808a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506404"
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-studio-experiment"></a>Genomgång steg 3: Skapa ett nytt Azure Machine Learning Studio-experiment
Det här är det tredje steget i den här genomgången [utveckla en lösning för förutsägelseanalys i Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Skapa en Machine Learning-arbetsyta](walkthrough-1-create-ml-workspace.md)
2. [Överför befintliga data](walkthrough-2-upload-data.md)
3. **Skapa ett nytt experiment**
4. [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuera webbtjänsten](walkthrough-5-publish-web-service.md)
6. [Få åtkomst till webbtjänsten](walkthrough-6-access-web-service.md)

- - -
Nästa steg i den här genomgången är att skapa ett experiment i Machine Learning Studio som använder den datauppsättning som vi laddade upp.  

1. I Studio, klickar du på **+ ny** längst ned i fönstret.
2. Välj **EXPERIMENT**, och välj sedan ”tomt Experiment”. 

    ![Skapa ett nytt experiment][0]

2. Välj det fördefinierade experimentnamnet överst på arbetsytan och Byt namn på den till något beskrivande.

    ![Byt namn på experimentet][5]
   
   > [!TIP]
   > Det är en bra idé att fylla i **sammanfattning** och **beskrivning** för experiment i den **egenskaper** fönstret. De här egenskaperna ger dig möjlighet att dokumentera experimentet så att alla som tittar på det senare kan förstå dina mål och metoder.
   > 
   > ![Egenskaper för experiment][6]
   > 
3. Expandera i modulpaletten till vänster om arbetsytan för experimentet **sparade datauppsättningar**.
4. Leta upp datauppsättningen som du skapade under **Mina datauppsättningar** och dra den till arbetsytan. Du kan också hitta datauppsättningen genom att ange namnet i den **Search** rutan ovanför paletten.  

    ![Lägga till datauppsättningen till experimentet][7]

## <a name="prepare-the-data"></a>Förbereda data
Du kan visa de första 100 datarader och del statistisk information för hela datauppsättningen: Klicka på utdataporten för datauppsättningen (liten cirkel längst ned på sidan) och välj **visualisera**.  

Eftersom datafilen inte levererades med kolumnrubriker, Studio har tillhandahållit allmän rubriker (Kol1, Col2, *etc.*). Bra rubriker är inte nödvändigt för att skapa en modell, men de gör det lättare att arbeta med data i experimentet. Dessutom när du publicerar till slut den här modellen i en webbtjänst, så att rubrikerna identifiera kolumner för användare av tjänsten.  

Vi kan lägga till kolumnrubrikerna med hjälp av den [redigera Metadata] [ edit-metadata] modulen.
Du använder den [redigera Metadata] [ edit-metadata] modul för att ändra metadata som associeras med en datauppsättning. I det här fallet använda vi den för att ge mer vänliga namn för kolumnrubrikerna. 

Att använda [redigera Metadata][edit-metadata], du först ange vilka kolumner som ska ändras (i det här fallet, alla.) Ange sedan åtgärden som ska utföras på dessa kolumner (i det här fallet, ändra kolumnrubrikerna.)

1. På modulpaletten, skriver du ”metadata” i den **Search** box. Den [redigera Metadata] [ edit-metadata] visas i listan modulen.

2. Klicka och dra den [redigera Metadata] [ edit-metadata] modulen till arbetsytan och släpp den under den datauppsättning som vi lade till tidigare.

3. Ansluta datauppsättningen till den [redigera Metadata][edit-metadata]: Klicka på utdataporten för datauppsättningen (liten cirkel längst ned i datauppsättningen) genom att dra till indataporten för [redigera Metadata] [ edit-metadata] (den lilla cirkeln överst i modulen), släpper musknappen. Datauppsättningen och modulen vara anslutna även om du flyttar antingen på arbetsytan.
   
   Experimentet bör nu se ut ungefär så här:  
   
   ![Att lägga till redigera Metadata][1]
   
   Rött utropstecken visar att vi inte har ställts in egenskaperna för den här modulen ännu. Vi ska göra nu.
   
   > [!TIP]
   > Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. I det här fallet dubbelklickar du på den [redigera Metadata] [ edit-metadata] modulen och Skriv kommentaren ”Add kolumnrubrikerna”. Klicka på någon annanstans på arbetsytan för att stänga textrutan. Om du vill visa kommentaren klickar du på nedpilen i modulen.
   > 
   > ![Redigera Metadatamodul med kommentar har lagts till][8]
   > 
4. Välj [redigera Metadata][edit-metadata], och i den **egenskaper** rutan till höger om arbetsytan och klicka på **starta kolumnväljaren**.

5. I den **Markera kolumner** dialogrutan Välj alla rader i **tillgängliga kolumner** och klicka på > att flytta dem till **valda kolumner**.
   Dialogrutan bör se ut så här:

   ![Kolumnväljaren med alla kolumner har valts][2]

6. Klicka på den **OK** kryssmarkeringen.

7. I den **egenskaper** rutan Sök efter den **nya kolumnnamnen** parametern. Ange en lista med namnen för 21 kolumner i datauppsättningen, avgränsade med kommatecken och i kolumnordning i det här fältet. Du kan hämta kolumnnamnen i datauppsättningen-dokumentationen på webbplatsen UCI eller för att underlätta kan du kopiera och klistra in följande lista:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   Egenskapsrutan ser ut så här:
   
   ![Egenskaper för redigera Metadata][3]

> [!TIP]
> Om du vill kontrollera kolumnrubrikerna, kör experimentet (klicka på **kör** under arbetsytan för experimentet). När den är klar körs (en grön bock visas på [redigera Metadata][edit-metadata]), klicka på utdataporten för den [redigera Metadata] [ edit-metadata] -modulen Välj **visualisera**. Du kan visa utdata från alla moduler på samma sätt att visa förloppet för data via experimentet.
> 
> 

## <a name="create-training-and-test-datasets"></a>Skapa utbildning och testa datauppsättningar
Vi behöver några data att träna modellen och vissa att testa den.
Så i nästa steg i experimentet vi dela datauppsättningen i två olika datauppsättningar: en för vår modell och en för att testa den.

Detta gör vi använder den [dela Data] [ split] modulen.  

1. Hitta den [dela Data] [ split] modulen genom att dra den till arbetsytan och anslut den till den [redigera Metadata] [ edit-metadata] modulen.

2. Som standard är dela förhållandet mellan 0,5 och **Randomized dela** parametern anges. Det innebär att en slumpmässig hälften av data är utdata via en port på den [dela Data] [ split] modulen och hälften via den andra. Du kan justera de här parametrarna behållargruppens **slumptal** parameter för att ändra delningen av träning och testning av data. I det här exemplet vi lämnar dem som – är.
   
   > [!TIP]
   > Egenskapen **del av rader i den första utdatauppsättningen** avgör hur mycket data är utdata via den *vänstra* utgående port. Till exempel om du anger i förhållande till 0,7 är 70% av data utdata via den vänstra porten och 30% via rätt port.  
   > 
   > 

3. Dubbelklicka på den [dela Data] [ split] modulen och Skriv kommentaren, ”träning och testning data delas upp 50%”. 

Vi kan använda utdata för den [dela Data] [ split] modulen men vi vill, men nu ska vi välja att använda vänstra utdata som träningsdata och rätt utdata som testdata.  

Som vi nämnde i den [föregående steg](walkthrough-2-upload-data.md), kostnaden för misclassifying en hög kreditrisk som låg är fem gånger högre än kostnaden för misclassifying ett låga kreditrisk som hög. För att redovisa detta vi för att generera en ny datauppsättning som visar den här kostnaden-funktionen. I den nya datauppsättningen replikeras varje hög risk exempel fem gånger medan varje lågrisk-exempel inte replikeras.   

Vi kan göra replikeringen med R-kod:  

1. Leta upp och dra den [kör R-skript] [ execute-r-script] modul till experimentets arbetsyta. 

2. Anslut den vänstra utdataporten för den [dela Data] [ split] modul till den första ingångsporten (”Dataset1”) för den [kör R-skript] [ execute-r-script] modulen.

3. Dubbelklicka på den [kör R-skript] [ execute-r-script] modulen och ange kommentaren (Set kostnad justering).

4. I den **egenskaper** fönstret Ta bort standardtexten i den **R-skriptet** parametern och ange det här skriptet:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R-skript i modulen köra R-skript][9]

Vi behöver göra den här samma replikeringsåtgärden för varje utdata från den [dela Data] [ split] modulen så att data för utbildning och tester har samma flytta. Det enklaste sättet att göra detta är genom att duplicera den [kör R-skript] [ execute-r-script] modul som vi just skapade och ansluter det till en annan utgående port på den [dela Data] [ split] modulen.

1. Högerklicka på den [kör R-skript] [ execute-r-script] modul och välj **kopiera**.

2. Högerklicka på arbetsytan för experimentet och välj **klistra in**.

3. Dra den nya modulen på plats och sedan ansluta den rätta utdataporten för den [dela Data] [ split] modul till den första indataporten på detta nya [kör R-skript] [ execute-r-script] modulen. 

4. Längst ned på arbetsytan klickar du på **kör**. 

> [!TIP]
> Kopia av den kör R-skript-modulen innehåller samma skript som den ursprungliga modulen. När du kopierar och klistrar in en modul på arbetsytan, behåller alla egenskaper för ursprungligt kopian.  
> 
> 

Vår experimentet nu ser ut ungefär så här:

![Att lägga till Split-modulen och R-skript][4]

Mer information om hur du använder R-skript i dina experiment finns i [Utöka ditt experiment med R](extend-your-experiment-with-r.md).

**Nästa: [Träna och utvärdera modellerna](walkthrough-4-train-and-evaluate-models.md)**

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
