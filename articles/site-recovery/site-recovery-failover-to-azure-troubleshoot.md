---
title: "Felsöka redundans till Azure fel | Microsoft Docs"
description: "Den här artikeln beskriver hur du felsöker vanliga fel i misslyckande till Azure"
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: pratshar
ms.openlocfilehash: 0e50433e1ccdcbc0010070eec110914f0d33b5ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Felsöka när det inte att redundansväxla en virtuell dator till Azure
Får du ett av följande fel medan växling vid fel på en virtuell dator till Azure. Felsök genom att använda beskrivs stegen för respektive feltillstånd.


## <a name="failover-failed-with-error-id-28031"></a>Redundans misslyckades med felet ID 28031

Site Recovery kunde inte skapa en misslyckades för en virtuell dator i Azure. Det kan bero på något av följande skäl:

* Det finns inte tillräcklig kvot som är tillgängliga för att skapa den virtuella datorn: du kan kontrollera tillgänglig kvot genom att gå till prenumerationen -> användningsdata + kvoter. Du kan öppna en [ny supportbegäran](http://aka.ms/getazuresupport) att öka kvoten.
     
* Du försöker att redundans virtuella datorer med olika storlek adresstyper i samma tillgänglighetsuppsättning. Se till att du väljer samma familj av storlek för alla virtuella datorer i samma tillgänglighetsuppsättning. Ändra storlek genom att gå till beräknings- och inställningarna för den virtuella datorn och försök sedan redundans.
  
* Det finns en princip på den prenumeration som går inte att skapa en virtuell dator. Ändra principen för att tillåta skapandet av en virtuell dator och försök sedan redundans. 

## <a name="failover-failed-with-error-id-28092"></a>Redundans misslyckades med felet ID 28092

Site Recovery kunde inte skapa ett nätverksgränssnitt för den misslyckade över virtuella datorn. Kontrollera att du har tillräcklig kvot som är tillgängliga för att skapa nätverksgränssnitt i prenumerationen. Du kan kontrollera tillgänglig kvot genom att gå till prenumerationen -> användningsdata + kvoter. Du kan öppna en [ny supportbegäran](http://aka.ms/getazuresupport) att öka kvoten. Om du har tillräcklig kvot, så det kan bero på ett tillfälligt utfärda, försök igen. Lämna en kommentar i slutet av det här dokumentet om problemet kvarstår trots upprepade försök.  

## <a name="failover-failed-with-error-id-70038"></a>Redundans misslyckades med felet ID 70038

Site Recovery kunde inte skapa en misslyckad över klassisk virtuell dator i Azure. Det kan bero på att:

* En av resurserna som ett virtuellt nätverk som krävs för den virtuella datorn skapas finns inte. Skapa det virtuella nätverket som anges under beräknings- och inställningar för den virtuella datorn eller ändra inställningen till ett virtuellt nätverk som redan finns och försök sedan redundans. 


## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp sedan publicera din fråga på [Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) eller lämna en kommentar i slutet av det här dokumentet. Vi har en aktiv-community som ska kunna hjälpa dig.