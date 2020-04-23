---
title: Fördelar med att använda Azure NetApp-filer med Oracle Database | Microsoft-dokument
description: Beskriver tekniken och ger en prestandajämförelse mellan Oracle Direct NFS (dNFS) och den traditionella NFS-klienten. Visar fördelarna med att använda dNFS med Azure NetApp-filer.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: a73da39dafcc8be78fbe1c023693ffa4a19aa1d3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085016"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Fördelar med att använda Azure NetApp Files med Oracle Database

Oracle Direct NFS (dNFS) gör det möjligt att driva högre prestanda än operativsystemets egen NFS-drivrutin. Den här artikeln förklarar tekniken och ger en prestandajämförelse mellan dNFS och den traditionella NFS-klienten (Kernel NFS). Det visar också fördelarna och enkelheten med att använda dNFS med Azure NetApp-filer.  

## <a name="how-oracle-direct-nfs-works"></a>Så här fungerar Oracle Direct NFS

Oracle Direct NFS (dNFS) kringgår operativsystemets buffertcache. Data cachelagras bara en gång i användarutrymmet, vilket eliminerar minneskopior.  

Den traditionella NFS-klienten använder ett enda nätverksflöde, vilket visas i följande exempel: 

![Traditionell NFS-klient med ett enda nätverksflöde](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

Oracle dNFS förbättrar däremot prestanda genom att belastningsbalansera nätverkstrafik över flera nätverksflöden. Med den här funktionen kan Oracle-databasen dynamiskt upprätta ett betydande antal 650 olika nätverksanslutningar, vilket visas i exemplet nedan:  

![Oracle Direct NFS förbättrar prestanda](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Oracle FAQ för Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) visar att Oracle dNFS är en optimerad NFS-klient. Det ger snabb och skalbar åtkomst till NFS-lagring som finns på NAS-lagringsenheter (tillgänglig via TCP/IP). dNFS är inbyggt i databaskärnan precis som ASM, som främst används med DAS eller SAN-lagring. Som sådan *är riktlinjen att använda dNFS vid implementering av NAS-lagring och använda ASM vid implementering av SAN-lagring.*

dNFS är standardalternativet i Oracle 18c.

dNFS är tillgängligt från och med Oracle Database 11g. Diagrammet nedan jämför dNFS med inbyggt NFS. När du använder dNFS kan en Oracle-databas som körs på en virtuell Azure-dator köra mer I/O än den inbyggda NFS-klienten.

![Oracle och Azure NetApp-filer jämförelse av dNFS med inbyggt NFS](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Du kan aktivera eller inaktivera dNFS genom att köra två kommandon och starta om databasen.

Så här aktiverar du:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Så här inaktiverar du:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp-filer i kombination med Oracle Direct NFS

Du kan förbättra prestanda för Oracle dNFS med Azure NetApp Files-tjänsten. Tjänsten ger dig total kontroll över programmets prestanda. Den kan uppfylla extremt krävande applikationer. Kombinationen av Oracle dNFS med Azure NetApp Files ger stor fördel för dina arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

- [Lösningsarkitekturer med Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Översikt över Oracle-program och lösningar på Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)
