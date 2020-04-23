---
title: Oracle-lösningar på Microsoft Azure | Microsoft-dokument
description: Lär dig mer om alternativ för att distribuera Oracle-program och lösningar på Microsoft Azure, inklusive att köra helt på Azure-infrastruktur eller använda molnöverskridande anslutning med Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.openlocfilehash: c737189650d571fb62a770707e84ed15c5a37a57
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870504"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Översikt över Oracle-program och lösningar på Azure

Den här artikeln introducerar funktioner för att köra Oracle-lösningar med Azure-infrastruktur. Se även detaljerade introduktioner till tillgängliga [Oracle VM-avbildningar](oracle-vm-solutions.md) på Azure Marketplace och möjligheten att [koppla samman Azure med Oracle Cloud Infrastructure (OCI).](oracle-oci-overview.md)

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-databaser på Azure-infrastruktur

Kör Oracle-databaser på Azure-infrastruktur med Oracle Database på Oracle Linux-avbildningar som är tillgängliga på Azure Marketplace:

* Oracle Database 12.1, 12.2 och 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 och 18.3 Standard Edition 

Du kan också välja att konfigurera Oracle Database på en linuxavbildning som inte är Oracle tillgänglig i Azure, basera en lösning på en anpassad avbildning som du skapar från grunden i Azure eller ladda upp en anpassad avbildning från din lokala miljö.

Konfigurera eventuellt med flera anslutna diskar och förbättra databasprestanda genom att installera Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Program på Oracle Linux och WebLogic Server

Kör företagsprogram i Azure på Oracle-operativsystem som stöds. Följande avbildningar är tillgängliga på Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux med Unbreakable Enterprise Kernel (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 och 7,6 

## <a name="high-availability-and-disaster-recovery-options"></a>Alternativ för hög tillgänglighet och katastrofåterställning

* Konfigurera [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), Active Data Guard med [FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) eller [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) på Azure-infrastruktur i samband med [tillgänglighetszoner](../../../availability-zones/az-overview.md) för hög tillgänglighet i regionen. Du kan också ställa in dessa konfigurationer i flera Azure-regioner för extra tillgänglighet och haveriberedskap.

* Använd [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) för att dirigera och hantera haveriberedskap för dina virtuella Oracle Linux-datorer i Azure och dina lokala eller fysiska servrar. 

* Aktivera RAC (Oracle Real Application Clusters) i Azure med [Azure VMWare Solution](https://docs.microsoft.com/azure/vmware-cloudsimple/oracle-real-application-clusters/) eller [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Arbetsbelastningar för Oracle säkerhetskopieras

* Säkerhetskopiera dina virtuella Oracle-datorer med [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)

* Säkerhetskopiera din Oracle-databas med Oracle RMAN och använd eventuellt [Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) för att montera ett [mycket röddraget Azure Blob Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-redundancy) och skriva dina RMAN-säkerhetskopior till den för extra återhämtning.

## <a name="integration-of-azure-with-oci"></a>Integrering av Azure med OCI

Kör Oracle-program i Azure-infrastruktur, anslutna till backend-databaser i Oracle Cloud Infrastructure (OCI). Den här lösningen använder följande funktioner: 

* **Nätverk över flera moln** – Använd den direkta sammankopplingen som är tillgänglig mellan Azure ExpressRoute och Oracle FastConnect för att upprätta anslutningar med hög bandbredd, privat och låg latens mellan programmet och databaslagret.
* **Integrerad identitet** – Konfigurera federerad identitet mellan Azure AD och Oracle IDCS för att skapa en enda identitetskälla för lösningarna. Aktivera enkel inloggning för att hantera resurser i OCI och Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Distribuera Oracle-program på Azure

Använd Terraform-mallar för att konfigurera Azure-infrastruktur och installera Oracle-program. 

> [!IMPORTANT]
> Oracle kommer att certifiera dessa program för att köras i Azure när du använder Azure / Oracle Cloud sammanlänkningslösningen senast i maj 2020.

* Svit E-Business
* JD Edwards EnterpriseOne
* Peoplesoft
* Oracle Retail-program
* Oracle Hyperion ekonomisk förvaltning

Distribuera även anpassade program i Azure som ansluter till OCI och andra Azure-tjänster.

### <a name="set-up-oracle-databases-in-oci"></a>Konfigurera Oracle-databaser i OCI

Använd Oracle Database Cloud Services (Autonom databas, RAC, Exadata, DBaaS, Single Node) tillsammans med Oracle-program som körs i Azure. Läs mer om [OCI-databasalternativ](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licensiering

Distribution av Oracle-program i Azure baseras på en "bring your own license"-modell. Det förutsätts att du är korrekt licensierad att använda Oracle-programvara och att du har ett aktuellt supportavtal på plats med Oracle. Oracle har garanterad licensmobilitet från lokalt till Azure. Se [vanliga frågor och svar om](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)Oracle-Azure .

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du distribuerar [avbildningar av Oracle-virtuella datorer](oracle-vm-solutions.md) i Azure-infrastruktur.

* Läs mer om hur du [kopplar samman Azure med OCI](oracle-oci-overview.md).

* Kolla in [Översiktssessionen oracle på Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) från Ignite 2019. 
