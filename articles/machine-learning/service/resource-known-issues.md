---
title: Kända problem & fel sökning
titleSuffix: Azure Machine Learning service
description: Hämta en lista över kända problem, lösningar, och felsökning för Azure Machine Learning-tjänsten.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 4e7b3905295e619c5a9500f80b5c43126b919e2f
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946462"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Kända problem och felsökning Azure Machine Learning-tjänsten

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstod när du använder Azure Machine Learning-tjänsten.

## <a name="visual-interface-issues"></a>Problem med visuella gränssnitt

Visuellt gränssnitt för Machine Learning service-problem.

### <a name="long-compute-preparation-time"></a>Förberedelse tid för lång beräkning

Det kan ta några minuter eller till och med längre tid att skapa en ny beräknings-eller använda. Teamet arbetar för optimering.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Det går inte att köra ett experiment som bara innehåller data uppsättning 

Du kanske vill köra ett experiment endast innehåller data uppsättning för att visualisera data uppsättningen. Men det är inte tillåtet att köra ett experiment som bara innehåller data uppsättningar idag. Vi åtgärdar det här problemet aktivt.
 
Före korrigeringen kan du ansluta data uppsättningen till en datatransformerings-modul (Välj kolumner i data uppsättning, redigera metadata, dela data osv.) och köra experimentet. Sedan kan du visualisera data uppsättningen. 

Bilden nedan visar hur: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK-installationsproblem

**Felmeddelande: Det går inte att avinstallera ' PyYAML '**

Azure Machine Learning SDK för python: PyYAML är ett distutils-installerat projekt. Därför kan vi inte korrekt avgöra vilka filer som tillhör den om det finns en delvis avinstallation. Om du vill fortsätta installerar denna SDK när du ignorera det här felet, använder du:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problem med att skapa beräkning av Azure Machine Learning

Det finns en ovanligt risk att vissa användare som skapade sin Azure Machine Learning-arbetsyta från Azure-portalen innan GA-versionen kan inte skapa beräkning av Azure Machine Learning på arbetsytan. Du kan generera en supportförfrågan mot tjänsten, eller så kan du skapa en ny arbetsyta via portalen eller SDK, för att avblockera själv omedelbart.

## <a name="image-building-failure"></a>Bild byggnad fel

Bild för att skapa fel när du distribuerar webbtjänsten. Lösningen är att lägga till ”pynacl == 1.2.1” som ett pip beroende till Conda-fil för konfiguration av avbildningen.

## <a name="deployment-failure"></a>Distributions problem

Om du `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`ser ändrar du SKU: n för virtuella datorer som används i distributionen till en som har mer minne.

## <a name="fpgas"></a>FPGA

Du kommer inte att kunna distribuera modeller på FPGA förrän du har begärt och godkänts för FPGA kvot. För att begära åtkomst, fyller du i formuläret för begäran av kvot: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatiserad maskininlärning

Den automatiska maskin inlärningen i styrkorts flöde stöder för närvarande inte flödes flödes version 1,13. Om den här versionen installeras kommer paket beroenden att sluta fungera. Vi arbetar för att åtgärda det här problemet i en framtida version. 

### <a name="experiment-charts"></a>Experiment diagram

Binära klassificerings diagram (precisions återkallning, ROC, kurva osv.) som visas i automatiserade ML experiment-iterationer återges inte korrekt i användar gränssnittet sedan 4/12. Diagram observationer visar för närvarande inversa resultat, där modeller med bättre prestanda visas med lägre resultat. En lösning är under undersökning.

## <a name="databricks"></a>Databricks

Databricks och Azure Machine Learning-problem.

### <a name="failure-when-installing-packages"></a>Det gick inte att installera paket

Azure Machine Learning SDK-installationen Miss lyckas på Azure Databricks när fler paket är installerade. Vissa paket, till exempel `psutil`, kan orsaka konflikter. Undvik installations fel genom att installera paket genom att frysa biblioteks versionen. Det här problemet är relaterat till Databricks och inte till Azure Machine Learning service SDK. Du kan också uppleva det här problemet med andra bibliotek. Exempel:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Du kan också använda init-skript om du behåller problem med att installera mot python-bibliotek. Den här metoden stöds inte officiellt. Mer information finns i [kluster omfång init-skript](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Avbryta en automatiserad Machine Learning-körning

När du använder automatiserade maskin inlärnings funktioner på Azure Databricks för att avbryta körningen och starta en ny experiment körning startar du om Azure Databricks klustret.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterationer för automatisk maskin inlärning

Om du har fler än 10 iterationer i automatiska inställningar för maskin inlärning anger `show_output` du till `False` när du skickar körningen.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget för Azure Machine Learning SDK/Automatisk maskin inlärning

Widgeten Azure Machine Learning SDK stöds inte i en Databricks Notebook eftersom antecknings böckerna inte kan parsa HTML-widgetar. Du kan visa widgeten i portalen genom att använda den här python-koden i din Azure Databricks Notebook-cell:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Import fel: Ingen modul med namnet "Pandas. Core. indexs"

Om du ser det här felet när du använder automatisk maskin inlärning:

1. Kör det här kommandot för att installera två paket i Azure Databricks klustret: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Koppla från och återanslut sedan klustret till din bärbara dator. 

Om de här stegen inte löser problemet kan du försöka med att starta om klustret.

### <a name="failtosendfeather"></a>FailToSendFeather

Om du ser ett `FailToSendFeather` fel när du läser data på Azure Databricks kluster, se följande lösningar:

* Uppgradera `azureml-sdk[automl_databricks]` paketet till den senaste versionen.
* Lägg `azure-dataprep` till version 1.1.8 eller senare.
* Lägg `pyarrow` till version 0,11 eller senare.

## <a name="azure-portal"></a>Azure Portal

Om du går direkt för att visa din arbetsyta från en delningslänk från SDK: N eller portalen kan du inte visa normala översikt översiktssidan med prenumerationsinformation i tillägget. Du kommer inte heller att kunna växla till en annan arbetsyta. Om du behöver visa en annan arbets yta är lösningen att gå direkt till [Azure Portal](https://portal.azure.com) och söka efter namnet på arbets ytan.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Ibland kan det vara bra om du kan ange diagnostisk information när du frågar om du behöver hjälp. Om du vill se några loggar går du till [Azure Portal](https://portal.azure.com) och går till din arbets yta och väljer **arbets yta > Experiment > Kör > loggar**.

> [!NOTE]
> Azure Machine Learning tjänst loggar information från en rad olika källor under utbildningen, till exempel AutoML eller Docker-behållaren som kör övnings jobbet. Många av dessa loggar dokumenteras inte. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.

## <a name="activity-logs"></a>Aktivitetsloggar

Vissa åtgärder i Azure Machine Learning-arbetsytan loggar inte information i __aktivitets loggen__. Du kan till exempel starta en utbildning för att köra eller registrera en modell.

Några av de här åtgärderna visas i området __aktiviteter__ i din arbets yta, men de visar inte vem som initierade aktiviteten.

## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om den [resurskvoter](how-to-manage-quotas.md) som kan uppstå när du arbetar med Azure Machine Learning.

## <a name="authentication-errors"></a>Autentiseringsfel

Om du utför en hanterings åtgärd på ett beräknings mål från ett Fjärrjobb får du ett av följande fel:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Till exempel visas ett fel meddelande om du försöker skapa eller ansluta ett beräknings mål från en ML-pipeline som skickas för fjärrkörning.

## <a name="overloaded-azurefile-storage"></a>Överlagrad AzureFile-lagring

Använd följande lösningar om du `Unable to upload project files to working directory in AzureFile because the storage is overloaded`får ett fel meddelande.

Om du använder fil resurs för andra arbets belastningar, till exempel data överföring, är rekommendationen att använda blobbar så att fil resursen är kostnads fri att användas för att skicka körningar. Du kan också dela upp arbets belastningen mellan två olika arbets ytor.
