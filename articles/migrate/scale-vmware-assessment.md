---
title: Utvärdera ett stort antal virtuella VMware-datorer för migrering till Azure med Azure Migrate
description: Beskriver hur du bedömer ett stort antal virtuella VMware-datorer för migrering till Azure med hjälp av Azure Migrate service.e
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: d404583b1bad474a5e24e8c7cf060aeb80d610bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336857"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Utvärdera ett stort antal virtuella virtuella datorer för migrering till Azure


I den här artikeln beskrivs hur du bedömer ett stort antal (1000-35 000) lokala virtuella datorer för VMware för migrering till Azure med hjälp av verktyget Azure Migrate Server Assessment.

[Azure Migrate](migrate-services-overview.md) innehåller ett nav med verktyg som hjälper dig att identifiera, bedöma och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Navet innehåller Azure Migrate-verktyg och ISV-erbjudanden (Independent Software Vendor) från tredje part. 

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för bedömning i stor skala.
> * Konfigurera Azure-behörigheter och förbered VMware för utvärdering.
> * Skapa ett Azure Migrate-projekt och skapa en utvärdering.
> * Granska bedömningen medan du planerar för migrering.


> [!NOTE]
> Om du vill prova ett proof-of-concept för att bedöma ett par virtuella datorer innan du bedömer i stor skala, följ vår [handledning serie](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Plan för bedömning

När du planerar för bedömning av ett stort antal virtuella datorer med VMware finns det ett par saker att tänka på:

- **Planera Azure Migrate-projekt:** Ta reda på hur du distribuerar Azure Migrate-projekt. Om dina datacenter till exempel finns i olika geografiska områden, eller om du behöver lagra identifierings-, bedömnings- eller migreringsrelaterade metadata i en annan geografi, kan du behöva flera projekt. 
- **Planinstallationer:** Azure Migrate använder en lokal Azure Migrate-installation, som distribueras som en virtuell virtuell VMware-dator, för att kontinuerligt identifiera virtuella datorer. Installationen övervakar miljöändringar som att lägga till virtuella datorer, diskar eller nätverkskort. Den skickar också metadata och prestandadata om dem till Azure. Du måste ta reda på hur många apparater du behöver distribuera.
- **Planera konton för identifiering:** Azure Migrate-enheten använder ett konto med åtkomst till vCenter Server för att identifiera virtuella datorer för utvärdering och migrering. Om du upptäcker fler än 10 000 virtuella datorer ställer du in flera konton.


## <a name="planning-limits"></a>Planeringsgränser
 
Använd de gränser som sammanfattas i den här tabellen för planering.

**Planering** | **Gränser**
--- | --- 
**Azure Migrera projekt** | Utvärdera upp till 35 000 virtuella datorer i ett projekt.
**Azure Migrate-installation** | En apparat kan identifiera upp till 10 000 virtuella datorer på en vCenter-server.<br/> En apparat kan bara ansluta till en enda vCenter-server.<br/> En installation kan bara associeras med ett enda Azure Migrate-projekt.<br/>  Valfritt antal enheter kan associeras med ett enda Azure Migrate-projekt. <br/><br/> 
**Grupp** | Du kan lägga till upp till 35 000 virtuella datorer i en enda grupp.
**Utvärdering av Azure Migrate** | Du kan bedöma upp till 35 000 virtuella datorer i en enda bedömning.

Med dessa gränser i åtanke, här är några exempel distributioner:


**vCenter-server** | **Virtuella datorer på servern** | **Rekommendation** | **Åtgärd**
---|---|---
En | < 10 000 | Ett Azure Migrate-projekt.<br/> En apparat.<br/> Ett vCenter-konto för identifiering. | Konfigurera apparat, anslut till vCenter Server med ett konto.
En | > 10 000 | Ett Azure Migrate-projekt.<br/> Flera apparater.<br/> Flera vCenter-konton. | Ställ in apparaten för varje 10 000 virtuella datorer.<br/><br/> Ställ in vCenter-konton och dela upp lagret för att begränsa åtkomsten för ett konto till mindre än 10 000 virtuella datorer.<br/> Anslut varje apparat till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan datorer som identifieras med olika enheter.
Flera | < 10 000 |  Ett Azure Migrate-projekt.<br/> Flera apparater.<br/> Ett vCenter-konto för identifiering. | Konfigurera apparater, anslut till vCenter Server med ett konto.<br/> Du kan analysera beroenden mellan datorer som identifieras med olika enheter.
Flera | > 10 000 | Ett Azure Migrate-projekt.<br/> Flera apparater.<br/> Flera vCenter-konton. | Om vCenter Server-identifiering < 10 000 virtuella datorer konfigurerar du en apparat för varje vCenter-server.<br/><br/> Om vCenter Server-identifiering > 10 000 virtuella datorer konfigurerar du en apparat för varje 10 000 virtuella datorer.<br/> Ställ in vCenter-konton och dela upp lagret för att begränsa åtkomsten för ett konto till mindre än 10 000 virtuella datorer.<br/> Anslut varje apparat till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan datorer som identifieras med olika enheter.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planera identifiering i en miljö med flera innehavare

Om du planerar för en miljö med flera innehavare kan du begränsa identifieringen på vCenter Server.

- Du kan ange dataomfånget för identifiering av installationen till ett vCenter Server-datacenter, kluster eller mapp med kluster, värdar eller mapp med värdar eller enskilda virtuella datorer.
- Om din miljö delas mellan klienter och du vill identifiera varje klient separat kan du begränsa åtkomsten till det vCenter-konto som enheten använder för identifiering. 
    - Du kanske vill begränsa efter vm-mappar om klienterna delar värdar. Azure Migrate kan inte identifiera virtuella datorer om vCenter-kontot har åtkomst beviljad på mappnivå för vCenter VM. Om du vill begränsa identifieringen av VM-mappar kan du göra det genom att se till att vCenter-kontot har skrivskyddad åtkomst tilldelad på vm-nivå. [Läs mer](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Förbered för bedömning

Förbered Azure och VMware för serverutvärdering. 

1. Verifiera [VMware supportkrav och begränsningar](migrate-support-matrix-vmware.md).
2. Ställ in behörigheter för ditt Azure-konto för att interagera med Azure Migrate.
3. Förbered VMware för bedömning.

Konfigurera dessa inställningar genom att följa instruktionerna i den [här självstudien.](tutorial-prepare-vmware.md)


## <a name="create-a-project"></a>Skapa ett projekt

Gör följande i enlighet med dina planeringskrav:

1. Skapa ett Azure Migrate-projekt.
2. Lägg till verktyget Utvärdering av Azure Migrate Server i projekten.

[Läs mer](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en bedömning

1. Skapa utvärderingar för virtuella datorer med VMware.
1. Granska bedömningarna inför migreringsplanering.


Konfigurera dessa inställningar genom att följa instruktionerna i den [här självstudien.](tutorial-assess-vmware.md)
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerade att skala Azure Migrate-utvärderingar för virtuella datorer med VMware
> * Förberedd Azure och VMware för bedömning
> * Skapade ett Azure Migrate-projekt och körde utvärderingar
> * Granskade bedömningar inför migrering.

Lär [dig nu hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur [du ändrar utvärderingar](how-to-modify-assessment.md).
