---
title: Förhindra lagrings begränsningar och experiment svars tid med in-och utdata-kataloger
description: I den här artikeln får du lära dig var du sparar dina experiment indatafiler och var du kan skriva utdatafiler för att förhindra lagrings begränsnings fel och experiment svars tid.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ea820536d93ec095f6f2929a9dc3b38d92779a58
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856055"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Var du ska spara och skriva filer för Azure Machine Learning experiment

I den här artikeln får du lära dig var du sparar indatafiler och var du kan skriva utdatafiler från experimenten för att förhindra lagrings gräns fel och experiment svars tid.

När du startar utbildning körs på ett [beräknings mål](how-to-set-up-training-targets.md), är de isolerade från externa miljöer. Syftet med den här designen är att säkerställa experimentets reproducerbarhet och portabilitet. Om du kör samma skript två gånger, på samma eller något annat beräknings mål, får du samma resultat. Med den här designen kan du behandla beräknings mål som tillstånds lösa beräknings resurser, var och en som inte har någon tillhörighet till de jobb som körs när de är klara.

## <a name="where-to-save-input-files"></a>Var indatafiler ska sparas

Innan du kan påbörja ett experiment på ett beräknings mål eller på den lokala datorn måste du se till att de nödvändiga filerna är tillgängliga för det beräknings målet, till exempel beroende filer och datafiler som din kod måste köra.

Azure Machine Learning kör utbildnings skript genom att kopiera hela mappen Script till mål beräknings kontexten och sedan ta en ögonblicks bild. Lagrings gränsen för ögonblicks bilder av experiment är 300 MB och/eller 2000 filer.

Därför rekommenderar vi följande:

* **Lagra filerna i ett Azure Machine Learning [data lager](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Detta förhindrar problem med experiment fördröjningen och har fördelarna med att komma åt data från ett fjärrberäknings mål, vilket innebär att autentisering och montering hanteras av Azure Machine Learning tjänsten. Läs mer om hur du anger ett data lager som käll katalog och laddar upp filer till ditt data lager i artikeln [åtkomst data från dina data lager](how-to-access-data.md) .

* **Om du bara behöver ett par datafiler och beroende skript och inte kan använda ett data lager,** placerar du filerna i samma katalog katalog som ditt utbildnings skript. Ange den här mappen som `source_directory` din direkt i utbildnings skriptet eller i koden som anropar ditt utbildnings skript.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Lagrings gränser för experiment-ögonblicksbilder

Vid experiment skapar Azure Machine Learning automatiskt en experiment ögonblicks bild av koden baserat på den katalog som du föreslår när du konfigurerar körningen. Detta har en total gräns på 300 MB och/eller 2000 filer. Om du överskrider den här gränsen visas följande fel:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Lös problemet genom att lagra dina experiment-filer på ett data lager. Om du inte kan använda ett data lager erbjuder tabellen nedan möjliga alternativa lösningar.

Experiment&nbsp;Beskrivning|Lösning för lagrings gräns
---|---
Mindre än 2000 filer & kan inte använda ett data lager| Åsidosätt storleks gräns för ögonblicks bild med <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Detta kan ta flera minuter beroende på antalet filer och filernas storlek.
Måste använda en speciell skript katalog| Skapa en `.amlignore` fil för att undanta filer från din ögonblicks bild av experimentet som inte ingår i käll koden. Lägg till fil namnen `.amlignore` i filen och placera den i samma katalog som ditt utbildnings skript. Filen använder samma [syntax och mönster](https://git-scm.com/docs/gitignore) som en `.gitignore` fil. `.amlignore`
Pipeline|Använd en annan under katalog för varje steg
Jupyter Notebooks| Skapa en `.amlignore` fil eller flytta din antecknings bok till en ny, tom, under katalog och kör koden igen.

## <a name="where-to-write-files"></a>Var du kan skriva filer

På grund av isolering av övnings experiment behålls inte ändringarna i filer som inträffar under körningarna utanför din miljö. Om skriptet ändrar de lokala filerna för beräkning, sparas inte ändringarna för nästa experiment och de sprids inte tillbaka till klient datorn automatiskt. Därför körs inte de ändringar som gjorts under det första experimentet och påverkar inte dem i den andra.

När du skriver ändringar rekommenderar vi att du skriver filer till ett Azure Machine Learning data lager. Se [få åtkomst till data från dina data lager](how-to-access-data.md).

Om du inte behöver ett data lager, skriver du filer `./outputs` till mappen och `./logs` /eller.

>[!Important]
> Två mappar, *utdata* och *loggar*, tar emot särskild behandling av Azure Machine Learning. När du skriver filer till`./outputs` och`./logs` mappar under träning överförs filerna automatiskt till din körnings historik, så att du har åtkomst till dem när körningen är färdig.

* **För utdata, t. ex. status meddelanden eller resultat,** kan du `./outputs` skriva filer till mappen så att de är beständiga som artefakter i körnings historiken. Var mindful av antalet och storleken på filer som skrivs till den här mappen, eftersom svars tiden kan uppstå när innehållet laddas upp till körnings historiken. Om det är ett problem med fördröjningen rekommenderas att skriva filer till ett data lager.

* **Spara skrivna filer som loggar i körnings historiken genom** att skriva `./logs` filer till mappen. Loggarna laddas upp i real tid, så den här metoden är lämplig för strömning av direktsända uppdateringar från en fjärrkörning.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [att komma åt data från dina data lager](how-to-access-data.md).

* Läs mer om [hur du ställer in utbildnings mål](how-to-set-up-training-targets.md).
