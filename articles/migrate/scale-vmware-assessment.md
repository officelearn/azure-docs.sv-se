---
title: Utvärdera stort antal virtuella VMware-datorer för migrering till Azure med Azure Migrate | Microsoft Docs
description: Beskriver hur du avgör stort antal virtuella VMware-datorer för migrering till Azure med tjänsten Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811341"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Utvärdera stort antal virtuella VMware-datorer för migrering till Azure


Den här artikeln beskrivs hur du avgör stort (1000-35 000) för lokala virtuella VMware-datorer för migrering till Azure med verktyget Azure Migrate Server-utvärdering

[Azure Migrate](migrate-services-overview.md) tillhandahåller en hubb för verktyg som hjälper dig att identifiera, utvärdera och migrera appar, infrastruktur och arbetsbelastningar till Microsoft Azure. Hubben innehåller Azure Migrate verktyg och erbjudanden för programvara från tredje part för oberoende programvaruleverantörer (ISV). 

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Planera för utvärdering i stor skala.
> * Konfigurera Azure-behörigheter och förbereda VMware för utvärdering.
> * Skapa ett Azure Migrate-projekt och skapa en utvärdering.
> * Granska utvärderingen när du planerar för migrering.


> [!NOTE]
> Om du vill prova att använda en proof of concept att utvärdera ett par med virtuella datorer innan du utvärdera i stor skala, följa våra [självstudieserien](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>Planera för utvärdering

När du planerar för utvärdering av stort antal virtuella VMware-datorer, finns det några saker att tänka på:

- **Planera Azure Migrate-projekt**: Ta reda på hur du distribuerar Azure Migrate-projekt. Till exempel om ditt datacenter finns i olika geografiska områden eller du behöver lagra identifiering, utvärdering eller migreringsrelaterade metadata i ett annat geografiskt område, behöva flera projekt. 
- **Planera installationer**: Azure Migrate använder en lokal Azure Migrate-installation, som distribueras som en VMware VM att kontinuerligt identifiera virtuella datorer. Installationen övervakar ändringar i miljön, till exempel att lägga till virtuella datorer, diskar och nätverkskort. Den skickar även metadata och prestanda data om dem till Azure. Du måste ta reda på hur många enheter som du behöver distribuera.
- **Planera konton för identifiering av**: Azure Migrate-installationen använder ett konto med åtkomst till vCenter-servern för att kunna identifiera virtuella datorer för bedömning och migrering. Om du identifiering av över 10 000 virtuella datorer, skapa flera konton.


## <a name="planning-limits"></a>Planera för gränser
 
Använd de gränser som sammanfattas i den här tabellen för att planera.

**Planering** | **Begränsningar**
--- | --- 
**Azure Migrate-projekt** | Utvärdera upp till 35 000 virtuella datorer i ett projekt.
**Azure Migrate-installation** | En apparat kan bara ansluta till en enda vCenter-Server.<br/><br/> En apparat kan bara associeras med en enda Azure Migrate-projekt.<br/> En apparat kan identifiera upp till 10 000 virtuella datorer på en vCenter-Server.
**Azure Migrate-utvärdering** | Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.

Här följer vissa exempeldistributioner med dessa gränser i åtanke:


**vCenter-server** | **Virtuella datorer på servern** | **Rekommendationen** | **Åtgärd**
---|---|---
En | < 10,000 | Ett Azure Migrate-projekt.<br/> En installation.<br/> En vCenter-konto för identifiering. | Konfigurera enheten, ansluta till vCenter-servern med ett konto.
En | > 10,000 | Ett Azure Migrate-projekt.<br/> Flera installationer.<br/> Flera vCenter-konton. | Konfigurera enheten för varje 10 000 virtuella datorer.<br/><br/> Ställ in vCenter och dela in inventering för att begränsa åtkomsten för ett konto på mindre än 10 000 virtuella datorer.<br/> Ansluta varje enhet till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan datorer som identifieras med olika enheter.
Flera | < 10,000 |  Ett Azure Migrate-projekt.<br/> Flera installationer.<br/> En vCenter-konto för identifiering. | Konfigurera enheter, ansluta till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan datorer som identifieras med olika enheter.
Flera | > 10,000 | Ett Azure Migrate-projekt.<br/> Flera installationer.<br/> Flera vCenter-konton. | Om identifiering av vCenter Server < 10 000 virtuella datorer, ställa in en tillämpning för varje vCenter-servern.<br/><br/> Om identifiering av vCenter Server > 10 000 virtuella datorer, konfigurera en installation för varje 10 000 virtuella datorer.<br/> Ställ in vCenter och dela in inventering för att begränsa åtkomsten för ett konto på mindre än 10 000 virtuella datorer.<br/> Ansluta varje enhet till vCenter-servern med ett konto.<br/> Du kan analysera beroenden mellan datorer som identifieras med olika enheter.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planera identifiering i en miljö med flera innehavare

Om du planerar för en miljö med flera organisationer, kan du begränsa identifiering på vCenter-servern.

- Du kan ange identifieringsomfånget installation till en vCenter Server datacenter, kluster eller mapp för kluster, värd eller mappen för värdar eller enskilda virtuella datorer.
- Om miljön delas mellan klienter och du inte vill identifiera varje klient separat, kan du begränsa åtkomst till vCenter-kontot som installationen använder för identifiering. 
    - Om klienterna delar värdar kan du skapa autentiseringsuppgifter med skrivskyddad åtkomst för de virtuella datorerna som hör till specifik klient. 
    - Använd dessa autentiseringsuppgifter för identifiering av Azure Migrate installation.
    - Azure Migrate-utvärdering kan inte identifiera virtuella datorer om vCenter-konto har åtkomst beviljas på mappnivå vCenter VM. Mappar av värdar och kluster stöds. 

## <a name="prepare-for-assessment"></a>Förbereda för utvärdering

Förbered Azure och VMware för server-utvärdering. 

1. Kontrollera [VMware stöd för krav och begränsningar](migrate-support-matrix-vmware.md).
2. Ställ in behörigheter för ditt Azure-konto och interagera med Azure Migrate.
3. Förbereda VMware för utvärdering.


Följ instruktionerna i [den här självstudien](tutorial-prepare-vmware.md) att konfigurera dessa inställningar.


## <a name="create-a-project"></a>Skapa ett projekt

I enlighet med dina planering krav, gör du följande:

1. Skapa ett Azure Migrate-projekt.
2. Lägg till verktyget Azure Migrate Server-utvärdering i projekt.

[Läs mer](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Skapa och granska en utvärdering

1. Skapa utvärderingar för virtuella VMware-datorer.
1. Granska utvärderingar inför planering av migreringsaktiviteter.


Följ instruktionerna i [den här självstudien](tutorial-assess-vmware.md) att konfigurera dessa inställningar.
    

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller följande avsnitt:
 
> [!div class="checklist"] 
> * Planerat att skala Azure Migrate-utvärderingar för virtuella VMware-datorer
> * Förberedd Azure och VMware för utvärdering
> * Skapat ett Azure Migrate-projekt och kört utvärderingar
> * Granskat utvärderingar som förberedelse inför migreringen.

Nu kan [Lär dig hur](concepts-assessment-calculation.md) utvärderingar beräknas och hur du [ändra utvärderingar](how-to-modify-assessment.md).
