---
title: Komma igång med SAP på Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om SAP-lösningar som körs på virtuella datorer (VM) i Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cf6291cb12366c4f710092c1b36c8cb0b8c14fb
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578403"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Använda Azure vid som är värd för och köra SAP-arbetsbelastningsscenarier

Genom att välja Microsoft Azure som din SAP redo cloud partner, kan du tillförlitligt köra verksamhetskritiska SAP arbetsbelastningar och scenarier på en skalbar, kompatibel och företagsklass plattform.  Få skalbarheten, flexibiliteten och kostnadseffektiviteten med Azure. Med det utökade partnerskapet mellan Microsoft och SAP kan du köra SAP-program i olika scenarion för utveckling/testning och produktion i Azure – och få fullständig support. Vi ger dig support från SAP NetWeaver till SAP S4/HANA, SAP BI, Linux till Windows, SAP HANA till SQL.

Förutom som är värd för SAP NetWeaver-scenarier med olika DBMS på Azure, och du kan ha olika andra SAP-arbetsbelastningsscenarier som SAP BI på Azure. Dokumentation om distribution av SAP NetWeaver på Azures inbyggda virtuella datorer finns i avsnittet ”SAP NetWeaver på Azure Virtual Machines”.

Unikhet av Azure för SAP HANA är ett unikt erbjudande som skiljer Azure från konkurrenterna. Dedikerad maskinvara utan operativsystem i syfte att köra SAP HANA-distributioner som kräver upp till 20 TB (60 TB skalbar) minne för för att aktivera som är värd för mer minne och processorresurser krävande SAP-scenarier som omfattar SAP HANA, Azure-erbjudanden användningen av kunden S/4HANA eller andra SAP HANA-arbetsbelastningar. Den här unika Azure-lösning för SAP HANA på Azure (stora instanser) kan du köra SAP HANA på den dedikerade maskinvaran utan operativsystem med SAP-programnivån eller arbetsbelastning mitten kod lager som finns i interna Azure-datorer. Den här lösningen dokumenteras i flera dokument i avsnittet ”SAP HANA på Azure (stora instanser)”.   

Som är värd för SAP-arbetsbelastningsscenarier i Azure kan också skapa krav för identitetsintegrering och enkel inloggning med hjälp av Azure Activity Directory olika SAP-komponenter och SAP SaaS eller erbjuder PaaS. En lista över sådant integrering och Single-Sign-On-scenarier med Azure Active Directory (AAD) och SAP-entiteter som beskrivs och beskrivs i avsnittet ”AAD SAP-Identitetsintegrering och enkel inloggning”.

## <a name="latest-changes"></a>Senaste ändringarna

Dokumentation om runt SAP HANA dynamisk lagringsnivåer för virtuella Azure-datorer

- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#sap-hana-dynamic-tiering-20-for-azure-virtual-machines)

Dokumentation om runt SAP HANA skala ut på Azure VM M128s har lagts till:

- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)
- [SAP HANA-tillgänglighet inom en Azure-region](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)


## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA på SAP HANA på Azure (stora instanser)

En serie med dokumentation leder dig genom SAP HANA på Azure (stora instanser) eller i korta stora HANA-instanser. Dokumenten omfatta listade områden med stora HANA-instanser:

- [Översikt över SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Arkitektur för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktur och anslutning till SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installera SAP HANA på SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Hög tillgänglighet och Haveriberedskap för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Felsökning och övervakning av SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Nästa steg:

- Läs [översikt och arkitektur för SAP HANA på Azure (stora instanser)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA på Azure Virtual Machines
Det här avsnittet av dokumentationen beskrivs olika aspekter av SAP HANA. Som ett krav bör du känna till de viktigaste tjänster i Azure som tillhandahåller grundläggande tjänster med Azure IaaS, så huvudsakligen kunskap om Azure-beräkning, lagring och nätverk. Dessa avsnitt mycket hanteras i den SAP NetWeaver relaterade [Azure Planning Guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

I dokumentationen som är specifika för HANA på Azure består av den här listan med artiklar och deras underordnade artiklar:

- [Snabbstart: Manuell installation av en instans SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Distribuera SAP S/4HANA eller BW/4HANA på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfigurationer och åtgärder för SAP HANA i Azure-infrastrukturer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA, hög tillgänglighet för Azure-datorer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [SAP HANA-tillgänglighet inom en Azure-region](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Tillgänglighet för SAP HANA i Azure-regioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Hög tillgänglighet för SAP HANA på Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Säkerhetskopieringsguide för SAP HANA på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA Azure Backup på filnivå](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA-säkerhetskopia baserat på ögonblicksbilder av lagring](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver distribueras på Azure Virtual Machines
I det här avsnittet finns dokumentation för planering och distribution för SAP NetWeaver och Business One på Azure. I dokumentationen i det här kapitlet fokuserar huvudsakligen runt grunderna och användning av icke-HANA-databaser med SAP-arbetsbelastningar på Azure. De är grunden för HANA, hög tillgänglighet i Azure samt de dokument och artiklar för hög tillgänglighet. listan över artiklar som:

- [SAP Business One på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Distribuera SAP IDES EHP7 SP3 för SAP ERP 6.0 på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Köra SAP NetWeaver på SUSE Linux-datorer för Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure virtuella datorer, planering och implementering av SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Skydda en programdistribution för flera nivåer SAP NetWeaver med hjälp av Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [SAP LaMa-anslutning för Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Om icke-HANA-databaser under SAP-arbetsbelastningar på azure som listan dokument:

- [Överväganden för distribution av Azure virtuella datorer DBMS för SAP-arbetsbelastningar](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server Azure virtuella datorer DBMS-distribution för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [DBMS-distribution för SAP-arbetsbelastning för Oracle på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [DBMS-distribution för SAP-arbetsbelastning för IBM DB2 på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [DBMS-distribution för SAP-arbetsbelastning för SAP ASE på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, liveCache och innehållsserver distribution på Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Kontrollera avsnittet SAP HANA på Azure Virtual Machines för SAP HANA-databaser på Azure.

För hög tillgänglighet för SAP-arbetsbelastningar på Azure finns i följande dokument:

- [Azure virtuella datorer hög tillgänglighet för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)
- [Arkitektur för hög tillgänglighet och scenarier för SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Använda Azure-infrastrukturen omstart av virtuella datorer för att uppnå ”högre tillgänglighet” för ett SAP-system](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-higher-availability-architecture-scenarios)
- [Klustret ett SAP ASCS/SCS-instans på en Windows-redundanskluster med hjälp av en delad klusterdisk i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Klustra SAP ASCS/SCS-instans i ett redundanskluster i Windows med hjälp av en filresurs i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Förbereda Azure-infrastrukturen för SAP hög tillgänglighet med hjälp av en Windows-redundanskluster och delad disk för SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- [Förbereda Azure-infrastrukturen för hög tillgänglighet för SAP genom att använda en Windows failover-kluster och filresursen för SAP ASCS/SCS-instanser](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- [Hög tillgänglighet för NFS på virtuella Azure-datorer på SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [Konfigurera Pacemaker på SUSE Linux Enterprise Server i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
- [https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk)
- [Installera SAP NetWeaver hög tillgänglighet på en Windows failover-kluster och filresursen för SAP ASCS/SCS-instanser i Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share)
- [Hög tillgänglighet för SAP NetWeaver på virtuella Azure-datorer på SUSE Linux Enterprise Server för SAP-program](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [SAP ASCS/SCS-instans – flera SÄKERHETSIDENTIFIERARE hög tillgänglighet med Windows Server Failover Clustering och delad disk på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
- [SAP ASCS/SCS-instans – flera SÄKERHETSIDENTIFIERARE hög tillgänglighet med Windows Server Failover Clustering och filresursen på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)


Integreringen mellan Azure Active Directory (AAD) och SAP-tjänster och Single-Sign-On, listar dokument som:

- [Självstudier: Azure Active Directory-integration med SAP-moln för kund](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med identitetsautentisering för SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med SAP-Molnplattform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Självstudier: Azure Active Directory-integrering med SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Din miljö för S/4HANA – Fiori Launchpad SAML enkel inloggning med Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

För integrering av Azure-tjänster i SAP-komponenter ut listan över dokument som:

- [Använda SAP HANA i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery och SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Använd anslutningstjänsten SAP BW i Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory erbjuder SAP HANA- och Business Warehouse dataintegrering](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




