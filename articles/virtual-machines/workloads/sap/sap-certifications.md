---
title: Microsoft Azure-certifieringar för SAP | Microsoft Docs
description: Uppdaterad lista över aktuella konfigurationer och certifieringar för SAP på Azure-plattformen.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/12/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: d1d2bd74a5118b48f7ed79a77ca2d7b6545607c6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072388"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-certifieringar och konfigurationer som körs på Microsoft Azure

SAP och Microsoft har lång erfarenhet av att arbeta tillsammans i en stark koppling som har ömsesidigt fördelar för sina kunder. Microsoft uppdaterar kontinuerligt sin plattform och att skicka ny certifiering information på SAP för att säkerställa att Microsoft Azure är den bästa plattformen som du vill köra SAP-arbetsbelastningar. Följande tabeller dispositionen Azure konfigurationer som stöds och listan över växande SAP-certifieringar. 

## <a name="sap-hana-certifications"></a>SAP HANA-certifieringar
Referenser:

- [SAP HANA-certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) för SAP HANA-stöd för interna virtuella Azure-datorer och stora HANA-instanser.

| SAP-produkt | OS som stöds | Azure-erbjudanden |
| --- | --- | --- |
| SAP HANA Developer Edition (inklusive HANA-klientprogramvaran som består av SQLODBC, ODBO-Windows endast, ODBC, JDBC-enheter, HANA studio och HANA-databas) | Red Hat Enterprise Linux, SUSE Linux Enterprise | D-serien VM-serie |
| Företag en på HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Kontrollerad tillgänglighet för GS5, M64s, M64ms, M128s, M128ms, SAP HANA på Azure (stora instanser) |
| Programsvit på HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 för icke-produktionsscenarion, M64s, M64ms, M128s, M128ms, SAP HANA på Azure (stora instanser) |
| HANA Enterprise för BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA på Azure (stora instanser) |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA på Azure (stora instanser) |

Alla virtuella datorer i Azure är certifierat för SAP HANA, skala upp hittills.

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-certifieringar
Microsoft Azure har certifierats för följande SAP-produkter, med fullständig support från Microsoft och SAP.
Referenser:

- [1928533 - SAP-program på Azure: produkter och Azure VM-typer](https://launchpad.support.sap.com/#/notes/1928533) för alla SAP NetWeaver-baserade program, inklusive SAP TREX, SAP LiveCache och SAP innehållsserver. Och alla databaser, exklusive SAP HANA.


| SAP-produkt | Gäst-OS | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| SAP Business Suite-program | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3 E2s_v3 till E64s_v3 M64s till M128ms |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3 E2s_v3 till E64s_v3 M64s till M128ms |
| SAP BusinessObjects BI | Windows |Gäller inte |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3 E2s_v3 till E64s_v3 M64s till M128ms |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11, D11 till D14, DS11 till DS14, DS11_v2 till DS15_v2, GS1 till GS5, D2s_v3 till D64s_v3 E2s_v3 till E64s_v3 M64s till M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Andra SAP-arbetsbelastningar som stöds i Azure

| SAP-produkt | Gäst-OS | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| SAP Business en på SQLServer | Windows  | SQL Server | Alla NetWeaver certifierade VM-typer<br /> [SAP-kommentar #928839](https://launchpad.support.sap.com/#/notes/928839) |
| SAP BITAR PER KANAL 10.01 MS SP08 | Windows och Linux | | Alla typer av NetWeaver certifierade virtuella datorer<br /> SAP-kommentar #2451795 |
| SAP Business objekt BI-plattform | Windows och Linux | | SAP-kommentar #2145537 |
| SAP-datatjänster 4.2 | | | SAP-kommentar #2288344 |
| SAP Hybris Commerce Platform 5.x och 6.x | Windows | SQLServer, Oracle | Alla NetWeaver certifierade VM-typer<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
