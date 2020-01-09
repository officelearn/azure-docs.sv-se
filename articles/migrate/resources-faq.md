---
title: Vanliga frågor om Azure Migrate
description: Få svar på vanliga frågor om tjänsten Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: 8bfa9237d365636c0bdaa3af06c5af23b683231d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563552"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: vanliga frågor

I den här artikeln besvaras vanliga frågor om Azure Migrate. Om du har fler frågor efter att ha läst den här artikeln kan du publicera dem i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum). Om du har andra frågor kan du läsa följande artiklar:

- [Frågor](common-questions-appliance.md) om Azure Migrate-enheten.
- [Frågor](common-questions-discovery-assessment.md) om identifiering, utvärdering och visualisering av beroenden.


## <a name="what-is-azure-migrate"></a>Vad är Azure Migrate?

Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata/offentliga virtuella moln datorer till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV). [Läs mer](migrate-services-overview.md).


## <a name="what-can-i-do-with-azure-migrate"></a>Vad kan jag göra med Azure Migrate?

Använd Azure Migrate för att identifiera, utvärdera och migrera lokal infrastruktur, program och data till Azure. Azure Migrate stöder utvärdering och migrering av lokala virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, andra virtualiserade virtuella datorer, databaser, webb program och virtuella skriv bord. 

## <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Vad är skillnaden mellan Azure Migrate och Site Recovery?

Azure Migrate tillhandahåller en centraliserad hubb för utvärdering och migrering till Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) är en katastrof återställnings lösning. Verktyget Azure Migrate: Migreringsverktyg använder vissa Server dels Site Recovery funktioner för att lyfta och flytta migreringen av vissa lokala datorer.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Vad är skillnaden mellan Azure Migrate Server utvärdering och kart verktygen?

Server utvärdering ger en utvärdering för att hjälpa till med migreringen och utvärdering av arbets belastningar för migrering till Azure. [Verktyget Microsoft Assessment and Planning (kart)](https://www.microsoft.com/download/details.aspx?id=7826) hjälper till med andra uppgifter, inklusive migrerings planering för nyare versioner av Windows-klient/server-operativsystem och spårning av program varu användning. Fortsätt att använda kart verktyget för dessa scenarier.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Vad är skillnaden mellan server utvärdering och Site Recovery distributions planeraren?

Server utvärdering är ett verktyg för migrerings planering. Site Recovery Deployment Planner är ett planerings verktyg för haveri beredskap.

- **Planera lokal migrering till Azure**: om du planerar att migrera dina lokala servrar till Azure använder du Server bedömning för migrering. Den bedömer lokala arbets belastningar och ger vägledning och verktyg som hjälper dig att migrera. När migrerings planen är på plats kan du använda verktyg, inklusive Azure Migrate Server-migrering, för att migrera datorerna till Azure.
- **Planera haveri beredskap till Azure**: om du planerar att konfigurera haveri beredskap från en lokal plats till Azure med Site Recovery använder du Site Recovery Deployment Planner. Distributions planeraren ger en djup, Site Recovery-bestämd utvärdering av din lokala miljö för katastrof återställning. Den ger rekommendationer kring haveri beredskap, till exempel replikering och redundans.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hur fungerar server migreringen med Site Recovery?

- Om du använder Azure Migrate: Server migrering för att utföra en agent lös migrering av lokala virtuella VMware-datorer, är migreringen intern för Azure Migrate och Site Recovery inte används.
- Om du använder Azure Migrate: Server-migrering för att utföra en agent-baserad migrering av virtuella VMware-datorer, eller migrera virtuella Hyper-V-datorer eller fysiska servrar, använder Azure Migrate Server-migreringen Azure Site Recovery-replikerings motor.


## <a name="which-geographies-are-supported"></a>Vilka geografiska områden stöds?

Granska Azure Migrate stödda geografiska områden för [virtuella VMware-datorer](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) och för [virtuella Hyper-V-datorer](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Du identifierar det verktyg som du behöver och lägger till det i ett Azure Migrate-projekt. Om du lägger till ett ISV-verktyg eller en driv kraft:
- Kom igång genom att skaffa en licens eller registrera dig för en kostnads fri utvärderings version, i enlighet med verktygs principen. Licensiering för verktyg är i enlighet med ISV-eller-verktygets licensierings modell.
- I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Följ anvisningarna och dokumentationen för verktyget för att ansluta verktyget med Azure Migrate.
Du kan centralt spåra din migrerings resa från Azure Migrate-projektet, via Azure och andra verktyg.

### <a name="how-do-i-delete-a-project"></a>Hur gör jag för att ta bort ett projekt?

[Lär dig hur du](how-to-delete-project.md) tar bort ett projekt. 






## <a name="next-steps"></a>Nästa steg
Läs [Azure Migrate översikt](migrate-services-overview.md).
