---
title: Oracle-lösningar på Microsoft Azure | Microsoft Docs
description: Lär dig mer om alternativ för att distribuera Oracle-program och-lösningar på Microsoft Azure, inklusive att köra helt i Azure-infrastrukturen eller med hjälp av anslutningar mellan moln med OCI (Oracle Cloud Infrastructure).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: e9e37c54668ec0343cbfd45e51e90216955b46c4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100011"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Översikt över Oracle-program och Azure-lösningar på Azure

Den här artikeln beskriver funktioner för att köra Oracle-lösningar med Azure-infrastruktur. Se även detaljerade introduktioner till tillgängliga [Oracle VM](oracle-vm-solutions.md) -avbildningar på Azure Marketplace och för hands versions möjligheten att sammanföra [Azure med Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-databaser på Azure-infrastruktur

Kör Oracle-databaser på Azure-infrastruktur med Linux-avbildningar som är tillgängliga på Azure Marketplace:

* Oracle Database 12,1, 12,2 och 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 och 18,3 Standard Edition 

Du kan också välja att basera en lösning på en anpassad avbildning som du skapar från grunden i Azure eller ladda upp en anpassad avbildning från din lokala miljö.

Du kan också konfigurera med flera anslutna diskar och förbättra databasens prestanda genom att installera ASM (automatisk lagrings hantering i Oracle).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Program på Oracle Linux-och WebLogic-Server

Kör företags program i Azure på Oracle-operativsystem som stöds. Följande avbildningar är tillgängliga på Azure Marketplace:

* 12.1.2 för Oracle WebLogic-Server

* Oracle Linux (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 och 7,6

## <a name="high-availability-and-disaster-recovery-options"></a>Alternativ för hög tillgänglighet och haveri beredskap

* Konfigurera Oracle data Guard, Active data Guard eller GoldenGate på Azure-infrastrukturen tillsammans med [Tillgänglighetszoner](../../../availability-zones/az-overview.md) för hög tillgänglighet.

* Använd [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) för att dirigera och hantera haveri beredskap för dina Oracle Linux virtuella datorer i Azure och dina lokala eller fysiska servrar. 

* Aktivera Oracle Real Application Clusters (RAC) i Azure med hjälp av [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integrering av Azure med OCI (för hands version)

Köra Oracle-program i Azure-infrastrukturen, anslutna till backend-databaser i Oracle Cloud Infrastructure (OCI). Den här lösningen använder följande funktioner: 

* **Nätverk över flera moln** – Använd den direkta sammanlänkningen som är tillgänglig mellan Azure ExpressRoute och Oracle FastConnect för att upprätta anslutningar med hög bandbredd, privat och låg latens mellan programmet och databas skiktet.
* **Integrerad identitet** – konfigurera federerade identiteter mellan Azure AD och Oracle IDCS för att skapa en enda identitets källa för lösningarna. Aktivera enkel inloggning för att hantera resurser i OCI och Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Distribuera Oracle-program på Azure

Använd terraform-mallar för att ställa in Azure-infrastrukturen och installera Oracle-program som verifierats och stöds för att köras i konfigurationen mellan molnet:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle återförsäljarversion-program
* Ekonomisk hantering för Oracle Hyperion

Distribuera även anpassade program i Azure som ansluter till OCI och andra Azure-tjänster.

### <a name="set-up-oracle-databases-in-oci"></a>Konfigurera Oracle-databaser i OCI

Använd Oracle Database Cloud Services (autonom databas, RAC, Exadata, DBaaS, enskild nod) tillsammans med Oracle-program som körs i Azure. Läs mer om [OCI-databas alternativ](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licenser

Distribution av Oracle-program i Azure baseras på en "ta med din egen licens"-modell. Vi förutsätter att du är korrekt licensierad till att använda Oracle-programvara och att du har ett aktuellt support avtal på plats med Oracle. Oracle har garanterat licens mobilitet från lokal plats till Azure. Se [vanliga frågor och svar om](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)Oracle – Azure.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att distribuera [VM](oracle-vm-solutions.md) -avbildningar i Azure-infrastrukturen.

* Lär dig mer om att ansluta [Azure med OCI](oracle-oci-overview.md).