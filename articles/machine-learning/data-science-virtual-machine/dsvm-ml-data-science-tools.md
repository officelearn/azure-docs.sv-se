---
title: Machine learning och data science verktyg – Azure | Microsoft Docs
description: Läs mer om de machine learning-verktyg och ramverk som redan är installerat på den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: d8c607b5f1d338ca3a2bd3844b26ef51d801c720
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086078"
---
# <a name="machine-learning-and-data-science-tools"></a>Machine learning och data science-verktyg
Azure virtuella datorer för datavetenskap har en omfattande uppsättning verktyg och bibliotek för machine learning (ML) tillgänglig för populära språk, till exempel Python, R och Julia. 

Här är några av ML-verktyg och bibliotek på virtuella datorer för datavetenskap. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml) SDK
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Azure Machine Learning är en molnbaserad tjänst som du kan använda för att utveckla och distribuera maskininlärningsmodeller. Du kan spåra dina modeller som du skapa, träna, skala och hantera dem med hjälp av Python-SDK. Distribuera modeller som behållare och köra dem i molnet, lokalt eller på Azure IoT Edge.   |
| Versioner som stöds     | Windows (conda-miljö: AzureML), Linux (conda-miljö: py36)    |
| Vanliga användningsområden      | Allmän ML-plattform      |
| Hur är det konfigurerade eller installerad?      |  Installeras med GPU-stöd   |
| Hur du använder eller köra den      | Som Python SDK och Azure CLI. Aktivera conda-miljö `AzureML` på Windows-version *eller* till `py36` på Linux-versionen.      |
| Länka till exempel      | Exempel Jupyter-anteckningsböcker som ingår i den `AzureML` katalogen under anteckningsböcker.  |
| Relaterade verktyg      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Vad är det?   |    XGBoost är en snabb, bärbar och distribuerade övertoning boosting (GBDT, GBRT eller GBM) bibliotek för Python, R, Java, Scala, C++ och mer. Den körs på en enskild dator, Hadoop och Spark.    |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användningsområden      | Allmän ML-biblioteket      |
| Hur är det konfigurerade eller installerad?      |  Installeras med GPU-stöd   |
| Hur du använder eller köra den      | Som Python-biblioteket (2.7 och 3.5), R-paket och på sökvägen kommandoradsverktyget (`C:\dsvm\tools\xgboost\bin\xgboost.exe` för Windows, `/dsvm/tools/xgboost/xgboost` för Linux)    |
| Innehåller länkar till exempel      | Exempel som ingår på den virtuella datorn i `/dsvm/tools/xgboost/demo` på Linux, och `C:\dsvm\tools\xgboost\demo` på Windows.   |
| Relaterade verktyg      | LightGBM MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Vowpal Wabbit (även kallat ”VW”) är en öppen källkod, snabba, learning systembibliotek out-för-kärna.    |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användningsområden      | Allmän ML-biblioteket      |
| Hur är det konfigurerade eller installerad?      |  Windows – msi-installationsprogrammet, Linux – apt-get |
| Hur du använder eller köra den      | Som ett-path-kommandoradsverktyget (`C:\Program Files\VowpalWabbit\vw.exe` på Windows, `/usr/bin/vw` på Linux)    |
| Länka till exempel      | [VowPal Wabbit-exempel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Relaterade verktyg      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är det?   |  Weka är en samling av ML-algoritmer för datautvinning uppgifter som rör. Algoritmerna kan antingen används direkt i en uppsättning data eller anropas från din egen Java-kod. Weka innehåller verktyg för förbearbetning av data, klassificering, regression, klustring, association regler och visualisering. |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användningsområden      | Allmänt ML-verktyg     |
| Hur du använder eller köra den      | På Windows, Sök efter Weka på Start-menyn. Logga in med X2Go på Linux, och gå sedan till **program** > **utveckling** > **Weka**. |
| Länka till exempel      | [Weka-exempel](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg      |LightGBM spännen, XGBoost   |

## <a name="rattle"></a>Spännen
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Spännen är ett grafiskt användargränssnitt för datautvinning med R.   |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna UI verktyg för datautvinning för R    |
| Hur du använder eller köra den      | UI-verktyget. Starta en kommandotolk på Windows, kör R och kör sedan följande i R `rattle()`. Träffa på Linux, X2Go, starta en terminal, köra R och kör sedan följande i R `rattle()`. |
| Länka till exempel      | [Spännen](https://togaware.com/onepager/) |
| Relaterade verktyg      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Vad är det?   | LightGBM är en snabb, distribuerad, högpresterande gradient boosting (GBDT, GBRT, GBM eller MART) framework baserat på beslut algoritmer. Den används för rankning, klassificering och många andra ML-aktiviteter.    |
| Versioner som stöds      | Windows, Linux    |
| Vanliga användningsområden      | Allmänna gradient boosting framework      |
| Hur är det konfigurerade eller installerad?      | På Windows installeras LightGBM som ett Python-paket. På Linux, är den kommandorad körbara filen i `/opt/LightGBM/lightgbm`R-paketet har installerats och Python-paket installeras.     |
| Länka till exempel      | [LightGBM Guide](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Relaterade verktyg      | MXNet XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Vad är det?   | H2O är en öppen källkod AI-plattform som stöder i minnet, distribuerad, snabb och skalbar ML.  |
| Versioner som stöds      | Linux   |
| Vanliga användningsområden      | Allmänna distribuerade och skalbara ML   |
| Hur är det konfigurerade eller installerad?      | H2O är installerat i `/dsvm/tools/h2o`.      |
| Hur du använder eller köra den      | Ansluta till den virtuella datorn med hjälp av X2Go. Starta en ny terminal och kör `java -jar /dsvm/tools/h2o/current/h2o.jar`. Starta en webbläsare och Anslut till sedan `http://localhost:54321`.      |
| Länka till exempel      | Exempel finns på den virtuella datorn i Jupyter under den `h2o` directory.      |
| Relaterade verktyg      | Apache Spark, MXNet, XGBoost, mousserande vatten, djup Water    |

Det finns flera andra ML-bibliotek på virtuella datorer för datavetenskap, för, till exempel populära `scikit-learn` paket som ingår i den Anaconda Python-distribution som installeras på virtuella datorer för datavetenskap. Kontrollera listan över paket som är tillgängliga i Python, R och Julia att köra respektive pakethanterare.
