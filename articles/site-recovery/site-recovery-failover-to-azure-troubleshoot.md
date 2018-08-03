---
title: Felsöka redundans till Azure fel | Microsoft Docs
description: Den här artikeln beskrivs olika sätt att felsöka vanliga fel i redundansväxel till Azure
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: ponatara
ms.openlocfilehash: b7b5dcd88b6e4e09dd9beb21e83ef405df148115
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443392"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Felsök fel när du växlar en virtuell dator till Azure

Du får något av följande fel när du gör redundans för en virtuell dator till Azure. Felsök genom att använda beskrivs stegen för respektive feltillstånd.

## <a name="failover-failed-with-error-id-28031"></a>Det gick inte att Redundansväxla med fel-ID 28031

Det gick inte att skapa en redundansväxlade virtuella datorn i Azure site Recovery. Det kan inträffa på grund av något av följande orsaker:

* Det finns inte tillräckligt många tillgängliga för att skapa den virtuella datorn: du kan kontrollera tillgänglig kvot genom att gå till prenumeration -> användning och kvoter. Du kan öppna en [ny supportbegäran](http://aka.ms/getazuresupport) att öka kvoten.

* Du försöker att redundansväxling för virtuella datorer av olika storlekar i samma tillgänglighetsuppsättning. Se till att du väljer samma storleksfamilj för alla virtuella datorer i samma tillgänglighetsuppsättning. Ändra storlek genom att gå till inställningarna för beräkning och nätverk för den virtuella datorn och försök att redundansväxla.

* Det finns en princip på den prenumeration som går inte att skapa en virtuell dator. Ändra principen för att tillåta skapandet av en virtuell dator och försök att redundansväxla.

## <a name="failover-failed-with-error-id-28092"></a>Det gick inte att Redundansväxla med fel-ID 28092

Site Recovery kunde inte skapa ett nätverksgränssnitt för den redundansväxlade virtuella datorn. Kontrollera att du har tillräckligt många tillgängliga för att skapa nätverksgränssnitt i prenumerationen. Du kan kontrollera tillgänglig kvot genom att gå till prenumeration -> användning och kvoter. Du kan öppna en [ny supportbegäran](http://aka.ms/getazuresupport) att öka kvoten. Om du har tillräckligt många så det kan vara ett tillfälligt problem, försök igen. Om problemet kvarstår trots upprepade försök, ska du lämna en kommentar i slutet av det här dokumentet.  

## <a name="failover-failed-with-error-id-70038"></a>Det gick inte att Redundansväxla med fel-ID 70038

Det gick inte att skapa en misslyckad över klassisk virtuell dator i Azure site Recovery. Det kan bero på:

* En av resurser, till exempel ett virtuellt nätverk som krävs för den virtuella datorn skapas finns inte. Skapa det virtuella nätverket som angavs under inställningarna för beräkning och nätverk för den virtuella datorn eller ändra inställningen till ett virtuellt nätverk som redan finns och försök att redundansväxla.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Det går inte att ansluta/RDP/SSH till den redundansväxlade virtuella datorn på grund av nedtonad knappen Anslut på den virtuella datorn

Om knappen Anslut är nedtonad och du inte är ansluten till Azure via en Express Route eller plats-till-plats-VPN-anslutning, sedan

1. Gå till **VM** > **nätverk**, klicka på namnet på nätverksgränssnittet som krävs.  ![nätverksskrivare](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
1. Gå till **Ip-konfigurationer**, klicka sedan på namnfältet på IP-konfiguration som krävs. ![IP-konfigurationer](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
1. Om du vill aktivera offentlig IP-adress, klickar du på **aktivera**. ![Aktivera IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
1. Klicka på **konfigurera nödvändiga inställningar** > **Skapa ny**. ![Skapa en ny](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
1. Ange namnet på offentlig adress, välj sedan standardalternativen för **SKU** och **tilldelning**, klicka sedan på **OK**.
1. Om du vill spara ändringarna, klicka på **spara**.
1. Stäng panelerna och gå till **översikt** delen av virtuell dator för att ansluta/RDP.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Det går inte att ansluta/RDP/SSH till den via virtuella datorn även om Connect knappen är tillgänglig (inte grå) på den virtuella datorn

Kontrollera **Startdiagnostik** på den virtuella datorn och Sök efter fel som anges i den här artikeln.

1. Om den virtuella datorn inte har startats försöker du växla till en tidigare återställningspunkt.
1. Om programmet i den virtuella datorn inte är igång försöka att växla till en programkonsekvent återställningspunkt.
1. Om den virtuella datorn är ansluten till en domän, se till att domänkontrollanten fungerar korrekt. Detta kan göras genom att följa den angivna stegen nedan.
    a. Skapa en ny virtuell dator i samma nätverk

    b.  Se till att den ska kunna ansluta till samma domän som den redundansväxlade virtuella datorn förväntas hamna.

    c. Om domänkontrollanten är **inte** fungerar korrekt, försök logga in på den redundansväxlade virtuella datorn med ett lokalt administratörskonto
1. Om du använder en anpassad DNS-server, se till att den kan nås. Detta kan göras genom att följa den angivna stegen nedan.
    a. Skapa en ny virtuell dator i samma nätverk och b. Kontrollera om den virtuella datorn kan utföra namnmatchning med hjälp av anpassad DNS-Server

>[!Note]
>Aktiverar någon annan inställning än Startdiagnostik kräver Azure VM-agenten installeras på den virtuella datorn innan redundansväxlingen sker

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan sedan publicera din fråga på [Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) eller lämna en kommentar i slutet av det här dokumentet. Vi har en aktiv community som ska kunna hjälpa dig.
