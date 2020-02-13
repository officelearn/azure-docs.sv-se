---
title: Utöka experimentet med R
titleSuffix: ML Studio (classic) - Azure
description: Hur du utökar funktionerna i Azure Machine Learning Studio (klassisk) via R-språket med hjälp av modulen kör R-skript.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: d6174459237399ea370ee9c36ba537391104a917
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168562"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klassisk): utöka ditt experiment med R 
Du kan utöka funktionerna i Azure Machine Learning Studio (klassisk) via R-språket med hjälp av modulen [Kör R-skript][execute-r-script] .

Den här modulen godkänner flera indata-datauppsättningar och ger en enda data uppsättning som utdata. Du kan ange ett R-skript i **R-skript** parametern för modulen [Kör R-skript][execute-r-script] .

Du kommer åt varje indataport i modulen med hjälp av kod som liknar följande:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Visar alla paket som för närvarande är installerade
Listan över installerade paket kan ändras. En lista över aktuella installerade paket finns i [R-paket som stöds av Azure Machine Learning Studio (klassisk)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Du kan också få en fullständig, aktuell lista över installerade paket genom att ange följande kod i modulen [Kör R-skript][execute-r-script] :

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Detta skickar listan med paket till utdataporten för modulen [Kör R-skript][execute-r-script] .
Om du vill visa paket listan ansluter du en konverterings modul, till exempel [konvertera till CSV][convert-to-csv] till vänster i [skriptet kör R-skript][execute-r-script] , kör experimentet och klickar sedan på utdata från modulen Conversion och väljer **Hämta**. 

![Hämta utdata från "konvertera till CSV"-modulen](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importera paket
Du kan importera paket som inte redan har installerats med hjälp av följande kommandon i modulen [Kör R-skript][execute-r-script] :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

där `my_favorite_package.zip`-filen innehåller ditt paket.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
