---
title: "Utöka ditt experiment med R | Microsoft Docs"
description: "Så här utökar funktionerna i Azure Machine Learning Studio via R-språk med hjälp av modulen köra R-skriptet."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.openlocfilehash: e9f11ec987a5dd71998f6b23399228554f1dcc11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-experiment-with-r"></a>Utöka experimentet med R
Du kan utöka funktionerna i Azure Machine Learning Studio via R-språk med hjälp av den [köra R-skriptet] [ execute-r-script] modul.

Den här modulen accepterar flera indatauppsättningar och ger en enda dataset som utdata. Du kan skriva ett R-skript i den **R-skriptet** parameter för den [köra R-skriptet] [ execute-r-script] modul.

Du har åtkomst till varje inkommande port på modulen som med hjälp av kod som liknar följande:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Visar en lista över alla installerade paket
Ändra listan över installerade paket. En lista över installerade paket finns i [R-paket som stöds av Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Du kan också få fullständig, aktuell listan över installerade paket genom att skriva in följande kod i den [köra R-skriptet] [ execute-r-script] modulen:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Listan över paket skickas till utdataporten för den [köra R-skriptet] [ execute-r-script] modul.
Om du vill visa paketlistan ansluta en konvertering modul som [konvertera till CSV] [ convert-to-csv] till vänstra utdataporten för den [köra R-skriptet] [ execute-r-script] modulen Kör experimentet, och sedan klicka på utdata från modulen konvertering och välj **hämta**. 

![Hämta utdata från modulen ”konvertera till CSV”](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importera paket
Du kan importera paket som inte redan är installerade med hjälp av följande kommandon i den [köra R-skriptet] [ execute-r-script] modulen:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

där den `my_favorite_package.zip` filen innehåller paketet.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
