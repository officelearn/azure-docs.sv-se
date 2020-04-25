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
ms.date: 04/24/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bf8c797edec143d09739272917b5781a239280ba
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147749"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Använd Azure för att vara värd för och köra SAP-arbetsbelastnings scenarier

När du använder Microsoft Azure kan du på ett tillförlitligt sätt köra dina verksamhets kritiska SAP-arbetsbelastningar och scenarier på en skalbar, kompatibel och företags beprövad plattform. Du får skalbarhet, flexibilitet och kostnads besparingar i Azure. Med det utökade partnerskapet mellan Microsoft och SAP kan du köra SAP-program över utvecklings-och testnings-och produktions scenarier i Azure och få fullständig support. Från SAP NetWeaver till SAP S/4HANA, SAP BI på Linux till Windows och SAP HANA till SQL, har vi lärt dig.

Förutom att vara värd för SAP NetWeaver-scenarier med olika DBMS i Azure kan du vara värd för andra SAP-arbetsbelastnings scenarier som SAP BI på Azure. 

Azures unikhet för SAP HANA är ett erbjudande som ställer in Azure. För att kunna vara värd för mer minne och processor resurs krävande SAP-scenarier som omfattar SAP HANA, erbjuder Azure användningen av kunddedikerat Bare Metal-maskinvara. Använd den här lösningen för att köra SAP HANA distributioner som kräver upp till 24 TB (120 TB-skalning) av minnet för S/4HANA eller annan SAP HANA arbets belastning. 

Att vara värd för SAP-arbetsbelastnings scenarier i Azure kan också skapa krav på identitets integrering och enkel inloggning. Den här situationen kan inträffa när du använder Azure Active Directory (Azure AD) för att ansluta olika SAP-komponenter och SAP-SaaS (Software-as-a-Service) eller PaaS-erbjudanden (Platform-as-a-Service). En lista över scenarier för integration och enkel inloggning med Azure AD och SAP-entiteter beskrivs och dokumenteras i avsnittet "AAD SAP Identity Integration och enkel inloggning".

## <a name="changes-to-the-sap-workload-section"></a>Ändringar i avsnittet SAP-arbetsbelastning
Ändringar av dokument i avsnittet SAP på Azure-arbetsbelastningar visas i slutet av den här artikeln. Posterna i ändrings loggen sparas i cirka 180 dagar.

## <a name="you-want-to-know"></a>Du vill veta
Om du har vissa frågor ska vi peka dig på vissa dokument eller flöden i det här avsnittet på Start sidan. Du vill veta:

- Det finns stöd för de virtuella Azure-datorer och HANA-instanser som stöds för vilka SAP-program versioner och vilka operativ system versioner som används. Läs dokumentet [vilka SAP-program som stöds för Azure-distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) för svar och processen för att hitta informationen
- Vilka SAP-distributions scenarier som stöds med virtuella Azure-datorer och HANA-stora instanser. Information om de scenarier som stöds finns i dokumenten:
    - [SAP-arbetsbelastningar i scenarier med virtuella Azure-datorer som stöds](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Scenarier som stöds för HANA stor instans](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Vilka Azure-tjänster, Azure VM-typer och Azure Storage-tjänster är tillgängliga i olika Azure-regioner, kontrollerar du de plats [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA på Azure (stora instanser)

En serie dokument vägleder dig genom SAP HANA på Azure (stora instanser) eller för korta, HANA stora instanser. Information om HANA stora instanser börjar med dokument [Översikt och arkitektur för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) och går igenom den relaterade dokumentationen i avsnittet Hana stor instans



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA på virtuella Azure-datorer
I det här avsnittet av dokumentationen beskrivs olika aspekter av SAP HANA. Som en förutsättning bör du vara bekant med huvud tjänsterna för Azure som tillhandahåller grundläggande tjänster för Azure IaaS. Så du behöver kunskap om Azure Compute, Storage och Networking. Många av dessa ämnen hanteras i SAP NetWeaver-relaterade [Azure Planning Guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Information om HANA i Azure finns i följande artiklar och deras under artiklar:

- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA hög tillgänglighet för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Säkerhets kopierings guide för SAP HANA på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP-NetWeaver distribueras på virtuella Azure-datorer
Det här avsnittet innehåller planerings-och distributions dokumentation för SAP NetWeaver och Business One på Azure. Dokumentationen fokuserar på grunderna och användningen av icke-HANA-databaser med en SAP-arbetsbelastning på Azure. Dokument och artiklar för hög tillgänglighet är också grunden för HANA hög tillgänglighet i Azure, till exempel:

- [Azures planerings guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Skydda en SAP NetWeaver program distribution med flera nivåer med hjälp av Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [SAP LaMa-anslutning för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Information om icke-HANA-databaser under en SAP-arbets belastning på Azure finns i:

- [Överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure Virtual Machines DBMS-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [IBM DB2 Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
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
- [Självstudier: Azure Active Directory-integration med SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integration med SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudie: Azure Active Directory integration med SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Din S/4HANA-miljö: Fiori starter SAML enkel inloggning med Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Information om hur du integrerar Azure-tjänster i SAP-komponenter finns i:

- [Använda SAP HANA i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery och SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Använda SAP BW-anslutningsappen i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory erbjuder dataintegrering för SAP HANA och Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Ändrings logg

- 04/24/2020: ändringar i [SAP HANA skalas ut med noden vänte läge på virtuella Azure-datorer med ANF på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse), i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med ANF på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel), [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i SLES med ANF](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) och [hög tillgänglighet för SAP NetWeaver på virtuella](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) Azure-datorer i RHEL med ANF för att lägga till information om att IP-
- 04/22/2020: ändra [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) för att ta `is-managed` bort meta-attributet från instruktionerna, eftersom det står i konflikt med att placera klustret i eller ur underhålls läge
- 04/21/2020: lade till SQL Azure DB som stöd för DBMS för SAP (Hybris) Commerce Platform 1811 och senare i artiklar [vilka SAP-program som stöds för Azure-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) och [SAP-certifieringar och konfigurationer som körs på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 04/16/2020: har lagts till SAP HANA as-DBMS som stöds för SAP (Hybris) Commerce Platform i artiklar [vilka SAP-program som stöds för Azure-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) och [SAP-certifieringar och konfigurationer som körs på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 04/13/2020: korrigera till exakta SAP ASE-release-nummer i [SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 04/07/2020: ändra i [ställa in pacemaker på SLES i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) för att klargöra Cloud-netconfig-Azure-instruktioner
- 04/06/2020: ändringar i [SAP HANA skalas ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) och i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) för att ta bort referenser till NetApp [tr-4435](https://www.netapp.com/us/media/tr-4746.pdf) (ersätts av [tr-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 03/31/2020: ändra [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) och [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) för att lägga till instruktioner för hur du anger stripe-storlek när du skapar stripe-volymer
- 03/27/2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) för att justera fil systemets monterings alternativ till NetApp TR-4746 (ta bort alternativet för Sync-montering)
- 03/26/2020: ändra [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att lägga till referens till NetApp TR-4746
- 03/26/2020: ändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [hög tillgänglighet för SAP-NetWeaver på virtuella Azure-datorer på SLES med Azure NetApp Files för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [hög tillgänglighet för NFS på virtuella Azure-datorer på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [hög tillgänglighet för SAP NETWEAVER på virtuella Azure-datorer på SLES multi-sid-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid), [hög tillgänglighet för SAP NETWEAVER på virtuella Azure-datorer på RHEL för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) och [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer i RHEL med Azure NetApp Files för](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) SAP-program för att uppdatera diagram och för tydliga instruktioner för att Azure Load Balancer-adresspoolen skapas
- 03/19/2020: större revidering av [snabb start för dokument: manuell installation av en instans SAP HANA på azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) för att [Installera SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020: ändra konfigurations inställningar [för pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) för att ta bort SBD-konfigurationsfilen som inte längre behövs
- 03/16/2020: klargörande av kolumn certifierings scenario i SAP HANA IaaS Certified Platform i [vilken SAP-program vara stöds för Azure-distributioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 03/11/2020: ändring i [SAP-arbetsbelastning i scenarier med virtuella Azure-datorer som stöds](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) för att klargöra flera databaser per instans stöd för DBMS
- 03/11/2020: ändring i [Azure Virtual Machines planering och implementering för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) som förklarar generation 1 och generation 2 virtuella datorer
- 03/10/2020: ändra i [SAP HANA lagrings konfiguration för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) för att klargöra verkliga befintliga data flödes gränser för ANF
- 03/09/2020: ändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Konfigurera pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [hög tillgänglighet för IBM DB2 LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), [hög tillgänglighet för SAP HANA på virtuella Azure-datorer med SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) och [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att uppdatera kluster resurser med Resource agent Azure-lb 
- 03/05/2020: struktur ändringar och innehålls ändringar för Azure-regioner och Azure Virtual Machines i [azure Virtual Machines planera och implementera för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) för att ändra till mer effektiv ANF
- 03/01/2020: [säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) inkludera Azure Backup-tjänsten. Minska och kondenserat innehåll i [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) och ta bort ett tredje dokument som hanterar säkerhets kopiering genom disk ögonblicks bilder. Innehållet hanteras i säkerhets kopierings guiden för SAP HANA på Azure Virtual Machines 
- 02/27/2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) och [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att justera "On failover"-kluster parametern
- 02/26/2020: ändra i [SAP HANA lagrings konfiguration för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) och klargör fil system val för Hana på Azure
- 02/26/2020: ändra i [arkitektur och scenarier med hög tillgänglighet för SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) för att inkludera länken till ha för SAP NetWeaver på virtuella Azure-datorer på RHEL multi-sid-guide
- 02/26/2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Azure VM hög tillgänglighet för SAP NetWeaver på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) och [virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) för att ta bort instruktionen att flera-sid ASCS/ers-kluster inte
- 02/26/2020: utgåva av [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på RHEL med multi-sid-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) för att lägga till en länk till SUSE multi-sid kluster guide
- 02/25/2020: ändra i [arkitektur och scenarier med hög tillgänglighet för SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) för att lägga till länkar till nya artiklar i ha
- 02/25/2020: ändring i [hög tillgänglighet för IBM DB2-LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) för att peka på dokument som beskriver åtkomst till den offentliga slut punkten med standard Azure Load Balancer
- 02/21/2020: fullständig revidering av artikeln [SAP ASE Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: ändra i [SAP HANA lagrings konfigurationen för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) för att representera ny rekommendation i stripe-storlek för/Hana/data och lägga till inställningen för i/O Scheduler
- 02/21/2020: ändringar i HANA-stora instans dokument som representerar nyligen certifierade SKU: er av S224 och S224m
- 02/21/2020: ändring av de virtuella Azure-datorerna med [hög tillgänglighet för SAP NetWeaver på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) och [virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) för att justera kluster begränsningarna för att köa Server replikering 2 Architecture (ENSA2)
- 02/20/2020: ändra [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att lägga till en länk till SUSE multi-sid-kluster guiden
- 02/13/2020: ändringar i [Azure Virtual Machines planering och implementering för SAP-NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) för att implementera länkar till nya dokument
- 02/13/2020: nya dokument [SAP-arbetsbelastningar har lagts till i scenariot för Azure Virtual Machine support](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 02/13/2020: nytt dokument som har lagts till [i SAP-programvaran stöds för Azure-distribution](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: ändring i [hög tillgänglighet för IBM DB2-LUW på virtuella Azure-datorer på Red Hat Enterprise Linux server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) för att peka på dokument som beskriver åtkomst till den offentliga slut punkten med Azures standard-belastningsutjämnare
- 02/13/2020: Lägg till de nya VM-typerna i [SAP-certifieringar och konfigurationer som körs på Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020: Lägg till nya SAP-support anteckningar [SAP-arbetsbelastningar på Azure: planering och distribution check lista](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: ändra på [virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) och [virtuella Azure-datorer med hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) för att justera kluster resurserna timeout till rekommendationerna för Red Hat-timeout
- 02/11/2020: version av [SAP HANA om migrering av stora Azure-instanser till azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: ändring i [offentlig slut punkts anslutning för virtuella datorer med Azure standard ILB i SAP ha-scenarier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) för att uppdatera exempel NSG skärm bild
- 02/03/2020: ändring i [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) och [hög tillgänglighet för SAP NW på virtuella Azure-datorer på SLES med ANF för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) för att ta bort varningen om att använda bindestreck i värdnamn för klusternoder på SLES
- 01/28/2020: ändring i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) för att justera SAP HANA kluster resursernas timeout till rekommendationerna för Red Hat-timeout
- 01/17/2020: ändringar i [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) för att ändra avsnittet om att flytta befintliga virtuella datorer till en närhets placerings grupp
- 01/17/2020: ändra i [SAP-arbetsbelastnings konfigurationer med Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) för att peka på den procedur som automatiserar mätningar av svars tider mellan Tillgänglighetszoner
- 01/16/2020: ändra i [så här installerar och konfigurerar du SAP HANA (stora instanser) på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) för att anpassa OS-versioner till Hana IaaS Hardware Directory
- 01/16/2020: ändringar i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES med multi-sid-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) för att lägga till instruktioner för SAP-system med hjälp av ENSA2 (Queue server 2 Architecture)
- 01/10/2020: ändringar i [SAP HANA skalas ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) och i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) för att lägga till instruktioner om hur du gör `nfs4_disable_idmapping` ändringar permanent.
- 01/10/2020: ändringar i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES med Azure NetApp Files för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) och i [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på RHEL med Azure NetApp Files för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) för att lägga till instruktioner för hur du monterar Azure NetApp Files NFSv4-volymer.
- 12/23/2019: version av [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SLES multi-sid-guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: version av [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: ändringar i [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) för att förenkla konfigureringen av NFS-ID-mappningen och ändra det rekommenderade primära nätverks gränssnittet för att förenkla routning.
- 11/15/2019: mindre ändringar i [hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program](high-availability-guide-suse-netapp-files.md) och [hög tillgänglighet för sap NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files för SAP-program](high-availability-guide-rhel-netapp-files.md) för att klargöra storleks begränsningar för kapacitet och ta bort instruktion som endast NFSv3-versionen stöds.
- 11/12/2019: version av [hög tillgänglighet för SAP NetWeaver på Windows med Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: ändringar i [hög tillgänglighet för SAP HANA på virtuella Azure-datorer på SUSE Linux Enterprise Server](sap-hana-high-availability.md), [konfiguration av SAP HANA system replikering på virtuella Azure-datorer (VM)](sap-hana-high-availability-rhel.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server för sap-program](high-availability-guide-suse.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NETAPP Files](high-availability-guide-suse-netapp-files.md), [azure Virtual Machines hög tillgänglighet för SAP-NetWeaver på Red Hat Enterprise Linux](high-availability-guide-rhel.md), [Azure Virtual Machines hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [hög tillgänglighet för NFS på virtuella Azure-datorer SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS på virtuella Azure-datorer i Red Hat Enterprise Linux för SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) för att rekommendera Azure standard Load Balancer  
- 11/08/2019: ändringar i [SAP-arbetsbelastnings planering och distributions check lista](sap-deployment-checklist.md) för att klargöra krypterings rekommendation  
- 11/04/2019: ändringar i [ställa in pacemaker på SUSE Linux Enterprise Server i Azure](high-availability-guide-suse-pacemaker.md) för att skapa klustret direkt med unicast-konfiguration  
- 10/29/2019: lansering av [offentlig slut punkts anslutning för Virtual Machines med Azure standard Load Balancer i SAP-scenarier med hög tillgänglighet](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: ändringar i [SAP HANA lagrings konfiguration för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) och [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) för att klargöra NFS-protokollet för/Hana/Shared volym
- 10/22/2019: ändring i [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [hög tillgänglighet för sap NetWeaver på virtuella Azure-datorer med Azure NETAPP Files för SAP-program,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)hög [tillgänglighet för NFS på virtuella azure-datorer på SUSE Linux Enterprise Server SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [installation av pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [hög tillgänglighet för IBM DB2-LUW på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)VM: ar med pacemaker och hög tillgänglighet för SUSE Linux Enterprise Server på virtuella Azure-datorer på SAP HANA med och [hög tillgänglighet för SUSE Linux Enterprise Server på](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) virtuella Azure-datorer i för Azure
- Ändrar ANF-avsnittet och rubrik avsnittet i [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: version av [SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: korrigera brutna länkar i [säkerhets kopiering och återställning](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: ändra det lägsta rekommenderade OS-värdet från SLES 12 SP3 till SLES 12 SP4 med [hög tillgänglighet för IBM DB2-LUW på virtuella Azure-datorer på SUSE Linux Enterprise Server med pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: ändringar av konfigurationer med Ultra disk Storage och introduktion av ANF i [SAP HANA Storage-konfigurationer för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: ändring av grafik för [Azure närhets placerings grupper för optimal nätverks fördröjning med SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) för att få mer klarhet
- 10/01/2019: ändra i [SAP HANA infrastruktur konfiguration och åtgärder på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) för att korrigera instruktioner kring en hög tillgänglig NFS-resurs för/Hana/Shared. 



