---
title: Vanliga frågor och svar om Azure Migrate
description: Få svar på vanliga frågor om Azure Migrate-tjänsten.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 9b71888b284fd9cc125def4758d1e3800a92acf2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530325"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrera: Vanliga frågor

Den här artikeln besvarar vanliga frågor om Azure Migrate. Om du har frågor när du har läst den här artikeln kan du publicera dem i [Azure Migrate-forumet](https://aka.ms/AzureMigrateForum). Du kan också granska följande artiklar:

- Frågor om [Azure Migrate-enheten](common-questions-appliance.md)
- Frågor om [identifiering, bedömning och beroendevisualisering](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Vad är Azure Migrate?

Azure Migrate är en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbetsbelastningar och privata och offentliga virtuella moln virtuella datorer till Azure. Hubben tillhandahåller Azure Migrate-verktyg för utvärdering och migrering och ISV-erbjudanden från tredje part. [Läs mer](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Vad kan jag göra med Azure Migrate?

Använd Azure Migrate för att identifiera, bedöma och migrera lokal infrastruktur, program och data till Azure. Azure Migrate stöder bedömning och migrering av lokala virtuella datorer med VMware, virtuella datorer med hyper-vm, fysiska servrar, andra virtualiserade virtuella datorer, databaser, webbappar och virtuella skrivbord. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Vad är skillnaden mellan Azure Migrate och Azure Site Recovery?

[Azure Migrate](migrate-services-overview.md) tillhandahåller en centraliserad hubb för utvärdering och migrering till Azure. 

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) är en lösning för haveriberedskap. 

Azure Migrate: ServerMigreringsverktyg använder vissa backend-site recovery-funktioner för lift-and-shift-migrering av vissa lokala datorer.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Vad är skillnaden mellan Azure Migrate: Server Assessment och MAP Toolkit?

Serverutvärdering ger utvärdering för att hjälpa till med migreringsberedskap och utvärdering av arbetsbelastningar för migrering till Azure. [Verktygslådan För Microsoft Assessment and Planning (MAP)](https://www.microsoft.com/download/details.aspx?id=7826) hjälper till med andra uppgifter, inklusive migreringsplanering för nyare versioner av Windows klient- och serveroperativsystem och spårning av programanvändning. I dessa scenarier fortsätter du att använda MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Vad är skillnaden mellan serverutvärdering och site recovery deployment planner?

Serverutvärdering är ett migreringsplaneringsverktyg. Site Recovery Deployment Planner är ett planeringsverktyg för katastrofåterställning.

Välj ditt verktyg baserat på vad du vill göra:

- **Planera lokal migrering till Azure:** Om du planerar att migrera dina lokala servrar till Azure använder du Serverutvärdering för migreringsplanering. Serverbedömning bedömer lokala arbetsbelastningar och ger vägledning och verktyg som hjälper dig att migrera. När migreringsplanen är på plats kan du använda verktyg som Azure Migrate: Servermigrering för att migrera datorerna till Azure.
- **Planera haveriberedskap till Azure:** Om du planerar att konfigurera haveriberedskap från lokalt till Azure med Site Recovery använder du planplaneraren för distribution av platsåterställning. Deployment Planner ger en djup, Site Recovery-specifik bedömning av din lokala miljö i syfte att katastrofåterställning. Den innehåller rekommendationer relaterade till haveriberedskap, till exempel replikering och redundans.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hur fungerar migrering av server med platsåterställning?

- Om du använder Azure Migrate: Servermigrering för att utföra en *agentlös* migrering av lokala virtuella datorer med VMware är migreringen infödd till Azure Migrate och Site Recovery används inte.
- Om du använder Azure Migrate: Servermigrering för att utföra en *agentbaserad* migrering av virtuella datorer med VMware, eller om du migrerar virtuella hyper-V-datorer eller fysiska servrar, använder Azure Migrate: Servermigrering azure site recovery replication-motorn.

## <a name="which-geographies-are-supported"></a>Vilka geografiska områden stöds?

Granska de geografiska områden som stöds för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [statliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-do-i-get-started"></a>Hur kommer jag igång?

Identifiera det verktyg du behöver och lägg sedan till verktyget i ett Azure Migrate-projekt. 

Så här lägger du till ett ISV-verktyg eller Movere:

1. Kom igång genom att skaffa en licens eller registrera dig för en kostnadsfri utvärderingsversion i enlighet med verktygspolicyn. Licensiering av verktyg är i enlighet med ISV- eller verktygslicensieringsmodellen.
2. I varje verktyg finns det ett alternativ för att ansluta till Azure Migrate. Följ verktygsinstruktionerna och dokumentationen för att ansluta verktyget till Azure Migrate.

Du kan spåra din migreringsresa inifrån Azure Migrate-projektet, i Azure och i andra verktyg.

## <a name="how-do-i-delete-a-project"></a>Hur tar jag bort ett projekt?

Läs om hur du [tar bort ett projekt](how-to-delete-project.md). 

## <a name="next-steps"></a>Nästa steg

Läs [översikten över Azure Migrate](migrate-services-overview.md).
