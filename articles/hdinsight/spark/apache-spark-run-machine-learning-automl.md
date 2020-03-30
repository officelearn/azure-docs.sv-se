---
title: Kör Azure Machine Learning-arbetsbelastningar på Apache Spark i HDInsight
description: Lär dig hur du kör Azure Machine Learning-arbetsbelastningar med automatisk maskininlärning (AutoML) på Apache Spark i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638892"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Kör Azure Machine Learning-arbetsbelastningar med automatiserad maskininlärning på Apache Spark i HDInsight

Azure Machine Learning förenklar och påskyndar uppbyggnad, utbildning och distribution av maskininlärningsmodeller. I automatisk maskininlärning (AutoML) börjar du med träningsdata som har en definierad målfunktion och sedan iterera genom kombinationer av algoritmer och funktionsval för att automatiskt välja den bästa modellen för dina data baserat på träningsresultaten. HDInsight gör det möjligt för kunder att etablera kluster med hundratals noder. AutoML körs på Spark i ett HDInsight-kluster tillåter användare att använda beräkningskapacitet över dessa noder för att köra utbildning jobb i en skala ut sätt, och att köra flera utbildning jobb parallellt. Detta gör det möjligt för användare att köra AutoML-experiment samtidigt som de delar beräkningen med sina andra stordataarbetsbelastningar.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Installera Azure Machine Learning i ett HDInsight-kluster

Allmänna självstudier av automatiserad maskininlärning finns i [Självstudiekurs: Använd automatiserad maskininlärning för att skapa din regressionsmodell](../../machine-learning/tutorial-auto-train-models.md).
Alla nya HDInsight-Spark-kluster är förinstallerade med AzureML-AutoML SDK.

> [!Note]
> Azure Machine Learning-paket installeras i Python3 conda-miljö. Den installerade jupyter-anteckningsboken ska köras med PySpark3-kärnan.

Du kan använda Zeppelin-anteckningsböcker för att använda AutoML också.

> [!Note]
> Zeppelin har ett [känt problem](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) där PySpark3 inte väljer rätt version av Python. Använd den dokumenterade work-around.

## <a name="authentication-for-workspace"></a>Autentisering för arbetsyta

Skapande och experimentöverföring i arbetsyta kräver en autentiseringstoken. Den här token kan genereras med hjälp av ett [Azure AD-program](../../active-directory/develop/app-objects-and-service-principals.md). En [Azure AD-användare](/azure/python/python-sdk-azure-authenticate) kan också användas för att generera den nödvändiga autentiseringstoken, om multifaktorautentisering inte är aktiverad på kontot.  

Följande kodavsnitt skapar en autentiseringstoken med hjälp av ett **Azure AD-program**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

Följande kodavsnitt skapar en autentiseringstoken med en **Azure AD-användare**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Läsa in datauppsättning

Automatiserad maskininlärning på Spark använder **dataflöden**, som är lättjefullt utvärderade, oföränderliga åtgärder på data.  Ett dataflöde kan läsa in en datauppsättning från en blob med offentlig läsåtkomst eller från en blob-URL med en SAS-token.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Du kan också registrera datalagret med arbetsytan med hjälp av en engångsregistrering.

## <a name="experiment-submission"></a>Inlämning av experiment

I den [automatiska maskininlärningskonfigurationen](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)ska egenskapen `spark_context` ställas in för att paketet ska köras i distribuerat läge. Egenskapen `concurrent_iterations`, som är det maximala antalet iterationer som körs parallellt, ska anges till ett tal som är mindre än executor-kärnorna för Spark-appen.

## <a name="next-steps"></a>Nästa steg

* Mer information om motivationen bakom automatiserad maskininlärning finns i [Släpp modeller i takt med hjälp av Microsofts automatiserade maskininlärning!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Mer information om hur du använder Azure ML Automated ML-funktioner finns [i Nya funktioner för automatiserad maskininlärning i Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [AutoML-projekt från Microsoft Research](https://www.microsoft.com/research/project/automl/)
