---
title: Kom igång med SAP på virtuella Azure-datorer | Microsoft Docs
description: Lär dig mer om SAP-lösningar som körs på virtuella datorer (VM) i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 777fe1a62002a765460617cd69f3d518d78ed331
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150879"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Använd Azure för att vara värd för och köra SAP-arbetsbelastnings scenarier

När du använder Microsoft Azure kan du på ett tillförlitligt sätt köra dina verksamhets kritiska SAP-arbetsbelastningar och scenarier på en skalbar, kompatibel och företags beprövad plattform. Du får skalbarhet, flexibilitet och kostnads besparingar i Azure. Med det utökade partnerskapet mellan Microsoft och SAP kan du köra SAP-program över utvecklings-och testnings-och produktions scenarier i Azure och få fullständig support. Från SAP NetWeaver till SAP S/4HANA, SAP BI på Linux till Windows och SAP HANA till SQL, har vi lärt dig.

Förutom att vara värd för SAP NetWeaver-scenarier med olika DBMS i Azure kan du vara värd för andra SAP-arbetsbelastnings scenarier som SAP BI på Azure. 

Azures unikhet för SAP HANA är ett erbjudande som ställer in Azure. För att kunna vara värd för mer minne och processor resurs krävande SAP-scenarier som omfattar SAP HANA, erbjuder Azure användningen av kunddedikerat Bare Metal-maskinvara. Använd den här lösningen för att köra SAP HANA distributioner som kräver upp till 24 TB (120 TB-skalning) av minnet för S/4HANA eller annan SAP HANA arbets belastning. 

Att vara värd för SAP-arbetsbelastnings scenarier i Azure kan också skapa krav på identitets integrering och enkel inloggning. Den här situationen kan inträffa när du använder Azure Active Directory (Azure AD) för att ansluta olika SAP-komponenter och SAP-SaaS (Software-as-a-Service) eller PaaS-erbjudanden (Platform-as-a-Service). En lista över scenarier för integration och enkel inloggning med Azure AD och SAP-entiteter beskrivs och dokumenteras i avsnittet "AAD SAP Identity Integration och enkel inloggning".

## <a name="changes-to-the-sap-workload-section"></a>Ändringar i avsnittet SAP-arbetsbelastning
Ändringar av dokument i avsnittet SAP på Azure-arbetsbelastningar visas i slutet av den här artikeln.


## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA på Azure (stora instanser)

En serie dokument vägleder dig genom SAP HANA på Azure (stora instanser) eller för korta, HANA stora instanser. För information om följande områden i HANA-stora instanser, se:

- [Översikt över SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Arkitektur för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktur och anslutning till SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installera SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Hög tillgänglighet och haveri beredskap för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Felsöka och övervaka SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Nästa steg:

- Läs [Översikt och arkitektur för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA på virtuella Azure-datorer
I det här avsnittet av dokumentationen beskrivs olika aspekter av SAP HANA. Som en förutsättning bör du vara bekant med huvud tjänsterna för Azure som tillhandahåller grundläggande tjänster för Azure IaaS. Så du behöver kunskap om Azure Compute, Storage och Networking. Många av dessa ämnen hanteras i SAP NetWeaver-relaterade [Azure Planning Guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Information om HANA i Azure finns i följande artiklar och deras under artiklar:

- [Snabb start: manuell installation av en instans SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Distribuera SAP S/4HANA eller BW/4HANA på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA hög tillgänglighet för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA tillgänglighet inom en Azure-region](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [SAP HANA tillgänglighet i Azure-regioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Säkerhets kopierings guide för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA säkerhets kopiering baserat på lagrings ögonblicks bilder](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP-NetWeaver distribueras på virtuella Azure-datorer
Det här avsnittet innehåller planerings-och distributions dokumentation för SAP NetWeaver och Business One på Azure. Dokumentationen fokuserar på grunderna och användningen av icke-HANA-databaser med en SAP-arbetsbelastning på Azure. Dokument och artiklar för hög tillgänglighet är också grunden för HANA hög tillgänglighet i Azure, till exempel:

- [SAP Business One på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Distribuera SAP IDE: er EHP7 SP3 för SAP ERP 6,0 på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Kör SAP NetWeaver på Microsoft Azure SUSE Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure Virtual Machines planera och implementera SAP-NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Skydda en SAP NetWeaver program distribution med flera nivåer med hjälp av Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [SAP LaMa-anslutning för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Information om icke-HANA-databaser under en SAP-arbets belastning på Azure finns i:

- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure Virtual Machines DBMS-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [DBMS-distribution för SAP-arbetsbelastning för IBM DB2 på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, Live cache och Content Server-distribution på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Information om SAP HANA databaser på Azure finns i avsnittet "SAP HANA på Azure Virtual Machines".

Information om hög tillgänglighet för en SAP-arbetsbelastning i Azure finns i:

- [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Det här dokumentet pekar på olika dokument för arkitektur och scenarier. I senare scenario dokument finns länkar till detaljerade tekniska dokument som förklarar distribution och konfiguration av olika metoder för hög tillgänglighet. De olika dokument som visar hur du upprättar och konfigurerar hög tillgänglighet för en SAP NetWeaver-arbetsbelastnings lucka Linux och Windows-operativsystem.


Information om integration mellan Azure Active Directory (Azure AD) och SAP-tjänster och enkel inloggning finns i:

- [Självstudie: Azure Active Directory integration med SAP Cloud för kunden](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integration med SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integration med SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integrering med SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integration med SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integration med SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Din S/4HANA-miljö: Fiori starter SAML enkel inloggning med Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Information om hur du integrerar Azure-tjänster i SAP-komponenter finns i:

- [Använda SAP HANA i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery och SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Använda SAP BW-anslutningsappen i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory erbjuder dataintegrering för SAP HANA och Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Ändringslogg
- 11/15/2019: mindre ändringar i [hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](high-availability-guide-suse-netapp-files.md) och [hög tillgänglighet för sap NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program](high-availability-guide-rhel-netapp-files.md) för att klargöra storleks begränsningar för kapacitet och ta bort instruktion som endast NFSv3-versionen stöds.
- 11/12/2019: version av [hög tillgänglighet för SAP NetWeaver på Windows med Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: ändringar i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](sap-hana-high-availability.md), [Konfigurera SAP HANA systemreplikering på Azure Virtual Machines (VM)](sap-hana-high-availability-rhel.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server för sap-program](high-availability-guide-suse.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NETAPP Files](high-availability-guide-suse-netapp-files.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux ](high-availability-guide-rhel.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [hög tillgänglighet för NFS på virtuella azure-datorer på SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS på virtuella Azure-datorer i Red Hat Enterprise Linux för SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) för att rekommendera Azure standard Load Balancer  
- 11/08/2019: ändringar i [SAP-arbetsbelastnings planering och distributions check lista](sap-deployment-checklist.md) för att klargöra krypterings rekommendation  
- 11/04/2019: ändringar i [ställa in pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa klustret direkt med unicast-konfiguration  
- 10/29/2019: lansering av [offentlig slut punkts anslutning för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: ändringar i [SAP HANA lagrings konfiguration för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) och [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) för att klargöra NFS-protokollet för/Hana/Shared volym
- 10/22/2019: ändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [hög tillgänglighet för IBM DB2 LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), och [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) för identifierings härdning av Azure-belastnings utjämning
- Ändrar ANF-avsnittet och rubrik avsnittet i [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: version av [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: korrigera brutna länkar i [säkerhets kopiering och återställning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: ändra det lägsta rekommenderade OS-värdet från SLES 12 SP3 till SLES 12 SP4 med [hög tillgänglighet för IBM DB2-LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: ändringar av konfigurationer med Ultra disk Storage och introduktion av ANF i [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: ändring av grafik för [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) för att få mer klarhet
- 10/01/2019: ändra i [SAP HANA infrastruktur konfiguration och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) för att korrigera instruktioner kring en hög tillgänglig NFS-resurs för/Hana/Shared. 
- 09/28/2019: ändring av [pacemaker på Red Hat Enterprise Linux i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) för att klargöra SBD som en avgränsnings funktion stöds inte på RHEL-kluster  
- 09/17/2019: ändring i NetWeaver planering och distributions guide för att förena villkoren kring VM-tillägget för SAP  
- 08/22/2019: ändringar i [ställa in pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) för att uppdatera URL: erna för att skapa en anpassad roll  
- 08/16/2019: ändringar i [ställa in pacemaker på Red Hat Enterprise Linux i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) för att påminna kunder om att uppdatera åtgärderna i den anpassade rollen, om de uppdateras till den nya versionen av Azure stängsel-agenten  
- 08/15/2019: ändringar i [SAP HANA lagrings konfiguration för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) som motsvarar allmän tillgänglighet för Ultra disk (tidigare Ultra SSD)
- 08/01/2019: ändringar i [konfiguration av pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) för att integrera ändringar specifikt för SLES 15 
- 07/23/2019: ändringar i [kluster en SAP ASCS/SCS-instans i ett Windows-redundanskluster med hjälp av en fil resurs i Azure](sap-high-availability-guide-wsfc-file-share.md) för att återspegla stöd för lagrings utrymme direkt av Azure Site Recovery Services
- 07/14/2019: versioner av [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](sap-proximity-placement-scenarios.md)
- 07/11/2019: ändringar i olika dokument täcker HANA-stora instanser för att täcka revidering 4 av HANA-stora instanser
- 07/09/2019: version av ny guide för [IBM DB2 hadr i Red Hat Enterprise Server](high-availability-guide-rhel-ibm-db2-luw.md)
- 06/13/2019: version av [hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program](high-availability-guide-rhel-netapp-files.md)


