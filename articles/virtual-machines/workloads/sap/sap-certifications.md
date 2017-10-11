---
title: "Microsoft Azure certifieringar för SAP | Microsoft Docs"
description: "Uppdaterad lista över aktuella konfigurationer och SAP-certifieringar på Azure-plattformen."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rclaus
ms.custom: 
ms.openlocfilehash: e4d5c78299903659a18aa9b8d04495e215bcca0d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>SAP-certifiering och konfigurationer som körs på Microsoft Azure

SAP och Microsoft har en lång historik över arbetar tillsammans i ett starkt partnerskap som har ömsesidigt fördelar för sina kunder. Microsoft uppdaterar kontinuerligt dess plattform och skicka ny certifiering information till SAP för att säkerställa att Microsoft Azure är den bästa plattformen att köra arbetsbelastningar för SAP. Följande tabeller beskriver vår lista över växande certifieringar och konfigurationer som stöds. 

## <a name="sap-hana-certifications"></a>SAP HANA-certifieringar

| SAP-produkt | OS som stöds | Azure-erbjudanden |
| --- | --- | --- |
| SAP HANA Developer Edition (inklusive klientprogrammet HANA består av SQLODBC, ODBO Windows endast, ODBC, JDBC-drivrutiner, HANA studio och HANA-databas) |Red Hat Enterprise Linux, SUSE Linux Enterprise | D-serien VM-familjen |
| HANA One |Red Hat Enterprise Linux, SUSE Linux Enterprise |DS14_v2 (vid allmän tillgänglighet) |
| SAP HANA FÖR S/4 |Red Hat Enterprise Linux, SUSE Linux Enterprise |Kontrollerade tillgänglighet för GS5, SAP HANA i Azure (stora instanser) |
| Programsvit på HANA, OLTP |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 för enskild nod distributioner för icke-produktion scenarier SAP HANA i Azure (stora instanser) |
| HANA Enterprise för BW, OLAP |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 för enskild nod distributioner SAP HANA i Azure (stora instanser) |
| SAP BW/4 HANA |Red Hat Enterprise Linux, SUSE Linux Enterprise |GS5 för enskild nod distributioner SAP HANA i Azure (stora instanser) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver-certifieringar
Microsoft Azure har certifierats för följande SAP-produkter, med fullständig support från Microsoft och SAP.

| SAP-produkt | Gäst-OS | RDBMS | Typer av virtuella datorer |
| --- | --- | --- | --- |
| SAP Business Suite-program |Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11 D11 till D14 DS11 till DS14 DS11_v2 till DS15_v2 GS1 till GS5 |
| SAP Business All-in-One |Windows, SUSE Linux Enterprise Red Hat Enterprise Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11 D11 till D14 DS11 till DS14 DS11_v2 till DS15_v2 GS1 till GS5 |
| SAP BusinessObjects BI |Windows |Saknas |A5 till A11 D11 till D14 DS11 till DS14 DS11_v2 till DS15_v2 GS1 till GS5 |
| SAP NetWeaver |Windows, SUSE Linux Enterprise Red Hat Enterprise Linux |SQLServer, Oracle (Windows och endast Oracle Linux), DB2, SAP ASE |A5 till A11 D11 till D14 DS11 till DS14 DS11_v2 till DS15_v2 GS1 till GS5 |
