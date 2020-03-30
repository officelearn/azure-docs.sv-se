---
title: Utvärdera ett stort antal virtuella hyper-virtuella datorer för migrering till Azure med Azure Migrate | Microsoft-dokument
description: Beskriver hur du bedömer ett stort antal virtuella hyper-virtuella datorer för migrering till Azure med hjälp av Azure Migrate-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: c1ae3a9ed8a775161aaf85ab2c91b1e43113d2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70279444"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Utvärdera ett stort antal virtuella hyper-virtuella datorer för migrering till Azure

I den här artikeln beskrivs hur du bedömer ett stort antal lokala virtuella hyper-virtuella datorer för migrering till Azure med hjälp av verktyget Utvärdering av Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) innehåller ett nav med verktyg som hjälper dig att identifiera, bedöma och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Navet innehåller Azure Migrate-verktyg och ISV-erbjudanden (Independent Software Vendor) från tredje part. 


I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för bedömning i stor skala.
> * Konfigurera Azure-behörigheter och förbered Hyper-V för utvärdering.
> * Skapa ett Azure Migrate-projekt och skapa en utvärdering.
> * Granska bedömningen medan du planerar för migrering.


> [!NOTE]
> Om du vill prova ett proof-of-concept för att bedöma ett par virtuella datorer innan du bedömer i stor skala, följ vår [handledning serie](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Plan för bedömning

När du planerar för bedömning av ett stort antal virtuella hyper-virtuella datorer finns det ett par saker att tänka på:

- **Planera Azure Migrate-projekt:** Ta reda på hur du distribuerar Azure Migrate-projekt. Om dina datacenter till exempel finns i olika geografiska områden, eller om du behöver lagra identifierings-, bedömnings- eller migreringsrelaterade metadata i en annan geografi, kan du behöva flera projekt.
- **Planinstallationer:** Azure Migrate använder en lokal Azure Migrate-installation, som distribueras som en virtuell hyper-V-dator, för att kontinuerligt identifiera virtuella datorer för utvärdering och migrering. Installationen övervakar miljöändringar som att lägga till virtuella datorer, diskar eller nätverkskort. Den skickar också metadata och prestandadata om dem till Azure. Du måste ta reda på hur många enheter som ska distribueras.


## <a name="planning-limits"></a>Planeringsgränser
 
Använd de gränser som sammanfattas i den här tabellen för planering.

**Planering** | **Gränser**
--- | --- 
**Azure Migrera projekt** | Utvärdera upp till 35 000 virtuella datorer i ett projekt.
**Azure Migrate-installation** | En apparat kan upptäcka upp till 5000 virtuella datorer.<br/> En apparat kan ansluta till upp till 300 Hyper-V-värdar.<br/> En installation kan bara associeras med ett enda Azure Migrate-projekt.<br/> Valfritt antal enheter kan associeras med ett enda Azure Migrate-projekt. <br/><br/> 
**Grupp** | Du kan lägga till upp till 35 000 virtuella datorer i en enda grupp.
**Utvärdering av Azure Migrate** | Du kan bedöma upp till 35 000 virtuella datorer i en enda bedömning.



## <a name="other-planning-considerations"></a>Andra planeringsöverväganden

- Om du vill starta identifieringen från installationen måste du välja varje Hyper-V-värd. 
- Om du kör en miljö med flera innehavare kan du för närvarande inte bara identifiera virtuella datorer som tillhör en viss klientorganisation. 

## <a name="prepare-for-assessment"></a>Förbered för bedömning

Förbered Azure och Hyper-V för serverutvärdering. 

1. Verifiera [hyper-V-supportkrav och begränsningar](migrate-support-matrix-hyper-v.md).
2. Konfigurera behörigheter för ditt Azure-konto för att interagera med Azure Migrate
3. Förbereda Hyper-V-värdar och virtuella datorer

Konfigurera dessa inställningar genom att följa instruktionerna i den [här självstudien.](tutorial-prepare-hyper-v.md)

## <a name="create-a-project"></a>Skapa ett projekt

Gör följande i enlighet med dina planeringskrav:

1. Skapa ett Azure Migrate-projekt.
2. Lägg till verktyget Utvärdering av Azure Migrate Server i projekten.

[Läs mer](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en bedömning

1. Skapa utvärderingar för virtuella hyper-virtuella datorer.
1. Granska bedömningarna inför migreringsplanering.

[Läs mer](tutorial-assess-hyper-v.md) om att skapa och granska utvärderingar.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerade att skala Azure Migrate-utvärderingar för virtuella hyper-virtuella datorer
> * Förberedd Azure och Hyper-V för bedömning
> * Skapade ett Azure Migrate-projekt och körde utvärderingar
> * Granskade bedömningar inför migrering.

Nu [kan du lära dig hur](concepts-assessment-calculation.md) bedömningar beräknas och hur du [ändrar bedömningar](how-to-modify-assessment.md)
