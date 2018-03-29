---
title: Microsoft Azure certifieringar för SAP | Microsoft Docs
description: Uppdaterad lista över aktuella konfigurationer och SAP-certifieringar på Azure-plattformen.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: 96a3b6f8f8a03019f0446aa7ba42f53298e21009
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-certifiering och konfigurationer som körs på Microsoft Azure

SAP och Microsoft har en lång historik över arbetar tillsammans i ett starkt partnerskap som har ömsesidigt fördelar för sina kunder. Microsoft uppdaterar kontinuerligt dess plattform och skicka ny certifiering information till SAP för att säkerställa att Microsoft Azure är den bästa plattformen att köra arbetsbelastningar för SAP. Följande tabeller beskriver vår lista över växande certifieringar och konfigurationer som stöds. 

## <a name="sap-hana-certifications"></a>SAP HANA-certifieringar
Referenser:

- [SAP-kommentar 2316233 - SAP HANA i Microsoft Azure (stora instanser)](https://launchpad.support.sap.com/#/notes/2316233) täcker HANA stora instanser för SAP HANA-stöd.
- [SAP HANA certifierade IaaS plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Amazon%20Web%20Services%2CMicrosoft%20Azure) för SAP HANA-stöd för interna virtuella Azure-datorer.

| SAP-produkt | OS som stöds | Azure-erbjudanden |
| --- | --- | --- |
| SAP HANA Developer Edition (inklusive klientprogrammet HANA består av SQLODBC, ODBO Windows endast, ODBC, JDBC-drivrutiner, HANA studio och HANA-databas) | Red Hat Enterprise Linux, SUSE Linux Enterprise | D-serien VM-familjen |
| En på HANA för företag | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrollerade tillgänglighet för GS5, SAP HANA i Azure (stora instanser) |
| Programsvit på HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 för enskild nod distributioner för icke-produktion scenarier SAP HANA i Azure (stora instanser) |
| HANA Enterprise för BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 för enskild nod distributioner SAP HANA i Azure (stora instanser) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 för enskild nod distributioner SAP HANA i Azure (stora instanser) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-certifieringar
Microsoft Azure har certifierats för följande SAP-produkter, med fullständig support från Microsoft och SAP.
Referenser:

- [1928533 - SAP-program i Azure: produkter och Virtuella Azure-typer](https://launchpad.support.sap.com/#/notes/1928533) för alla SAP NetWeaver baserat program, inklusive SAP TREX, SAP LiveCache och SAP innehållsserver. Och alla databaser, exklusive SAP HANA.


| SAP-produkt | Gäst-OS | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| SAP Business Suite-program |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11 D11 till D14 DS11 till DS14 DS11_v2 till DS15_v2 GS1 till GS5 D2s_v3 till D64s_v3 E2s_v3 till E64s_v3 M64s till M128ms |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11 D11 till D14 DS11 till DS14 DS11_v2 till DS15_v2 GS1 till GS5 D2s_v3 till D64s_v3 E2s_v3 till E64s_v3 M64s till M128ms |
| SAP BusinessObjects BI |Windows |Gäller inte |A5 till A11 D11 till D14 DS11 till DS14 DS11_v2 till DS15_v2 GS1 till GS5 D2s_v3 till D64s_v3 E2s_v3 till E64s_v3 M64s till M128ms |
| SAP NetWeaver |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11 D11 till D14 DS11 till DS14 DS11_v2 till DS15_v2 GS1 till GS5 D2s_v3 till D64s_v3 E2s_v3 till E64s_v3 M64s till M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Andra SAP arbetsbelastning som stöds på Azure

| SAP-produkt | Gäst-OS | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| SAP Business en på SQL-Server | Windows  | SQL Server | Alla NetWeaver certifierade VM-typer |
| SAP BITAR 10.01 MS SP08 | Windows | | Alla typer av NetWeaver certifierade VM<br /> SAP-kommentar #2451795 |
| SAP Business objekt BI plattform | Windows | | SAP-kommentar #2145537 |
| SAP Data Services 4.2 | | | SAP-kommentar #2288344 |
| SAP Hybris Commerce-plattformen 5.x och 6.x | Windows | SQL Server, Oracle | Alla NetWeaver certifierade VM-typer<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
