---
title: 'Kör R-skript: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen köra R-skript i Azure Machine Learning-tjänsten för att köra R-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518058"
---
# <a name="execute-r-script"></a>Köra R-skript

Den här artikeln beskriver hur du använder den **kör R-skript** modul för att köra R-kod i experimentet visuella gränssnittet.

Med R, kan du utföra uppgifter som för närvarande inte stöds av befintliga moduler som: 
- Skapa anpassade Datatransformationer
- Använd dina egna mått för att utvärdera förutsägelser
- Skapa modeller med hjälp av algoritmer som inte är implementerat som fristående moduler i visuella gränssnittet

## <a name="r-version-support"></a>Stöd för R-version

Det visuella gränssnittet för Azure Machine Learning-tjänsten använder CRAN (Comprehensive R Archive Network) distribution av R. Den aktuella versionen är CRAN 3.5.1.

## <a name="supported-r-packages"></a>R-paket som stöds

R-miljö är förinstallerade med över 100 paket. En fullständig lista finns i avsnittet [förinstallerat R-paket](#pre-installed-r-packages).

Du kan också lägga till följande kod till någon **kör R-skript** modulen och för att se de installerade paket.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Installera R-paket
Om du vill installera ytterligare R-paket i `install.packages()` metod. Glöm inte att ange CRAN repository. Paket som är installerade för varje **kör R-skript** modulen, och inte delas mellan andra **kör R-skript** moduler.

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

## <a name="how-to-configure-execute-r-script"></a>Så här konfigurerar du kör R-skript

Den **kör R-skript** modulen innehåller exempelkod som du kan använda som startpunkt. Så här konfigurerar du den **kör R-skript** modulen, tillhandahåller en uppsättning indata och kod att köra.

![R-modul](media/module/execute-r-script.png)

Datauppsättningar som lagras i visuella gränssnittet konverteras automatiskt till en dataram i R vid har lästs in med den här modulen.

1.  Lägg till den **kör R-skript** modulen i experimentet.

    > [!NOTE]
    > Alla data som skickas till den **kör R-skript** modulen konverteras till R `data.frame` format.

1. Anslut alla indata som krävs av skriptet. Indata är valfria och kan innehålla data och ytterligare R-kod.

    * **Dataset1**: Referera till den första indatan som `dataframe1`. Datauppsättningen för indata måste formateras som en CSV, TVS, ARFF eller du kan ansluta en Azure Machine Learning-datauppsättning.

    * **Dataset2**: Referera till andra indata som `dataframe2`. Den här datauppsättningen också måste formateras som en CSV, TVS, ARFF fil, eller som en Azure Machine Learning-datauppsättning.

    * **Skriptet paket**: Tredje indata accepterar ZIP-filer. Den komprimerade filen kan innehålla flera filer och flera filtyper.

1. I den **R-skriptet** textrutan skriver eller klistrar du in giltig R-skript.

    Hjälper dig att komma igång, den **R-skriptet** textrutan är förifylld med exempelkod, som du kan redigera eller ersätta.

    * Skriptet måste innehålla en funktion som heter `azureml_main`, vilket är startpunkten för den här modulen.

    * Funktionens startadress kan innehålla upp till två inkommande argument: `Param<dataframe1>` och `Param<dataframe2>`
 
    > [!NOTE]
    >  Befintlig R-kod kan behöva mindre ändringar att köras i ett experiment med visuella gränssnittet. Till exempel ska indata som du anger i CSV-format explicit konverteras till en datauppsättning innan du kan använda den i din kod. Data och column typer som används i R-språket skiljer sig även på vissa sätt från data och column-typer som används i det visuella gränssnittet.

1.  **Slumpmässig dirigering**: Ange ett värde som ska användas i R-miljö som slumpmässiga seed-värdet. Den här parametern motsvarar att anropa `set.seed(value)` i R-kod.  

1. Kör experimentet.  

## <a name="results"></a>Resultat

Den **kör R-skript** moduler kan returnera flera utdata, men de måste anges som R dataramar. Dataramar konverteras automatiskt till visuella gränssnittet datauppsättningar för kompatibilitet med andra moduler.

Standard-meddelanden och fel från R returneras till modulens log.

## <a name="sample-scripts"></a>Exempelskript

Det finns många sätt som du kan utöka ditt experiment med hjälp av anpassade R-skript.  Det här avsnittet innehåller exempelkod för vanliga uppgifter.


### <a name="add-r-script-as-an-input"></a>Lägg till R-skript som indata

Den **kör R-skript** modulen stöder godtyckliga R-skript-filer som indata. Om du vill göra det, måste de överföras till din arbetsyta som en del av ZIP-filen.

1. Om du vill överföra en ZIP-fil som innehåller R-kod till din arbetsyta, klickar du på **New**, klickar du på **datauppsättning**, och välj sedan **från lokal fil** och **Zip-filen**alternativet.  

1. Kontrollera att den komprimerade filen är tillgänglig på den **sparade datauppsättningar** lista.

1.  Ansluta datauppsättningen till den **skriptet paket** indataporten.

1. Alla filer som finns i ZIP-filen är tillgängliga under experiment körtid. 

    Om skriptet samlingsfil innehöll en katalogstruktur, bevaras strukturen. Dock måste du ändra koden och Lägg till åtkomstgruppen katalogen **. / skript paketera** till sökvägen.

### <a name="process-data"></a>Bearbeta data

I följande exempel visas hur du skalar och normalisera indata:

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

Det här exemplet visas hur du använder en datauppsättning i en ZIP-fil som indata till den **kör R-skript** modulen.

1. Skapa datafilen i CSV-format och ge den namnet ”mydatafile.csv”.
1. Skapa en ZIP-fil och lägga till CSV-filen i arkivet.
1. Ladda upp den komprimerade filen till din Azure Machine Learning-arbetsyta. 
1. Anslut den resulterande datauppsättningen till den **ScriptBundle** indata av din **kör R-skript** modulen.
1. Med följande kod för att läsa CSV-data från ZIP-fil.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikera rader

Det här exemplet visar hur du replikerar positivt poster i en datauppsättning för att balansera exemplet:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Skicka R-objekt mellan moduler som kör R-skript

Du kan skicka R-objekt mellan olika instanser av den **kör R-skript** modulen med hjälp av funktionen intern serialisering. Det här exemplet förutsätts att du vill flytta R-objekt med namnet `A` mellan två **kör R-skript** moduler.

1. Lägg till först **kör R-skript** modul till ditt experiment, och ange följande kod i den **R-skriptet** textrutan för att skapa ett serialiserat objekt `A` som en kolumn i modulen utdatafiler datatabell:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    För explicit konvertering till heltalstyp görs eftersom funktionen serialisering matar ut data i R `Raw` format, vilket inte stöds av det visuella gränssnittet.

1. Lägg till en andra instans av den **kör R-skript** modulen, och ansluter den till utdataporten för den föregående modulen.

1. Skriv följande kod i den **R-skriptet** textrutan för att extrahera objekt `A` från tabellen inkommande Data. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Förinstallerade R-paket

Den aktuella listan över tidigare installerade R-paket som kan användas:

|              |            | 
|--------------|------------| 
| Paket      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backportar    | 1.1.4      | 
| Base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| start         | 1.3-22     | 
| kvast        | 0.5.2      | 
| callr        | 3.2.0      | 
| cirkumflex        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| Klass        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| Kluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| avläsningsmeddelanden   | 1.4-1      | 
| compiler     | 3.5.1      | 
| Crayon       | 1.3.4      | 
| CURL         | 3.3        | 
| data.Table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| utvärdera     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| Foreach      | 1.4.4      | 
| sekundärnyckel      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generisk     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| sammanlänkande         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafik     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| rutnät         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| Iteratorer    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| Märkning     | 0.3        | 
| gitter      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matris       | 1.2-17     | 
| Metoder      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| Pelare       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| Pågår     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| RandomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recept      | 0.1.5      | 
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
| skalor       | 1.0.0      | 
| selectr      | 0.4-1      | 
| Spatial      | 7.3-11     | 
| spline-kurvor      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| Statistik        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| Överlevnadsguide     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| verktyg        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 