---
title: Felsöka redundans till Azure fel | Microsoft Docs
description: Den här artikeln beskriver hur du felsöker vanliga fel i misslyckande till Azure
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
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 838eac510fc17d56f808f541f4e205a279f63c56
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318899"
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

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Det gick inte att ansluta/RDP/SSH till den misslyckade nedtonad över virtuella datorn på grund av att knappen Anslut på den virtuella datorn

Om Connect-knappen är nedtonad och du inte är ansluten till Azure via en Express Route eller plats-till-plats VPN-anslutning, sedan

1. Gå till **virtuella** > **nätverk**, klicka på namnet på nödvändiga gränssnitt.  ![gränssnitt](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Gå till **Ip-konfigurationer**, klicka på namnfältet av IP-konfiguration. ![IPConfigurations](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Om du vill aktivera offentlig IP-adress, klickar du på **aktivera**. ![Aktivera IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klicka på **konfigurera nödvändiga inställningar** > **Skapa nytt**. ![Skapa en ny](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Ange namnet på offentlig adress, Välj standardalternativen för **SKU** och **tilldelning**, klicka på **OK**.
6. Om du vill spara ändringarna som du **spara**.
7. Stäng panelerna och gå till **översikt** avsnitt i den virtuella datorn ska ansluta/RDP.

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-even-though-connect-button-is-available-not-grayed-out-on-the-virtual-machine"></a>Det gick inte att ansluta/RDP/SSH till den via virtuella datorn även om Connect knappen är tillgänglig (inte nedtonat) på den virtuella datorn

Kontrollera **starta diagnostik** på den virtuella datorn och Sök efter fel som anges i den här artikeln.

1. Om den virtuella datorn inte har startat försök inte att redundansväxla till en tidigare återställningspunkt.
2. Om programmet inuti den virtuella datorn inte är upp försök misslyckas över till en app konsekvent återställningspunkt.
3. Om den virtuella datorn är ansluten till en domän, se till att domänkontrollanten fungerar korrekt. Detta kan göras genom att följa de angivna stegen nedan.
    a. Skapa en ny virtuell dator i samma nätverk

    b.  Se till att den kan ansluta till samma domän som den misslyckade över virtuella förväntas fram.

    c. Om domänkontrollanten är **inte** fungerar korrekt, försök logga in på den misslyckade över virtuell dator med ett lokalt administratörskonto
4. Om du använder en anpassad DNS-server, se till att den kan nås. Detta kan göras genom att följa de angivna stegen nedan.
    a. Skapa en ny virtuell dator i samma nätverk och b. Kontrollera om den virtuella datorn kan namnmatchning med hjälp av anpassade DNS-servern

>[!Note]
>Aktivera alla inställningar än Startdiagnostikinställningar kräver Azure VM-agenten installeras på den virtuella datorn innan växling vid fel

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp sedan publicera din fråga på [Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) eller lämna en kommentar i slutet av det här dokumentet. Vi har en aktiv-community som ska kunna hjälpa dig.
