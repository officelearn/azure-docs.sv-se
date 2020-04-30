---
title: Kopiera program och data till poolnoder
description: Lär dig hur du kopierar program och data till pool-noder.
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 700e9b80f8420266c0300b47bdd30bc271f8421c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115592"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Kopiera program och data till poolnoder

Azure Batch stöder flera olika sätt att hämta data och program på datornoder så att data och program kan användas av uppgifter. Data och program kan behövas för att köra hela jobbet och måste installeras på varje nod. Vissa kan bara krävas för en viss uppgift eller måste installeras för jobbet men behöver inte finnas på varje nod. Batch har verktyg för var och en av dessa scenarier.

- **Resurspool för start aktivitet**för en pool: för program eller data som måste installeras på varje nod i poolen. Använd den här metoden tillsammans med antingen ett programpaket eller start aktivitetens resurs fil samling för att utföra ett installations kommando.  

Exempel: 
- Använd kommando raden starta aktivitet för att flytta eller installera program

- Ange en lista med vissa filer eller behållare i ett Azure Storage-konto. Mer information finns i [Lägg till # resourcefile i rest-dokumentationen](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- Varje jobb som körs i poolen kör appen. exe som först måste installeras med unapplication. msi. Om du använder den här mekanismen måste du ange start uppgiftens **vänta på** att egenskapen lyckades anges till **Sant**. Mer information finns i [Lägg till # starttask i rest-dokumentationen](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask).

- **Programpaket referenser** i poolen: för program eller data som måste installeras på varje nod i poolen. Det finns inget installations kommando som är associerat med ett programpaket, men du kan använda en start uppgift för att köra ett installations kommando. Om programmet inte kräver installation eller består av ett stort antal filer, kan du använda den här metoden. Programpaket lämpar sig väl för stora mängder filer eftersom de kombinerar ett stort antal fil referenser till en liten nytto Last. Om du försöker att inkludera fler än 100 separata resursfiler i en aktivitet kan batch-tjänsten komma fram till interna system begränsningar för en enskild uppgift. Använd även program paket om du har rigorösa versions krav där du kan ha många olika versioner av samma program och behöver välja mellan dem. Mer information finns i [distribuera program till Compute-noder med batch-programpaket](https://docs.microsoft.com/azure/batch/batch-application-packages).

- **Resursfiler för jobb förberedelse aktivitet**: för program eller data som måste installeras för att jobbet ska köras, men som inte behöver installeras på hela poolen. Exempel: om din pool har många olika typer av jobb och endast en jobbtyp behöver programmet. msi för att köra, är det bra att ställa in installations steget i en jobb förberedelse aktivitet. Mer information om jobb förberedelse aktiviteter finns i [köra jobb förberedelse och jobb publicerings aktiviteter i batch Compute-noder](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/).

- **Resurs filer för aktivitet**: när ett program eller data endast är relevanta för en enskild uppgift. Exempel: du har fem aktiviteter, varje bearbetar en annan fil och skriver sedan utdata till Blob Storage.  I det här fallet ska indatafilen anges i samlingen **tasks-resursfiler** eftersom varje aktivitet har sin egen indatafil.

## <a name="determine-the-scope-required-of-a-file"></a>Fastställer omfattningen som krävs för en fil

Du måste bestämma omfånget för en fil – är den fil som krävs för en pool, ett jobb eller en aktivitet. Filer som är begränsade till poolen bör använda poolprogram paket eller en start uppgift. Filer som omfattas av jobbet bör använda en jobb förberedelse aktivitet. Ett lämpligt exempel på filer som omfattas av poolen eller jobb nivån är program. Filer som är begränsade till aktiviteten bör använda resursfiler för aktiviteter.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Andra sätt att hämta data till batch Compute-noder

Det finns andra sätt att hämta data till batch Compute-noder som inte är officiellt integrerade i batch-REST API. Eftersom du har kontroll över Azure Batch noder och kan köra anpassade körbara filer, kan du hämta data från valfritt antal anpassade källor så länge batch-noden har anslutning till målet och du har autentiseringsuppgifterna till källan på noden Azure Batch. Några vanliga exempel är:

- Hämtar data från SQL
- Hämta data från andra webb tjänster/anpassade platser
- Mappa en nätverks resurs

### <a name="azure-storage"></a>Azure-lagring

Blob Storage har nedladdnings bara skalbarhets mål. Skalbarhet för Azure Storage-filresursen är desamma som för en enda blob. Storleken påverkar antalet noder och pooler som du behöver.

