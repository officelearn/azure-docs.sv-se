---
title: Azure Data Lake Store mellan region migrering | Microsoft Docs
description: Mer information om migrering mellan region för Azure Data Lake Store.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 488a9954cef210b727518375e218fe084129a6f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="migrate-data-lake-store-across-regions"></a>Migrera Data Lake Store över regioner

Som Azure Data Lake Store blir tillgängliga i nya regioner, kan du välja att göra en enstaka migrering för att dra nytta av nya region. Lär dig vad du ska tänka på när du planerar och slutföra migreringen.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Mer information finns i [skapa din kostnadsfria Azure-konto idag](https://azure.microsoft.com/pricing/free-trial/).
* **Ett Data Lake Store-konto i två olika regioner**. Mer information finns i [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Mer information finns i [Introduktion till Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Överväganden vid migrering

Först identifiera migreringsstrategi som passar bäst för ditt program som skriver, läser eller bearbetar data i Data Lake Store. När du väljer en strategi, Överväg programmets tillgänglighet och avbrottstiden som uppstår under migreringen. Till exempel kan den enklaste metoden är att använda migrering ”lift och SKIFT” molnmodellen. I den här metoden pausar programmet i din befintliga region när alla data kopieras till den nya regionen. När kopieringen är klar du återuppta ditt program i den nya regionen och ta sedan bort det gamla Data Lake Store-kontot. Det krävs avbrott under migreringen.

Om du vill minska avbrottstiden starta du omedelbart att mata in nya data i den nya regionen. När du har den minsta mängd data som behövs, kör ditt program i den nya regionen. Fortsätta att kopiera äldre data från befintliga Data Lake Store-kontot till det nya Data Lake Store-kontot i den nya regionen i bakgrunden. Med den här metoden kan du växeln till den nya regionen lite avbrott. Ta bort det gamla Data Lake Store-kontot när alla äldre data har kopierats.

Viktig information att tänka på när du planerar migreringen är:

* **Datavolymen**. Mängden data (i GB, hur många filer och mappar och så vidare) påverkar tid och resurser som du behöver för migreringen.

* **Data Lake Store-kontonamnet**. Det nya kontonamnet i den nya regionen måste vara globalt unika. Namnet på ditt gamla Data Lake Store-konto i östra USA 2 kan till exempel vara contosoeastus2.azuredatalakestore.net. Ditt nya Data Lake Store-konto i Norra Europa contosonortheu.azuredatalakestore.net kan namnet.

* **Verktyg för**. Vi rekommenderar att du använder den [Azure Data Factory-Kopieringsaktiviteten](../data-factory/connector-azure-data-lake-store.md) att kopiera Data Lake Store-filer. Data Factory har stöd för flytt av data med hög prestanda och tillförlitlighet. Tänk på att Data Factory kopierar endast mapphierarkin och innehållet i filerna. Du måste manuellt koppla alla åtkomstkontrollistor (ACL) som du använder i det gamla kontot till det nya kontot. Mer information, inklusive prestandamål för bästa möjliga scenarier finns i [prestandajustering guide och Kopieringsaktivitet prestanda](../data-factory/copy-activity-performance.md). Om du vill att data som kopieras snabbare kan du behöva använda ytterligare enheter i molnet Data Movement. Vissa andra verktyg som AdlCopy, stöder inte kopiering av data mellan regioner.  

* **Kostnader för bandbredd**. [Kostnader för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/) gäller eftersom data överförs utanför en Azure-region.

* **ACL: er på dina data**. Skydda dina data i den nya regionen genom att använda ACL: er för filer och mappar. Mer information finns i [att skydda data som lagras i Azure Data Lake Store](data-lake-store-secure-data.md). Vi rekommenderar att du använder migrering för att uppdatera och ändra din ACL: er. Du kanske vill använda inställningarna för liknar de aktuella inställningarna. Du kan visa ACL: er som tillämpas på alla filer med hjälp av Azure portal [PowerShell-cmdlets](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission), eller SDK: er.  

* **Platsen för Analystjänster**. För bästa prestanda måste din analytics tjänster, till exempel Azure Data Lake Analytics eller Azure HDInsight vara i samma region som dina data.  

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Data Lake Store](data-lake-store-overview.md)
