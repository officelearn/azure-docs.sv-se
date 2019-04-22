---
title: Kom igång med SAP på Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om SAP-lösningar som körs på virtuella datorer (VM) i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c22715434693abd5cebdc5ffd196a0e39b227d1b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698521"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Använd Azure för att hantera och köra SAP-arbetsbelastningsscenarier

När du använder Microsoft Azure kan köra du tillförlitligt dina verksamhetskritiska SAP-arbetsbelastningar och scenarier på en skalbar, kompatibel och företagsklass plattform. Du får skalbarhet, flexibilitet och kostnadseffektiviteten med Azure. Med det utökade partnerskapet mellan Microsoft och SAP kan du köra SAP-program i olika scenarion för utveckling och testning och produktion i Azure och få fullständig support. Från SAP NetWeaver till SAP S/4HANA, SAP BI på Linux till Windows- och SAP HANA till SQL, har vi något för dig.

Du kan ha andra SAP-arbetsbelastningsscenarier som SAP BI på Azure förutom som är värd för SAP NetWeaver-scenarier med olika DBMS på Azure. 

Unikhet av Azure för SAP HANA är ett erbjudande som anger Azure ifrån varandra. Om du vill aktivera som är värd för mer minne och CPU-resurs-krävande SAP scenarier för SAP HANA, erbjuder Azure användningen av kund – dedikerad maskinvara utan operativsystem. Använd den här lösningen för att köra SAP HANA-distributioner som kräver upp till 24 TB (120 TB skalbar) minne för S/4HANA eller andra SAP HANA-arbetsbelastningar. 

Som är värd för SAP-arbetsbelastningsscenarier i Azure kan också skapa krav identitetsintegrering och enkel inloggning. Den här situationen kan uppstå när du använder Azure Active Directory (Azure AD) för att koppla samman olika komponenter i SAP och SAP software-as-a-service (SaaS) eller plattform som tjänst (PaaS)-erbjudanden. En lista över sådant integrering och enkel inloggning med Azure AD och SAP-entiteter som beskrivs och beskrivs i avsnittet ”AAD SAP-identitetsintegrering och enkel inloggning”.

## <a name="latest-changes"></a>Senaste ändringarna

- Versionen av [Azure HANA stora instanser styra via Azure portal](hana-li-portal.md)

- Versionen av [hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server med Azure NetApp-filer för SAP-program](high-availability-guide-suse-netapp-files.md)

- Information på **Linux OS parametern net.ipv4.tcp_timestamps** inställningar tillsammans med en Azure-belastningsutjämnare

- Versionen av [konfigurationer för SAP-arbetsbelastning med tillgänglighetszoner i Azure](sap-ha-availability-zones.md)

- Versionen av [SAP arbetsbelastning planerings- och Checklista](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA på Azure (stora instanser)

En serie dokument leder dig genom SAP HANA på Azure (stora instanser) eller för kort, stora HANA-instanser. Information på följande områden med stora HANA-instanser finns i:

- [Översikt över SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Arkitektur för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktur och anslutning till SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installera SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Hög tillgänglighet och katastrofåterställning återställning av SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Felsöka och övervaka SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Nästa steg:

- Läs [översikt och arkitektur för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA på Azure virtual machines
Det här avsnittet av dokumentationen beskrivs olika aspekter av SAP HANA. Som ett krav bör du känna till de viktigaste tjänster i Azure som tillhandahåller grundläggande tjänster med Azure IaaS. Därför måste du känna till Azure-beräkning, lagring och nätverk. Många av dessa ämnen hanteras i SAP NetWeaver-relaterade [Azure Planeringsguiden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Information om HANA på Azure finns i följande artiklar och deras subarticles:

- [Snabbstart: Manuell installation av en instans SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Distribuera SAP S/4HANA eller BW/4HANA på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA, hög tillgänglighet för Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA-tillgänglighet inom en Azure-region](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Tillgänglighet för SAP HANA i Azure-regioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Hög tillgänglighet för SAP HANA på Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Säkerhetskopieringsguide för SAP HANA på Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver distribueras på Azure virtual machines
Det här avsnittet innehåller dokumentation för planering och distribution för SAP NetWeaver och Business One på Azure. Dokumentationen handlar om grunderna och användning av icke-HANA-databaser med en SAP-arbetsbelastningar på Azure. Dokument och artiklar för hög tillgänglighet finns också grunden för HANA, hög tillgänglighet i Azure, till exempel:

- [SAP Business One på Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Kör SAP NetWeaver på SUSE Linux-datorer för Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure virtuella datorer, planering och implementering av SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Skydda en skikt SAP NetWeaver-programdistribution med hjälp av Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [SAP LaMa-anslutning för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Information om icke-HANA-databaser under en SAP-arbetsbelastningar på Azure finns i:

- [Överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure virtuella datorer DBMS-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [DBMS-distribution för SAP-arbetsbelastning för IBM DB2 på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, Live-Cache och Content Server distribution på virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Information om SAP HANA-databaser på Azure finns i avsnittet ”SAP HANA på Azure virtual machines”.

Information om hög tillgänglighet för en SAP-arbetsbelastningar på Azure finns i:

- [Azure virtuella datorer hög tillgänglighet för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Det här dokumentet pekar på olika andra arkitektur och scenario dokument. I senare scenariot dokument finns länkar till detaljerad teknisk dokument som beskriver distributionen och konfigurationen av de olika metoderna för hög tillgänglighet. De olika dokument som visar hur du etablera och konfigurera hög tillgänglighet för en SAP NetWeaver-arbetsbelastning täcker Linux och Windows-operativsystem.


Mer information om integrering mellan Azure Active Directory (Azure AD) och SAP-tjänster och enkel inloggning, se:

- [Självstudier: Azure Active Directory-integrering med SAP-moln för kund](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med identitetsautentisering för SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med SAP-Molnplattform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Din miljö för S/4HANA: Fiori Launchpad SAML enkel inloggning med Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Information om integrering av Azure-tjänster i SAP-komponenter finns i:

- [Använda SAP HANA i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery och SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Använda SAP BW-anslutningsappen i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory erbjuder dataintegrering för SAP HANA och Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




