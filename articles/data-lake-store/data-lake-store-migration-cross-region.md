---
title: Azure Data Lake Storage Gen1 interregionala-migrering | Microsoft Docs
description: Läs mer om migrering över flera regioner för Azure Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233056"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrera Azure Data Lake Storage Gen1 över regioner

Eftersom Azure Data Lake Storage Gen1 blir tillgängligt i nya regioner kan du välja att göra ett tillfälle för att kunna utnyttja det nya området. Lär dig vad du ska tänka på när du planerar och slutföra migreringen.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Mer information finns i [skapa ditt kostnadsfria Azure-konto i dag](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Data Lake Storage Gen1 konto i två olika regioner**. Mer information finns i [Kom igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Mer information finns i [Introduktion till Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Överväganden vid migrering

Först identifiera migreringsstrategi som fungerar bäst för ditt program som skriver, läser och bearbetar data i Data Lake Storage Gen1. När du väljer en strategi du programmets tillgänglighet och stilleståndstid som uppstår under migreringen. Exempelvis kanske den enklaste metoden att använda ”lift and shift” molnet migration-modellen. Den här metoden kan pausa du programmet i din befintliga region medan alla dina data har kopierats till det nya området. När kopieringen är klar kan du återuppta ditt program i det nya området och ta sedan bort det gamla Data Lake Storage Gen1-kontot. Det krävs avbrott under migreringen.

Om du vill minska stopptiden börja du omedelbart att mata in nya data i det nya området. När du har den minsta mängd data som behövs, kör du ditt program i det nya området. I bakgrunden, fortsätter du att kopiera äldre data från det befintliga Gen1 för Data Lake Storage-kontot till det nya Gen1 för Data Lake Storage-kontot i det nya området. Med den här metoden kan göra du växeln till det nya området med lite avbrott. Ta bort det gamla Data Lake Storage Gen1-kontot när alla äldre data har kopierats.

Annan viktig information att tänka på när du planerar din migrering är:

* **Datavolym**. Mängden data (i GB, hur många filer och mappar och så vidare) påverkar tid och resurser du behöver för migreringen.

* **Kontonamnet för data Lake Storage Gen1**. Det nya kontonamnet i det nya området måste vara globalt unikt. Namnet på ditt gamla Gen1 för Data Lake Storage-konto i USA, östra 2 kan till exempel vara contosoeastus2.azuredatalakestore.net. Du kan namnge ditt nya Gen1 för Data Lake Storage-konto i Norra Europa contosonortheu.azuredatalakestore.net.

* **Verktyg för**. Vi rekommenderar att du använder den [Kopieringsaktiviteten för Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) att kopiera Data Lake Storage Gen1 filer. Data Factory stöder dataförflyttning med höga prestanda och tillförlitlighet. Tänk på att Data Factory kopierar endast mapphierarkin och innehållet i filerna. Du måste manuellt tillämpa några åtkomstkontrollistor (ACL) som du använder i det gamla kontot till det nya kontot. Mer information, inklusive prestandamål för bästa möjliga scenarier finns i den [Kopieringsaktiviteten prestanda- och Justeringsguiden](../data-factory/copy-activity-performance.md). Om du vill att data som kopieras snabbare kan du behöva använda ytterligare enheter för Molndataflytt. Vissa andra verktyg som AdlCopy, stöder inte kopiering av data mellan regioner.  

* **Bandbreddsavgifter**. [Bandbreddsavgifter](https://azure.microsoft.com/pricing/details/bandwidth/) gäller eftersom data överförs utanför en Azure-region.

* **ACL: er på dina data**. Skydda dina data i det nya området genom att använda ACL: er för filer och mappar. Mer information finns i [skydda data som lagrats i Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Vi rekommenderar att du använder migrering för att uppdatera och justera dina ACL: er. Du kanske vill använda inställningarna som liknar de aktuella inställningarna. Du kan visa ACL: er som tillämpas på alla filer med hjälp av Azure-portalen [PowerShell-cmdletar](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission), eller SDK: er.  

* **Platsen för Analystjänster**. För bästa prestanda bör dina Analystjänster som Azure Data Lake Analytics eller Azure HDInsight, vara i samma region som dina data.  

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
