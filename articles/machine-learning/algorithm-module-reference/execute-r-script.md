---
title: 'Kör R-skript: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen kör R-skript i Azure Machine Learning-tjänsten för att köra R-kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3594d9670e8fb94b053479352fb88997caa16db6
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016478"
---
# <a name="execute-r-script"></a>Köra R-skript

I den här artikeln beskrivs hur du använder modulen **Kör r-skript** för att köra r-kod i ditt visuella gränssnitt.

Med R kan du utföra uppgifter som för närvarande inte stöds av befintliga moduler, till exempel: 
- Skapa anpassade data transformationer
- Använd dina egna mått för att utvärdera förutsägelser
- Bygg modeller med algoritmer som inte har implementerats som fristående moduler i Visual Interface

## <a name="r-version-support"></a>Stöd för R-versioner

Det visuella gränssnittet för Azure Machine Learnings tjänsten använder CRAN-distributionen (omfattande R Archive Network) för R. Den version som används är CRAN 3.5.1.

## <a name="supported-r-packages"></a>R-paket som stöds

R-miljön är förinstallerad med över 100-paket. En fullständig lista finns i avsnittet förinstallerade [R-paket](#pre-installed-r-packages).

Du kan också lägga till följande kod i valfri **execute R-skript** -modul och se de installerade paketen.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Installera R-paket
Om du vill installera ytterligare R-paket `install.packages()` använder du-metoden. Se till att ange CRAN-lagringsplatsen. Paket installeras för varje **execute r-skript** -modul och delas inte mellan andra **Kör r-skript** moduler.

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

**Skriptet kör R-skript** innehåller exempel kod som du kan använda som utgångs punkt. Konfigurera modulen **Kör R-skript** genom att ange en uppsättning indata och kod att köra.

![R-modul](media/module/execute-r-script.png)

Data uppsättningar lagrade i visuella gränssnitt konverteras automatiskt till en R data-ram när de läses in med den här modulen.

1.  Lägg till modulen **Kör R-skript** i experimentet.

  

1. Anslut alla indata som behövs för skriptet. Indata är valfria och kan innehålla data och ytterligare R-kod.

    * **Dataset1**: Referera till den första indatamängden som `dataframe1`. Data uppsättningen för indata måste formateras som en CSV-, TSV-ARFF eller så kan du ansluta en Azure Machine Learning data uppsättning.

    * **Dataset2**: Referera till den andra ingången som `dataframe2`. Den här data uppsättningen måste också formateras som en CSV-, TSV-, ARFF-fil eller som en Azure Machine Learning data uppsättning.

    * **Skript paket**: De tredje indatafilerna accepterar ZIP-filer. Den zippade filen kan innehålla flera filer och flera filtyper.

1. I text rutan **R-skript** skriver eller klistrar du in giltigt R-skript.

    För att hjälpa dig att komma igång fylls text rutan **R-skript** i förväg med exempel kod, som du kan redigera eller ersätta.
    
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

 * Skriptet måste innehålla en funktion med namnet `azureml_main`, som är start punkten för den här modulen.

 * Start punkts funktionen kan innehålla upp till två indataargument: `Param<dataframe1>` och`Param<dataframe2>`
 
   > [!NOTE]
    > Data som skickas till modulen **Kör R-skript** refereras till som `dataframe1` och `dataframe2`, som skiljer sig från Azure Machine Learning Studio (Studio Reference as `dataset1`, `dataset2`). Kontrol lera att indata är korrekt referneced i skriptet.  
 
    > [!NOTE]
    >  Befintlig R-kod kan kräva mindre ändringar för att köras i ett visuellt gränssnitts experiment. Indata som du anger i CSV-format ska till exempel uttryckligen konverteras till en data uppsättning innan du kan använda den i din kod. Data-och kolumn typer som används i R-språket skiljer sig också på vissa sätt från data-och kolumn typerna som används i det visuella gränssnittet.

1.  **Slumpmässigt utsäde**: Ange ett värde som ska användas i R-miljön som det slumpmässiga startvärdet. Den här parametern motsvarar anrop `set.seed(value)` i R-kod.  

1. Kör experimentet.  

## <a name="results"></a>Resultat

**Kör R** -skriptets moduler kan returnera flera utdata, men de måste anges som R-databildor. Data ramar konverteras automatiskt till Visual Interface-datauppsättningar för kompatibilitet med andra moduler.

Standard meddelanden och fel från R returneras till modulens logg.

## <a name="sample-scripts"></a>Exempelskript

Det finns många sätt som du kan använda för att utöka experimentet med hjälp av anpassat R-skript.  Det här avsnittet innehåller exempel kod för vanliga uppgifter.


### <a name="add-r-script-as-an-input"></a>Lägg till R-skript som inmatade

Modulen **Kör R-skript** stöder godtyckliga r-skriptfiler som indata. För att göra det måste de överföras till din arbets yta som en del av ZIP-filen.

1. Om du vill ladda upp en ZIP-fil som innehåller R-kod till din arbets yta, klickar du på **ny**, **data uppsättning**och väljer sedan alternativet **från lokal fil** och **zip-fil** .  

1. Kontrol lera att den zippade filen är tillgänglig i listan med **sparade data uppsättningar** .

1.  Anslut data uppsättningen till **skript paketets** indataport.

1. Alla filer som finns i ZIP-filen är tillgängliga under experimentets körnings tid. 

    Om skript paket filen innehåller en katalog struktur bevaras strukturen. Du måste dock ändra koden för att lägga Directory **./script-paketet** till sökvägen.

### <a name="process-data"></a>Bearbeta data

I följande exempel visas hur du skalar och normaliserar indata:

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

### <a name="read-a-zip-file-as-input"></a>Läs en ZIP-fil som indata

Det här exemplet visar hur du använder en data uppsättning i en ZIP-fil som indata för modulen **Kör R-skript** .

1. Skapa data filen i CSV-format och ge den namnet "min datafile. csv".
1. Skapa en ZIP-fil och Lägg till CSV-filen i arkivet.
1. Ladda upp den zippade filen till din Azure Machine Learning-arbetsyta. 
1. Anslut den resulterande data uppsättningen till **ScriptBundle** -indata för modulen **Kör R-skript** .
1. Använd följande kod för att läsa CSV-data från den zippade filen.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikera rader

Det här exemplet visar hur du replikerar positiva poster i en data uppsättning för att balansera exemplet:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Pass R-objekt mellan exekvera R-skript moduler

Du kan skicka R-objekt mellan instanser av modulen **Kör R-skript** med hjälp av funktionen för intern serialisering. Det här exemplet förutsätter att du vill flytta R-objektet `A` med namnet mellan två **köra r-skript** moduler.

1. Lägg till den första **execute R-skript** -modulen i experimentet och skriv följande kod i text rutan R- **skript** för att skapa ett `A` serialiserat objekt som en kolumn i modulen utdata data tabell:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Den explicita konverteringen till Integer-typ görs eftersom serialiserings funktionen matar ut data i R `Raw` -formatet, vilket inte stöds av det visuella gränssnittet.

1. Lägg till en andra instans av modulen **Kör R-skript** och Anslut den till utdataporten för den föregående modulen.

1. Skriv följande kod i text rutan **R-skript** för att extrahera objekt `A` från indata-tabellen. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Förinstallerade R-paket

Den aktuella listan över förinstallerade R-paket som är tillgängliga för användning:

|              |            | 
|--------------|------------| 
| Paket      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| grund         | 3.5.1      | 
| base64enc    | 0,1-3      | 
| BH           | 1.69.0-1   | 
| binder        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| i överkant       | 1.0 – 6      | 
| starta         | 1.3 – 22     | 
| broom        | 0.5.2      | 
| anropare        | 3.2.0      | 
| tecken        | 6.0 – 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| Klass        | 7.3-15     | 
| CLI          | 1.1.0      | 
| klippare        | 0.6.0      | 
| kluster      | 2.0.7-1    | 
| codetools    | 0,2 – 16     | 
| colorspace   | 1.4-1      | 
| kompilatorn     | 3.5.1      | 
| färg       | 1.3.4      | 
| klammerparentes         | 3.3        | 
| data. table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| utvärdera     | 0,14       | 
| fläktar        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| förvalta      | 0,8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generisk typ     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| emot         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| bild     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| rutnät         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| Jag        | 2.1.0      | 
| hög        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9 – 9      | 
| iteratorer    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| märkning     | 0,3        | 
| rutindex      | 0,20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2 – 17     | 
| indatametod      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| Modellerare       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| vasslepulver         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pelar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| pågår     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1,5 – 7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| Reader        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recept      | 0.1.5      | 
| matcha om      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1,12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1,13       | 
| ROCR         | 1,0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| skalas       | 1.0.0      | 
| väljare      | 0.4-1      | 
| Geospatial      | 7.3-11     | 
| spline      | 3.5.1      | 
| KVADRATISKT      | 2017.10-1  | 
| spelarna        | 3.5.1      | 
| stats4       | 3.5.1      | 
| strängi      | 1.4.3      | 
| strängare      | 1.3.1      | 
| lever     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| städat        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043,102   | 
| tinytex      | 0,13       | 
| verktyg        | 3.5.1      | 
| tseries      | 0,10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| uppgradering        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| hårs värde      | 0,3-2      | 
| med        | punkt      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| XTS          | 0,11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| Zoo          | 1.8 – 6      | 

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
