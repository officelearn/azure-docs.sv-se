---
title: Köra arbetsbelastningar för Azure Machine Learning med automatiserade machine learning (AutoML) i Apache Spark i Azure HDInsight
description: Lär dig hur du kör Azure Machine Learning arbetsbelastningar med automatiserade machine learning (AutoML) på Apache Spark i Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 896cae9b7fc43765e340ba3b92351e04b5512efd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762559"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Köra arbetsbelastningar för Azure Machine Learning med automatiserade machine learning (AutoML) i Apache Spark i Azure HDInsight

Azure Machine Learning är ett där flera användare kan dra och släpp verktyg som du kan använda för att skapa, testa och distribuera prediktiva Analyslösningar utifrån dina data. Azure Machine Learning publicerar modeller som webbtjänster som enkelt kan användas av anpassade appar eller BI-verktyg som Excel. Automatisk (AutoML) för maskininlärning hjälper dig att skapa högkvalitativ maskininlärningsmodeller med intelligent automatisering och optimering. AutoML avgör rätt algoritmen och hyper parametrar som ska användas för typer av specifika problem.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installera Azure Machine Learning på ett HDInsight-kluster

> [!Note]
> Azure ML-arbetsyta är för närvarande tillgängligt i följande regioner: eastus, usaöstra2 och västra centrala USA. HDInsight-kluster bör också skapas i en av dessa regioner.

Allmän självstudier för Azure Machine Learning och automatiserade machine learning finns [självstudien: Skapa ditt första dataexperiment i Azure Machine Learning Studio](../../machine-learning/studio/create-experiment.md) och [självstudien: Använd automatiserade machine learning för att skapa din regressionsmodell](../../machine-learning/service/tutorial-auto-train-models.md).
Installera AzureML på ditt Azure HDInsight-kluster som kör skriptåtgärd - [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) , på huvudnoderna och arbetsnoder i ett kluster för HDInsight 3.6 Spark 2.3.0 (rekommenderas). Den här skriptåtgärden kan köras antingen som en del av klustret skapas eller i ett befintligt kluster via Azure portal.

Mer information om skriptåtgärder [anpassa Linux-baserade HDInsight-kluster med skriptåtgärder](../hdinsight-hadoop-customize-cluster-linux.md). Förutom att installera Azure Machine Learning-paket och beroenden skriptet kan även laddas ned ett exempel Jupyter Notebook (till sökvägen `HdiNotebooks/PySpark` av standard store). Den här Jupyter-anteckningsbok visar hur du använder en automatiserad machine learning-klassificerare för en enkel klassificeringsproblem.

> [!Note]
> Azure Machine Learning-paket installeras till Python3 conda-miljö. Installerade Jupyter-anteckningsboken ska köras med hjälp av PySpark3 kernel.

## <a name="authentication-for-workspace"></a>Autentisering för arbetsytan

Skapa arbetsyta och experiment bidrag kräver en autentiseringstoken. Denna token kan genereras med hjälp av en [Azure AD-program](../../active-directory/develop/app-objects-and-service-principals.md). En [Azure AD-användare](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) kan också används för att generera autentiseringstoken krävs om multifaktorautentisering inte är aktiverat för kontot.  

Följande kodfragment skapar en autentisering token med hjälp av en **Azure AD-program**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
                )
```
Följande kodfragment skapar en autentisering token med hjälp av en **Azure AD-användare**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Läser in datauppsättningen

Automatiserad machine learning i Spark använder **dataflöden**, som är lazily utvärderade, kan ändras av åtgärder med data.  Ett dataflöde kan läsa in en datauppsättning från en blob med offentlig läsbehörighet, eller från en blob-URL med en SAS-token.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Du kan också registrera datalagringen med arbetsytan med en registrering.

## <a name="experiment-submission"></a>Skicka experiment

I den automatiska learning datorkonfigurationen, egenskapen `spark_context` ska ställas in för att paketet körs på distribuerat läge. Egenskapen `concurrent_iterations`, vilket är det maximala antalet iterationer som körs parallellt, sättas till ett lägre antal än executor kärnor för Spark-app.

## <a name="next-steps"></a>Nästa steg

* Läs mer på motivationen bakom automatiserade maskininlärning [versionen modeller i takt med hjälp av Microsofts automatiserad maskininlärning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/).
* [AutoML projektet från Microsoft Research](https://www.microsoft.com/research/project/automl/)