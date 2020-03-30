---
title: Kopiera program och data till poolnoder
description: Lär dig hur du kopierar program och data till poolnoder.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385589"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Kopiera program och data till poolnoder

Azure Batch stöder flera sätt att hämta data och program på beräkningsnoder så att data och program är tillgängliga för användning av uppgifter. Data och program kan krävas för att köra hela jobbet och måste därför installeras på varje nod. Vissa kan endast krävas för en viss uppgift, eller behöver installeras för jobbet men behöver inte vara på varje nod. Batch har verktyg för vart och ett av dessa scenarier.

- **Poolstart aktivitetsresursfiler:** För program eller data som måste installeras på varje nod i poolen. Använd den här metoden tillsammans med antingen ett programpaket eller startaktivitetens resursfilsamling för att utföra ett installationskommando.  

Exempel: 
- Använda kommandoraden startuppgift för att flytta eller installera program

- Ange en lista över specifika filer eller behållare i ett Azure-lagringskonto. Mer information finns [i add#resourcefile i REST-dokumentation](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- Varje jobb som körs i poolen kör MyApplication.exe som först måste installeras med MyApplication.msi. Om du använder den här mekanismen måste du ställa in startuppgiftens **vänta på** att lyckasgenskapen ska **vara sann**. Mer information finns [i add#starttask i REST-dokumentationen](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask).

- **Programpaketreferenser** i poolen: För program eller data som måste installeras på varje nod i poolen. Det finns inget installationskommando som är associerat med ett programpaket, men du kan använda en startuppgift för att köra alla installationskommandon. Om programmet inte kräver installation eller består av ett stort antal filer kan du använda den här metoden. Programpaket är väl lämpade för ett stort antal filer eftersom de kombinerar ett stort antal filreferenser till en liten nyttolast. Om du försöker inkludera fler än 100 separata resursfiler i en aktivitet kan batchtjänsten stöta på interna systembegränsningar för en enskild aktivitet. Använd också programpaket om du har rigorösa versionskrav där du kan ha många olika versioner av samma program och måste välja mellan dem. Mer information finns i [Distribuera program för att beräkna noder med Batch-programpaket](https://docs.microsoft.com/azure/batch/batch-application-packages).

- **Uppgiftsresursfiler för jobbförberedelse:** För program eller data som måste installeras för att jobbet ska kunna köras, men som inte behöver installeras på hela poolen. Till exempel: om din pool har många olika typer av jobb, och endast en jobbtyp behöver MyApplication.msi för att köra, är det vettigt att placera installationssteget i en jobbförberedelseuppgift. Mer information om jobbförberedelseaktiviteter finns i [Kör jobbförberedelser och jobbfrisättningsuppgifter på batchberäkningsnoder](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/).

- **Aktivitetsresursfiler**: För när ett program eller data endast är relevanta för en enskild aktivitet. Till exempel: Du har fem uppgifter, varje bearbetar en annan fil och sedan skriver utdata till blob lagring.  I det här fallet bör indatafilen anges i samlingen **aktiviteter resursfiler** eftersom varje aktivitet har sin egen indatafil.

## <a name="determine-the-scope-required-of-a-file"></a>Fastställa omfattningen som krävs för en fil

Du måste bestämma omfattningen av en fil - är den fil som krävs för en pool, ett jobb eller en uppgift. Filer som är begränsade till poolen bör använda poolprogrampaket eller en startuppgift. Filer som begränsas till jobbet bör använda en jobbförberedelseuppgift. Ett bra exempel på filer som omfattas på poolen eller jobbnivå är program. Filer som är begränsade till aktiviteten bör använda aktivitetsresursfiler.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Andra sätt att hämta data till batchberäkningsnoder

Det finns andra sätt att hämta data på batchberäkningsnoder som inte är officiellt integrerade i BATCH REST API. Eftersom du har kontroll över Azure Batch-noder och kan köra anpassade körbara filer kan du hämta data från valfritt antal anpassade källor så länge batchnoden har anslutning till målet och du har autentiseringsuppgifterna till den källan till Azure Batch-noden. Några vanliga exempel är:

- Hämta data från SQL
- Ladda ned data från andra webbtjänster/anpassade platser
- Mappa en nätverksresurs

### <a name="azure-storage"></a>Azure Storage

Blob-lagring har nedladdningskalbarhetsmål. Azure storage file share scalability targets är desamma som för en enda blob. Storleken påverkar antalet noder och pooler du behöver.

