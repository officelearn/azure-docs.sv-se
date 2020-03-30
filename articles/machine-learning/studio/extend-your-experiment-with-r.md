---
title: Utöka experimentet med R
titleSuffix: ML Studio (classic) - Azure
description: Så här utökar du funktionerna i Azure Machine Learning Studio (klassisk) via R-språket med hjälp av modulen Kör R-skript.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218039"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure Machine Learning Studio (klassisk): Utöka experimentet med R 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Du kan utöka funktionerna i Azure Machine Learning Studio (klassisk) via R-språket med hjälp av modulen [Kör R-skript.][execute-r-script]

Den här modulen accepterar flera indatauppsättningar och ger en enda datauppsättning som utdata. Du kan skriva ett **R Script** R-skript i R Script-parametern för modulen [Kör R-skript.][execute-r-script]

Du kommer åt varje indataport i modulen med hjälp av kod som liknar följande:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Lista alla paket som för närvarande är installerade
Listan över installerade paket kan ändras. En lista över installerade paket finns i [R-paket som stöds av Azure Machine Learning Studio (klassisk)](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Du kan också få den fullständiga, aktuella listan över installerade paket genom att ange följande kod i [modulen Kör R-skript:][execute-r-script]

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Detta skickar listan över paket till utdataporten för modulen [Kör R-skript.][execute-r-script]
Om du vill visa paketlistan ansluter du en konverteringsmodul som [Konvertera till CSV][convert-to-csv] till den vänstra utdata för modulen [Kör R-skript,][execute-r-script] kör experimentet och klickar sedan på utdata från konverteringsmodulen och väljer **Hämta**. 

![Hämta utdata från modulen "Konvertera till CSV"](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importera paket
Du kan importera paket som inte redan är installerade med hjälp av följande kommandon i modulen [Kör R-skript:][execute-r-script]

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

där `my_favorite_package.zip` filen innehåller ditt paket.




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
