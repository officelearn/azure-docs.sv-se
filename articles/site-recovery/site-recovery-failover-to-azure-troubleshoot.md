---
title: Felsöka redundans till Azure fel | Microsoft Docs
description: Den här artikeln beskrivs olika sätt att felsöka vanliga fel i redundansväxel till Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/04/2019
ms.author: mayg
ms.openlocfilehash: 811d75ec2246199662a25afd6b96b23035444211
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436042"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Felsök fel när redundansväxlingen VMware VM eller en fysisk dator till Azure

Du får något av följande fel när du gör redundans för en virtuell dator till Azure. Felsök genom att använda beskrivs stegen för respektive feltillstånd.

## <a name="failover-failed-with-error-id-28031"></a>Det gick inte att Redundansväxla med fel-ID 28031

Det gick inte att skapa en redundansväxlade virtuella datorn i Azure site Recovery. Det kan inträffa på grund av något av följande orsaker:

* Det finns inte tillräckligt många tillgängliga för att skapa den virtuella datorn: Du kan kontrollera tillgänglig kvot genom att gå till prenumeration -> användning och kvoter. Du kan öppna en [ny supportbegäran](http://aka.ms/getazuresupport) att öka kvoten.

* Du försöker att redundansväxling för virtuella datorer av olika storlekar i samma tillgänglighetsuppsättning. Se till att du väljer samma storleksfamilj för alla virtuella datorer i samma tillgänglighetsuppsättning. Ändra storlek genom att gå till inställningarna för beräkning och nätverk för den virtuella datorn och försök att redundansväxla.

* Det finns en princip på den prenumeration som går inte att skapa en virtuell dator. Ändra principen för att tillåta skapandet av en virtuell dator och försök att redundansväxla.

## <a name="failover-failed-with-error-id-28092"></a>Det gick inte att Redundansväxla med fel-ID 28092

Site Recovery kunde inte skapa ett nätverksgränssnitt för den redundansväxlade virtuella datorn. Kontrollera att du har tillräckligt många tillgängliga för att skapa nätverksgränssnitt i prenumerationen. Du kan kontrollera tillgänglig kvot genom att gå till prenumeration -> användning och kvoter. Du kan öppna en [ny supportbegäran](http://aka.ms/getazuresupport) att öka kvoten. Om du har tillräckligt många så det kan vara ett tillfälligt problem, försök igen. Om problemet kvarstår trots upprepade försök, ska du lämna en kommentar i slutet av det här dokumentet.  

## <a name="failover-failed-with-error-id-70038"></a>Det gick inte att Redundansväxla med fel-ID 70038

Det gick inte att skapa en misslyckad över klassisk virtuell dator i Azure site Recovery. Det kan bero på:

* En av resurser, till exempel ett virtuellt nätverk som krävs för den virtuella datorn skapas finns inte. Skapa det virtuella nätverket som angavs under inställningarna för beräkning och nätverk för den virtuella datorn eller ändra inställningen till ett virtuellt nätverk som redan finns och försök att redundansväxla.

## <a name="failover-failed-with-error-id-170010"></a>Det gick inte att Redundansväxla med fel-ID 170010

Det gick inte att skapa en redundansväxlade virtuella datorn i Azure site Recovery. Det kan inträffa eftersom en intern aktivitet för hydrering misslyckades för den lokala virtuella datorn.

Om du vill ta fram en dator i Azure, kräver Azure-miljön några av drivrutinerna i startavbildningen starta tillstånd och -tjänster som DHCP är i tillståndet autostart. Därför hydrering aktivitet, vid tidpunkten för växling vid fel, konverterar starttyp för **atapi, intelide, storflt, vmbus och storvsc drivrutiner** Start början. Det konverterar också starttypen för några tjänster som DHCP till autostart. Den här aktiviteten kan misslyckas på grund av problem med miljön. 

Att manuellt ändra starttypen för drivrutiner för **Windows-Gästoperativsystem**, följer du de stegen nedan:

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

1. Gå till **VM** > **nätverk**, klicka på namnet på nätverksgränssnittet som krävs.  ![network-interface](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
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

Detta är vanligtvis inte en orsaka problem och kan vanligtvis ignoreras vid oplanerade redundanser. Om redundansen planeras, kontrollera att den virtuella datorn är korrekt avstängd innan du redundansväxlar och ger tillräckligt med tid för väntande replikering data lokalt som ska skickas till Azure. Använd sedan den **senaste** alternativet på den [redundans skärmen](site-recovery-failover.md#run-a-failover) så att alla data som väntar på Azure har bearbetats i en återställningspunkt, som sedan används för VM-redundans.

## <a name="unable-to-select-the-datastore"></a>Det går inte att välja databasen

Det här problemet visas när det inte går att se datalager i Azure portalen när du försöker återaktivera skyddet för den virtuella dator som har uppstått en redundansväxling. Detta beror på Master target identifieras inte som en virtuell dator under vCenters som lagts till i Azure Site Recovery.

Läs mer om att skydda en virtuell dator, [skydda igen och misslyckas tillbaka datorer till en lokal plats efter redundansväxlingen till Azure](vmware-azure-reprotect.md).

Att lösa problemet:

Skapa manuellt huvudservern mål i vCenter som hanterar källdatorn. Databasen blir tillgänglig efter nästa vCenter-identifiering och uppdatera fabric åtgärderna.

> [!Note]
> 
> Identifiering och fabric uppdateringsåtgärder kan ta upp till 30 minuter att slutföra. 

## <a name="linux-master-target-registration-with-cs-fails-with-an-ssl-error-35"></a>Linux Huvudmålserver registreringen med CS misslyckas med ett SSL-fel 35 

Azure Site Recovery-huvudmål Target registreringen med konfigurationsservern misslyckas på grund av autentiserad Proxy som håller på att aktiveras på Huvudmålet. 
 
Det här felet visas med följande strängar i installationsloggen: 

RegisterHostStaticInfo påträffade undantaget config/talwrapper.cpp(107) [post] CurlWrapper Post misslyckades: server: 10.38.229.221 port: 443, phpUrl: request_handler.php, säker: SANT, ignoreCurlPartialError: false med fel: [på curlwrapperlib/curlwrapper.cpp:processCurlResponse:231] Det gick inte att skicka begäran: (35) - anslutningsfel för SSL. 
 
Att lösa problemet:
 
1. Öppna en kommandotolk på konfigurationsservern VM och kontrollera proxy-inställningar med hjälp av följande kommandon:

    cat /etc/environment echo $http_proxy echo $https_proxy 

2. Om utdata från de tidigare kommandona visar att den http_proxy eller https_proxy inställningar är definierade, använder du någon av följande metoder för att avblockera Master Target-kommunikation med konfigurationsservern:
   
   - Ladda ned den [PsExec verktyget](https://aka.ms/PsExec).
   - Du kan använda verktyget för att komma åt användarkontext System och avgöra om Proxyadressen är konfigurerat. 
   - Öppna Internet Explorer om proxyservern har konfigurerats i en användarkontext för system med hjälp av verktyget PsExec.
  
     **PSExec -s -i ”%programfiles%\Internet Explorer\iexplore.exe”**

   - Att se till att huvudmålservern kan kommunicera med konfigurationsservern:
  
     - Ändra proxyinställningarna i Internet Explorer för att kringgå Huvudmålet serverns IP-adress via proxy.   
     Eller
     - Inaktivera proxyservern på huvudmålservern. 


## <a name="next-steps"></a>Nästa steg
- Felsöka [RDP-anslutning till Windows-VM](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- Felsöka [SSH-anslutningen till Linux VM](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Om du behöver mer hjälp kan sedan publicera din fråga på [Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) eller lämna en kommentar i slutet av det här dokumentet. Vi har en aktiv community som ska kunna hjälpa dig.
