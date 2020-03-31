---
title: Utvärdera ett stort antal fysiska servrar för migrering till Azure med Azure Migrate | Microsoft-dokument
description: Beskriver hur du bedömer ett stort antal fysiska servrar för migrering till Azure med hjälp av Azure Migrate-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: a19a1b6e7416667079ab07fc5440ee8828c26bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76294375"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Utvärdera ett stort antal fysiska servrar för migrering till Azure

I den här artikeln beskrivs hur du bedömer ett stort antal lokala fysiska servrar för migrering till Azure med hjälp av verktyget Utvärdering av Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) innehåller ett nav med verktyg som hjälper dig att identifiera, bedöma och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Navet innehåller Azure Migrate-verktyg och ISV-erbjudanden (Independent Software Vendor) från tredje part. 


I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för bedömning i stor skala.
> * Konfigurera Azure-behörigheter och förbered fysiska servrar för bedömning.
> * Skapa ett Azure Migrate-projekt och skapa en utvärdering.
> * Granska bedömningen medan du planerar för migrering.


> [!NOTE]
> Om du vill prova ett proof-of-concept för att bedöma ett par servrar innan du bedömer i stor skala, följ vår [handledning serie](tutorial-prepare-physical.md).

## <a name="plan-for-assessment"></a>Plan för bedömning

När du planerar för bedömning av ett stort antal fysiska servrar, det finns ett par saker att tänka på:

- **Planera Azure Migrate-projekt:** Ta reda på hur du distribuerar Azure Migrate-projekt. Om dina datacenter till exempel finns i olika geografiska områden, eller om du behöver lagra identifierings-, bedömnings- eller migreringsrelaterade metadata i en annan geografi, kan du behöva flera projekt.
- **Planinstallationer:** Azure Migrate använder en lokal Azure Migrate-installation, som distribueras på en Windows-dator, för att kontinuerligt identifiera servrar för utvärdering och migrering. Installationen övervakar miljöändringar som att lägga till virtuella datorer, diskar eller nätverkskort. Den skickar också metadata och prestandadata om dem till Azure. Du måste ta reda på hur många enheter som ska distribueras.


## <a name="planning-limits"></a>Planeringsgränser
 
Använd de gränser som sammanfattas i den här tabellen för planering.

**Planering** | **Gränser**
--- | --- 
**Azure Migrera projekt** | Utvärdera upp till 35 000 servrar i ett projekt.
**Azure Migrate-installation** | En apparat kan upptäcka upp till 250 servrar.<br/> En installation kan bara associeras med ett enda Azure Migrate-projekt.<br/> Valfritt antal enheter kan associeras med ett enda Azure Migrate-projekt. <br/><br/> 
**Grupp** | Du kan lägga till upp till 35 000 servrar i en enda grupp.
**Utvärdering av Azure Migrate** | Du kan bedöma upp till 35 000 servrar i en enda bedömning.


## <a name="other-planning-considerations"></a>Andra planeringsöverväganden

- Om du vill starta identifieringen från installationen måste du välja varje fysisk server. 

## <a name="prepare-for-assessment"></a>Förbered för bedömning

Förbered Azure och fysiska servrar för serverutvärdering. 

1. Verifiera [krav och begränsningar för fysisk serversupport](migrate-support-matrix-physical.md).
2. Ställ in behörigheter för ditt Azure-konto för att interagera med Azure Migrate.
3. Förbered de fysiska servrarna.

Konfigurera dessa inställningar genom att följa instruktionerna i den [här självstudien.](tutorial-prepare-physical.md)

## <a name="create-a-project"></a>Skapa ett projekt

Gör följande i enlighet med dina planeringskrav:

1. Skapa ett Azure Migrate-projekt.
2. Lägg till verktyget Utvärdering av Azure Migrate Server i projekten.

[Läs mer](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en bedömning

1. Skapa utvärderingar för fysiska servrar.
1. Granska bedömningarna inför migreringsplanering.

[Läs mer](tutorial-assess-physical.md) om att skapa och granska utvärderingar.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerade att skala Azure Migrate-utvärderingar för fysiska servrar.
> * Förberedd Azure och fysiska servrar för bedömning.
> * Skapade ett Azure Migrate-projekt och körde utvärderingar.
> * Granskade bedömningar inför migrering.

Lär [dig nu hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur [du ändrar utvärderingar](how-to-modify-assessment.md).
