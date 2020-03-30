---
title: 'Kör R-skript: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Kör R-skript i Azure Machine Learning för att köra R-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: f038293b48956ac89314e426df3f5dc491954df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064207"
---
# <a name="execute-r-script"></a>Köra R-skript

I den här artikeln beskrivs hur du använder modulen **Kör R-skript** för att köra R-kod i din Azure Machine Learning designer (förhandsversion) pipeline.

Med R kan du utföra uppgifter som för närvarande inte stöds av befintliga moduler, till exempel: 
- Skapa anpassade dataomvandlingar
- Använd dina egna mått för att utvärdera förutsägelser
- Skapa modeller med hjälp av algoritmer som inte implementeras som fristående moduler i designern

## <a name="r-version-support"></a>Stöd för R-versionen

Azure Machine Learning designer använder CRAN (Comprehensive R Archive Network) distribution av R. Den version som för närvarande används är CRAN 3.5.1.

## <a name="supported-r-packages"></a>R-paket som stöds

R-miljön är förinstallerad med över 100 paket. En fullständig lista finns i avsnittet [Förinstallerade R-paket](#pre-installed-r-packages).

Du kan också lägga till följande kod i en **Körning R Script-modul** och se de installerade paketen.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Installera R-paket
Om du vill installera ytterligare `install.packages()` R-paket använder du metoden. Var noga med att ange CRAN-databasen. Paket installeras för varje **Kör R Script-modul** och delas inte mellan andra Kör **R-skriptmoduler.**

Det här exemplet visar hur du installerar Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
  > Kontrollera om paketet redan finns innan du installerar det för att undvika att upprepa installationen. Som `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` i ovanstående exempelkod. Upprepad installation kan orsaka timeout för webbtjänstbegäran.     

## <a name="upload-files"></a>Överföra filer
**Kör R-skriptet** stöder överföring av filer med Azure Machine Learning R SDK.

I följande exempel visas hur du laddar upp en bildfil i **Execute R Script:**
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

När pipelinekörningen är klar kan du förhandsgranska bilden på modulens högra panel

> [!div class="mx-imgBorder"]
> ![Uppladdad bild](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Konfigurera Kör R-skript

Modulen **Kör R-skript** innehåller exempelkod som du kan använda som utgångspunkt. Om du vill konfigurera modulen **Kör R-skript** anger du en uppsättning indata och kod som ska köras.

![R-modul](media/module/execute-r-script.png)

Datauppsättningar som lagras i designern konverteras automatiskt till en R-dataram när de läses in med den här modulen.

1.  Lägg till modulen **Kör R-skript** i pipelinen.

  

1. Anslut alla indata som behövs av skriptet. Indata är valfria och kan innehålla data och ytterligare R-kod.

    * **Dataset1**: Referera till `dataframe1`den första indata som . Indatauppsättningen måste formateras som en CSV, TSV, ARFF eller så kan du ansluta en Azure Machine Learning-datauppsättning.

    * **Dataset2**: Referera till `dataframe2`den andra indata som . Den här datauppsättningen måste också formateras som en CSV-, TSV-, ARFF-fil eller som en Azure Machine Learning-datauppsättning.

    * **Script Bundle**: Den tredje ingången accepterar ZIP-filer. Den zippade filen kan innehålla flera filer och flera filtyper.

1. Skriv eller klistra in giltigt R-skript i textrutan **R-skript.**

    För att hjälpa dig att komma igång fylls i textrutan **R-skript** med exempelkod som du kan redigera eller ersätta.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main
    # which is the entry point for this module.

    # The entry point function can contain up to two input arguments:
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a zip file is connected to the third input port, it is
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * Skriptet måste innehålla `azureml_main`en funktion med namnet , som är startpunkten för den här modulen.

 * Ingångsfunktionen kan innehålla upp till `Param<dataframe1>` två indataargument: och`Param<dataframe2>`
 
   > [!NOTE]
    > Data som skickas till modulen **Kör R-skript** refereras `dataframe1` som `dataframe2`och , vilket `dataset1`skiljer `dataset2`sig från Azure Machine Learning designer (designerreferensen som , ). Kontrollera att indata refereras korrekt i skriptet.  
 
    > [!NOTE]
    >  Befintlig R-kod kan behöva mindre ändringar för att köras i en designerpipeline. Indata som du tillhandahåller i CSV-format bör till exempel uttryckligen konverteras till en datauppsättning innan du kan använda dem i koden. Data- och kolumntyper som används på R-språket skiljer sig också på vissa sätt från de data- och kolumntyper som används i designern.

1.  **Slumpmässigt frö:** Skriv ett värde som ska användas i R-miljön som slumpmässigt frövärde. Den här parametern `set.seed(value)` motsvarar anrop i R-kod.  

1. Skicka pipelinen.  

## <a name="results"></a>Resultat

**Modulen Kör R-skript** kan returnera flera utdata, men de måste anges som R-dataramar. Dataramar konverteras automatiskt till datauppsättningar i designern för kompatibilitet med andra moduler.

Standardmeddelanden och fel från R returneras till modulens logg.

Om du behöver skriva ut resultat i R-skriptet kan du hitta de utskrivna resultaten i **70_driver_log** under fliken **Utdata+loggar** på modulens högra panel.

## <a name="sample-scripts"></a>Exempelskript

Det finns många sätt att utöka pipelinen med hjälp av anpassade R-skript.  Det här avsnittet innehåller exempelkod för vanliga uppgifter.


### <a name="add-r-script-as-an-input"></a>Lägga till R-skript som indata

**Modulen Kör R-skript** stöder godtyckliga R-skriptfiler som indata. För att kunna göra det måste de överföras till din arbetsyta som en del av ZIP-filen.

1. Om du vill ladda upp en ZIP-fil som innehåller R-kod till arbetsytan går du till sidan **Datasets** tillgång, klickar på **Skapa datauppsättning**och väljer sedan **Från lokal fil** och alternativet Fildatauppsättningstyp. **File**  

1. Kontrollera att den zippade filen är tillgänglig i listan **Mina datauppsättningar** under kategorin **Datauppsättningar** i det vänstra modulträdet.

1.  Anslut datauppsättningen till indataporten **Script Bundle.**

1. Alla filer som finns i ZIP-filen är tillgängliga under pipelinekörningstiden. 

    Om skriptpaketfilen innehöll en katalogstruktur bevaras strukturen. Du måste dock ändra koden för att förbereda katalogen **./Script Bundle** till sökvägen.

### <a name="process-data"></a>Bearbeta data

Följande exempel visar hur du skalar och normaliserar indata:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Läsa en ZIP-fil som indata

Det här exemplet visar hur du använder en datauppsättning i en ZIP-fil som indata till modulen **Kör R-skript.**

1. Skapa datafilen i CSV-format och ge den namnet "mydatafile.csv".
1. Skapa en ZIP-fil och lägg till CSV-filen i arkivet.
1. Ladda upp den zippade filen till arbetsytan Azure Machine Learning. 
1. Anslut den resulterande datauppsättningen till **ScriptBundle-indata** för modulen **Kör R-skript.**
1. Använda följande kod för att läsa CSV-data från den zippade filen.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikera rader

Det här exemplet visar hur du replikerar positiva poster i en datauppsättning för att balansera exemplet:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Skicka R-objekt mellan Kör R-skriptmoduler

Du kan skicka R-objekt mellan instanser av modulen **Kör R-skript** med hjälp av den interna serialiseringsmekanismen. Det här exemplet förutsätter att du `A` vill flytta R-objektet som namnges mellan två **Kör R-skriptmoduler.**

1. Lägg till den första **execute r-skriptmodulen** i pipelinen och skriv följande kod `A` i textrutan **R-skript** för att skapa ett serialiserat objekt som en kolumn i modulens utdatatabell:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Den explicita konverteringen till heltalstypen görs eftersom `Raw` serialiseringsfunktionen matar ut data i R-format, som inte stöds av designern.

1. Lägg till en andra instans av modulen **Kör R-skript** och anslut den till utdataporten för den föregående modulen.

1. Skriv följande kod i textrutan **R** `A` Script för att extrahera objekt från indatatabellen. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Förinstallerade R-paket

Den aktuella listan över förinstallerade R-paket som är tillgängliga att använda:

|              |            | 
|--------------|------------| 
| Paket      | Version    | 
| Askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| start         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| cirkumflex        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| klass        | 7.3-15     | 
| cli          | 1.1.0      | 
| klippare        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datauppsättningar     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| utvärdera     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Generika     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafik     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matris       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| Ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex (på)      | 0.13       | 
| verktyg        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun (på ett sätt)         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
