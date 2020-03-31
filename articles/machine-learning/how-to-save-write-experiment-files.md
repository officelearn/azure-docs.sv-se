---
title: Var du kan spara & skriva experimentfiler
titleSuffix: Azure Machine Learning
description: Lär dig var du sparar experimentindatafiler och var du kan skriva utdatafiler för att förhindra lagringsbegränsningsfel och experimentfördröjning.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 12a38b08fd429280f34b4eb02d4b72187b622261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078408"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Var du kan spara och skriva filer för Azure Machine Learning-experiment
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig var du kan spara indatafiler och var du kan skriva utdatafiler från experimenten för att förhindra lagringsbegränsningsfel och experimentfördröjning.

När du startar utbildning körs på ett [beräkningsmål](how-to-set-up-training-targets.md)är de isolerade från externa miljöer. Syftet med denna konstruktion är att säkerställa reproducerbarhet och portabilitet av experimentet. Om du kör samma skript två gånger får du samma resultat på samma eller ett annat beräkningsmål. Med den här designen kan du behandla beräkningsmål som tillståndslösa beräkningsresurser, var och en har ingen tillhörighet till de jobb som körs när de är klara.

## <a name="where-to-save-input-files"></a>Var du sparar indatafiler

Innan du kan initiera ett experiment på ett beräkningsmål eller din lokala dator måste du se till att nödvändiga filer är tillgängliga för beräkningsmålet, till exempel beroendefiler och datafiler som koden behöver köra.

Azure Machine Learning kör utbildningsskript genom att kopiera hela skriptmappen till målberäkningskontexten och sedan en ögonblicksbild. Lagringsgränsen för experimentögonblicksbilder är 300 MB och/eller 2 000 filer.

Av denna anledning rekommenderar vi:

* **Lagra dina filer i ett Azure Machine [Learning-datalager](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Detta förhindrar problem med experimentfördröjning och har fördelarna med att komma åt data från ett fjärrberäkningsmål, vilket innebär att autentisering och montering hanteras av Azure Machine Learning. Läs mer om hur du anger ett datalager som källkatalog och laddar upp filer till datalagret i artikeln [Access-data från dina datalager.](how-to-access-data.md)

* **Om du bara behöver ett par datafiler och beroendeskript och inte kan använda ett datalager placerar** du filerna i samma mappkatalog som utbildningsskriptet. Ange den här `source_directory` mappen som din direkt i träningsskriptet eller i koden som anropar utbildningsskriptet.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Lagringsgränser för ögonblicksbilder av experiment

För experiment gör Azure Machine Learning automatiskt en experimentögonblicksbild av din kod baserat på den katalog du föreslår när du konfigurerar körningen. Detta har en total gräns på 300 MB och/eller 2000 filer. Om du överskrider den här gränsen visas följande fel:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Lös det här felet genom att lagra experimentfilerna i ett datalager. Om du inte kan använda ett datalager erbjuder tabellen nedan möjliga alternativa lösningar.

Beskrivning&nbsp;av experiment|Lösning för lagringsgräns
---|---
Mindre än 2 000 filer & inte kan använda ett datalager| Åsidosätt storleksgränsen för ögonblicksbilder med <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Detta kan ta flera minuter beroende på antalet filer och storlek.
Måste använda specifik skriptkatalog| Gör `.amlignore` en fil för att utesluta filer från experimentögonblicksbilden som inte ingår i källkoden. Lägg till filnamnen `.amlignore` i filen och placera det i samma katalog som utbildningsskriptet. Filen `.amlignore` använder samma [syntax och](https://git-scm.com/docs/gitignore) mönster `.gitignore` som en fil.
Pipeline|Använda en annan underkatalog för varje steg
Jupyter Notebooks| Skapa `.amlignore` en fil eller flytta anteckningsboken till en ny, tom, underkatalog och kör koden igen.

## <a name="where-to-write-files"></a>Var du ska skriva filer

På grund av isoleringen av träningsexperiment kvarstår inte nödvändigtvis ändringarna av filer som sker under körningar utanför din miljö. Om skriptet ändrar filerna lokalt för att beräkna sparas inte ändringarna för nästa experimentkörning och de sprids inte tillbaka till klientdatorn automatiskt. Därför påverkar inte de ändringar som gjordes under den första experimentkörningen och bör inte påverka dem i den andra.

När du skriver ändringar rekommenderar vi att du skriver filer till ett Azure Machine Learning-datalager. Se [Åtkomstdata från dina datalager](how-to-access-data.md).

Om du inte behöver ett datalager skriver `./outputs` du `./logs` filer till och/eller mappen.

>[!Important]
> Två mappar, *utdata* och *loggar*, får särskild behandling av Azure Machine Learning. Under träningen, när du`./outputs` `./logs` skriver filer till och mappar, laddas filerna automatiskt upp till din körningshistorik, så att du har tillgång till dem när körningen är klar.

* **För utdata som statusmeddelanden eller bedömningsresultat** skriver du filer till `./outputs` mappen, så att de sparas som artefakter i körningshistoriken. Tänk på antalet filer som skrivits till den här mappen, eftersom svarstiden kan uppstå när innehållet överförs för att köra historiken. Om svarstid är ett problem rekommenderas att du skriver filer till ett datalager.

* **Om du vill spara den skrivna filen som loggar i körningshistoriken** skriver du filer till `./logs` mappen. Loggarna laddas upp i realtid, så den här metoden är lämplig för direktuppspelning av liveuppdateringar från en fjärrkörning.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur du kommer åt data från dina datalager](how-to-access-data.md).

* Läs mer om [Hur du ställer in träningsmål](how-to-set-up-training-targets.md).
