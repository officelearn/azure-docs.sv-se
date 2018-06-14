---
title: Datorn learning och datavetenskap verktyg - Azure | Microsoft Docs
description: Verktyg för Machine learning och vetenskap
keywords: datavetenskap verktyg, datavetenskap virtuell dator, verktyg för datavetenskap, datavetenskap för linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: cf73f792bcce906a673da0c6fb2eec099239c1c2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409558"
---
# <a name="machine-learning-and-data-science-tools"></a>Verktyg för Machine learning och vetenskap
Den virtuella datorn på vetenskap (DSVM) har en omfattande uppsättning verktyg och bibliotek för machine learning i populära språk som Python, R, Julia. 

Här är några av maskininlärning verktyg och bibliotek på DSVM. 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Vad är det?   |    Fast portabla och distribuerade toning förstärkning (GBDT, GBRT eller GBM) bibliotek för Python, R, Java, Scala, C++ med mera. Körs på samma dator, Hadoop, Spark    |
| Stöds DSVM versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmän ML-bibliotek      |
| Hur är det konfigurerade / installerad på DSVM?      |  Installerad med stöd för GPU   |
| Hur du använder / kör den?      | Som Python-bibliotek (2.7 och 3.5) R-paket och på sökväg-kommandoradsverktyget (`C:\dsvm\tools\xgboost\bin\xgboost.exe` för Windows, `/dsvm/tools/xgboost/xgboost` för Linux)    |
| Länkar till exempel      | Exempel finns på den virtuella datorn i `/dsvm/tools/xgboost/demo` på Linux och `C:\dsvm\tools\xgboost\demo` i Windows   |
| Relaterade verktyg på DSVM      | LightGBM MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Vowpal Wabbit (även kallat ”VW”) är en öppen källkod snabb out-för-core learning systembibliotek    |
| Stöds DSVM versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmän ML-bibliotek      |
| Hur är det konfigurerade / installerad på DSVM?      |  Windows - msi installer, Linux - lgh get |
| Hur du använder / kör den?      | Ett kommandoradsverktyg för på sökväg (`C:\Program Files\VowpalWabbit\vw.exe` i Windows, `/usr/bin/vw` på Linux)    |
| Länkar till exempel      | [VowPal Wabbit-exempel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Relaterade verktyg på DSVM      |LightGBM MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är det?   |  Weka är en samling av maskininlärningsalgoritmer för uppgifter som rör utvinningsmodellen. Algoritmer som du kan antingen tillämpas direkt på en datamängd eller anropas från din egen Java-kod. Weka innehåller verktyg för före databearbetning, klassificering, regression, kluster, association regler och visualisering. |
| Stöds DSVM versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna ML-verktyget     |
| Hur du använder / kör den?      | På Windows, söka efter Weka i Start-menyn. Logga in med X2Go på Linux, och sedan gå till program -> Development Weka ->. |
| Länkar till exempel      | [Weka-exempel](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg på DSVM      |LightGBM spännen, XGBooost   |

## <a name="rattle"></a>Spännen
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användargränssnitt för datautvinning med R   |
| Stöds DSVM versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna UI Data Mining-verktyget för R    |
| Hur du använder / kör den?      | UI-verktyget. Starta en kommandotolk i Windows, kan köra R, sedan i R kör `rattle()`. På Linux, ansluta med X2Go, starta en terminal, kör R, sedan i R kör `rattle()`. |
| Länkar till exempel      | [Spännen](https://togaware.com/onepager/) |
| Relaterade verktyg på DSVM      |LightGBM Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Vad är det?   | Snabb, distribuerade och högpresterande toningar förstärkning (GBDT, GBRT, GBM eller MART) framework baserat på beslut trädet-algoritmer som används för rangordning, klassificering och många andra maskininlärning uppgifter.    |
| Stöds DSVM versioner      | Windows, Linux    |
| Vanliga användningsområden      | Allmänna toning framework      |
| Hur är det konfigurerade / installerad på DSVM?      | På Windows installeras LightGBM som en Python-paket. På Linux, kommandorad körbara filen är `/opt/LightGBM/lightgbm`R-paket installeras och Python-paket som är installerade.     |
| Länkar till exempel      | [LightGBM Guide](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Relaterade verktyg på DSVM      | MXNet XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Vad är det?   | En öppen källkod AI-plattform som stöder i minnet, distribuerade, snabb och skalbar maskininlärning  |
| Stöds DSVM versioner      | Linux   |
| Vanliga användningsområden      | Generella som distribuerats, skalbara ML   |
| Hur är det konfigurerade / installerad på DSVM?      | H2O installeras i `/dsvm/tools/h2o`.      |
| Hur du använder / kör den?      | Ansluta till den virtuella datorn med hjälp av X2Go. Starta en ny terminal och kör `java -jar /dsvm/tools/h2o/current/h2o.jar`. Starta en webbläsare och Anslut till sedan `http://localhost:54321`.      |
| Länkar till exempel      | Exempel är tillgängliga på den virtuella datorn i Jupyter under `h2o` directory.      |
| Relaterade verktyg på DSVM      | Apache Spark, MXNet, XGBoost, mousserande vattenstämplar, djup vattenstämplar    |

Det finns flera andra ML-bibliotek på DSVM som den populära `scikit-learn` paket som ingår som en del av den Anaconda Python-distribution som är installerad på DSVM. Glöm inte att checka ut listan över paket som är tillgängliga i Python, R och Julia genom att köra de respektive paket cheferna. 
