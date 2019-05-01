---
title: Köra arbetsbelastningar för Azure Machine Learning med automatiserade machine learning (AutoML) i Apache Spark i Azure HDInsight
description: Lär dig hur du kör Azure Machine Learning arbetsbelastningar med automatiserade machine learning (AutoML) på Apache Spark i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917046"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Köra arbetsbelastningar för Azure Machine Learning med automatiserade machine learning (AutoML) i Apache Spark i Azure HDInsight

Azure Machine Learning förenklar och går det snabbare att skapa, utbildning och distribuera machine learning-modeller. Automatiserad Machine learning (AutoML), börja med träningsdata som har ett definierat mål-funktionen och sedan gå igenom kombinationer av algoritmer och val av funktioner att automatiskt välja modellen med bäst för dina data baserat på poäng för utbildning. HDInsight kan kunderna etablera kluster med hundratals noder. AutoML som körs på Spark i ett HDInsight-kluster kan du använda beräkningskapacitet på dessa noder köra upplärningsjobb på en skalbar sätt och för att köra flera upplärningsjobb parallellt. På så sätt kan användare köra AutoML experiment och dela beräkningen som med andra stordataarbetsbelastningar.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installera Azure Machine Learning på ett HDInsight-kluster

Allmän självstudier av automatiserade machine learning finns [självstudien: Använd automatiserade machine learning för att skapa din regressionsmodell](../../machine-learning/service/tutorial-auto-train-models.md).
Alla nya HDInsight Spark-kluster levereras förinstallerade med AzureML-AutoML SDK. Du kan komma igång med AutoML på HDInsight med den här [exempel Jupyter-anteckningsbok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). Den här Jupyter-anteckningsbok visar hur du använder en automatiserad machine learning-klassificerare för en enkel klassificeringsproblem.

> [!Note]
> Azure Machine Learning-paket installeras till Python3 conda-miljö. Installerade Jupyter-anteckningsboken ska köras med hjälp av PySpark3 kernel.

Du kan också använda Zeppelin-anteckningsböcker för att använda AutoML samt.

> [!Note]
> Zeppelin har en [känt problem](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) där PySpark3 inte välja rätt version av Python. Använd dokumenterade arbete-runt.

## <a name="authentication-for-workspace"></a>Autentisering för arbetsytan

Skapa arbetsyta och experiment bidrag kräver en autentiseringstoken. Denna token kan genereras med hjälp av en [Azure AD-program](../../active-directory/develop/app-objects-and-service-principals.md). En [Azure AD-användare](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) kan också används för att generera autentiseringstoken krävs om multifaktorautentisering inte är aktiverat för kontot.  

Följande kodfragment skapar en autentisering token med hjälp av en **Azure AD-program**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
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

I den [automatiserade machine learning-konfiguration](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), egenskapen `spark_context` ska ställas in för att paketet körs på distribuerat läge. Egenskapen `concurrent_iterations`, vilket är det maximala antalet iterationer som körs parallellt, sättas till ett lägre antal än executor kärnor för Spark-app.

## <a name="next-steps"></a>Nästa steg

* Läs mer på motivationen bakom automatiserade maskininlärning [versionen modeller i takt med hjälp av Microsofts automatiserad maskininlärning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Mer information om hur du använder Azure ML automatiserade ML-funktioner finns i [New automatiserad maskininlärningsförmågor i Azure Machine Learning-tjänsten](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [AutoML projektet från Microsoft Research](https://www.microsoft.com/research/project/automl/)
