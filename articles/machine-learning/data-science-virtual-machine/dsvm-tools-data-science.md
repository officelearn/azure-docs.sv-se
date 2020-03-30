---
title: Verktyg för maskininlärning och datavetenskap
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig mer om verktyg och ramverk för maskininlärning som är förinstallerade på den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c414087270558e21340e50114c0563ff7e50064c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282315"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Verktyg för maskininlärning och datavetenskap på virtuella Azure Data Science-datorer
Virtuella Azure Data Science-datorer (DSVM) har en omfattande uppsättning verktyg och bibliotek för maskininlärning som är tillgängliga på populära språk, till exempel Python, R och Julia.

Här är några av verktygen och biblioteken för maskininlärning på DSVM:er.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning-SDK för Python

Se den fullständiga referensen för [Azure Machine Learning SDK för Python](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Vad är detta?   |   Azure Machine Learning är en molntjänst som du kan använda för att utveckla och distribuera maskininlärningsmodeller. Du kan spåra dina modeller när du bygger, tränar, skalar och hanterar dem med hjälp av Python SDK. Distribuera modeller som behållare och kör dem i molnet, lokalt eller på Azure IoT Edge.   |
| Utgåvor som stöds     | Windows (conda-miljö: AzureML), Linux (conda-miljö: py36)    |
| Typiska användningsområden      | Allmän maskininlärningsplattform      |
| Hur är det konfigurerat eller installerat?      |  Installeras med GPU-stöd   |
| Så här använder eller kör du den      | Som Python SDK och i Azure CLI. Aktivera till conda-miljön `AzureML` i Windows Edition *eller* på `py36` Linux-utgåvan.      |
| Länk till exempel      | Exempel på jupyter-anteckningsböcker ingår i katalogen `AzureML` under anteckningsböcker.  |
| Relaterade verktyg      | Visual Studio-kod, Jupyter   |

## <a name="h2o"></a>H2o

|    |           |
| ------------- | ------------- |
| Vad är detta?   | En AI-plattform med öppen källkod som stöder in-memory, distribuerad, snabb och skalbar maskininlärning.  |
| Versioner som stöds      | Linux   |
| Typiska användningsområden      | Distribuerad, skalbar maskininlärning för allmänt syfte   |
| Hur är det konfigurerat eller installerat?      | H2O är `/dsvm/tools/h2o`installerat i .      |
| Så här använder eller kör du den      | Anslut till den virtuella datorn med hjälp av X2Go. Starta en ny terminal `java -jar /dsvm/tools/h2o/current/h2o.jar`och kör . Starta sedan en webbläsare `http://localhost:54321`och anslut till .      |
| Länk till exempel      | Prover finns tillgängliga på den virtuella `h2o` datorn i Jupyter under katalogen.      |
| Relaterade verktyg      | Apache Spark, MXNet, XGBoost, Kolsyrat vatten, djupt vatten    |

Det finns flera andra maskininlärningsbibliotek på DSVM:er, till exempel det populära `scikit-learn` paketet som ingår i Anaconda Python-distributionen för DSVMs. Om du vill kolla in listan över paket som är tillgängliga i Python, R och Julia kör du respektive pakethanterare.

## <a name="lightgbm"></a>LightGBM

|    |           |
| ------------- | ------------- |
| Vad är detta?   | Ett snabbt, distribuerat, högpresterande gradient-öka (GBDT, GBRT, GBM eller MART) ram baserat på beslut träd algoritmer. Den används för rangordning, klassificering och många andra maskininlärningsuppgifter.    |
| Versioner som stöds      | Windows, Linux    |
| Typiska användningsområden      | Ramverket för att öka det allmänna syftet      |
| Hur är det konfigurerat eller installerat?      | På Windows installeras LightGBM som ett Python-paket. På Linux är kommandoraden körbar i `/opt/LightGBM/lightgbm`, R-paketet är installerat och Python-paket installeras.     |
| Länk till exempel      | [LightGBM-guide](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Relaterade verktyg      | MXNet, XgBoost  |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| Vad är detta?   |   Ett grafiskt användargränssnitt för datautvinning med hjälp av R.   |
| Utgåvor som stöds     | Windows, Linux     |
| Typiska användningsområden      | Allmänt verktyg för datautvinning av användargränssnitt för R    |
| Så här använder eller kör du den      | Som ett gränssnittsverktyg. Starta en kommandotolk i Windows, kör R `rattle()`och kör sedan inuti R . På Linux, anslut med X2Go, starta en terminal, kör `rattle()`R och sedan inuti R, kör . |
| Länk till exempel      | [Rattle](https://togaware.com/onepager/) |
| Relaterade verktyg      |LightGBM, Weka, XGBoost   |

## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Vad är detta?   |   Ett snabbt bibliotek med ett system med ett system med öppen källkod    |
| Utgåvor som stöds     | Windows, Linux     |
| Typiska användningsområden      | Allmänt maskininlärningsbibliotek      |
| Hur är det konfigurerat eller installerat?      |  Windows: msi installer<br/>Linux: apt-get |
| Så här använder eller kör du den      | Som ett kommandoradsverktyg på`C:\Program Files\VowpalWabbit\vw.exe` väg (på Windows, `/usr/bin/vw` på Linux)    |
| Länk till exempel      | [VoPal Wabbit prover](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Relaterade verktyg      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är detta?   |  En samling maskininlärningsalgoritmer för datautvinningsuppgifter. Algoritmerna kan antingen tillämpas direkt på en datauppsättning eller anropas från din egen Java-kod. Weka innehåller verktyg för förbearbetning, klassificering, regression, klustring, associationsregler och visualisering. |
| Utgåvor som stöds     | Windows, Linux     |
| Typiska användningsområden      | Allmänt verktyg för maskininlärning     |
| Så här använder eller kör du den      | Sök efter Weka på **Start-menyn** i Windows. På Linux loggar du in med X2Go och går sedan till **Applications** > **Development** > **Weka**. |
| Länk till exempel      | [Weka prover](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg      |LightGBM, Skallra, XGBoost   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Vad är detta?   |   Ett snabbt, bärbart och distribuerat gradient-öka (GBDT, GBRT eller GBM) bibliotek för Python, R, Java, Scala, C ++, med mera. Den körs på en enda maskin, och på Apache Hadoop och Spark.    |
| Utgåvor som stöds     | Windows, Linux     |
| Typiska användningsområden      | Allmänt maskininlärningsbibliotek      |
| Hur är det konfigurerat eller installerat?      |  Installeras med GPU-stöd   |
| Så här använder eller kör du den      | Som ett Python-bibliotek (2.7 och 3.5), R-paket och`C:\dsvm\tools\xgboost\bin\xgboost.exe` kommandoradsverktyg på väg (för Windows och `/dsvm/tools/xgboost/xgboost` För Linux)    |
| Länkar till exempel      | Exempel ingår på den `/dsvm/tools/xgboost/demo` virtuella datorn, `C:\dsvm\tools\xgboost\demo` i linux och på Windows.   |
| Relaterade verktyg      | LightGBM, MXNet   |

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Vad är detta?   | SQL-frågemotor med öppen källkod på stordata    |
| DSVM-versioner som stöds      | Windows 2019, Linux  |
| Hur är det konfigurerat och installerat på DSVM?      |  Endast `/dsvm/tools/drill*` installerat i inbäddat läge   |
| Typiska användningsområden      |  För datautforskning på plats utan att kräva extrakt, transformering, belastning (ETL). Fråga olika datakällor och format, inklusive CSV, JSON, relationstabeller och Hadoop.     |
| Så här använder och kör du den      | Genväg till skrivbordet  <br/> [Kom igång med Drill om 10 minuter](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Relaterade verktyg på DSVM      |   Skallra, Weka, SQL Server Management Studio      |


