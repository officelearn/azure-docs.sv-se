---
title: Utöka ditt experiment med R | Microsoft Docs
description: Så här utökar funktionerna i Azure Machine Learning Studio via R-språket med hjälp av modulen köra R-skript.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 3767d7e48d1760b7b7453b1708118caa305652d9
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823885"
---
# <a name="extend-your-experiment-with-r"></a>Utöka experimentet med R
Du kan utöka funktionerna i Azure Machine Learning Studio via R-språket med hjälp av den [kör R-skript] [ execute-r-script] modulen.

Den här modulen accepterar flera indatauppsättningar och ger en enda datauppsättning som utdata. Du kan skriva ett R-skript i den **R-skriptet** -parametern för den [kör R-skript] [ execute-r-script] modulen.

Du har åtkomst till varje indataporten för modulen med hjälp av kod som liknar följande:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Visa en lista över alla installerade paket
Ändra listan över installerade paket. En lista över installerade paket finns i [R-paket som stöds av Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt741980.aspx).

Du kan också hämta fullständig, aktuell lista över installerade paket genom att ange följande kod till den [kör R-skript] [ execute-r-script] modulen:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Listan över paket skickas till utdataporten för den [kör R-skript] [ execute-r-script] modulen.
Om du vill visa paketlistan, Anslut en konvertering till modulen som [konvertera till CSV] [ convert-to-csv] till vänstra utdataporten för den [kör R-skript] [ execute-r-script] -modulen Kör experimentet, och sedan klicka på utdata från modulen konvertering och välj **hämta**. 

![Hämta utdata från modulen ”konvertera till CSV”](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importera paket
Du kan importera paket som inte redan är installerade med hjälp av följande kommandon i den [kör R-skript] [ execute-r-script] modulen:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

där den `my_favorite_package.zip` filen innehåller ditt paket.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
