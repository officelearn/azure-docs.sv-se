---
title: Machine learning och data science-verktyg
description: Lär dig mer om de maskin inlärnings verktyg och ramverk som är förinstallerade på Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: dec1589898cf6cc239dbe737519e078f1c4bc7b1
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744930"
---
# <a name="machine-learning-and-data-science-tools-on-azure-data-science-virtual-machines"></a>Maskin inlärnings-och data vetenskaps verktyg i Azure Data Science Virtual Machines
Azure Data Science Virtual Machines (Dsvm) har en omfattande uppsättning verktyg och bibliotek för maskin inlärning som är tillgängligt på populära språk, till exempel python, R och Julia.

Här följer några av verktygen och biblioteken för maskin inlärning på Dsvm.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK för python
Se den fullständiga referensen för [Azure Machine Learning SDK för python](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml).

|    |           |
| ------------- | ------------- |
| Vad är det?   |   Azure Machine Learning är en moln tjänst som du kan använda för att utveckla och distribuera maskin inlärnings modeller. Du kan spåra dina modeller som du skapa, träna, skala och hantera dem med hjälp av Python-SDK. Distribuera modeller som behållare och köra dem i molnet, lokalt eller på Azure IoT Edge.   |
| Versioner som stöds     | Windows (Conda-miljö: AzureML), Linux (Conda-miljö: py36)    |
| Vanliga användningsområden      | Allmän plattform för maskin inlärning      |
| Hur är det konfigurerade eller installerad?      |  Installeras med GPU-stöd   |
| Hur du använder eller köra den      | Som ett python SDK och i Azure CLI. Aktivera conda-miljö `AzureML` på Windows-version *eller* till `py36` på Linux-versionen.      |
| Länka till exempel      | Exempel Jupyter-anteckningsböcker som ingår i den `AzureML` katalogen under anteckningsböcker.  |
| Relaterade verktyg      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett snabb, portabelt och distribuerat GBDT-bibliotek (, GBRT eller GBM) för python, R, Java, Scala, C++med mera. Den körs på en enskild dator och på Apache Hadoop och Spark.    |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användningsområden      | Allmänt maskin inlärnings bibliotek      |
| Hur är det konfigurerade eller installerad?      |  Installeras med GPU-stöd   |
| Hur du använder eller köra den      | Som ett Python-bibliotek (2,7 och 3,5), R-paket och kommando rads verktyget på en sökväg (`C:\dsvm\tools\xgboost\bin\xgboost.exe` för Windows och `/dsvm/tools/xgboost/xgboost` Linux)    |
| Innehåller länkar till exempel      | Exempel som ingår på den virtuella datorn i `/dsvm/tools/xgboost/demo` på Linux, och `C:\dsvm\tools\xgboost\demo` på Windows.   |
| Relaterade verktyg      | LightGBM MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett snabbt och enkelt utbildnings system bibliotek med öppen källkod    |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användningsområden      | Allmänt maskin inlärnings bibliotek      |
| Hur är det konfigurerade eller installerad?      |  Windows: MSI installations program<br/>Linux: apt-get |
| Hur du använder eller köra den      | Som ett-path-kommandoradsverktyget (`C:\Program Files\VowpalWabbit\vw.exe` på Windows, `/usr/bin/vw` på Linux)    |
| Länka till exempel      | [VowPal Wabbit-exempel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Relaterade verktyg      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är det?   |  En samling Machine Learning-algoritmer för data utvinnings aktiviteter. Algoritmerna kan antingen används direkt i en uppsättning data eller anropas från din egen Java-kod. Weka innehåller verktyg för förbearbetning av data, klassificering, regression, klustring, association regler och visualisering. |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användningsområden      | Allmänt verktyg för maskin inlärning     |
| Hur du använder eller köra den      | I Windows söker du efter Wekas på **Start** menyn. Logga in med X2Go på Linux, och gå sedan till **program** > **utveckling** > **Weka**. |
| Länka till exempel      | [Weka-exempel](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg      |LightGBM spännen, XGBoost   |

## <a name="rattle"></a>Spännen
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användar gränssnitt för Data utvinning med hjälp av R.   |
| Versioner som stöds     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna GRÄNSSNITTs data utvinnings verktyg för R    |
| Hur du använder eller köra den      | Som ett UI-verktyg. Starta en kommando tolk i Windows, kör R och sedan i R, kör `rattle()`. I Linux ansluter du till X2Go, startar en Terminal, kör R och sedan i R, kör `rattle()`. |
| Länka till exempel      | [Spännen](https://togaware.com/onepager/) |
| Relaterade verktyg      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| Vad är det?   | En snabb, distribuerad, högpresterande gradient-förstärkning (GBDT, GBRT, GBM eller MART) som baseras på algoritmer för besluts träd. Den används för rankning, klassificering och många andra uppgifter för maskin inlärning.    |
| Versioner som stöds      | Windows, Linux    |
| Vanliga användningsområden      | Generell användnings ramverk för tonings förstärkning      |
| Hur är det konfigurerade eller installerad?      | På Windows installeras LightGBM som ett Python-paket. På Linux, är den kommandorad körbara filen i `/opt/LightGBM/lightgbm`R-paketet har installerats och Python-paket installeras.     |
| Länka till exempel      | [LightGBM-guide](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Relaterade verktyg      | MXNet XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| Vad är det?   | En AI-plattform med öppen källkod som har stöd för minnes intern, distribuerad, snabb och skalbar maskin inlärning.  |
| Versioner som stöds      | Linux   |
| Vanliga användningsområden      | Allmänt distribuerad, skalbar maskin inlärning   |
| Hur är det konfigurerade eller installerad?      | H2O är installerat i `/dsvm/tools/h2o`.      |
| Hur du använder eller köra den      | Ansluta till den virtuella datorn med hjälp av X2Go. Starta en ny terminal och kör `java -jar /dsvm/tools/h2o/current/h2o.jar`. Starta en webbläsare och Anslut till sedan `http://localhost:54321`.      |
| Länka till exempel      | Exempel finns på den virtuella datorn i Jupyter under den `h2o` directory.      |
| Relaterade verktyg      | Apache Spark, MXNet, XGBoost, mousserande vatten, djup Water    |

Det finns flera andra maskin inlärnings bibliotek på dsvm, till exempel det populära `scikit-learn` paketet som är en del av AnaConDa python-distributionen för dsvm. Kontrollera listan över paket som är tillgängliga i Python, R och Julia att köra respektive pakethanterare.
