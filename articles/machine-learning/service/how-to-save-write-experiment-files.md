---
title: Förhindra att storage-begränsningar och experimentera svarstid med inkommande och utgående kataloger
description: I den här artikeln lär du dig var du vill spara dina experiment indatafiler och var du vill skriva utdatafilerna för att förhindra begränsning lagringsfel och experimentera svarstid.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 1f9199b5bae0c82cd46750d8ef5522a0d3579671
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595283"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Där det är att spara och skriva filer för Azure Machine Learning-experiment

I den här artikeln lär du dig var du vill spara indatafiler och var du vill skriva utdatafilerna från dina experiment för att förhindra att lagringen begränsa fel och experimentera svarstid.

När startar utbildning körs på en [beräkningsmålet](how-to-set-up-training-targets.md), de är isolerade från utanför miljöer. Syftet med den här designen är att säkerställa reproducerbarhet och portabilitet av experimentet. Om du kör samma skript två gånger på samma eller en annan compute mål, du får samma resultat. Med den här designen kan du hantera beräkningsmål som tillståndslösa beräkningsresurser som vart och ett har ingen tillhörighet ifråga om jobb som körs när de är klar.

## <a name="where-to-save-input-files"></a>Var du vill spara inkommande filer

Innan du kan initiera ett experiment på beräkningsmål eller den lokala datorn, måste du kontrollera att de nödvändiga filerna är tillgängliga för den beräkningsmål, till exempel beroendefiler och datafiler koden ska köras.

Azure Machine Learning körs utbildningsskript genom att kopiera mappen hela skriptet till mål-beräkningskontexten och sedan tar en ögonblicksbild. Lagringsgränsen för experiment ögonblicksbilder är 300 MB och/eller 2000-filer.

Därför rekommenderar vi:

* **Lagra filer i en Azure Machine Learning [datalager](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Detta förhindrar experiment svarstidsproblem och har fördelar för att komma åt data från en fjärransluten beräkningsmål, vilket innebär att autentisering och montering som hanteras av Azure Machine Learning-tjänsten. Mer information om att ange ett datalager som källkatalogen och ladda upp filer till ditt datalager i den [komma åt data från ditt datalager](how-to-access-data.md) artikeln.

* **Om du behöver bara ett par datafiler och beroende skript och kan inte använda ett datalager** placera filerna i katalogen med samma mapp som dina utbildningsskript. Anger den här mappen som din `source_directory` direkt i dina utbildningsskript, eller i den kod som anropar skriptet utbildning.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Lagringsgränserna experiment ögonblicksbilder

Experiment görs Azure Machine Learning för ett experiment ögonblicksbild av din kod baserat på den katalog som du föreslå när du konfigurerar körningen. Detta har högst 300 MB och/eller 2000-filer. Om du överskrider den här gränsen, visas följande fel:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Lös felet genom att lagra experiment filer på ett datalager. Om du inte kan använda ett datalager i tabellen nedan erbjuder alternativa lösningar.

Experiment&nbsp;beskrivning|Gränsen lagringslösning
---|---
Mindre än 2000 filer och det går inte att använda ett datalager| Åsidosätt gräns för ögonblicksbilder storlek med <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Det kan ta flera minuter beroende på antalet och storleken på filerna.
Måste använda ett visst skript directory| Gör en `.amlignore` filen du undantar filer från dina experiment ögonblicksbild som inte är en del av källkoden. Lägg till filnamnen till den `.amlignore` filen och placera den i samma katalog som dina utbildningsskript. Den `.amlignore` filen använder samma [syntax och mönster](https://git-scm.com/docs/gitignore) som en `.gitignore` fil.
Pipeline|Använd en annan underkatalog för varje steg
Jupyter Notebooks| Skapa en `.amlignore` filen eller flytta din bärbara dator till en ny, tom underkatalogen och köra din kod igen.

## <a name="where-to-write-files"></a>Var du vill skriva filer

På grund av isoleringen av utbildning experiment, är ändringar i filerna som sker under körningar inte nödvändigtvis beständiga utanför din miljö. Om skriptet ändrar filerna som är lokala för compute, ändringarna sparas inte för experimentet nästa kör och de inte har spritts till klientdatorn automatiskt. Därför ändringar som görs under det första experimentet kör inte och bör inte påverka andra.

När du skriver ändringar, rekommenderar vi skriva filer till en Azure Machine Learning-datalager. Se [komma åt data från ditt datalager](how-to-access-data.md).

Om du inte behöver ett datalager kan skriva filer till den `./outputs` och/eller `./logs` mapp.

>[!Important]
> Två mappar *matar ut* och *loggar*, ta emot särskild behandling av Azure Machine Learning. Vid träning, när du skriver filer till`./outputs` och`./logs` mappar, filerna kommer automatiskt att överföra till din körningshistorik, så att du har åtkomst till dem när din körning är klar.

* **För utdata, till exempel statusmeddelanden eller bedömnings resultat** skriva filer till den `./outputs` mappen, så att de har sparats som artefakter i körningshistoriken. Tänk också på antalet och storleken på filerna som skrivits till den här mappen som svarstid kan uppstå när innehåll överförs till körningshistorik. Om svarstiden är något problem, bör skriva filer till ett datalager.

* **Att spara skriftliga filer som loggar i körningshistorik,** skriva filer till `./logs` mapp. Loggarna överförs i realtid, så att den här metoden är lämplig för strömning live uppdateringar från en fjärransluten kör.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [kommer åt data från ditt datalager](how-to-access-data.md).

* Läs mer om [så ange utbildning mål](how-to-set-up-training-targets.md).
