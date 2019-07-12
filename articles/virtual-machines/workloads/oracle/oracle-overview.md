---
title: Oracle-lösningar på Microsoft Azure | Microsoft Docs
description: Läs mer om alternativ för att distribuera Oracle-program och lösningar på Microsoft Azure, inklusive kör helt och hållet i Azure-infrastruktur eller använder molnöverskridande anslutning med molnet infrastruktur OCI (Oracle).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b8bfa0dfa82f73cad010a608150eac48c7f3d4c8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707455"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Översikt över Oracle-program och lösningar på Azure

Den här artikeln innehåller funktioner för att köra Oracle-lösningar med hjälp av Azure-infrastrukturen. Se även detaljerad introduktioner till tillgängliga [Oracle VM-avbildningar](oracle-vm-solutions.md) i Azure Marketplace och möjlighet att förhandsgranska [sammankoppling Azure med molnet infrastruktur OCI (Oracle)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-databaser på Azure-infrastrukturen

Kör Oracle-databaser på Azure-infrastrukturen med hjälp av Linux-avbildningar på Azure Marketplace:

* Oracle Database 12.1 och 12.2 18,3 tum Enterprise Edition 

* Oracle Database 12.1 och 12.2 18,3 tum Standard Edition 

Du kan också välja att basera en lösning på en anpassad avbildning som du skapar från grunden i Azure eller ladda upp en anpassad avbildning från din lokala miljö.

Du kan också konfigurera med flera anslutna diskar och förbättra databasens prestanda genom att installera Oracle automatiserad Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Program på Oracle Linux- och WebLogic-Server

Kör företagsprogram i Azure på Oracle-operativsystem som stöds. Följande bilder är tillgängliga i Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6,9, 6.10, 7.3, 7.4, 7.5 och 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Hög tillgänglighet och katastrofåterställning återställningsalternativ

* Konfigurera Oracle Data Guard, aktiva Data Guard och GoldenGate på Azure-infrastrukturen tillsammans med [Tillgänglighetszoner](../../../availability-zones/az-overview.md) för hög tillgänglighet.

* Använd [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) att dirigera och hantera haveriberedskap för din Oracle Linux-datorer i Azure och din lokala eller fysiska servrar. 

* Aktivera Oracle Real Application kluster (RAC) i Azure med hjälp av [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integrering av Azure med OCI (förhandsversion)

Kör Oracle-program i Azure-infrastrukturen ansluten till backend-databaser i molnet infrastruktur OCI (Oracle). Den här lösningen använder följande funktioner: 

* **Nätverk mellan moln** -användning direkt sammankoppling mellan Azure ExpressRoute och Oracle FastConnect för att upprätta anslutningar för snabba, privata och låg latens mellan programmet och databasen lagret.
* **Integrerad identitet** -konfigurera federerad identitet mellan Azure AD och Oracle IDCS att skapa en enskild identitet som källa för lösningarna. Aktivera enkel inloggning att hantera resurser i OCI och Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Distribuera Oracle-program i Azure

Använd Terraform mallar för att konfigurera Azure-infrastrukturen och installera Oracle-program verifieras och stöds körs i molnet-konfiguration:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail-program
* Oracle Hyperion finansiell hantering

Distribuera anpassade program i Azure som ansluter med OCI och andra Azure-tjänster.

### <a name="set-up-oracle-databases-in-oci"></a>Konfigurera Oracle-databaser i OCI

Använda molntjänster i Oracle Database (autonoma databasen, RAC, Exadata, DBaaS, nod) tillsammans med Oracle-program som körs i Azure. Läs mer om [OCI databasalternativ](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licensiering

Distribution av Oracle-program i Azure baseras på en modell för ”ta med din egen licens”. Det förutsätts att du är korrekt licensierade om du vill använda Oracle-programvara och att du har en aktuell support-avtal med Oracle. Oracle har garanterat licensmobilitet från en lokal plats till Azure. Se hur Oracle-Azure [vanliga frågor och svar](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du distribuerar [Oracle VM-avbildningar](oracle-vm-solutions.md) i Azure-infrastrukturen.

* Mer information om hur du [sammankoppling Azure med OCI](oracle-oci-overview.md).