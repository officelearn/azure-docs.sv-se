---
title: Machine learning och data science verktyg – Azure | Microsoft Docs
description: Machine learning och data science-verktyg
keywords: data science tools, data science virtual machine, tools for data science, linux data science
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
ms.openlocfilehash: 9a9dc868c4f22f95ca5027e3c95513d176c69eac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392412"
---
# <a name="machine-learning-and-data-science-tools"></a>Machine learning och data science-verktyg
Den virtuella datorn på datavetenskap (DSVM) har en omfattande uppsättning verktyg och bibliotek för machine learning som är tillgängliga i populära språk som Python, R, Julia. 

Här är några av de machine learning-verktyg och bibliotek på DSVM. 

## <a name="azure-machine-learning-servicehttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Azure Machine Learning-tjänsten](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Azure Machine Learning-tjänsten är en molnbaserad tjänst som du kan använda för att utveckla och distribuera machine learning-modeller.  Du kan spåra dina modeller som du skapa, träna, skala och hantera dem med hjälp av Python SDK. Distribuera modeller som behållare och kör dem i molnet, onprem eller IoT Edge.   |
| Stöds DSVM-versioner     | Windows (Conda-miljö: AzureML), Linux (Conda-miljö: py36)    |
| Vanliga användningsområden      | Allmänna ML-plattformen      |
| Hur är det konfigurerade / installerad på DSVM?      |  Installeras med GPU-stöd   |
| Hur du använder / köra den?      | SDK för Python och Azure kommandoradsverktyget (AZ CLI). Aktivera conda miljö `AzureML` på Windows-version eller till `py36` på Linux-versionen.      |
| Innehåller länkar till exempel      | Exempel Jupyter-anteckningsböcker som ingår i den `AzureML` katalogen under anteckningsböcker  |
| Relaterade verktyg på DSVM      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Vad är det?   |    Snabb, portabla och distribuerade Gradient Boosting (GBDT, GBRT eller GBM) bibliotek för Python, R, Java, Scala, C++ med mera. Körs på enskild dator, Hadoop, Spark    |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmän ML-biblioteket      |
| Hur är det konfigurerade / installerad på DSVM?      |  Installeras med GPU-stöd   |
| Hur du använder / köra den?      | Som bibliotek för Python 2.7 och 3.5, R-paket och på sökvägen kommandoradsverktyget (`C:\dsvm\tools\xgboost\bin\xgboost.exe` för Windows, `/dsvm/tools/xgboost/xgboost` för Linux)    |
| Innehåller länkar till exempel      | Exempel som ingår på den virtuella datorn i `/dsvm/tools/xgboost/demo` på Linux och `C:\dsvm\tools\xgboost\demo` på Windows   |
| Relaterade verktyg på DSVM      | LightGBM MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Vowpal Wabbit (även kallat ”VW”) är ett snabbt out-för-core learning-systembiblioteket med öppen källkod    |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmän ML-biblioteket      |
| Hur är det konfigurerade / installerad på DSVM?      |  Windows - msi-installationsprogrammet, Linux - apt-get |
| Hur du använder / köra den?      | Som ett kommandoradsverktyg för på sökvägen (`C:\Program Files\VowpalWabbit\vw.exe` på Windows, `/usr/bin/vw` på Linux)    |
| Innehåller länkar till exempel      | [VowPal Wabbit-exempel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Relaterade verktyg på DSVM      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är det?   |  Weka är en samling av machine learning-algoritmer för datautvinning uppgifter som rör. Algoritmerna kan antingen används direkt i en datauppsättning eller anropas från din egen Java-kod. Weka innehåller verktyg för förbearbetning av data, klassificering, regression, klustring, association regler och visualisering. |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna ML-verktyget     |
| Hur du använder / köra den?      | På Windows, söka efter Weka på Start-menyn. Logga in med X2Go på Linux, och gå till program -> utveckling Weka ->. |
| Innehåller länkar till exempel      | [Weka-exempel](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg på DSVM      |LightGBM spännen, XGBooost   |

## <a name="rattle"></a>Spännen
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användargränssnitt för datautvinning med R   |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna UI datautvinning-verktyget för R    |
| Hur du använder / köra den?      | UI-verktyget. På Windows, starta en kommandotolk genom att köra R, sedan i R kör `rattle()`. På Linux, Anslut med X2Go, starta en terminal, kör R, sedan i R kör `rattle()`. |
| Innehåller länkar till exempel      | [Spännen](https://togaware.com/onepager/) |
| Relaterade verktyg på DSVM      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Vad är det?   | En snabb, distribuerad, högpresterande gradient boosting (GBDT, GBRT, GBM eller MART) framework baserat på beslut algoritmer, används för rankning, klassificering och många andra machine learning-aktiviteter.    |
| Stöds DSVM-versioner      | Windows, Linux    |
| Vanliga användningsområden      | Allmänna gradient boosting framework      |
| Hur är det konfigurerade / installerad på DSVM?      | På Windows installeras LightGBM som ett Python-paket. På Linux, är den kommandorad körbara filen i `/opt/LightGBM/lightgbm`R-paketet har installerats och Python-paket installeras.     |
| Innehåller länkar till exempel      | [LightGBM Guide](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Relaterade verktyg på DSVM      | MXNet XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Vad är det?   | En öppen källkod AI-plattform som stöder i minnet, distribuerad, snabb och skalbar machine learning  |
| Stöds DSVM-versioner      | Linux   |
| Vanliga användningsområden      | Generell användning distribuerade, skalbara ML   |
| Hur är det konfigurerade / installerad på DSVM?      | H2O är installerat i `/dsvm/tools/h2o`.      |
| Hur du använder / köra den?      | Anslut till den virtuella datorn med X2Go. Starta en ny terminal och kör `java -jar /dsvm/tools/h2o/current/h2o.jar`. Starta en webbläsare och Anslut till sedan `http://localhost:54321`.      |
| Innehåller länkar till exempel      | Exempel finns på den virtuella datorn i Jupyter under `h2o` directory.      |
| Relaterade verktyg på DSVM      | Apache Spark, MXNet, XGBoost, mousserande vatten, djup Water    |

Det finns flera andra ML-bibliotek på DSVM som populära `scikit-learn` paket som ingår i den Anaconda Python-distribution som är installerad på DSVM. Glöm inte att checka ut listan över paket som är tillgängliga i Python, R och Julia genom att köra respektive pakethanterare. 
