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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: da5ae8220c63333b92c671a3f2ab288ef5ff4b48
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679086"
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

## <a name="failover-failed-with-error-id-170010"></a>Det gick inte att Redundansväxla med fel-ID 170010

Det gick inte att skapa en redundansväxlade virtuella datorn i Azure site Recovery. Det kan inträffa eftersom en intern aktivitet för hydrering misslyckades för den lokala virtuella datorn.

Om du vill ta fram en dator i Azure, kräver Azure-miljön några av drivrutinerna i startavbildningen starta tillstånd och -tjänster som DHCP är i tillståndet autostart. Därför hydrering aktivitet, vid tidpunkten för växling vid fel, konverterar starttyp för **atapi, intelide, storflt, vmbus och storvsc drivrutiner** Start början. Det konverterar också starttypen för några tjänster som DHCP till autostart. Den här aktiviteten kan misslyckas på grund av problem med miljön. Så här ändrar du starttypen för drivrutiner manuellt i stegen nedan:

1. [Ladda ned](http://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) nr hydrering skript och kör det som följer. Det här skriptet kontrollerar om virtuell dator kräver hydrering.

    `.\Script-no-hydration.ps1`

    Det ger följande resultat om hydrering krävs:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Om den virtuella datorn uppfyller Nej hydrering, ger skriptet resultatet ”systemet uppfyller Nej hydrering”. I det här fallet alla drivrutiner och tjänster som är i tillståndet som krävs av Azure och hydrering på den virtuella datorn är inte obligatoriskt.

2. Kör skriptet Nej-hydrering-set på följande sätt om den virtuella datorn inte uppfyller kravet på Nej hydrering.

    `.\Script-no-hydration.ps1 -set`
    
    Detta konverterar starttyp för drivrutiner och ger resultatet som den här:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Det går inte att ansluta/RDP/SSH till den redundansväxlade virtuella datorn på grund av nedtonad knappen Anslut på den virtuella datorn

Om den **Connect** knappen på den redundansväxlade virtuella datorn i Azure är nedtonad och du inte är ansluten till Azure via en Express Route eller plats-till-plats-VPN-anslutning, sedan

1. Gå till **VM** > **nätverk**, klicka på namnet på nätverksgränssnittet som krävs.  ![nätverksskrivare](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Gå till **Ip-konfigurationer**, klicka sedan på namnfältet på IP-konfiguration som krävs. ![IP-konfigurationer](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Om du vill aktivera offentlig IP-adress, klickar du på **aktivera**. ![Aktivera IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klicka på **konfigurera nödvändiga inställningar** > **Skapa ny**. ![Skapa en ny](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Ange namnet på offentlig adress, välj sedan standardalternativen för **SKU** och **tilldelning**, klicka sedan på **OK**.
6. Om du vill spara ändringarna, klicka på **spara**.
7. Stäng panelerna och gå till **översikt** delen av virtuell dator för att ansluta/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Det går inte att ansluta/RDP/SSH - VM knappen för anslutning tillgänglig

Om den **Connect** knappen på den redundansväxlade virtuella datorn i Azure är tillgängliga (inte grå), så kontrollera **Startdiagnostik** på den virtuella datorn och Sök efter fel som anges i [i den här artikeln](../virtual-machines/windows/boot-diagnostics.md).

1. Om den virtuella datorn inte har startats försöker du växla till en tidigare återställningspunkt.
2. Om programmet i den virtuella datorn inte är igång försöka att växla till en programkonsekvent återställningspunkt.
3. Om den virtuella datorn är ansluten till en domän, se till att domänkontrollanten fungerar korrekt. Detta kan göras genom att följa den angivna stegen nedan:

    a. Skapa en ny virtuell dator i samma nätverk.

    b.  Se till att den ska kunna ansluta till samma domän som den redundansväxlade virtuella datorn förväntas hamna.

    c. Om domänkontrollanten är **inte** fungerar korrekt, försök logga in på den redundansväxlade virtuella datorn med ett lokalt administratörskonto.
4. Om du använder en anpassad DNS-server, se till att den kan nås. Detta kan göras genom att följa den angivna stegen nedan:

    a. Skapa en ny virtuell dator i samma nätverk och

    b. Kontrollera om den virtuella datorn kan utföra namnmatchning med hjälp av anpassad DNS-Server

>[!Note]
>Aktiverar någon annan inställning än Startdiagnostik kräver Azure VM-agenten installeras på den virtuella datorn innan redundansväxlingen sker

## <a name="unexpected-shutdown-message-event-id-6008"></a>Oväntad avstängningsmeddelande (händelse-ID 6008)

När du startar upp en virtuell Windows-dator efter redundans om en oväntad avstängning visas på den återställda virtuella datorn, anger en VM-avstängning inte hämtades i den återställningspunkt som används för redundans. Detta händer när du återställer till en tidpunkt när den virtuella datorn inte hade varit helt stängs av.

Detta är vanligtvis inte en orsaka problem och kan vanligtvis ignoreras vid oplanerade redundanser. När det gäller en planerad redundans, kontrollera att den virtuella datorn är korrekt avstängd innan du redundansväxlar och ger tillräckligt med tid för väntande replikering data lokalt som ska skickas till Azure. Använd sedan den **senaste** alternativet på den [redundans skärmen](site-recovery-failover.md#run-a-failover) så att alla data som väntar på Azure har bearbetats i en återställningspunkt, som sedan används för VM-redundans.

## <a name="retaining-drive-letter-after-failover"></a>Behåller enhetsbeteckningen efter redundans
Om du vill behålla enhetsbeteckning på virtuella datorer efter en redundansväxling kan du ställa in den **SAN-princip** för de virtuella datorn lokalt till **OnlineAll**. [Läs mer](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="next-steps"></a>Nästa steg
- Felsöka [RDP-anslutning till Windows-VM](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Felsöka [SSH-anslutningen till Linux VM](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Om du behöver mer hjälp kan sedan publicera din fråga på [Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) eller lämna en kommentar i slutet av det här dokumentet. Vi har en aktiv community som ska kunna hjälpa dig.
