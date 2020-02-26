---
title: Microsoft Azure certifieringar för SAP | Microsoft Docs
description: Uppdaterad lista över aktuella konfigurationer och certifieringar av SAP på Azure-plattformen.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2020
ms.author: juergent
ms.custom: ''
ms.openlocfilehash: 43fc2e9369b9d11b1ac0205beddea01b633fb633
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598382"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-certifieringar och konfigurationer som körs på Microsoft Azure

SAP och Microsoft har en lång historik för att samar beta i ett starkt partnerskap som har ömsesidiga förmåner för sina kunder. Microsoft uppdaterar ständigt sin plattform och skickar in ny certifierings information till SAP för att säkerställa att Microsoft Azure är den bästa plattformen för att köra dina SAP-arbetsbelastningar. I följande tabeller beskrivs Azure-konfigurationer som stöds och en lista över växande SAP-certifieringar. 

## <a name="sap-hana-certifications"></a>SAP HANA certifieringar
Reference

- [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) för SAP HANA stöd för interna virtuella Azure-datorer och Hana-stora instanser.

| SAP-produkt | Stödda operativsystem | Azure-erbjudanden |
| --- | --- | --- |
| SAP HANA Developer Edition (inklusive HANA-klientprogramvaran som består av SQLODBC, endast ODBO-Windows, ODBC, JDBC-drivrutiner, HANA Studio och HANA-databas) | Red Hat Enterprise Linux, SUSE Linux Enterprise | VM-serien i D-serien |
| Företag ett på HANA | SUSE Linux Enterprise | DS14_v2, M32ts, M32ls, M64ls, M64s <br /> [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrollerad tillgänglighet för GS5. Fullständigt stöd för M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, M416s_v2, M416ms_v2, <br /> SAP HANA på Azure (stora instanser) [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| Paket på HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA på Azure (stora instanser) [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| HANA Enterprise för BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2, M416ms_v2, SAP HANA på Azure (stora instanser) [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2, M208ms_v2, <br /> M416s_v2 M416ms_v2 SAP HANA på Azure (stora instanser) <br /> [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) |

Tänk på att SAP använder termen "klustring" i [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) som synonymer för "Scale-Out" och inte för kluster med hög tillgänglighet

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-certifieringar
Microsoft Azure är certifierat för följande SAP-produkter, med fullständig support från Microsoft och SAP.
Reference

- [1928533 – SAP-program på Azure: produkter och typer av virtuella Azure-datorer som stöds](https://launchpad.support.sap.com/#/notes/1928533) för alla SAP NetWeaver-baserade program, inklusive SAP Trex, SAP LIVECACHE och SAP Content Server. Och alla databaser, exklusive SAP HANA.


| SAP-produkt | Gästoperativsystem | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| SAP Business Suite-programvara | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (endast Windows och Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 till E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP Business all-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (endast Windows och Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 till E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP BusinessObjects BI | Windows |Saknas |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 till E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (endast Windows och Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3, D2as_v4 D64as_v4, E2s_v3 E64s_v3, E2as_v4 till E64as_v4, M64s, M64ms, M128s, M128ms, M64ls, M32ls, M32ts, M208s_v2 , M208ms_v2, M416s_v2, M416ms_v2 |

## <a name="other-sap-workload-supported-on-azure"></a>Andra SAP-arbetsbelastningar som stöds i Azure

| SAP-produkt | Gästoperativsystem | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| SAP Business One på SQL Server | Windows  | SQL Server | Alla NetWeaver-certifierade VM-typer<br /> [SAP-anteckning #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BPC 10,01 MS SP08 | Windows och Linux | | Alla NetWeaver-certifierade VM-typer<br /> SAP-anteckning #2451795 |
| SAP Business Objects BI-plattform | Windows och Linux | | SAP-anteckning #2145537 |
| SAP Data Services 4,2 | | | SAP-anteckning #2288344 |
| SAP Hybris Commerce-plattform 5. x och 6. x | Windows | SQL Server, Oracle | Alla NetWeaver-certifierade VM-typer<br /> [Hybris wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
