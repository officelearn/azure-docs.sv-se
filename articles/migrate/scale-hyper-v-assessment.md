---
title: Utvärdera stort antal Hyper-V-datorer för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du avgör stort antal Hyper-V-datorer för migrering till Azure med tjänsten Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811549"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Utvärdera stort antal Hyper-V-datorer för migrering till Azure

Den här artikeln beskriver hur du avgör stora mängder (> 1000) med en lokal Hyper-V-datorer för migrering till Azure med verktyget Azure Migrate Server-utvärdering.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en hubb för verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Hubben innehåller Azure Migrate verktyg och erbjudanden för programvara från tredje part för oberoende programvaruleverantörer (ISV). 


I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för utvärdering i stor skala.
> * Konfigurera Azure-behörigheter och förbereda Hyper-V för utvärdering.
> * Skapa ett Azure Migrate-projekt och skapa en utvärdering.
> * Granska utvärderingen när du planerar för migrering.


> [!NOTE]
> Om du vill prova att använda en proof of concept att utvärdera ett par med virtuella datorer innan du utvärdera i stor skala, följa våra [självstudieserien](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>Planera för utvärdering

När du planerar för utvärdering av stort antal Hyper-V-datorer, finns det några saker att tänka på:

- **Planera Azure Migrate-projekt**: Ta reda på hur du distribuerar Azure Migrate-projekt. Till exempel om ditt datacenter finns i olika geografiska områden eller du behöver lagra identifiering, utvärdering eller migreringsrelaterade metadata i ett annat geografiskt område, behöva flera projekt.
- **Planera installationer**: Azure Migrate använder en lokal Azure Migrate-installation, som distribueras som en Hyper-V-dator för att identifiera virtuella datorer kontinuerligt för bedömning och migrering. Installationen övervakar ändringar i miljön, till exempel att lägga till virtuella datorer, diskar och nätverkskort. Den skickar även metadata och prestanda data om dem till Azure. Du måste ta reda på hur många enheter för att distribuera.


## <a name="planning-limits"></a>Planera för gränser
 
Använd de gränser som sammanfattas i den här tabellen för att planera.

**Planering** | **Begränsningar**
--- | --- 
**Azure Migrate-projekt** | Utvärdera upp till 10 000 virtuella datorer i ett projekt.
**Azure Migrate-installation** | En apparat kan identifiera upp till 5 000 virtuella datorer.<br/> En apparat kan ansluta till upp till 300 Hyper-V-värdar.<br/> En apparat kan bara associeras med en enda Azure Migrate-projekt.<br/><br/> 
**Azure Migrate-utvärdering** | Du kan utvärdera upp till 10 000 virtuella datorer i en enda utvärdering.



## <a name="other-planning-considerations"></a>Andra överväganden

- Du måste välja varje Hyper-V-värden för att starta identifieringen av programmet. 
- Om du kör en miljö med flera organisationer, kan du för närvarande identifiera endast virtuella datorer som tillhör en specifik klient. 

## <a name="prepare-for-assessment"></a>Förbereda för utvärdering

Förbered Azure och Hyper-V för server-utvärdering. 

1. Kontrollera [Hyper-V stöd för krav och begränsningar](migrate-support-matrix-hyper-v.md).
2. Konfigurera behörigheter för ditt Azure-konto och interagera med Azure Migrate
3. Förbereda Hyper-V-värdar och virtuella datorer

Följ instruktionerna i [den här självstudien](tutorial-prepare-hyper-v.md) att konfigurera dessa inställningar.

## <a name="create-a-project"></a>Skapa ett projekt

I enlighet med dina planering krav, gör du följande:

1. Skapa ett Azure Migrate-projekt.
2. Lägg till verktyget Azure Migrate Server-utvärdering i projekt.

[Läs mer](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en utvärdering

1. Skapa utvärderingar för Hyper-V-datorer.
1. Granska utvärderingar inför planering av migreringsaktiviteter.

[Läs mer](tutorial-assess-hyper-v.md) om att skapa och granska utvärderingar.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerat att skala Azure Migrate-utvärderingar för Hyper-V-datorer
> * Förberedd Azure och Hyper-V för utvärdering
> * Skapat ett Azure Migrate-projekt och kört utvärderingar
> * Granskat utvärderingar som förberedelse inför migreringen.

Nu kan [Lär dig hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur du [ändra utvärderingar](how-to-modify-assessment.md)
