---
title: Azure Data Lake Storage Gen2 hierarkiskt namn område
description: Beskriver konceptet för det hierarkiska namn området för Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 0b98892bd31b097e3dc217d54f52f12550599d32
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847142"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Azure Data Lake Storage Gen2 hierarkiskt namn område

En nyckel mekanism som gör det möjligt för Azure Data Lake Storage Gen2 att tillhandahålla fil system prestanda vid objekt lagrings skala och priser är att lägga till ett **hierarkiskt namn område**. På så sätt kan samling av objekt/filer i ett konto ordnas i en hierarki med kataloger och kapslade under kataloger på samma sätt som fil systemet på datorn är ordnat. Med det hierarkiska namn området aktiverat kan ett lagrings konto tillhandahålla skalbarhet och kostnads effektivitet för objekt lagring, med semantiska fil system som är välkända för analys motorer och ramverk.

## <a name="the-benefits-of-the-hierarchical-namespace"></a>Fördelarna med det hierarkiska namn området

Följande fördelar är kopplade till fil system som implementerar ett hierarkiskt namn område över BLOB-data:

- **Atomic Directory-manipulering:** Objekt lager uppskattar en katalog-hierarki genom att använda en konvention för att bädda in snedstreck (/) i objekt namnet för att ange Sök vägs segment. Även om den här konventionen används för att ordna objekt ger konventionen ingen hjälp för åtgärder som att flytta, byta namn på eller ta bort kataloger. Utan riktiga kataloger måste program bearbeta potentiellt miljon tals enskilda blobbar för att kunna uppnå aktiviteter på katalog nivå. Som kontrast bearbetar det hierarkiska namn området dessa uppgifter genom att uppdatera en enskild post (överordnad katalog).

    Den här dramatiska optimeringen är särskilt viktig för många Big data Analytics-ramverk. Verktyg som Hive, Spark osv. skriver ofta utdata till tillfälliga platser och byter sedan namn på platsen i slutet av jobbet. Utan det hierarkiska namn området kan det här namnet ofta ta längre tid än själva analys processen. Lägre jobb fördröjning motsvarar lägre totala ägande kostnader (TCO) för analyser av arbets belastningar.

- **Välbekant gränssnitts stil:** Fil systemen kan förstås av både utvecklare och användare. Du behöver inte lära dig ett nytt lagrings paradigm när du flyttar till molnet eftersom fil system gränssnittet som exponeras av Data Lake Storage Gen2 är samma paradigm som används av datorer, stora och små.

En av orsakerna till att objektet inte tidigare har stöd för ett hierarkiskt namn område är att ett hierarkiskt namn område begränsar skala. Men den hierarkiska namn rymden för Data Lake Storage Gen2 skalas linjärt och försämrar inte data kapaciteten eller prestandan.

## <a name="when-to-enable-the-hierarchical-namespace"></a>När du ska aktivera det hierarkiska namn området

Vi rekommenderar att du aktiverar det hierarkiska namn området för lagrings arbets belastningar som är utformade för fil system som manipulerar kataloger. Detta omfattar alla arbets belastningar som främst används för analys bearbetning. Data uppsättningar som kräver en hög grad av organisation kommer också att ha nytta av att aktivera det hierarkiska namn området.

Orsakerna till att aktivera det hierarkiska namn området bestäms av en TCO-analys. I allmänhet kan förbättringar av arbets belastnings fördröjningen på grund av lagrings acceleration kräva beräknings resurser för kortare tid. Svars tiden för många arbets belastningar kan förbättras på grund av atomisk katalog manipulation som aktive ras av den hierarkiska namn rymden. I många arbets belastningar representerar beräknings resursen > 85% av den totala kostnaden och så vidare är en liten minskning av arbets belastnings fördröjningen lika med en betydande mängd TCO-besparingar. Även i de fall där aktivering av hierarkiskt namn område ökar lagrings kostnaderna sänks ägande kostnaden fortfarande på grund av sänkta beräknings kostnader.

## <a name="when-to-disable-the-hierarchical-namespace"></a>När du ska inaktivera det hierarkiska namn området

Vissa objekt lagrings arbets belastningar kanske inte får någon förmån genom att aktivera det hierarkiska namn området. Exempel på detta är säkerhets kopior, avbildnings lagring och andra program där objekt organisation lagras separat från själva objekten (till exempel: i en separat databas).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagrings konto](./data-lake-storage-quickstart-create-account.md)
