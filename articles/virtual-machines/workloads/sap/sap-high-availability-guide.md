---
title: "Azure virtuella datorer hög tillgänglighet för SAP NetWeaver | Microsoft Docs"
description: "Hög tillgänglighet guide för SAP NetWeaver på Azure Virtual Machines"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d00db895ffcf9ba9a51e3df2dae5d33c0277dd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Azure virtuella datorer hög tillgänglighet för SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md



Azure Virtual Machines är lösningen för organisationer som behöver bearbetning, lagring och nätverksresurser, minimalt, och utan långa inköp cykler. Du kan använda Azure virtuella datorer för att distribuera klassiska program som SAP NetWeaver-baserade ABAP, Java och en ABAP + Java-stacken. Utöka tillförlitlighet och tillgänglighet utan ytterligare lokala resurser. Virtuella Azure-datorer stöder korsanslutningar, så du kan integrera Azure virtuella datorer i din organisations lokala domäner, privata moln och SAP system liggande.

I den här artikeln beskriver vi de steg som du kan vidta för att distribuera hög tillgänglighet SAP-system i Azure med hjälp av Azure Resource Manager-distributionsmodellen. Vi går du igenom dessa viktiga uppgifter:

* Hitta rätt SAP-information och riktlinjer för installation, som anges i den [resurser] [ sap-ha-guide-2] avsnitt. Den här artikeln kompletterar SAP dokumentationen och SAP anteckningar, vilka är de primära resurser som hjälper dig att installera och distribuera program på vissa plattformar.
* Lär dig skillnaderna mellan Azure Resource Manager-distributionsmodellen och Azure klassiska distributionsmodellen.
* Läs mer om Windows Server Failover Clustering kvorum läge, så du kan välja den modell som passar din Azure-distribution.
* Läs mer om Windows Server Failover Clustering delad lagring i Azure-tjänster.
* Lär dig hur du kan skydda en punkt i fel komponenter som avancerade Business Application Programming (ABAP) SAP centrala tjänster (ASCS) / SAP centrala Services (SCS) och databashanteringssystem (DBMS) och redundanta komponenter som SAP Programserver i Azure.
* Följ ett steg för steg-exempel på en installation och konfiguration av en SAP-systemet för hög tillgänglighet i ett redundanskluster i Windows Server-kluster i Azure med hjälp av Azure Resource Manager.
* Mer information om ytterligare steg som krävs för att använda Windows Server Failover Clustering i Azure, men som inte behövs i en lokal distribution.

Vi använder SAP tre skikt hög tillgänglighet Resource Manager-mallar för att förenkla distribution och konfiguration i den här artikeln. Mallarna automatisera distributionen av hela infrastrukturen som du behöver för ett SAP-system med hög tillgänglighet. Infrastrukturen stöder också SAP programmets prestanda Standard (SAP) storleksändring av SAP-system.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>Nödvändiga komponenter
Innan du börjar bör du kontrollera att du uppfyller kraven som beskrivs i följande avsnitt. Dessutom måste du kontrollera att alla resurser som finns i den [resurser] [ sap-ha-guide-2] avsnitt.

I den här artikeln använder vi Azure Resource Manager-mallar för [trelagers-SAP NetWeaver med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). En bra översikt över mallar finns [SAP Azure Resource Manager-mallar](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>Resurser
Dessa artiklar beskriver SAP-distributioner i Azure:

* [Azure virtuella datorer planering och implementering för SAP NetWeaver][planning-guide]
* [Distribution av Azure virtuella datorer för SAP NetWeaver][deployment-guide]
* [Azure virtuella datorer DBMS-distribution för SAP NetWeaver][dbms-guide]
* [Azure virtuella datorer hög tillgänglighet för SAP NetWeaver (den här guiden)][sap-ha-guide]

> [!NOTE]
> När det är möjligt, får du en länk till den refererande SAP-installationsguiden (finns i [SAP installationen guider][sap-installation-guides]). Det är en bra idé att läsa SAP NetWeaver installationen guider noggrant krav och information om installationen. Den här artikeln beskriver endast specifika uppgifter för SAP NetWeaver-baserade datorer som du kan använda med Azure Virtual Machines.
>
>

Anteckningarna SAP är relaterade till SAP i Azure-avsnitt:

| Nummer | Rubrik |
| --- | --- |
| [1928533] |SAP-program i Azure: produkter och storlek som stöds |
| [2015553] |SAP på Microsoft Azure: stöd för krav |
| [1999351] |Förbättrad Azure övervakning för SAP |
| [2178632] |Key som övervakning mått för SAP på Microsoft Azure |
| [1999351] |Virtualisering på Windows: förbättrad övervakning |
| [2243692] |Användning av Azure Premium SSD-lagring för SAP DBMS-instans |

Lär dig mer om den [begränsningar i Azure-prenumerationer][azure-subscription-service-limits-subscription], inklusive allmänna standardbegränsningar och maximala begränsningar.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Hög tillgänglighet SAP med Azure Resource Manager kontra Azure klassiska distributionsmodellen
Azure Resource Manager och Azure klassiska distributionsmodeller skiljer sig inom följande områden:

- Resursgrupper
- Azure interna belastningen belastningsutjämnare beroende på Azure-resursgrupp
- Stöd för scenarier för SAP multi-SID

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a>Resursgrupper
Azure Resource Manager du använda resursgrupper för att hantera alla programresurser i din Azure-prenumeration. En integrerad lösning i en resursgrupp, alla resurser som har samma livscykel. Till exempel alla resurser skapas på samma gång och de tas bort samtidigt. Läs mer om [resursgrupper](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Azure interna belastningen belastningsutjämnare beroende på Azure-resursgrupp

I Azure klassiska distributionsmodellen finns det ett beroende mellan Azure intern belastningsutjämnare (Azure belastningsutjämnare service) och Molntjänsten. Varje intern belastningsutjämnare måste en tjänst i molnet.

Azure Resource Manager varje Azure-resurs måste placeras i en Azure-resursgrupp och detta är giltigt för Azure belastningsutjämnare samt. Dock behöver inte ha en Azure-resursgrupp per Azure belastningsutjämnare, t.ex. en Azure-resursgrupp kan innehålla flera Azure belastningsutjämnare. Miljön är enklare och mer flexibelt. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Stöd för scenarier för SAP multi-SID

Du kan installera flera SAP system-ID (SID) ASCS/SCS instanser i ett kluster i Azure Resource Manager. Multi-SID-instanserna är möjligt tack vare stöd för flera IP-adresser för varje Azure interna belastningsutjämnare.

Om du vill använda Azure klassiska distributionsmodellen, följer du procedurerna som beskrivs i [SAP NetWeaver i Azure: Clustering SAP ASCS/SCS instanser med hjälp av Windows Server Failover Clustering i Azure med SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder Azure Resource Manager-distributionsmodellen för SAP-installationer. Den erbjuder många fördelar som inte är tillgängliga i den klassiska distributionsmodellen. Lär dig mer om Azure [distributionsmodeller][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>Windows Server Failover-kluster
Windows Server Failover Clustering är grunden för en hög tillgänglighet SAP ASCS/SCS installation och DBMS i Windows.

Ett redundanskluster är en grupp 1 + n oberoende servrar (noder) som arbetar tillsammans för att öka tillgängligheten för program och tjänster. Om det inträffar ett nodfel, beräknar Windows Server Failover Clustering antalet fel som kan uppstå samtidigt som ett felfritt kluster för att tillhandahålla program och tjänster. Du kan välja från olika kvorumlägen att uppnå failover-kluster.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>Kvorumlägen
Du kan välja mellan fyra kvorumlägen när du använder Windows Server Failover Clustering:

* **Nodmajoritet**. Varje nod i klustret kan rösta. Klustret fungerar bara med en majoritet av röster, det vill säga med mer än hälften röster. Vi rekommenderar att det här alternativet för kluster som har ett ojämnt antal noder. Till exempel tre noder i ett kluster med sju noder kan misslyckas och klustret stillbilder uppnår en majoritet och fortsätter att köras.  
* **Nod- och**. Varje nod och en angiven disk (ett diskvittne) i klusterlagringen kan rösta när de är tillgängliga och kommunikation. Klustret fungerar bara med en majoritet av röster, det vill säga med mer än hälften röster. Det här läget är meningsfullt i en klustermiljö med ett jämnt antal noder. Om hälften av noderna och disken är online kan förblir klustret i felfritt tillstånd.
* **Nod- och filresurs majoritet**. Varje nod plus en avsedda filresurs (ett filresursvittne) som skapar administratören kan rösta, oavsett om noderna och filresursen är tillgänglig och kommunikation. Klustret fungerar bara med en majoritet av röster, det vill säga med mer än hälften röster. Det här läget är meningsfullt i en klustermiljö med ett jämnt antal noder. Den liknar nod- och diskmajoritet läge, men den använder en filresurs vittne i stället för en vittnesdisk. Det här läget är enkel att implementera, men om filresursen själva har inte hög tillgänglighet, så kan det bli en enskild felpunkt.
* **Ingen majoritet: Endast Disk**. Klustret har ett kvorum om en nod är tillgänglig och kommunikation med en viss disk i klusterlagringen. Endast de noder som är också vid kommunikation med disken kan ansluta till klustret. Vi rekommenderar att du inte använder det här läget.
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a>Windows Server Failover-kluster lokalt
Bild 1 visar ett kluster med två noder. Om nätverksanslutningen mellan noder misslyckas och både noder stanna upp och köras, en kvorumdisk eller fil delar avgör vilken nod som kommer att fortsätta att tillhandahålla klustrets program och tjänster. Noden som har åtkomst till kvorum disk eller filresurs är den nod som säkerställer att tjänster fortsätter.

Eftersom det här exemplet använder ett kluster med två noder kan använda vi kvorumläge för nod och filresursmajoritet. Nod- och diskmajoritet är också ett giltigt alternativ. I en produktionsmiljö rekommenderar vi att du använder en kvorumdisk. Nätverks- och system-tekniken kan användas för att ge den hög tillgänglighet.

![Bild 1: Exempel på en Windows Server Failover Clustering konfiguration för SAP ASCS/SCS i Azure][sap-ha-guide-figure-1000]

_**Bild 1:** exempel på en Windows Server Failover Clustering konfiguration för SAP ASCS/SCS i Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>Delad lagring
Bild 1 visar också ett kluster med två noder delad lagring. I ett kluster för lokala delad lagring identifiera alla noder i klustret delad lagring. En mekanism för låsning förhindrar skadade data. Alla noder kan känna av om en annan nod misslyckas. Om en nod misslyckas den återstående noden ägarskapet till storage-resurser och garanterar tillgängligheten av tjänster.

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-program, precis som med SQL Server. SQL Server alltid aktiverad replikerar du DBMS data och loggfilen filerna från den lokala disken på en klusternod till den lokala disken på en annan klusternod. I så fall behöver klusterkonfigurationen Windows inte en delad disk.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>Nätverk och namnmatchning
Klientdatorer når klustret via en virtuell IP-adress och ett virtuellt värdnamn som ger DNS-servern. Lokala noder och DNS-server kan hantera flera IP-adresser.

I en typisk konfiguration kan du använda två eller flera nätverksanslutningar:

* Dedicerad anslutning till lagring
* En kluster-interna nätverksanslutning för pulsslag
* Ett offentligt nätverk som klienter använder för att ansluta till klustret

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>Windows Server Failover-kluster i Azure
Azure Virtual Machines krävs jämfört med bare metal eller privat molndistributioner, ytterligare steg för att konfigurera Windows Server Failover Clustering. När du skapar en delad klusterdisk som du behöver konfigurera flera IP-adresser och virtuella värdnamn för SAP ASCS/SCS-instansen.

I den här artikeln tar vi upp viktiga begrepp och ytterligare steg som krävs för att skapa ett kluster med SAP centrala tjänster med hög tillgänglighet i Azure. Vi visar dig hur du ställer in tredjepartsverktyg SIOS DataKeeper och hur du konfigurerar Azure intern belastningsutjämnare. Du kan använda dessa verktyg för att skapa ett redundanskluster i Windows med ett filresursvittne i Azure.

![Bild 2: Windows Server Failover Clustering konfiguration i Azure utan en delad disk][sap-ha-guide-figure-1001]

_**Bild 2:** Windows Server Failover Clustering konfiguration i Azure utan en delad disk_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a>Delad disk i Azure med SIOS DataKeeper
Du måste ingå i klustret delad lagring för en SAP ASCS/SCS-instans med hög tillgänglighet. Från och med September 2016 erbjuder inte Azure delad lagring som du kan använda för att skapa ett kluster med delad lagring. Du kan använda tredjepartsprogram SIOS DataKeeper Cluster Edition för att skapa en speglad lagring som simulerar delad klusterlagring. SIOS lösningen ger realtidsdata synkron replikering. Detta beror på hur du kan skapa en delad diskresurs för ett kluster:

1. Anslut en ytterligare disk till var och en av de virtuella datorerna (VM) i en Windows-klusterkonfiguration.
2. Kör SIOS DataKeeper Cluster Edition på båda noderna för virtuell dator.
3. Konfigurera SIOS DataKeeper Cluster Edition så att den speglar innehållet i den ytterligare anslutna volymen från den virtuella källdatorn att ytterligare anslutna diskvolymen för den virtuella måldatorn. SIOS DataKeeper avlägsnar käll- och lokala volymer och visar dem till Windows Server Failover Clustering som en delad disk.

Hämta mer information [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![Bild 3: Windows Server Failover Clustering konfigurationen i Azure med SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Bild 3:** Windows Server Failover Clustering konfigurationen i Azure med SIOS DataKeeper_

> [!NOTE]
> Du behöver inte delade diskar för hög tillgänglighet med vissa DBMS-produkter, t.ex. SQL Server. SQL Server alltid aktiverad replikerar du DBMS data och loggfilen filerna från den lokala disken på en klusternod till den lokala disken på en annan klusternod. I det här fallet behöver klusterkonfigurationen Windows inte en delad disk.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Namnmatchning i Azure
Plattformen Azure-molnet kan inte välja att konfigurera den virtuella IP-adresser som flytande IP-adresser. Du måste en alternativ lösning för att konfigurera en virtuell IP-adress till klusterresursen i molnet.
Azure har en intern belastningsutjämnare i tjänsten Azure belastningsutjämnare. Med den interna belastningsutjämnaren nå klienter klustret via klustrets virtuella IP-adress.
Du måste distribuera den interna belastningsutjämnaren i resursgruppen som innehåller klusternoderna. Konfigurera sedan alla nödvändiga port vidarebefordran regler med avsökningen portar för den interna belastningsutjämnaren.
Klienterna kan ansluta via virtuella värdnamn. DNS-servern löser klustrets IP-adress och interna belastningsutjämnare handtag porten vidarebefordran till den aktiva noden i klustret.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>SAP NetWeaver hög tillgänglighet i Azure Infrastructure-as-a-Service (IaaS)
För att uppnå SAP-program med hög tillgänglighet som för SAP programvarukomponenter, måste du skydda följande komponenter:

* SAP Application Server-instans
* SAP ASCS/SCS-instans
* DBMS-server

Mer information om hur du skyddar SAP komponenter i scenarier med hög tillgänglighet finns [Azure Virtual Machines planering och implementering för SAP NetWeaver][planning-guide-11].

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>Hög tillgänglighet SAP-programserver
Vanligtvis behöver du inte en specifik lösning för hög tillgänglighet för SAP-programserver och dialogrutan instanser. Du kan uppnå hög tillgänglighet med redundans och konfigurerar du dialogrutan alla instanser i olika instanser av virtuella datorer i Azure. Du bör ha minst två instanser för SAP-program installeras i två instanser av virtuella datorer i Azure.

![Bild 4: Hög tillgänglighet SAP-programserver][sap-ha-guide-figure-2000]

_**Bild 4:** hög tillgänglighet SAP-programserver_

Du måste placera alla virtuella datorer som värd SAP Application Server-instanser i samma Azure tillgänglighet. En Azure tillgänglighetsuppsättning säkerställer att:

* Alla virtuella datorer är en del av samma uppgraderingsdomänen. En uppgraderingsdomän, till exempel ser till att de virtuella datorerna inte är uppdaterade på samma gång under planerat underhåll driftstopp.
* Alla virtuella datorer ingår i samma feldomän. En feldomän, till exempel ser till att virtuella datorer distribueras så att ingen enskild felpunkt påverkar tillgängligheten för alla virtuella datorer.

Mer information om hur du [hantera tillgängligheten för virtuella datorer][virtual-machines-manage-availability].

Ohanterad disk: eftersom Azure storage-konto är en potentiell felpunkt, är det viktigt att ha minst två Azure storage-konton som minst två virtuella datorer distribueras. I en perfekt installationsprogrammet skulle diskar för varje virtuell dator som kör en instans för SAP-dialogrutan distribueras i ett annat lagringskonto.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>Hög tillgänglighet SAP ASCS/SCS instans
Bild 5 är ett exempel på en hög tillgänglighet SAP ASCS/SCS-instans.

![Bild 5: Hög tillgänglighet SAP ASCS/SCS instans][sap-ha-guide-figure-2001]

_**Bild 5:** hög tillgänglighet SAP ASCS/SCS-instans_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>SAP ASCS/SCS instans hög tillgänglighet med Windows Server Failover Clustering i Azure
Azure Virtual Machines krävs jämfört med bare metal eller privat molndistributioner, ytterligare steg för att konfigurera Windows Server Failover Clustering. För att skapa ett redundanskluster i Windows, behöver du en delad klusterdisk, flera IP-adresser, flera virtuella värdnamn och en Azure intern belastningsutjämnare för kluster för en SAP ASCS/SCS-instans. Detta diskuterar vi i detalj senare i artikeln.

![Bild 6: Windows Server Failover Clustering för en SAP ASCS/SCS-konfiguration i Azure med hjälp av SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Bild 6:** Windows Server Failover Clustering för en SAP ASCS/SCS-konfiguration i Azure med SIOS DataKeeper_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Hög tillgänglighet DBMS-instans
DBMS är också en enda kontaktpunkt i ett SAP-system. Du måste skydda den med hjälp av en lösning för hög tillgänglighet. Bild 7 visar en SQL Server alltid på hög tillgänglighet lösning i Azure, med Windows Server Failover Clustering och Azure interna belastningsutjämnare. SQL Server alltid aktiverad replikerar DBMS data och loggfilen filer med hjälp av sin egen DBMS-replikering. I detta fall kan behöver du inte kluster delade diskar som förenklar hela installationen.

![Bild 7: Exempel på en SAP-DBMS med hög tillgänglighet, med SQL Server alltid aktiverad][sap-ha-guide-figure-2003]

_**Bild 7:** exempel på ett SAP-DBMS med hög tillgänglighet, med SQL Server alltid aktiverad_

Mer information om kluster och SQL Server i Azure med hjälp av Azure Resource Manager-distributionsmodellen finns i följande artiklar:

* [Konfigurera Always On-tillgänglighetsgrupp i Azure Virtual Machines manuellt med hjälp av hanteraren för filserverresurser] [virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurera en Azure intern belastningsutjämnare för en tillgänglighetsgrupp alltid på i Azure] [virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>Distributionsscenarier för slutpunkt till slutpunkt hög tillgänglighet

### <a name="deployment-scenario-using-architectural-template-1"></a>Scenario för distribution med arkitekturen mall 1

Bild 8 visar ett exempel på en arkitektur för SAP NetWeaver hög tillgänglighet i Azure för **en** SAP-system. Det här scenariot är inställd på följande sätt:

- Ett dedikerat kluster används för SAP ASCS/SCS-instans.
- Ett dedikerat kluster används för DBMS-instans.
- SAP Application Server-instanser har distribuerats i sina egna dedikerade virtuella datorer.

![Bild 8: SAP hög tillgänglighet arkitektur mall 1, med dedikerade kluster för ASCS/SCS och DBMS][sap-ha-guide-figure-2004]

_**Bild 8:** SAP hög tillgänglighet arkitektur mall 1 dedikerade kluster för ASCS/SCS och DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Scenario för distribution med arkitekturen mall 2

Bild 9 illustrerar ett exempel på en arkitektur för SAP NetWeaver hög tillgänglighet i Azure för **en** SAP-system. Det här scenariot är inställd på följande sätt:

- Ett dedikerat kluster används för **både** SAP ASCS/SCS-instans och DBMS.
- SAP Application Server-instanser har distribuerats i egna dedikerade virtuella datorer.

![Bild 9: SAP hög tillgänglighet arkitektur mall 2, med ett kluster för ASCS/SCS och ett kluster för DBMS][sap-ha-guide-figure-2005]

_**Bild 9:** SAP hög tillgänglighet arkitektur mall 2, med ett kluster för ASCS/SCS och ett kluster för DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Scenario för distribution med arkitekturen mall 3

Bild 10 illustrerar ett exempel på en arkitektur för SAP NetWeaver hög tillgänglighet i Azure för **två** SAP-system med &lt;SID1&gt; och &lt;SID2&gt;. Det här scenariot är inställd på följande sätt:

- Ett dedikerat kluster används för **både** SAP ASCS/SCS SID1 instansen *och* SAP ASCS/SCS SID2-instans (kluster).
- Ett dedikerat kluster används för DBMS SID1 och ett annat dedikerade kluster används för DBMS SID2 (två kluster).
- SAP Application Server-instanser för SAP-systemet SID1 har sina egna dedikerade virtuella datorer.
- SAP Application Server-instanser för SAP-systemet SID2 har sina egna dedikerade virtuella datorer.

![Figur 10: SAP hög tillgänglighet arkitektur mall 3, med ett kluster för olika ASCS/SCS-instanser][sap-ha-guide-figure-6003]

_**Figur 10:** SAP hög tillgänglighet arkitektur mall 3, med ett kluster för olika ASCS/SCS-instanser_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>Förbered infrastrukturen

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Förbereda infrastrukturen för arkitektur mall 1
Azure Resource Manager-mallar för SAP underlätta distributionen av de nödvändiga resurserna.

Trelagers-mallar i Azure Resource Manager stöder också scenarier med hög tillgänglighet som i arkitekturen mall 1, som har två kluster. Varje kluster är ett SAP felpunkt för SAP ASCS/SCS och DBMS.

Här är där du kan hämta Azure Resource Manager-mallar för exempelscenario som beskrivs i den här artikeln:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace-avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Anpassad avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Förbereda infrastrukturen för arkitektur mall 1:

- I Azure-portalen på den **parametrar** blad i den **SYSTEMAVAILABILITY** väljer **HA**.

  ![Figur 11: Ange parametrar för SAP hög tillgänglighet Azure Resource Manager][sap-ha-guide-figure-3000]

_**Figur 11:** ange parametrar för SAP hög tillgänglighet Azure Resource Manager_


  Skapa mallar:

  * **Virtuella datorer**:
    * SAP programserver virtuella datorer: <*SAPSystemSID*> - di - <*tal*>
    * ASCS/SCS klustra virtuella datorer: <*SAPSystemSID*> - ascs - <*tal*>
    * DBMS-kluster: <*SAPSystemSID*> - db - <*tal*>

  * **Nätverkskort för alla virtuella datorer med tillhörande IP-adresser**:
    * <*SAPSystemSID*> - nic - di - <*tal*>
    * <*SAPSystemSID*> - nic - ascs - <*tal*>
    * <*SAPSystemSID*> - nic - db - <*tal*>

  * **Azure storage-konton (endast ohanterade diskar)**

  * **Tillgänglighetsgrupper** för:
    * SAP programserver virtuella datorer: <*SAPSystemSID*> - avset - di
    * SAP ASCS/SCS klustra virtuella datorer: <*SAPSystemSID*> - avset - ascs
    * DBMS klustra virtuella datorer: <*SAPSystemSID*> - avset - db

  * **Azure intern belastningsutjämnare**:
    * Med alla portar för ASCS/SCS-instansen och IP-adressen <*SAPSystemSID*> - lb - ascs
    * Med alla portar för SQL Server DBMS och IP-adressen <*SAPSystemSID*> - lb - db

  * **Nätverkssäkerhetsgruppen**: <*SAPSystemSID*> - nsg - ascs-0  
    * Med en öppen externa Remote Desktop Protocol (RDP)-port till den <*SAPSystemSID*> - ascs - 0 virtuell dator

> [!NOTE]
> Alla IP-adresser för nätverkskort och Azure interna belastningsutjämnare **dynamisk** som standard. Ändra dem till **Statiska** IP-adresser. Vi beskriver hur du gör det senare i artikeln.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Distribuera virtuella datorer med företagets nätverksanslutning (mellan platser) ska användas i produktion
Distribuera virtuella Azure-datorer med för produktion SAP-system, [företagets nätverksanslutning (mellan platser)] [ planning-guide-2.2] med hjälp av Azure plats-till-plats-VPN eller Azure ExpressRoute.

> [!NOTE]
> Du kan använda Azure Virtual Network-instans. Virtuellt nätverk och undernät har redan skapats och förberetts.
>
>

1.  I Azure-portalen på den **parametrar** blad i den **NEWOREXISTINGSUBNET** väljer **befintliga**.
2.  I den **SUBNETID** Lägg till fullständig strängen förberedda Azure nätverkets SubnetID där du planerar att distribuera Azure virtuella datorer.
3.  Hämta en lista över alla undernät i Azure-nätverk, Kör PowerShell-kommando:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  Den **ID** fältet visar den **SUBNETID**.
4. Att hämta en lista över alla **SUBNETID** värden, Kör PowerShell-kommando:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  Den **SUBNETID** ser ut så här:

  ```
  /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
  ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Distribuera endast molnbaserad SAP-instanser för testning och demo
Du kan distribuera din SAP-systemet för hög tillgänglighet i en endast molnbaserad distributionsmodell. Den här typen av distribution är främst användbart för demonstration och testa användningsområden. Det är inte lämpligt för produktion användningsfall.

- I Azure-portalen på den **parametrar** blad i den **NEWOREXISTINGSUBNET** väljer **nya**. Lämna den **SUBNETID** fältet tomt.

  SAP Azure Resource Manager-mallen skapar automatiskt den virtuella Azure-nätverk och undernät.

> [!NOTE]
> Du måste distribuera minst en dedikerad virtuell dator för Active Directory och DNS i samma Azure Virtual Network-instans. Mallen skapas inte de virtuella datorerna.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Förbereda infrastrukturen för arkitektur mall 2

Du kan använda den här Azure Resource Manager-mallen för SAP för att underlätta distributionen av infrastrukturresurser för SAP arkitektur mall 2.

Här är där du kan hämta Azure Resource Manager-mallar för det här distributionsscenariot:

* [Azure Marketplace-avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Anpassad avbildning med hjälp av hanterade diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Förbereda infrastrukturen för arkitektur mall 3

Du kan förbereda infrastrukturen och konfigurera SAP för **multi-SID**. Du kan exempelvis lägga till en ytterligare SAP ASCS/SCS-instans i en *befintliga* klusterkonfigurationen. Mer information finns i [konfigurera en ytterligare SAP ASCS/SCS-instans i en befintlig klusterkonfiguration för att skapa en konfiguration för SAP multi-SID i Azure Resource Manager][sap-ha-multi-sid-guide].

Om du vill skapa ett nytt multi-SID-kluster, kan du använda multi-SID [snabbstartsmallar på GitHub](https://github.com/Azure/azure-quickstart-templates).
Om du vill skapa ett nytt multi-SID-kluster måste du distribuera följande tre mallar:

* [ASCS/SCS mall](#ASCS-SCS-template)
* [Databasmall för](#database-template)
* [Mall för servrar](#application-servers-template)

I följande avsnitt har mer information om mallar och parametrar måste du ange i mallar.

#### <a name="ASCS-SCS-template"></a>ASCS/SCS mall

ASCS/SCS mallen distribuerar två virtuella datorer som du kan använda för att skapa ett redundanskluster i Windows Server som är värd för flera ASCS/SCS-instanser.

Att konfigurera mallen ASCS/SCS multi-SID i den [ASCS/SCS multi-SID-mall] [ sap-templates-3-tier-multisid-xscs-marketplace-image] eller [ASCS/SCS multi-SID-mall med hjälp av hanterade diskar][sap-templates-3-tier-multisid-xscs-marketplace-image-md], ange värden för följande parametrar:

  - **Resursen prefixet**.  Ange resurs-prefixet, vilket är visas för alla resurser som har skapats under distributionen. Resurserna som inte hör till endast en SAP-system, är prefixet för resursen inte eftersom SID för en SAP-system.  Prefixet måste vara mellan **tre till sex tecken**.
  - **Stacken typen**. Välj stack-typ av SAP-system. Beroende på stack-typ har Azure belastningsutjämnare en (ABAP eller endast Java) eller två (ABAP + Java) privata IP-adresser per SAP-system.
  -  **OS-typen**. Välj operativsystem för de virtuella datorerna.
  -  **SAP System antal**. Välj antal SAP-system som du vill installera i det här klustret.
  -  **Filsystemets tillgänglighet**. Välj **HA**.
  -  **Användarnamn och lösenord för Admin administratör**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Ny eller befintlig undernät**. Ange om ett nytt virtuellt nätverk och undernät som ska skapas eller ett befintligt undernät som ska användas. Om du redan har ett virtuellt nätverk som är anslutna till ditt lokala nätverk väljer **befintliga**.
  -  **Undernät-Id**. Ange ID för det undernät som de virtuella datorerna ska anslutas. Välj undernät virtuellt privat nätverk (VPN) eller ExpressRoute virtuellt nätverk att ansluta den virtuella datorn till ditt lokala nätverk. ID: T ser oftast ut så här:

   /subscriptions/ <*prenumerations-id*> /resourceGroups/ <*resursgruppnamn*> /providers/Microsoft.Network/virtualNetworks/ <*virtuella nätverksnamnet*> /subnets/ <*undernätsnamn*>

Mallen distribuerar en belastningsutjämnare för Azure-instans som har stöd för flera SAP-system.

- ASCS-instanserna är konfigurerade för instansnummer 00, 10, 20...
- SCS-instanserna är konfigurerade för instansnummer 01, 11, 21...
- ASCS sätta replikering Server (ERS) (endast Linux)-instanserna är konfigurerade för instansnummer 02, 12, 22...
- SCS ERS (endast Linux)-instanserna är konfigurerade för instansnummer 03, 13, 23...

Belastningsutjämnaren innehåller 1.2 för Linux VIP(s), 1 x-VIP för ASCS/SCS och 1 x-VIP för ÄNDARE (endast Linux).

Följande lista innehåller alla belastningsutjämningsregler (där x är numret på SAP-systemet, till exempel 1, 2, 3...):
- Windows-specifika portar för varje system för SAP: 445, 5985
- ASCS-portar (instansnummer x0): 32 x 0, 36 x 0, 39 x 0, 81 0 x, 5 x 013, 5 x 014, 5 x 016
- SCS portar (instansnummer x1): 32 x 1, 33 x 1, 39 x 1, 81 x 1, 5 x 113, 5 x 114, 5 x 116
- ASCS ERS portar på Linux (instansnummer x2): 33 x 2, 5 x 213, 5 x 214, 5 x 216
- SCS ERS portar på Linux (instansnummer x3): 33 x 3, 5 x 313, 5 x 314, 5 x 316

Belastningsutjämnaren har konfigurerats för att använda följande avsökningen portar (där x är numret på SAP-systemet, till exempel 1, 2, 3...):
- ASCS/SCS interna läsa in belastningsutjämning avsökningsport: 620 x 0
- ÄNDARE interna läsa in belastningsutjämning avsökningsport (endast Linux): 621 x 2

#### <a name="database-template"></a>Databasmall för

Mallen databasen distribuerar en eller två virtuella datorer som du kan använda för att installera systemet för relationsdatabashantering (RDBMS) för en SAP-system. Om du distribuerar en ASCS/SCS-mall för fem SAP-system, måste du distribuera den här mallen fem gånger.

Att konfigurera mallen databasen multi-SID i den [multi-SID-mall för databas] [ sap-templates-3-tier-multisid-db-marketplace-image] eller [databasen multi-SID-mall med hjälp av hanterade diskar][sap-templates-3-tier-multisid-db-marketplace-image-md], ange värden för följande parametrar:

  -  **SAP System-Id**. Ange ID för SAP-system för SAP-system som du vill installera. ID som ska användas som ett prefix för de resurser som har distribuerats.
  -  **OS-typen**. Välj operativsystem för de virtuella datorerna.
  -  **DbType**. Välj typ av databas som du vill installera i klustret. Välj **SQL** om du vill installera Microsoft SQL Server. Välj **HANA** om du planerar att installera SAP HANA på de virtuella datorerna. Se till att välja rätt operativsystemtypen: Välj **Windows** för SQL och välj en Linux-distributionsplats för HANA. Azure-belastningsutjämnaren som är anslutna till virtuella datorer kommer att konfigureras för att stödja den valda databastyp:
    * **SQL**. Belastningsutjämnaren att belastningsutjämna port 1433. Se till att använda den här porten för din SQL Server Always On-konfiguration.
    * **HANA**. Belastningsutjämnaren att belastningsutjämna portarna 35015 och 35017. Se till att installera SAP HANA med instansnummer **50**.
    Belastningsutjämnaren använder avsökningsport 62550.
  -  **SAP systemstorlek**. Ange antalet SAP ger det nya systemet. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegreraren.
  -  **Filsystemets tillgänglighet**. Välj **HA**.
  -  **Användarnamn och lösenord för Admin administratör**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernät-Id**. Ange ID för det undernät som du använde under distributionen av mallen ASCS/SCS eller ID för det undernät som har skapats som en del av malldistribution ASCS/SCS.

#### <a name="application-servers-template"></a>Mall för servrar

Servrar programmall distribuerar två eller flera virtuella datorer som kan användas som SAP Application Server-instanser för en SAP-system. Om du distribuerar en ASCS/SCS-mall för fem SAP-system, måste du distribuera den här mallen fem gånger.

Att ställa in programmet servrar multi-SID-mall i den [programmall servrar multi-SID] [ sap-templates-3-tier-multisid-apps-marketplace-image] eller [multi-SID mall för program-servrar med hjälp av hanterade diskar][sap-templates-3-tier-multisid-apps-marketplace-image-md], ange värden för följande parametrar:

  -  **SAP System-Id**. Ange ID för SAP-system för SAP-system som du vill installera. ID som ska användas som ett prefix för de resurser som har distribuerats.
  -  **OS-typen**. Välj operativsystem för de virtuella datorerna.
  -  **SAP systemstorlek**. Antal SAP ger det nya systemet. Om du inte vet hur många SAP systemet kräver, be din SAP-teknikpartner eller systemintegreraren.
  -  **Filsystemets tillgänglighet**. Välj **HA**.
  -  **Användarnamn och lösenord för Admin administratör**. Skapa en ny användare som kan användas för att logga in på datorn.
  -  **Undernät-Id**. Ange ID för det undernät som du använde under distributionen av mallen ASCS/SCS eller ID för det undernät som har skapats som en del av malldistribution ASCS/SCS.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Virtuella Azure-nätverket
I vårt exempel är i virtuella Azure-nätverket adressutrymme 10.0.0.0/16. Det finns ett undernät som kallas **undernät**, med en-adressintervallet 10.0.0.0/24. Alla virtuella datorer och interna belastningsutjämnare har distribuerats i det här virtuella nätverket.

> [!IMPORTANT]
> Inte göra ändringar i nätverksinställningar i gästoperativsystemet. Detta inkluderar IP-adresser, DNS-servrar och undernät. Konfigurera nätverksinställningarna i Azure. Tjänsten DHCP Dynamic Host Configuration Protocol () sprider dina inställningar.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS IP-adresser

Om du vill ange krävs DNS IP-adresser, gör du följande steg.

1.  I Azure-portalen på den **DNS-servrar** bladet, se till att det virtuella nätverket **DNS-servrar** alternativet är inställt på **anpassad DNS**.
2.  Välj dina inställningar beroende på vilken typ av nätverk som du har. Mer information finns i följande resurser:
    * [Företagets nätverksanslutning (mellan platser)][planning-guide-2.2]: Lägg till IP-adresserna för de lokala DNS-servrarna.  
    Du kan utöka lokala DNS-servrar till de virtuella datorerna som körs i Azure. Du kan lägga till IP-adresserna för de virtuella Azure-datorer som du kör DNS-tjänsten i det scenariot.
    * [Endast molnbaserad distribution][planning-guide-2.1]: distribuera en ytterligare virtuell dator i samma virtuella nätverk-instans som fungerar som en DNS-server. Lägg till IP-adresserna för de virtuella Azure-datorer som du har konfigurerat för att köra DNS-tjänsten.

    ![Figur 12: Konfigurera DNS-servrar för Azure-nätverk][sap-ha-guide-figure-3001]

    _**Figur 12:** konfigurera DNS-servrar för Azure-nätverk_

  > [!NOTE]
  > Du måste starta om de virtuella Azure-datorerna för att tillämpa ändringen och sprida de nya DNS-servrarna om du ändrar IP-adresser för DNS-servrar.
  >
  >

I vårt exempel har DNS-tjänsten installerats och konfigurerats på dessa virtuella Windows-datorer:

| Rollen virtuell dator | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första DNS-servern |domcontr 0 |PR1-nic-domcontr-0 |10.0.0.10 |
| Andra DNS-server |domcontr 1 |PR1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Värdnamn och statiska IP-adresser för SAP ASCS/SCS klustrade instansen och DBMS klustrad instans

För lokal distribution måste dessa reserverade värdnamn och IP-adresser:

| Virtuell värd namn roll | Virtuella värdnamn | Virtuell statisk IP-adress |
| --- | --- | --- |
| SAP ASCS/SCS första klustret virtuellt värdnamn (för hantering av kluster) |PR1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS virtuell värd instansnamn |PR1-ascs-sap |10.0.0.43 |
| SAP DBMS andra kluster virtuellt värdnamn (hantering) |PR1-dbms-vir |10.0.0.32 |

När du skapar klustret kan du skapa virtuella värdnamn **pr1-ascs-vir** och **pr1-dbms-vir** och associerade IP-adresser som hantera själva klustret. Information om hur du gör detta finns [samla in klusternoder i en klusterkonfiguration][sap-ha-guide-8.12.1].

Du kan manuellt skapa de andra två virtuella värdnamn, **pr1-ascs-sap** och **pr1-dbms-sap**, och de associerade IP-adresserna på DNS-servern. Använd dessa resurser den klustrade instansen SAP ASCS/SCS och den klustrade DBMS-instansen. Information om hur du gör detta finns [skapa ett virtuellt värdnamn för en klustrad instans av SAP ASCS/SCS][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Ange statiska IP-adresser för de virtuella datorerna för SAP
När du distribuerar virtuella datorer i klustret måste du ange statiska IP-adresser för alla virtuella datorer. Gör detta i Azure Virtual Network-konfigurationen och inte i gästoperativsystemet.

1.  Välj i Azure-portalen **resursgruppen** > **nätverkskort** > **inställningar** > **IP-adress**.
2.  På den **IP-adresser** bladet under **tilldelning**väljer **statiska**. I den **IP-adress** ange IP-adressen som du vill använda.

  > [!NOTE]
  > Om du ändrar IP-adressen för nätverkskortet, måste du starta om de virtuella Azure-datorerna för att tillämpa ändringen.  
  >
  >

  ![Figur 13: Ange statiska IP-adresser för nätverkskort på varje virtuell dator][sap-ha-guide-figure-3002]

  _**Figur 13:** ange statiska IP-adresser för nätverkskort på varje virtuell dator_

  Upprepa det här steget för alla nätverksgränssnitt som är, för alla virtuella datorer, inklusive virtuella datorer som du vill använda för din Active Directory/DNS-tjänst.

I vårt exempel har vi dessa virtuella datorer och statiska IP-adresser:

| Rollen virtuell dator | Värdnamn för virtuell dator | Namn på nätverkskort | Statisk IP-adress |
| --- | --- | --- | --- |
| Första SAP Application Server-instansen |PR1-di-0 |PR1-nic-di-0 |10.0.0.50 |
| Andra SAP Application Server-instans |PR1-di-1 |PR1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Senaste SAP Application Server-instans |PR1-di-5 |PR1-nic-di-5 |10.0.0.55 |
| Första klusternoden för ASCS/SCS-instans |PR1-ascs-0 |PR1-nic-ascs-0 |10.0.0.40 |
| Andra klusternod för ASCS/SCS-instans |PR1-ascs-1 |PR1-nic-ascs-1 |10.0.0.41 |
| Första klusternoden för DBMS-instans |PR1-db-0 |PR1-nic-db-0 |10.0.0.30 |
| Andra klusternod för DBMS-instans |PR1-db-1 |PR1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Ange en statisk IP-adress för den interna belastningsutjämnaren som Azure

SAP Azure Resource Manager-mallen skapar en Azure intern belastningsutjämnare som används för det SAP ASCS/SCS instans och DBMS-klustret.

> [!IMPORTANT]
> IP-adressen för den virtuella värdnamnet för SAP ASCS/SCS är samma som IP-adressen för den interna belastningsutjämnaren SAP ASCS/SCS: **pr1-lb-ascs**.
> IP-adressen för det virtuella namnet på DBMS är samma som IP-adressen för den interna belastningsutjämnaren DBMS: **pr1-lb-dbms**.
>
>

Ange en statisk IP-adress för den interna belastningsutjämnaren som Azure:

1.  Den första distributionen anger interna IP-adressen för belastningsutjämnaren till **dynamiska**. I Azure-portalen på den **IP-adresser** bladet under **tilldelning**väljer **statiska**.
2.  Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-ascs** till IP-adressen för den virtuella värdnamnet för SAP ASCS/SCS-instansen.
3.  Ange IP-adressen för den interna belastningsutjämnaren **pr1-lb-dbms** IP-adressen för den virtuella värdnamnet för DBMS-instansen.

  ![Figur 14: Ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instans][sap-ha-guide-figure-3003]

  _**Figur 14:** ange statiska IP-adresser för den interna belastningsutjämnaren för SAP ASCS/SCS-instans_

I vårt exempel har vi två Azure interna belastningsutjämnare som har dessa statiska IP-adresser:

| Azure interna belastningsutjämnarrollen | Azure interna belastningsutjämnarens namn | Statisk IP-adress |
| --- | --- | --- |
| Interna belastningsutjämnare för SAP ASCS/SCS-instans |ascs-lb-PR1 |10.0.0.43 |
| SAP DBMS intern belastningsutjämnare |PR1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Standard ASCS/SCS belastningsutjämningsregler för Azure interna belastningsutjämnare

SAP Azure Resource Manager-mallen skapar de portar som du behöver:
* En ABAP ASCS-instans med standard-instansnummer **00**
* En Java-SCS-instans med standard-instansnummer **01**

När du installerar din SAP ASCS/SCS-instans måste du använda instansen Standardnumret **00** för ABAP ASCS-instansen och instansen Standardnumret **01** för din Java SCS-instans.

Skapa sedan nödvändiga intern belastningsutjämning slutpunkter för SAP NetWeaver-portar.

Om du vill skapa nödvändiga intern belastningsutjämning slutpunkter först skapa dessa slutpunkter för SAP NetWeaver ABAP ASCS-portar för belastningsutjämning:

| Tjänsten/belastningen belastningsutjämning Regelnamn | Standardportnumren. | Konkret portar för (ASCS-instans med instansnummer 00) (ERS med 10) |
| --- | --- | --- |
| Sätta Server / *lbrule3200* |32 <*InstanceNumber*> |3200 |
| ABAP Message Server / *lbrule3600* |36 <*InstanceNumber*> |3600 |
| Internt ABAP meddelande / *lbrule3900* |39 <*InstanceNumber*> |3900 |
| Serverns HTTP-meddelandet / *Lbrule8100* |81 <*InstanceNumber*> |8100 |
| SAP Start Service ASCS HTTP / *Lbrule50013* |5 <*InstanceNumber*> 13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5 <*InstanceNumber*> 14 |50014 |
| Sätta replikering / *Lbrule50016* |5 <*InstanceNumber*> 16 |50016 |
| SAP Start Service ÄNDARE HTTP *Lbrule51013* |5 <*InstanceNumber*> 13 |51013 |
| SAP Start Service ÄNDARE HTTP *Lbrule51014* |5 <*InstanceNumber*> 14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Filresurs *Lbrule445* | |445 |

_**Tabell 1:** portnummer för SAP NetWeaver ABAP ASCS-instanser_

Skapa sedan dessa slutpunkter för SAP NetWeaver Java SCS-portar för belastningsutjämning:

| Tjänsten/belastningen belastningsutjämning Regelnamn | Standardportnumren. | Konkret portar för (SCS-instans med instansnummer 01) (ERS med 11) |
| --- | --- | --- |
| Sätta Server / *lbrule3201* |32 <*InstanceNumber*> |3201 |
| Gateway-Server / *lbrule3301* |33 <*InstanceNumber*> |3301 |
| Java Message Server / *lbrule3900* |39 <*InstanceNumber*> |3901 |
| Serverns HTTP-meddelandet / *Lbrule8101* |81 <*InstanceNumber*> |8101 |
| SAP Start Service SCS HTTP / *Lbrule50113* |5 <*InstanceNumber*> 13 |50113 |
| SAP Start Service SCS HTTPS / *Lbrule50114* |5 <*InstanceNumber*> 14 |50114 |
| Sätta replikering / *Lbrule50116* |5 <*InstanceNumber*> 16 |50116 |
| SAP Start Service ÄNDARE HTTP *Lbrule51113* |5 <*InstanceNumber*> 13 |51113 |
| SAP Start Service ÄNDARE HTTP *Lbrule51114* |5 <*InstanceNumber*> 14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Filresurs *Lbrule445* | |445 |

_**Tabell 2:** portnummer för SAP NetWeaver Java SCS-instanser_

![Figur 15: Standard ASCS/SCS belastningsutjämningsregler för Azure interna belastningsutjämnare][sap-ha-guide-figure-3004]

_**Figur 15:** standard ASCS/SCS regler för Azure intern belastningsutjämnare för belastningsutjämning_

Ange IP-adressen för belastningsutjämnaren **pr1-lb-dbms** IP-adressen för den virtuella värdnamnet för DBMS-instansen.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Ändra ASCS/SCS standard belastningsutjämning regler för Azure interna belastningsutjämnare

Om du vill använda andra värden för SAP ASCS eller SCS instanser måste du ändra namn och värden på deras hamnar från standardvärdena.

1.  Välj i Azure-portalen  **<* SID*> - lb - ascs läsa in belastningsutjämning ** > **regler för att läsa in belastningsutjämning**.
2.  För alla belastningsutjämningsregler som tillhör instansen SAP ASCS eller SCS, ändra dessa värden:

  * Namn
  * Port
  * Backend-port

  Om du vill ändra standardvärdet för ASCS-instans från 00 och 31, måste du göra ändringar för alla portar som anges i tabell 1.

  Här är ett exempel på en uppdatering för port *lbrule3200*.

  ![Bild 16: Ändra ASCS/SCS standard belastningsutjämning regler för Azure interna belastningsutjämnare][sap-ha-guide-figure-3005]

  _**Bild 16:** ändra ASCS/SCS standard belastningsutjämning regler för Azure interna belastningsutjämnare_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Lägga till Windows-datorer i domänen

När du tilldelar en statisk IP-adress till de virtuella datorerna kan du lägga till de virtuella datorerna till domänen.

![Figur 17: Lägga till en virtuell dator i en domän][sap-ha-guide-figure-3006]

_**Figur 17:** lägga till en virtuell dator i en domän_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Lägg till registerposter på båda klusternoderna för SAP ASCS/SCS-instansen

Azure belastningsutjämnare har en intern belastningsutjämnare att stängs anslutningar när anslutningar är inaktiva för en viss tid (en timeout för inaktivitet). SAP arbetsprocesser i dialogrutan instanser öppna anslutningar till SAP sätta bearbeta så snart som de första sätta/status Created begäran måste skickas. Dessa anslutningar förblir oftast etablerade förrän arbetsprocessen eller sätta processen startas om. Om anslutningen är inaktiv under en angiven tidsperiod, stängs den interna belastningsutjämnaren som Azure anslutningar. Detta är inte ett problem Eftersom arbetsprocessen för SAP återupprättar anslutningen till sätta processen om det inte längre finns. Dessa aktiviteter finns dokumenterade i developer spår av SAP processer, men de skapar en stor mängd extra innehåll i dessa spår. Det är en bra idé att ändra TCP/IP `KeepAliveTime` och `KeepAliveInterval` på båda klusternoderna. Kombinera ändringarna i TCP/IP-parametrar med SAP profil-parametrar som beskrivs senare i artikeln.

För att lägga till poster i registret på båda klusternoderna för SAP ASCS/SCS-instansen, Lägg först till dessa registerposter i Windows på båda klusternoderna i Windows för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveTime` |
| Variabeltyp |REG_DWORD (decimalt) |
| Värde |120000 |
| Länka till dokumentationen |[https://technet.microsoft.com/en-us/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx) |

_**Tabell 3:** ändra den första parametern för TCP/IP_

Lägg sedan till den här Windows-registerposter på båda klusternoderna i Windows för SAP ASCS/SCS:

| Sökväg | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variabelnamn |`KeepAliveInterval` |
| Variabeltyp |REG_DWORD (decimalt) |
| Värde |120000 |
| Länka till dokumentationen |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

_**Tabell 4:** ändra andra TCP/IP-parameter_

**Om du vill tillämpa ändringarna genom att starta om båda klusternoderna**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Konfigurera ett redundanskluster i Windows Server-kluster för en SAP ASCS/SCS-instans

Konfigurera ett redundanskluster i Windows Server-kluster för en SAP ASCS/SCS-instans innebär att dessa uppgifter:

- Insamling av klusternoderna i en klusterkonfiguration
- Konfigurera ett filresursvittne för kluster

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Samla in klusternoderna i en klusterkonfiguration

1.  Guiden Lägg till roller och funktioner, lägga till redundansklustring till båda klusternoderna.
2.  Ställ in failover-kluster med hjälp av hanteraren för redundanskluster. I hanteraren för redundanskluster, Välj **Skapa kluster**, och Lägg sedan till bara namnet på det första klustret nod A. Lägg inte till den andra noden ännu; Du måste lägga till den andra noden i ett senare steg.

  ![Figur 18: Lägg till namnet på servern eller den virtuella datorn på den första noden i klustret][sap-ha-guide-figure-3007]

  _**Figur 18:** Lägg till namnet på servern eller den virtuella datorn på den första noden i klustret_

3.  Ange nätverksnamn (virtuellt värdnamn) i klustret.

  ![Bild 19: Ange klusternamnet][sap-ha-guide-figure-3008]

  _**Bild 19:** ange klusternamnet_

4.  När du har skapat klustret, kör du ett klustervalideringstest.

  ![Figur 20: Kör verifieringen kluster][sap-ha-guide-figure-3009]

  _**Figur 20:** kör verifieringen kluster_

  Du kan ignorera alla varningar om diskarna nu i processen. Du ska lägga till ett filresursvittne och SIOS delade diskar senare. I det här skedet behöver du inte bekymra dig om att ha ett kvorum.

  ![Figur 21: Inga kvorumdisken hittas][sap-ha-guide-figure-3010]

  _**Figur 21:** inga kvorumdisken hittas_

  ![Figur 22: Core klusterresurs måste en ny IP-adress][sap-ha-guide-figure-3011]

  _**Figur 22:** Core klusterresurs måste en ny IP-adress_

5.  Ändra IP-adressen för klustertjänsten kärnor. Klustret kan inte starta tills du ändrar IP-adressen för klustertjänsten core eftersom IP-adressen för servern som pekar på en av noderna virtuell dator. Gör detta på den **egenskaper** sidan core klustertjänsten IP-resurs.

  Till exempel vi måste du tilldela en IP-adress (i vårt exempel **10.0.0.42**) för det virtuella värdnamnet för klustret **pr1-ascs-vir**.

  ![Figur 23: I dialogrutan Egenskaper ändra IP-adressen][sap-ha-guide-figure-3012]

  _**Figur 23:** i den **egenskaper** dialogrutan Ändra IP-adress_

  ![Figur 24: Tilldela IP-adress som är reserverad för klustret][sap-ha-guide-figure-3013]

  _**Figur 24:** tilldela IP-adress som är reserverad för klustret_

6.  Placera det virtuella värden klusternamnet.

  ![Bild 25: Core klustertjänsten är igång och körs och med rätt IP-adress][sap-ha-guide-figure-3014]

  _**Bild 25:** core klustertjänsten är igång och körs och med rätt IP-adress_

7.  Lägg till den andra noden i klustret.

  Nu när klustertjänsten kärnor är igång, kan du lägga till den andra noden i klustret.

  ![Bild 26: Lägga till den andra noden i klustret][sap-ha-guide-figure-3015]

  _**Bild 26:** lägga till den andra noden i klustret_

8.  Ange ett namn för den andra nod klustervärden.

  ![Bild 27: Ange andra värden klusternodnamnet][sap-ha-guide-figure-3016]

  _**Bild 27:** ange andra värden klusternodnamnet_

  > [!IMPORTANT]
  > Kontrollera att den **lägga till alla tillgängliga lagringsenheter i klustret** kryssrutan är **inte** valda.  
  >
  >

  ![Bild 28: Markera inte kryssrutan][sap-ha-guide-figure-3017]

  _**Bild 28:** gör **inte** Markera kryssrutan_

  Du kan ignorera varningar om kvorum och diskar. Du måste ange kvorum och dela disken senare, enligt beskrivningen i [installerar SIOS DataKeeper Cluster Edition för SAP ASCS/SCS klusterdisk resursen][sap-ha-guide-8.12.3].

  ![Bild 29: Ignorera varningar om disken kvorum][sap-ha-guide-figure-3018]

  _**Bild 29:** Ignorera varningar om disken kvorum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Konfigurera ett filresursvittne för kluster

När du konfigurerar ett kluster filresursvittne inkluderar dessa uppgifter:

- Skapa en filresurs
- Ange filen filresurs vittne kvorum i hanteraren för redundanskluster

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Skapa en filresurs

1.  Välj ett filresursvittne i stället för en kvorumdisk. SIOS DataKeeper stöder det här alternativet.

  I exemplen i den här artikeln är filresursvittnet på Active Directory/DNS-server som körs i Azure. Filresursvittnet kallas **domcontr 0**. Eftersom du skulle ha konfigurerat en VPN-anslutning till Azure (via plats-till-plats-VPN eller Azure ExpressRoute) kan dela din Active Directory/DNS-tjänsten är lokalt och inte är lämpligt att köra en fil vittne.

  > [!NOTE]
  > Om Active Directory/DNS-tjänsten körs bara lokalt, inte konfigurera din filresursvittne på Active Directory/DNS-Windows-operativsystemet som körs lokalt. Nätverksfördröjningen mellan klusternoder som körs i Azure och Active Directory/DNS lokalt kan vara för stort och orsaka problem med nätverksanslutningen. Se till att konfigurera filresursvittnet på en virtuell Azure-dator som kör nära noden i klustret.  
  >
  >

  Kvorum-enhet behöver minst 1 024 MB ledigt utrymme. Vi rekommenderar 2 048 MB ledigt utrymme för enheten kvorum.

2.  Lägg till klusternamnobjektet.

  ![Bild 30: Tilldela behörigheter för resurs för klusternamnobjektet][sap-ha-guide-figure-3019]

  _**Bild 30:** tilldela behörigheter för resurs för klusternamnobjektet_

  Se till att behörigheterna som inkluderar behörighet att ändra data i resursen för klusternamnobjektet (i vårt exempel **pr1-ascs-vir$**).

3.  Om du vill lägga till klusternamnobjektet i listan, Välj **Lägg till**. Ändra filtreringen för att söka efter datorobjekt, förutom de som visas i bild 31.

  ![Bild 31: Ändra objekttyper för att inkludera datorer][sap-ha-guide-figure-3020]

  _**Bild 31:** ändra objekttyper för att inkludera datorer_

  ![Bild 32: Markera kryssrutan datorer][sap-ha-guide-figure-3021]

  _**Bild 32:** markerar du den **datorer** kryssruta_

4.  Ange klusternamnobjektet som visas i bild 31. Eftersom posten redan har skapats kan du ändra behörigheter, som visas i bild 30.

5.  Välj den **säkerhet** för resursen och ange sedan mer detaljerade behörigheter för klusternamnobjektet.

  ![Bild 33: Ange säkerhetsattribut för klusterobjektet namn på filen resursen kvorum][sap-ha-guide-figure-3022]

  _**Bild 33:** ange säkerhetsattribut för klusterobjektet namn på filen resursen kvorum_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Ange filen filresurs vittne kvorum i hanteraren för redundanskluster

1.  Öppna den kvorum inställningen guiden Konfigurera.

  ![Bild 34: Starta konfigurera inställningen guiden klusterkvorum][sap-ha-guide-figure-3023]

  _**Bild 34:** börja konfigurera inställningen guiden klusterkvorum_

2.  På den **Välj kvorumkonfigurationen** väljer **Välj kvorumvittnet**.

  ![Bild 35: Kvorumkonfigurationer som du kan välja mellan][sap-ha-guide-figure-3024]

  _**Bild 35:** kvorumkonfigurationer som du kan välja mellan_

3.  På den **Välj Kvorumvittne** väljer **konfigurera ett filresursvittne**.

  ![Bild 36: Välj filresursvittnet][sap-ha-guide-figure-3025]

  _**Bild 36:** Välj filresursvittnet_

4.  Ange UNC-sökvägen till filresursen (i vårt exempel \\domcontr 0\FSW). Om du vill se en lista över ändringar kan du markera **nästa**.

  ![Bild 37: Definiera filresursplats för resursen vittne][sap-ha-guide-figure-3026]

  _**Bild 37:** definiera filresursplats för resursen vittne_

5.  Välj önskade ändringar och välj sedan **nästa**. Du måste konfigurera om klusterkonfigurationen har enligt figur 38.  

  ![Bild 38: Bekräfta att klustret har konfigurerats om][sap-ha-guide-figure-3027]

  _**Bild 38:** bekräfta att klustret har konfigurerats om_

När du har installerat Windows-redundanskluster har förändringar som måste göras för vissa tröskelvärden för att anpassa redundans identifiering till villkoren i Azure. Parametrarna för att ändra dokumenteras i den här bloggen: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/. Förutsatt att din två virtuella datorer som bygger Windows klusterkonfigurationen för ASCS/SCS är i samma undernät, måste följande parametrar ändras till dessa värden:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Dessa inställningar har testats med kunder och tillhandahålls en bra kompromiss om du vill bli motståndskraftiga på ena sidan. Å andra sidan dessa inställningar ger snabb tillräckligt med redundans i verkliga fel vid SAP programvara eller nod och VM-fel. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resursen klusterdisken

Nu har du en fungerande konfiguration för Windows Server Failover Clustering i Azure. Men om du vill installera en SAP ASCS/SCS-instans, måste en delad disk-resurs. Du kan inte skapa de delade diskresurserna som du behöver i Azure. SIOS DataKeeper Cluster Edition är en lösning för från tredje part som du kan använda för att skapa delade diskresurserna.

Installera SIOS DataKeeper Cluster Edition för SAP ASCS/SCS resursen klusterdisken omfattar dessa aktiviteter:

- Lägga till .NET Framework 3.5
- Installera SIOS DataKeeper
- Ställa in SIOS DataKeeper

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Lägg till .NET Framework 3.5
Microsoft .NET Framework 3.5 är inte automatiskt aktiverats eller installerats på Windows Server 2012 R2. Eftersom SIOS DataKeeper kräver .NET Framework på alla noder som du installerar DataKeeper på, måste du installera .NET Framework 3.5 på gästoperativsystemet för alla virtuella datorer i klustret.

Det finns två sätt att lägga till .NET Framework 3.5:

- Använd guiden Lägg till roller och funktioner i Windows som visas i bild 39.

  ![Bild 39: Installera .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3028]

  _**Bild 39:** installera .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner_

  ![Bild 40: Installationen förloppsindikator när du installerar .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner][sap-ha-guide-figure-3029]

  _**Bild 40:** Installation förloppsindikator när du installerar .NET Framework 3.5 med hjälp av guiden Lägg till roller och funktioner_

- Använd kommandoradsverktyget dism.exe. För den här typen av installation som du behöver åtkomst till katalogen SxS på installationsmediet för Windows. Skriv följande vid en upphöjd kommandotolk:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>Installera SIOS DataKeeper

Installera SIOS DataKeeper Cluster Edition på varje nod i klustret. För att skapa virtuella delad lagring med SIOS DataKeeper, skapa en synkroniserad spegling och simulera delad klusterlagring.

Innan du installerar programvaran SIOS skapa domänanvändaren **DataKeeperSvc**.

> [!NOTE]
> Lägg till den **DataKeeperSvc** användare till den **lokal administratör** på båda klusternoderna.
>
>

Installera SIOS DataKeeper:

1.  Installera den SIOS på båda klusternoderna.

  ![SIOS installer][sap-ha-guide-figure-3030]

  ![Bild 41: Första sidan i SIOS DataKeeper-installation][sap-ha-guide-figure-3031]

  _**Bild 41:** första sidan i SIOS DataKeeper-installation_

2.  Välj i dialogrutan som visas i bild 42 **Ja**.

  ![Bild 42: DataKeeper informerar dig om att en tjänst kommer att inaktiveras][sap-ha-guide-figure-3032]

  _**Bild 42:** DataKeeper informerar dig om att en tjänst kommer att inaktiveras_

3.  I dialogrutan som visas i bild 43 rekommenderar vi att du väljer **domän eller Server konto**.

  ![Bild 43: Användarens val för SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Bild 43:** användarens val för SIOS DataKeeper_

4.  Ange domänanvändarnamn och lösenord som du skapade för SIOS DataKeeper.

  ![Bild 44: Ange användarnamn och lösenord för SIOS DataKeeper-installation][sap-ha-guide-figure-3034]

  _**Bild 44:** ange användarnamn och lösenord för SIOS DataKeeper installationen_

5.  Installera licensnyckeln för SIOS DataKeeper-instans som visas i bild 45.

  ![Bild 45: Ange licensnyckeln SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Bild 45:** Ange licensnyckeln SIOS DataKeeper_

6.  När du uppmanas starta om den virtuella datorn.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>Ställ in SIOS DataKeeper

När du installerar SIOS DataKeeper på båda noderna, måste du starta konfigurationen. Målet med konfigurationen är att ha synkron replikering mellan ytterligare diskar som är kopplade till var och en av de virtuella datorerna.

1.  Starta verktyget DataKeeper hantering och konfiguration och välj sedan **ansluta servern**. (I figur 46 det här alternativet är i en röd cirkel.)

  ![Bild 46: SIOS DataKeeper hantering och verktyg][sap-ha-guide-figure-3036]

  _**Bild 46:** SIOS DataKeeper hantering och konfiguration av verktyget_

2.  Ange namn eller TCP/IP-adressen för den första noden verktyget hantering och konfiguration ska ansluta till, och i andra steget, den andra noden.

  ![Bild 47: Infoga namnet eller TCP/IP-adressen för den första noden hanteringen och konfigurationsverktyget ska ansluta till och i andra steget, den andra noden][sap-ha-guide-figure-3037]

  _**Bild 47:** infoga namn eller TCP/IP-adress för den första noden verktyget hantering och konfiguration ska ansluta till och i andra steget, den andra noden_

3.  Skapa jobbet replikering mellan två noder.

  ![Bild 48: Skapa ett jobb för replikering][sap-ha-guide-figure-3038]

  _**Bild 48:** skapa ett jobb för replikering_

  En guide hjälper dig att skapa ett jobb för replikering.
4.  Definiera namn, TCP/IP-adress och diskvolymen till Källnoden.

  ![Bild 49: Ange namnet på jobbet replikering][sap-ha-guide-figure-3039]

  _**Bild 49:** definiera namnet på jobbet replikering_

  ![Bild 50: Definiera grundläggande data för noden som ska vara aktuella Källnoden][sap-ha-guide-figure-3040]

  _**Bild 50:** definiera grundläggande data för noden som ska vara aktuella Källnoden_

5.  Definiera namn, TCP/IP-adress och volymen av målnoden.

  ![Bild av 51: Definiera grundläggande data för noden som ska vara den aktuella målnoden][sap-ha-guide-figure-3041]

  _**Bild av 51:** definiera grundläggande data för noden som ska vara den aktuella målnoden_

6.  Definiera komprimeringsalgoritmer. I vårt exempel rekommenderar vi att du komprimera dataströmmen för replikering. Särskilt i omsynkroniseringen situationer minskar komprimeringen av replikering dataströmmen kraftigt omsynkroniseringen tid. Observera att komprimering används processor och RAM resurser för en virtuell dator. När hastigheten med komprimering ökar, ökar mängden processorresurser som används. Du kan också ändra denna inställning senare.

7.  En annan inställning som du behöver kontrollera är om replikeringen sker synkront eller asynkront. *När du skyddar SAP ASCS/SCS konfigurationer, måste du använda synkron replikering*.  

  ![Bild 52: Definiera replikeringsinformation][sap-ha-guide-figure-3042]

  _**Bild 52:** definiera replikeringsinformation_

8.  Definiera om volymen som replikeras av replikering jobbet ska representeras som en delad disk på en Windows Server Failover Clustering klusterkonfigurationen. SAP ASCS/SCS-konfiguration, markera **Ja** så att Windows-kluster ser replikerade volymen som en delad disk som kan användas som en volym i klustret.

  ![Bild 53: Välj Ja om du vill ange replikerade volymen som en klustervolym][sap-ha-guide-figure-3043]

  _**Bild 53:** Välj **Ja** ange replikerade volymen som en klustervolym_

  När volymen har skapats, verktyget DataKeeper hantering och konfiguration som visar att jobbet replikering är aktiv.

  ![Bild 54: DataKeeper synkroniserad spegling för SAP ASCS/SCS dela disken är aktiv][sap-ha-guide-figure-3044]

  _**Bild 54:** DataKeeper synkroniserad spegling för SAP ASCS/SCS dela disken är aktiv_

  Hanteraren för redundanskluster innehåller nu disk som en DataKeeper disk som visas i figur 55.

  ![Bild 55: Klusterhanterare för växling vid fel visas den disk som DataKeeper replikeras][sap-ha-guide-figure-3045]

  _**Bild 55:** Klusterhanteraren visar disken som DataKeeper replikeras_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>Installera SAP NetWeaver

Vi kommer inte beskriver DBMS-inställningarna eftersom inställningar varierar beroende på DBMS-system som du använder. Men förutsätter vi att få hög tillgänglighet med DBMS behandlas med funktioner som har stöd för olika DBMS-leverantörer för Azure. Till exempel alltid på eller databasspegling för SQL Server och Oracle Data Guard för Oracle-databaser. I scenariot som vi använder i den här artikeln inte vi lägga till mer skydd DBMS.

Det finns inga särskilda överväganden när olika DBMS tjänster interagera med den här typen av klustrad SAP ASCS/SCS konfiguration i Azure.

> [!NOTE]
> Installationsproceduren för SAP NetWeaver ABAP system, Java-system och ABAP + Java system är nästan identiska. Den viktigaste skillnaden är att ett SAP ABAP system har en ASCS-instans. SAP Java-systemet har en SCS-instans. SAP ABAP + Java-systemet har en ASCS-instans och en SCS-instans som körs i gruppen samma Microsoft för failover-kluster. Installationen skillnader för varje SAP NetWeaver installationen stack anges explicit. Du kan anta att alla delar är samma.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installera SAP med en hög tillgänglighet ASCS/SCS-instans

> [!IMPORTANT]
> Glöm inte att placera växlingsfilen på DataKeeper speglade volymer. DataKeeper stöder inte speglade volymer. Du kan lämna växlingsfilen på den tillfälliga enheten D av en virtuell Azure-dator, vilket är standard. Om den inte redan finns där, flytta växlingsfilen till enheten D: på ditt Azure-datorn.
>
>

Dessa uppgifter omfattar att installera SAP med en hög tillgänglighet ASCS/SCS-instans:

- Skapa ett virtuellt värdnamn för den klustrade instansen SAP ASCS/SCS
- Installera den första klusternoden SAP
- Ändra SAP-profilen för ASCS/SCS-instans
- Lägger till en avsökningsport
- Öppna avsökningsport för Windows-brandväggen

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Skapa ett virtuellt värdnamn för den klustrade instansen SAP ASCS/SCS

1.  Skapa en DNS-post för virtuella värdnamn för ASCS/SCS-instansen i Windows DNS-hanteraren.

  > [!IMPORTANT]
  > IP-adressen som du tilldelar virtuella värdnamn för ASCS/SCS-instansen måste vara samma som IP-adressen som du tilldelade till Azure belastningsutjämnare (**<*SID*> - lb - ascs **).  
  >
  >

  IP-adressen för den virtuella värdnamnet för SAP ASCS/SCS (**pr1-ascs-sap**) är samma som IP-adressen för Azure belastningsutjämnare (**pr1-lb-ascs**).

  ![Bild 56: Definiera DNS-posten för SAP ASCS/SCS virtuell klusternamnet och TCP/IP-adress][sap-ha-guide-figure-3046]

  _**Bild 56:** definiera DNS-posten för SAP ASCS/SCS virtuell klusternamnet och TCP/IP-adress_

2.  Ange IP-adress som tilldelats till namnet på virtuella värddatorn, välja **DNS-hanteraren** > **domän**.

  ![Bild 57: Nya virtuella namn och TCP/IP-adress för SAP ASCS/SCS klusterkonfiguration][sap-ha-guide-figure-3047]

  _**Bild 57:** nya virtuella namn och TCP/IP-adress för SAP ASCS/SCS klusterkonfiguration_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Installera den första klusternoden SAP

1.  Kör det första klustra nod alternativet på klusternoden A. Till exempel på den **pr1-ascs-0** värden.
2.  Om du vill behålla standard portar för den interna belastningsutjämnaren som Azure, väljer du:

  * **ABAP system**: **ASCS** instansen nummer **00**
  * **Java system**: **SCS** instansen nummer **01**
  * **ABAP + Java system**: **ASCS** instansen nummer **00** och **SCS** instansen nummer **01**

  Om du vill använda instans siffror än 00 för ABAP ASCS-instansen och 01 för Java SCS-instans måste du ändra den Azure interna standard belastningsutjämnare NLB regler som beskrivs i [ändra ASCS/SCS standard belastningsutjämning regler för den interna belastningsutjämnaren som Azure][sap-ha-guide-8.9].

Nästa några åtgärder beskrivs inte i dokumentationen för standard SAP-installationen.

> [!NOTE]
> I dokumentationen för installation av SAP beskriver hur du installerar den första noden i klustret ASCS/SCS.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Ändra SAP-profil för ASCS/SCS-instansen

Du måste lägga till en ny profil-parameter. Parametern profil förhindrar anslutningar mellan SAP arbetsprocesser och placera servern stängs när de är inaktiv för länge. Vi har nämnt problemet i [lägga till registerposter på båda klusternoderna för SAP ASCS/SCS-instansen][sap-ha-guide-8.11]. I det här avsnittet introduceras vi några grundläggande TCP/IP-anslutningsparametrar också två ändringar. I nästa steg behöver du sätta servern ska skicka en `keep_alive` signalerar så att anslutningarna inte nått Azure interna belastningsutjämnarens inaktiv tröskelvärdet.

Ändra SAP-profilen för ASCS/SCS-instans:

1.  Lägg till den här profilen parametern SAP ASCS/SCS instans-profilen:

  ```
  enque/encni/set_so_keepalive = true
  ```
  I vårt exempel är sökvägen:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Till exempel till SCS SAP instans profil och motsvarande sökväg:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  Om du vill tillämpa ändringarna SAP ASCS /SCS-instansen startas om.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Lägga till en avsökningsport

Använd den interna belastningsutjämnaren avsökningen funktioner för att göra hela klusterkonfigurationen arbeta med Azure belastningsutjämnare. Azure intern belastningsutjämnare distribuerar vanligtvis inkommande belastningen jämnt mellan deltagande virtuella datorer. Men fungerar det inte i vissa klusterkonfigurationer eftersom endast en instans är aktiv. Den andra instansen är passiva och kan inte ta emot någon av arbetsbelastningen. Det hjälper dig att en avsökning funktionalitet när Azure intern belastningsutjämnare tilldelar fungerar endast för en aktiv instans. Med funktionen avsökningen identifiera den interna belastningsutjämnaren vilka instanser som är aktiva och rikta endast instansen med arbetsbelastningen.

Lägg till en avsökningsport:

1.  Kontrollera aktuellt **ProbePort** inställningen genom att köra följande PowerShell-kommando. Köra den från inom någon av de virtuella datorerna i klusterkonfigurationen.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

2.  Definiera en avsökningsport. Avsökningen Standardportnumret är **0**. I vårt exempel vi använder avsökningsport **62000**.

  ![Bild 58: Avsökningsport för kluster-konfigurationen är 0 som standard][sap-ha-guide-figure-3048]

  _**Bild 58:** klustret configuration avsökningen standardporten är 0_

  Portnumret är definierad i SAP Azure Resource Manager-mallar. Du kan tilldela portnummer i PowerShell.

  Ange ett nytt ProbePort värde för den  **SAP <*SID*> IP ** klusterresurs, kör följande PowerShell-skript. Uppdatera variablerna PowerShell för din miljö. När skriptet körs, uppmanas du att starta om SAP klustergrupp för att aktivera ändringarna.

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  Clear-Host
  $SAPClusterRoleName = "SAP $SAPSID"
  $SAPIPresourceName = "SAP $SAPSID IP"
  $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
  $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
  $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
  $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
  $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
  $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
  $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

  $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

  Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

  Write-Host
  Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
  Write-Host
  Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
  Write-Host

  $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

  Write-Host

  $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

  if($ActivateChanges -eq "yes"){
  Write-Host
  Write-Host "Activating changes..." -ForegroundColor Cyan

  Write-Host
  write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
  Stop-ClusterResource -Name $SAPIPresourceName
  sleep 5

  Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
  Start-ClusterGroup -Name $SAPClusterRoleName

  Write-Host "New ProbePort parameter is active." -ForegroundColor Green
  Write-Host

  Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
  Write-Host
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
  }else
  {
  Write-Host "Changes are not activated."
  }
  ```

  När du tar den  **SAP <*SID*> ** rollen online bör du kontrollera att **ProbePort** är inställd på det nya värdet.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```

  ![Bild 59: Avsökning porten klustret när du ställer in det nya värdet][sap-ha-guide-figure-3049]

  _**Bild 59:** avsökning porten klustret när du ställer in det nya värdet_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Öppna avsökningsport för Windows-brandväggen

Du måste öppna en Windows-brandväggen avsökningsport på båda klusternoderna. Använd följande skript för att öppna en Windows-brandväggen avsökningsport. Uppdatera variablerna PowerShell för din miljö.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

Den **ProbePort** är inställd på **62000**. Nu kan du komma åt filresursen  **\\\ascsha-clsap\sapmnt** från andra värdar, exempel från **ascsha dbas**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Installera databasinstansen

För att installera databasinstansen, följer du processen som beskrivs i dokumentationen för SAP-installationen.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>Installera den andra noden i klustret

Följ stegen i installationsguiden för SAP för att installera det andra klustret.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Ändra starttypen för SAP ÄNDARE Windows service-instans

Ändra starttypen för tjänsten SAP ÄNDARE Windows att **automatiskt (förskjuten Start)** på båda klusternoderna.

![Bild 60: Ändra service-typen för instansen SAP ÄNDARE till fördröjd automatisk][sap-ha-guide-figure-3050]

_**Bild 60:** ändra tjänsttypen för SAP ÄNDARE-instans till fördröjd automatisk_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Installera den primära SAP-programservern

Installera den primära programmet Server (PROVIDERADRESSER)-instansen <*SID*> - di - 0 på den virtuella datorn som du har angett som värd för PROVIDERADRESSERNA. Det finns inga beroenden på Azure eller DataKeeper-specifika inställningar.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Installera ytterligare programservern för SAP

Installera en SAP ytterligare program Server (AAS) på alla virtuella datorer som du har angett som värd för en SAP Application Server-instans. Till exempel på <*SID*> - di - 1 till <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Installationen av ett system med hög tillgänglighet SAP NetWeaver slutförs. Fortsätt sedan med testning av redundans.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Testa redundans för SAP ASCS/SCS-instans och SIOS replikering
Det är enkelt att testa och övervaka en växling vid fel för SAP ASCS/SCS-instans och SIOS diskreplikering med hjälp av verktyg för hanteraren för redundanskluster och SIOS DataKeeper hantering och konfiguration.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS-instans som körs på en nod i klustret

Den **SAP PR1** klustergrupp körs på klusternoden A. Till exempel på **pr1-ascs-0**. Tilldela den delade diskenheten S, som är en del av den **SAP PR1** klustergrupp och som använder ASCS/SCS-instans för klustret nod A.

![Bild 61: Hanteraren för redundanskluster: det SAP < SID > klustergrupp körs på en nod i klustret][sap-ha-guide-figure-5000]

_**Bild 61:** Klusterhanterare för växling vid fel: det SAP <*SID*> klustergrupp körs på en nod i klustret_

I verktyget SIOS DataKeeper hantering och konfiguration kan du se att delad diskdata replikeras synkront i från volymen källenheten S på en nod i klustret till volymen målenheten S på klusternoden B. Till exempel har replikerats från **pr1-ascs-0 [10.0.0.40]** till **pr1-ascs-1 [10.0.0.41]**.

![Bild 62: I SIOS DataKeeper replikera lokala volymen från en nod i klustret till klusternod B][sap-ha-guide-figure-5001]

_**Bild 62:** i SIOS DataKeeper replikera lokala volymen från en nod i klustret till klusternoden B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Växling vid fel från nod A till B-nod

1.  Välj något av dessa alternativ för att påbörja en växling av SAP <*SID*> klustergrupp från klusternoden A klusternoden B:
  - Använd hanteraren för redundanskluster  
  - Använda PowerShell för Failover-kluster

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Starta om klustret nod A Windows-gästoperativsystem (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från nod A till B-nod).  
3.  Starta om klustret nod A från Azure-portalen (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från nod A till B-nod).  
4.  Starta om klustret nod A med hjälp av Azure PowerShell (Detta startar en automatisk redundans för SAP <*SID*> klustergrupp från nod A till B-nod).

  Efter växling vid fel, SAP <*SID*> klustergrupp körs på klusternoden B. Till exempel den körs på **pr1-ascs-1**.

  ![Bild 63: I Hanteraren för redundanskluster, SAP < SID > klustergrupp körs på klusternoden B][sap-ha-guide-figure-5002]

  _**Bild 63**: I Hanteraren för redundanskluster, SAP <*SID*> klustergrupp körs på klusternoden B_

  Den delade disken nu är monterad på klustret nod B. SIOS DataKeeper replikerar data från volymen källenheten S på klusternoden B till volymen målenheten S på klusternoden A. Till exempel den replikerar från **pr1-ascs-1 [10.0.0.41]** till **pr1-ascs-0 [10.0.0.40]**.

  ![Bild 64: SIOS DataKeeper replikerar den lokala volymen från klusternoden B klustra nod A][sap-ha-guide-figure-5003]

  _**Bild 64:** SIOS DataKeeper replikerar den lokala volymen från klusternoden B klustra nod A_
