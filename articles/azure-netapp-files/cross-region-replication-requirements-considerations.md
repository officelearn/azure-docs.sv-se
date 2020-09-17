---
title: Krav och överväganden för att använda Azure NetApp Files volym replikering mellan regioner | Microsoft Docs
description: Beskriver kraven och övervägandena för att använda replikeringen av volymer över flera regioner i Azure NetApp Files.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: f2a50872fdb71419a0c3f068712ec67523a098e0
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90709034"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Krav och överväganden för att använda replikering över flera regioner 

Observera följande krav och överväganden om hur [du använder replikeringen över flera regioner](cross-region-replication-create-peering.md) i Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Krav och överväganden 

* Funktionen för replikering över flera regioner finns för närvarande i offentlig för hands version. Du måste skicka in en Waitlist-begäran för att få åtkomst till funktionen via [Azure NetApp Files över flera regioner Waitlist sändnings sida](https://aka.ms/anfcrrpreviewsignup). Vänta tills en officiell bekräftelse skickas från Azure NetApp Files-teamet innan du använder funktionen för replikering över flera regioner.
* Azure NetApp Files replikering är endast tillgänglig i vissa fasta region par. Se de [region par som stöds](cross-region-replication-introduction.md#supported-region-pairs). 
* SMB-volymer stöds tillsammans med NFS-volymer. Replikering av SMB-volymer kräver en Active Directory anslutning i käll-och mål NetApp-kontona. Målets AD-anslutning måste ha åtkomst till DNS-servrarna eller lägga till domänkontrollanter som kan nås från det delegerade under nätet i mål regionen. Mer information finns i [krav för Active Directory anslutningar](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Mål kontot måste finnas i en annan region än käll volymens region. Du kan också välja ett befintligt NetApp-konto i en annan region.  
* Azure NetApp Files replikering har för närvarande inte stöd för flera prenumerationer. alla replikeringar måste utföras under en enda prenumeration.
* Du kan ställa in högst fem volymer för replikering inom en enskild prenumeration per region. Du kan öppna ett support ärende för att begära en ökning av standard kvoten för fem mål volymer för replikering (per prenumeration i en region). 
* Det kan finnas en fördröjning på upp till fem minuter för gränssnittet för att avspegla en nyligen tillagd ögonblicks bild på käll volymen.  
* Det finns inte stöd för överlappande och fläkt i/ut-topologier.
* Det går inte att konfigurera volym replikering för käll volymer som skapats från ögonblicks bilder för tillfället.
* När du har konfigurerat replikering mellan regioner skapar *SnapMirror ögonblicks bilder* för att tillhandahålla referenser mellan käll volymen och mål volymen. SnapMirror-ögonblicksbilder förflyttas automatiskt när en ny skapas för varje stegvis överföring. Det går inte att ta bort SnapMirror-ögonblicksbilder förrän replikeringsrelationen och volymen har tagits bort. 
* Du kan ta bort manuella ögonblicks bilder på käll volymen för en replikeringsrelation när replikeringsrelationen är aktiv eller bruten, och även efter att replikeringsrelationen har tagits bort. Du kan inte ta bort manuella ögonblicks bilder för mål volymen förrän replikeringsrelationen är bruten.

## <a name="next-steps"></a>Nästa steg
* [Skapa replikerings-peering](cross-region-replication-create-peering.md)
* [Visa hälso status för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Hantera haveriberedskap](cross-region-replication-manage-disaster-recovery.md)
* [Mått för volym replikering](azure-netapp-files-metrics.md#replication)
* [Felsöka replikering över flera regioner](troubleshoot-cross-region-replication.md)


