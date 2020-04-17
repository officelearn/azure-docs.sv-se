---
title: Komma igång med SAP på virtuella Azure-datorer | Microsoft-dokument
description: Lär dig mer om SAP-lösningar som körs på virtuella datorer (VMs) i Microsoft Azure
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
ms.date: 04/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4a361f384167fa522278b9c84a7927e938c12cb8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534813"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Använda Azure för att vara värd för och köra SAP-arbetsbelastningsscenarier

När du använder Microsoft Azure kan du på ett tillförlitligt sätt köra dina verksamhetskritiska SAP-arbetsbelastningar och scenarier på en skalbar, kompatibel och företagsbeprövad plattform. Du får skalbarhet, flexibilitet och kostnadsbesparingar för Azure. Med det utökade partnerskapet mellan Microsoft och SAP kan du köra SAP-program över utvecklings- och test- och produktionsscenarier i Azure och få fullt stöd. Från SAP NetWeaver till SAP S/4HANA, SAP BI på Linux till Windows och SAP HANA till SQL, vi har dig täckt.

Förutom att vara värd för SAP NetWeaver-scenarier med de olika DBMS på Azure kan du vara värd för andra SAP-arbetsbelastningsscenarier, till exempel SAP BI på Azure. 

Det unika med Azure för SAP HANA är ett erbjudande som skiljer Azure åt. Azure erbjuder användning av maskinvara för bare metal för att aktivera mer minne och CPU-resurskrävande SAP-scenarier som involverar SAP HANA. Använd den här lösningen för att köra SAP HANA-distributioner som kräver upp till 24 TB (120 TB utskalning) minne för S/4HANA eller annan SAP HANA-arbetsbelastning. 

Hosting SAP-arbetsbelastningsscenarier i Azure kan också skapa krav på identitetsintegrering och enkel inloggning. Den här situationen kan uppstå när du använder Azure Active Directory (Azure AD) för att ansluta olika SAP-komponenter och SAP software-as-a-service (SaaS) eller PaaS -erbjudanden (Platform-as-a-service). En lista över sådana integrations- och enkelloggningsscenarier med Azure AD- och SAP-entiteter beskrivs och dokumenteras i avsnittet "AAD SAP-identitetsintegrering och enkel inloggning".

## <a name="changes-to-the-sap-workload-section"></a>Ändringar i avsnittet SAP-arbetsbelastning
Ändringar av dokument i SAP på Azure-arbetsbelastningen visas i slutet av den här artikeln. Posterna i ändringsloggen sparas i cirka 180 dagar.

## <a name="you-want-to-know"></a>Du vill veta
Om du har specifika frågor kommer vi att peka dig på specifika dokument eller flöden i det här avsnittet på startsidan. Du vill veta:

- Vilka virtuella Azure-datorer och HANA-enheter med stor instans stöds för vilka SAP-programversioner och vilka operativsystemversioner. Läs dokumentet [Vad SAP-programvara stöds för Azure-distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) för svar och processen för att hitta informationen
- Vilka SAP-distributionsscenarier stöds med virtuella Azure-datorer och STORA HANA-instanser. Information om scenarier som stöds finns i dokumenten:
    - [SAP-arbetsbelastning på Azure-virtuell dator som stöds scenarier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Scenarier som stöds för HANA Large Instance](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Vilka Azure Services,Azure VM-typer och Azure-lagringstjänster är tillgängliga i de olika Azure-regionerna, kontrollera webbplatsen [Produkter som är tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA på Azure (stora instanser)

En serie dokument leder dig via SAP HANA på Azure (stora instanser) eller för korta, HANA Stora instanser. För information om STORA HANA-instanser börjar med dokumentet [Översikt och arkitektur för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) och går igenom den relaterade dokumentationen i avsnittet HANA Large Instance



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA på virtuella Azure-datorer
Detta avsnitt av dokumentationen täcker olika aspekter av SAP HANA. Som en förutsättning bör du känna till de viktigaste tjänsterna i Azure som tillhandahåller grundläggande tjänster för Azure IaaS. Så du behöver kunskap om Azure-beräkning, lagring och nätverk. Många av dessa ämnen hanteras i [PLANERINGSGUIDEN FÖR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)SAP NetWeaver- relaterade i Azure . 

Information om HANA på Azure finns i följande artiklar och deras underartik:

- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA hög tillgänglighet för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Reservguide för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver distribueras på virtuella Azure-datorer
I det här avsnittet visas planerings- och distributionsdokumentation för SAP NetWeaver och Business One på Azure. Dokumentationen fokuserar på grunderna och användningen av icke-HANA-databaser med en SAP-arbetsbelastning på Azure. Dokumenten och artiklarna för hög tillgänglighet är också grunden för HANA-hög tillgänglighet i Azure, till exempel:

- [Planeringsguide för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Skydda en multitier-DISTRIBUTION av SAP NetWeaver-program med hjälp av Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [SAP LaMa-anslutning för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Information om icke-HANA-databaser under en SAP-arbetsbelastning på Azure finns i:

- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure Virtual Machines DBMS-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [IBM DB2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB-, Live-cache- och content server-distribution på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Information om SAP HANA-databaser på Azure finns i avsnittet "SAP HANA på virtuella Azure-datorer".

Information om hög tillgänglighet för en SAP-arbetsbelastning på Azure finns i:

- [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Det här dokumentet pekar på flera andra arkitektur- och scenariodokument. I senare scenariodokument finns länkar till detaljerade tekniska dokument som förklarar distributionen och konfigurationen av de olika metoderna med hög tillgänglighet. De olika dokument som visar hur du upprättar och konfigurerar hög tillgänglighet för en SAP NetWeaver-arbetsbelastning täcker Linux- och Windows-operativsystem.


Information om integrering mellan Azure Active Directory (Azure AD) och SAP-tjänster och enkel inloggning finns i:

- [Självstudiekurs: Azure Active Directory-integrering med SAP Cloud för kund](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudiekurs: Azure Active Directory-integrering med SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudiekurs: Azure Active Directory-integrering med SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integration med SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integration med SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudiekurs: Azure Active Directory-integrering med SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Din S/4HANA-miljö: Fiori Launchpad SAML enkel inloggning med Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Information om integrering av Azure-tjänster i SAP-komponenter finns i:

- [Använda SAP HANA i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery och SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Använda SAP BW-anslutningsappen i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory erbjuder dataintegrering för SAP HANA och Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Ändra logg

- 2020-04-16: Lade till SAP HANA som stöds DBMS för SAP (Hybris) Commerce Platform i artiklar [Vad SAP-programvara stöds för Azure-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) och [SAP-certifieringar och konfigurationer som körs på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 2020-04-13: Korrigera exakta SAP ASE-versionsnummer i [SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 2020-04-07: Ändring i [inställning av pacemaker på SLES i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) för att förtydliga moln-netconfig-azure-instruktioner
- 2020/04/06: Ändringar i [SAP HANA-utskalning med väntenod på Azure-virtuella datorer med Azure NetApp-filer på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) och i [SAP HANA-utskalning med väntenod på Azure-virtuella datorer med Azure NetApp-filer på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) för att ta bort referenser till NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) (ersatt av [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 2020/03/31: Förändring i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) och Hög tillgänglighet för SAP [HANA på Virtuella Azure-datorer på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) för att lägga till instruktioner om hur du anger stripe-storlek när du skapar stripe-volymer
- 2020-03-27: Ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) att justera filsystemmonteringsalternativen till NetApp TR-4746 (ta bort alternativet för synkroniseringsfäste)
- 2020-03-26: Förändring i [hög tillgänglighet för SAP NetWeaver på Virtuella Azure-datorer på SLES multi-SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att lägga till referens till NetApp TR-4746
- 2020/03/26: Förändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), Hög tillgänglighet för SAP [NetWeaver på Virtuella Azure-datorer på SLES med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Hög tillgänglighet för NFS på Virtuella Azure-datorer på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-SID-guide,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på RHEL för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) och [hög tillgänglighet för SAP NetWeaver på Virtuella Azure-datorer på RHEL med Azure NetApp-filer för SAP-program för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) att uppdatera diagram och förtydliga instruktioner för azure load balancer-backend-pool skapas
- 2020-03-19: Större version av dokumentet [Snabbstart: Manuell installation av SAP HANA med en instans på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) till [installation av SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 2020-03-17: Ändring i [konfiguration av pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) för att ta bort SBD-konfigurationsinställning som inte längre är nödvändig
- 2020-03-16: Förtydligande av kolumncertifieringsscenario i SAP HANA IaaS-certifierad plattform i [Vad SAP-programvara stöds för Azure-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 2020-03-11: Ändring i [SAP-arbetsbelastning på Azure-virtuell dator som stöds scenarier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) för att klargöra flera databaser per DBMS-instansstöd
- 2020-03-11: Förändring i [Azure Virtual Machines planering och implementering för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) som förklarar virtuella generation 1- och generation 2-datorer
- 2020-03-10: Ändring i [SAP HANA Azure-konfigurationer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) för lagring av virtuella datorer för virtuella datorer för att förtydliga verkliga befintliga dataflödesgränser för ANF
- 2020-03-09: Förändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), Hög tillgänglighet för SAP [NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), Konfigurera Pacemaker på [SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), Hög tillgänglighet för IBM [Db2 LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), Hög tillgänglighet för SAP [HANA på Virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) och Hög tillgänglighet för SAP [NetWeaver på Virtuella Azure-datorer på SLES multi-SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att uppdatera klusterresurser med resursagenten azure-lb 
- 2020-03-05: Strukturändringar och innehållsändringar för Azure-regioner och virtuella Azure-datorer i [Azure Virtual Machines planering och implementering för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 2020-03-03: Förändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) att ändra till effektivare ANF-volymlayout
- 2020-03-01: Omarbetad [säkerhetskopieringsguide för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) för att inkludera Azure Backup-tjänsten. Minskat och komprimerat innehåll i [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) och tog bort ett tredje dokument som hanterar säkerhetskopiering via diskögonblicksbild. Innehåll hanteras i backupguide för SAP HANA på virtuella Azure-datorer 
- 20200-02-27: Förändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på Virtuella SLES-program för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), Hög tillgänglighet för SAP NW på Virtuella [Azure-datorer på SLES med ANF för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) och [Hög tillgänglighet för SAP NetWeaver på Azure-virtuella datorer på SLES multi-SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att justera klusterparametern "on fail" för fel
- 2020-02-26: Ändring i [SAP HANA Azure-konfigurationer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) för lagring av virtuella datorer för virtuella datorer för att förtydliga filsystemvalet för HANA på Azure
- 2020-02-26: Ändring i [arkitektur och scenarier med hög tillgänglighet för SAP för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) att inkludera länken till HA för SAP NetWeaver på virtuella Azure-datorer på RHEL multi-SID-guide
- 20200/02/26: Ändring i [hög tillgänglighet för SAP NW på Virtuella Azure-datorer på Virtuella SLES-program för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), Hög tillgänglighet för SAP NW på Virtuella [Azure-datorer på SLES med ANF för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Azure VMs hög tillgänglighet för SAP NetWeaver på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) och [Azure VMs hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp-filer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) för att ta bort uttrycket att multi-SID ASCS/ERS-kluster inte stöds
- 2020/02/26: Utgivning av [hög tillgänglighet för SAP NetWeaver på Virtuella Azure-datorer på RHEL multi-SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) för att lägga till en länk till SUSE-klusterguiden för flera SID-kluster
- 2020-02-25: Förändring i [arkitektur och scenarier med hög tillgänglighet för SAP för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) att lägga till länkar till nyare HA-artiklar
- 2020-02-25: Förändring i [hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) för att peka på dokument som beskriver åtkomst till offentlig slutpunkt med Standard Azure Load Balancer
- 2020/02/21: Fullständig ändring av artikeln [SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 2020-02-21: Ändring i [SAP HANA Azure-lagringskonfiguration för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) för virtuella datorer för att representera ny rekommendation i stripe-storlek för /hana/data och lägga till inställning för I/O-schemaläggare
- 2020-02-21: Ändringar i HANA Large Instance-dokument som representerar nycertifierade SKU:er för S224 och S224m
- 2020-02-21-02: Ändring i [Virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) och [Azure VMs hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp-filer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) för att justera klusterbegränsningarna för enqueue server replication 2-arkitektur (ENSA2)
- 2020-02-20: Förändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att lägga till en länk till SUSE-klusterguiden för flera SID-kluster
- 2020-02-13: Ändringar i [Planering och implementering av Virtuella Azure-datorer för SAP NetWeaver för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) att implementera länkar till nya dokument
- 2020-02-13: Lade till ny [SAP-arbetsbelastning för dokument på Azure-virtuell dator som stöds scenario](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 2020-02-13: Lade till nytt dokument [Vad SAP-programvara stöds för Azure-distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 2020-02-13: Förändring i [hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) för att peka på dokument som beskriver åtkomst till offentlig slutpunkt med Standard Azure Load Balancer
- 2020-02-13: Lägg till de nya [VM-typerna i SAP-certifieringar och konfigurationer som körs på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 2020-02-13: Lägg till nya [SAP-supportanteckningar sap-arbetsbelastningar på Azure: checklista för planering och distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 2020-02-13: Ändring i [Virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) och [Virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp-filer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) för att anpassa tidsgränsen för klusterresurser till red hat-timeout-rekommendationerna
- 2020/02/11: Utgivning av [SAP HANA på Azure Large Instance-migrering till virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 2020-02-02:: Ändring i [offentlig slutpunktsanslutning för virtuella datorer med Azure Standard ILB i SAP HA-scenarier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) för att uppdatera exempel NSG-skärmbild
- 20200-02-03: Ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) och Hög tillgänglighet för SAP NW på Virtuella [Azure-datorer på SLES med ANF för SAP-program för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) att ta bort varningen om hur du använder instrumentbrädan i värdnamnen för klusternoder på SLES
- 2020-01-28: Förändring i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) för att justera timeout för SAP HANA-klusterresurser till timeout-rekommendationerna för Red Hat
- 2020-01-17: Förändring i [Azure-närhetsplaceringsgrupper för optimal nätverksfördröjning med SAP-program för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) att ändra avsnittet om att flytta befintliga virtuella datorer till en närhetsplaceringsgrupp
- 2020-01-17: Ändring i [SAP-arbetsbelastningskonfigurationer med Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) för att peka på proceduren som automatiserar mätningar av svarstid mellan tillgänglighetszoner
- 2020-01-16: Ändra hur [du installerar och konfigurerar SAP HANA (stora instanser) på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) för att anpassa OS-versioner till HANA IaaS maskinvarukatalog
- 2020-01-16: Ändringar i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att lägga till instruktioner för SAP-system med hjälp av enqueue server 2-arkitektur (ENSA2)
- 2020/01/10: Ändringar i [SAP HANA-utskalning med väntenod på Azure-virtuella datorer med Azure NetApp-filer på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) och i [SAP HANA-utskalning med väntenod på Azure-virtuella datorer med Azure NetApp-filer på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) för att lägga till instruktioner om hur du gör `nfs4_disable_idmapping` ändringar permanenta.
- 2020-01-10: Ändringar i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) och i Azure Virtual Machines hög tillgänglighet för SAP [NetWeaver på RHEL med Azure NetApp-filer för SAP-program för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) att lägga till instruktioner hur du monterar Azure NetApp Files NFSv4-volymer.
- 2019-12-23: Lansering av [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES-fler SID-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 2019-12-18: Utgivning av [SAP HANA-utskalning med väntenod på Virtuella Azure-datorer med Azure NetApp-filer på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 2019-11-21: Ändringar i [SAP HANA-utskalning med väntenod på Virtuella Azure-datorer med Azure NetApp-filer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) för att förenkla konfigurationen för NFS ID-mappning och ändra det rekommenderade primära nätverksgränssnittet för att förenkla routning.
- 2019-11-15: Mindre ändringar i [hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program](high-availability-guide-suse-netapp-files.md) och hög tillgänglighet för SAP [NetWeaver på Red Hat Enterprise Linux med Azure NetApp-filer för SAP-program för](high-availability-guide-rhel-netapp-files.md) att klargöra begränsningar för kapacitetspoolstorlek och ta bort uttalande om att endast NFSv3-version stöds.
- 2019-11-12: Lansering av [hög tillgänglighet för SAP NetWeaver i Windows med Azure NetApp-filer (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 2019-11-08: Förändringar i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](sap-hana-high-availability.md), Konfigurera SAP [HANA System Replication på virtuella Azure-datorer (virtuella datorer),](sap-hana-high-availability-rhel.md) [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server för SAP-program](high-availability-guide-suse.md), Azure Virtual Machines hög tillgänglighet för SAP [NetWeaver på SUSE Linux Enterprise Server med Azure NetApp-filer](high-availability-guide-suse-netapp-files.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux](high-availability-guide-rhel.md), Azure Virtual Machines hög tillgänglighet för SAP [NetWeaver på Red Hat Enterprise Linux med Azure NetApp-filer](high-availability-guide-rhel-netapp-files.md), [Hög tillgänglighet för NFS på Virtuella Azure-datorer på SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS på Azure VIRTUELLA datorer på Red Hat Enterprise Linux för SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) att rekommendera Azure standard belastningsutjämnare  
- 2019-08-11: Ändringar i [SAP:s checklista](sap-deployment-checklist.md) för planering och distribution av SAP för att förtydliga krypteringsrekommendationen  
- 2019-04-11: Ändringar i [konfigurationen av Pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa klustret direkt med unicast-konfiguration  
- 2019-10-29: Lansering av [offentlig slutpunktsanslutning för virtuella datorer med Azure Standard Load Balancer i SAP-scenarier med hög tillgänglighet](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 2019-10-25: Ändringar i [SAP HANA Azure-konfigurationer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) för virtuell datorlagring och [SAP HANA-utskalning med väntenod på Virtuella Azure-datorer med Azure NetApp-filer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) för att förtydliga NFS-protokollet för /hana/delad volym
- 2019-10-22: Förändring i [hög tillgänglighet för SAP NetWeaver på Virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), Hög tillgänglighet för SAP [NetWeaver på Virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Hög tillgänglighet för NFS på Virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), Konfigurera Pacemaker på [SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), Hög tillgänglighet för IBM [Db2 LUW på Virtuella Azure-datorer på SUSE Linux Enterprise Server med Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)och hög tillgänglighet för SAP [HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) för Azure Load-Balancer Detection Hardening
- Ändrar ANF-avsnitt och rubrikavsnitt i [SAP HANA Azure-lagringskonfigurationer för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 2019-10-21: Utgivning av [SAP HANA-utskalning med väntenod på Virtuella Azure-datorer med Azure NetApp-filer på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 2019-10-16: Åtgärda brutna länkar i [Säkerhetskopiering och återställning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 2019-10-16: Ändra det minsta rekommenderade operativsystemet från SLES 12 SP3 till SLES 12 SP4 i [hög tillgänglighet för IBM Db2 LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 2019-10-11: Ändringar i Ultra-disklagringskonfigurationer och introduktion av ANF i [SAP HANA Azure-lagringskonfigurationer för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 2019-01-01: Ändring i grafik av [Azure-närhetsplaceringsgrupper för optimal nätverksfördröjning med SAP-program för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) att få mer klarhet
- 2019-01-01: Förändring i [SAP HANA-infrastrukturkonfigurationer och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) för att korrigera satser runt högtillgänglig NFS-resurs för /hana/shared. 



