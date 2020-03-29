---
title: Azure Data Lake Storage Gen1-migrering över region | Microsoft-dokument
description: Lär dig mer om migrering mellan regioner för Azure Data Lake Storage Gen1.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60518465"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrera Azure Data Lake Storage Gen1 mellan regioner

När Azure Data Lake Storage Gen1 blir tillgängligt i nya regioner kan du välja att göra en engångsmigrering för att dra nytta av den nya regionen. Lär dig vad du bör tänka på när du planerar och slutför migreringen.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Mer information finns i [Skapa ditt kostnadsfria Azure-konto idag](https://azure.microsoft.com/pricing/free-trial/).
* **Ett DataSjölagringsgenm1-konto i två olika regioner**. Mer information finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Mer information finns i [Introduktion till Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Överväganden vid migrering

Identifiera först den migreringsstrategi som fungerar bäst för ditt program som skriver, läser eller bearbetar data i Data Lake Storage Gen1. När du väljer en strategi bör du tänka på programmets tillgänglighetskrav och driftstopp som inträffar under en migrering. Din enklaste metod kan till exempel vara att använda molnmigreringsmodellen "lift-and-shift". I den här metoden pausar du programmet i din befintliga region medan alla dina data kopieras till den nya regionen. När kopieringen är klar återupptar du programmet i den nya regionen och tar sedan bort det gamla Data Lake Storage Gen1-kontot. Driftstopp under migreringen krävs.

Om du vill minska stilleståndstiden kan du omedelbart börja inta nya data i den nya regionen. När du har de minsta data som behövs kör du programmet i den nya regionen. I bakgrunden fortsätter du att kopiera äldre data från det befintliga Data Lake Storage Gen1-kontot till det nya Data Lake Storage Gen1-kontot i den nya regionen. Genom att använda den här metoden kan du växla till den nya regionen med lite driftstopp. När alla äldre data har kopierats tar du bort det gamla Data Lake Storage Gen1-kontot.

Andra viktiga detaljer att tänka på när du planerar migreringen är:

* **Datavolym**. Mängden data (i gigabyte, antalet filer och mappar och så vidare) påverkar den tid och de resurser du behöver för migreringen.

* **Kontonamn för DataSjölagringsgen1**. Det nya kontonamnet i den nya regionen måste vara globalt unikt. Namnet på ditt gamla Data Lake Storage Gen1-konto i östra US 2 kan till exempel vara contosoeastus2.azuredatalakestore.net. Du kan namnge ditt nya Data Lake Storage Gen1-konto i norra EU contosonortheu.azuredatalakestore.net.

* **Verktyg**. Vi rekommenderar att du använder [Azure Data Factory Copy Activity](../data-factory/connector-azure-data-lake-store.md) för att kopiera Data Lake Storage Gen1-filer. Data Factory stöder datarörelser med hög prestanda och tillförlitlighet. Tänk på att Data Factory bara kopierar mapphierarkin och innehållet i filerna. Du måste manuellt tillämpa alla åtkomstkontrollistor (ACL: er) som du använder i det gamla kontot på det nya kontot. Mer information, inklusive prestandamål för bästa tänkbara scenarier, finns i [prestanda- och justeringsguiden för kopiera aktivitet](../data-factory/copy-activity-performance.md). Om du vill att data ska kopieras snabbare kan du behöva använda ytterligare molndatarörelseenheter. Vissa andra verktyg, till exempel AdlCopy, stöder inte kopiering av data mellan regioner.  

* **Bandbredd avgifter**. [Bandbreddsavgifter](https://azure.microsoft.com/pricing/details/bandwidth/) tillkommer eftersom data överförs från en Azure-region.

* **ACL:er på dina data**. Skydda dina data i den nya regionen genom att använda ACL:er på filer och mappar. Mer information finns [i Skydda data som lagras i Azure Data Lake Storage Gen1](data-lake-store-secure-data.md). Vi rekommenderar att du använder migreringen för att uppdatera och justera dina åtkomstkontrollistor. Du kanske vill använda inställningar som liknar dina aktuella inställningar. Du kan visa de ACL:er som tillämpas på valfri fil med hjälp av Azure-portalen, [PowerShell-cmdlets](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)eller SDK:er.  

* **Placering av analystjänster**. För bästa prestanda bör dina analystjänster, som Azure Data Lake Analytics eller Azure HDInsight, beta i samma region som dina data.  

## <a name="next-steps"></a>Nästa steg
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
