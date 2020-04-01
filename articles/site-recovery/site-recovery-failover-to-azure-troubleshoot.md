---
title: Felsöka redundans till Azure-fel | Microsoft-dokument
description: I den här artikeln beskrivs olika sätt att felsöka vanliga fel i att misslyckas med att gå över till Azure
author: ponatara
manager: abhemraj
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2020
ms.author: mayg
ms.openlocfilehash: 54e44a12f593d2074eefe5b2ff890863db3199f7
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478950"
---
# <a name="troubleshoot-errors-when-failing-over-vmware-vm-or-physical-machine-to-azure"></a>Felsöka fel när det misslyckas över virtuell VMware-dator eller fysisk dator till Azure

Du kan få ett av följande fel när du gör redundans för en virtuell dator till Azure. Om du vill felsöka använder du de beskrivna stegen för varje feltillstånd.

## <a name="failover-failed-with-error-id-28031"></a>Redundans misslyckades med fel-ID 28031

Site Recovery kunde inte skapa en misslyckad över virtuell dator i Azure. Det kan hända på grund av något av följande skäl:

* Det finns inte tillräcklig kvot tillgänglig för att skapa den virtuella datorn: Du kan kontrollera den tillgängliga kvoten genom att gå till Prenumeration -> Användning + kvoter. Du kan öppna en [ny supportbegäran för](https://aka.ms/getazuresupport) att öka kvoten.

* Du försöker redundans virtuella datorer av olika storlek familjer i samma tillgänglighetsuppsättning. Se till att du väljer samma storlek familj för alla virtuella datorer i samma tillgänglighetsuppsättning. Ändra storlek genom att gå till Beräknings- och nätverksinställningar för den virtuella datorn och sedan försöka redundansen igen.

* Det finns en princip för prenumerationen som förhindrar att en virtuell dator skapas. Ändra principen så att det kan skapas en virtuell dator och försök sedan med redundans igen.

## <a name="failover-failed-with-error-id-28092"></a>Redundans misslyckades med fel-ID 28092

Site Recovery kunde inte skapa ett nätverksgränssnitt för den misslyckade över den virtuella datorn. Kontrollera att du har tillräcklig kvot tillgänglig för att skapa nätverksgränssnitt i prenumerationen. Du kan kontrollera den tillgängliga kvoten genom att gå till Prenumeration -> Användning + kvoter. Du kan öppna en [ny supportbegäran för](https://aka.ms/getazuresupport) att öka kvoten. Om du har tillräcklig kvot kan det bero på ett återkommande problem, försök med åtgärden igen. Om problemet kvarstår även efter återförsök lämnar du en kommentar i slutet av det här dokumentet.  

## <a name="failover-failed-with-error-id-70038"></a>Redundans misslyckades med fel-ID 70038

Site Recovery kunde inte skapa en misslyckad över klassisk virtuell dator i Azure. Det kan hända eftersom:

* En av resurserna, till exempel ett virtuellt nätverk som krävs för att den virtuella datorn ska skapas, finns inte. Skapa det virtuella nätverket enligt beräknings- och nätverksinställningarna för den virtuella datorn eller ändra inställningen till ett virtuellt nätverk som redan finns och försök sedan med redundans igen.

## <a name="failover-failed-with-error-id-170010"></a>Redundans misslyckades med fel-ID 170010

Site Recovery kunde inte skapa en misslyckad över virtuell dator i Azure. Det kan hända eftersom en intern aktivitet av hydrering misslyckades för den lokala virtuella datorn.

För att ta upp alla datorer i Azure kräver Azure-miljön att vissa av drivrutinerna är i starttillstånd och tjänster som DHCP för att vara i autostart-tillstånd. Således, hydrering aktivitet, vid tidpunkten för redundans, konverterar **starttypen atapi, intelide, storflt, vmbus och storvsc drivrutiner** för att starta start. Det konverterar också starttypen för några tjänster som DHCP för att starta automatiskt. Den här aktiviteten kan misslyckas på grund av miljöspecifika problem. 

Så här ändrar du starttypen för drivrutiner för **Windows Gästoperativsystem**manuellt:

1. [Ladda ner](https://download.microsoft.com/download/5/D/6/5D60E67C-2B4F-4C51-B291-A97732F92369/Script-no-hydration.ps1) no-hydration skript och köra det enligt följande. Det här skriptet kontrollerar om den virtuella datorn kräver hydrering.

    `.\Script-no-hydration.ps1`

    Det ger följande resultat om hydrering krävs:

        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0

        This system doesn't meet no-hydration requirement.

    Om den virtuella datorn uppfyller kravet på no-hydration kommer skriptet att ge resultatet "Detta system uppfyller krav på no-hydration". I det här fallet är alla drivrutiner och tjänster i det tillstånd som krävs av Azure och hydrering på den virtuella datorn krävs inte.

2. Kör skriptet för no-hydration-set enligt följande om den virtuella datorn inte uppfyller kravet på återfuktning.

    `.\Script-no-hydration.ps1 -set`
    
    Detta kommer att konvertera start typ av drivrutiner och kommer att ge resultatet som nedan:
    
        REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc           start =  3 expected value =  0 

        Updating registry:  REGISTRY::HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\services\storvsc   start =  0 

        This system is now no-hydration compatible. 

## <a name="unable-to-connectrdpssh-to-the-failed-over-virtual-machine-due-to-grayed-out-connect-button-on-the-virtual-machine"></a>Det gick inte att ansluta/RDP/SSH till den misslyckade över den virtuella datorn på grund av nedtonad anslutningsknapp på den virtuella datorn

Om **knappen Anslut** på den misslyckade över den virtuella datorn i Azure är nedtonad och du inte är ansluten till Azure via en Express Route- eller Site-to-Site VPN-anslutning,

1. Gå till **Virtuell dator** > **Nätverk**, klicka på namnet på krävs nätverksgränssnitt.  ![nätverksgränssnitt](media/site-recovery-failover-to-azure-troubleshoot/network-interface.PNG)
2. Navigera till **IP-konfigurationer**och klicka sedan på namnfältet för nödvändig IP-konfiguration. ![IP-konfigurationer](media/site-recovery-failover-to-azure-troubleshoot/IpConfigurations.png)
3. Om du vill aktivera offentlig IP-adress klickar du på **Aktivera**. ![Aktivera IP](media/site-recovery-failover-to-azure-troubleshoot/Enable-Public-IP.png)
4. Klicka på **Konfigurera obligatoriska inställningar** > **Skapa nya**. ![Skapa nya](media/site-recovery-failover-to-azure-troubleshoot/Create-New-Public-IP.png)
5. Ange namnet på den offentliga adressen, välj standardalternativ för **SKU** och **tilldelning**och klicka sedan på **OK**.
6. Nu, för att spara de ändringar som gjorts, klicka på **Spara**.
7. Stäng panelerna och navigera till **översiktsavsnittet** på den virtuella datorn för att ansluta/RDP.

## <a name="unable-to-connectrdpssh---vm-connect-button-available"></a>Det går inte att ansluta/RDP/SSH - VM Connect-knappen tillgänglig

Om **knappen Anslut** på den misslyckade över den virtuella datorn i Azure är tillgänglig (inte nedtonad) kontrollerar du **Startdiagnostiken** på den virtuella datorn och kontrollerar om det finns fel i [den här artikeln](../virtual-machines/windows/boot-diagnostics.md).

1. Om den virtuella datorn inte har startat kan du försöka gå över till en äldre återställningspunkt.
2. Om programmet inuti den virtuella datorn inte är uppe kan du försöka gå över till en appkonsekvent återställningspunkt.
3. Om den virtuella datorn är domänansluten kontrollerar du att domänkontrollanten fungerar korrekt. Detta kan göras genom att följa nedanstående steg:

    a. Skapa en ny virtuell dator i samma nätverk.

    b.  Se till att den kan ansluta till samma domän där den misslyckade över den virtuella datorn förväntas komma upp.

    c. Om domänkontrollanten **inte** fungerar korrekt kan du försöka logga in på den misslyckade datorn via ett lokalt administratörskonto.
4. Om du använder en anpassad DNS-server ska du se till att den kan nås. Detta kan göras genom att följa nedanstående steg:

    a. Skapa en ny virtuell dator i samma nätverk och

    b. Kontrollera om den virtuella datorn kan göra namnmatchning med den anpassade DNS-servern

>[!Note]
>Om du aktiverar alla andra inställningar än Boot Diagnostics måste Azure VM-agenten installeras i den virtuella datorn före redundansen

## <a name="unable-to-open-serial-console-after-failover-of-a-uefi-based-machine-into-azure"></a>Det gick inte att öppna seriell konsol efter redundans för en UEFI-baserad dator i Azure

Om du kan ansluta till datorn med RDP men inte kan öppna seriell konsol följer du stegen nedan:

* Om datoroperativsystemet är Red Hat eller Oracle Linux 7.*/8.0 kör du följande kommando på den kostnadsfria Azure-virtuella datorn med rotbehörigheter. Starta om den virtuella datorn efter kommandot.

        grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg

* Om datorns OPERATIVSYSTEM är CentOS 7.*kör du följande kommando på den kostnadsfria Azure-virtuella datorn med rotbehörigheter. Starta om den virtuella datorn efter kommandot.

        grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

## <a name="unexpected-shutdown-message-event-id-6008"></a>Oväntat avstängningsmeddelande (händelse-ID 6008)

När du startar upp en Windows VM-postväxling, om du får ett oväntat avstängningsmeddelande på den återställda virtuella datorn, indikerar det att ett feltillstånd för vm inte fångades i återställningspunkten som användes för redundans. Detta händer när du återställer till en punkt där den virtuella datorn inte hade stängts av helt.

Detta är normalt inte en anledning till oro och kan vanligtvis ignoreras för oplanerade redundans. Om redundansen är planerad, se till att den virtuella datorn stängs av korrekt före redundans och ger tillräckligt med tid för väntande replikeringsdata lokalt som ska skickas till Azure. Använd sedan det **senaste** alternativet på [redundansskärmen](site-recovery-failover.md#run-a-failover) så att väntande data på Azure bearbetas till en återställningspunkt, som sedan används för vm-redundans.

## <a name="unable-to-select-the-datastore"></a>Det går inte att välja Datastore

Det här problemet anges när du inte kan se datalagret i Azure portalen när du försöker reprotera den virtuella datorn som har upplevt en redundans. Detta beror på att huvudmålet inte känns igen som en virtuell dator under vCenters som läggs till i Azure Site Recovery.

Mer information om hur du skyddar en virtuell dator igen finns i [Reprotect och redundansdatorer till en lokal plats efter redundans till Azure](vmware-azure-reprotect.md).

Så här löser du problemet:

Skapa huvudmålet manuellt i vCenter som hanterar källmaskinen. Datalagret kommer att vara tillgängligt efter nästa vCenter-identifiering och uppdateringsinfrastruktur.

> [!Note]
> 
> Identifierings- och uppdateringsinfrastrukturåtgärder kan ta upp till 30 minuter att slutföra. 

## <a name="linux-master-target-registration-with-cs-fails-with-a-tls-error-35"></a>Linux Master Target registrering med CS misslyckas med ett TLS-fel 35 

Azure Site Recovery Master Target-registreringen med konfigurationsservern misslyckas på grund av att den autentiserade proxyn aktiveras på huvudmålet. 
 
Det här felet indikeras av följande strängar i installationsloggen: 

```
RegisterHostStaticInfo encountered exception config/talwrapper.cpp(107)[post] CurlWrapper Post failed : server : 10.38.229.221, port : 443, phpUrl : request_handler.php, secure : true, ignoreCurlPartialError : false with error: [at curlwrapperlib/curlwrapper.cpp:processCurlResponse:231]   failed to post request: (35) - SSL connect error. 
```

Så här löser du problemet:
 
1. Öppna en kommandotolk på konfigurationsserverns virtuella dator och verifiera proxyinställningarna med följande kommandon:

    katt / etc / miljö eko $http_proxy eko $https_proxy 

2. Om utdata från tidigare kommandon visar att antingen http_proxy- eller https_proxy-inställningarna har definierats använder du någon av följande metoder för att häva blockeringen av huvudmålkommunikationen med konfigurationsservern:
   
   - Ladda ner [PsExec-verktyget](https://aka.ms/PsExec).
   - Använd verktyget för att komma åt systemanvändarkontexten och avgöra om proxyadressen är konfigurerad. 
   - Om proxyn är konfigurerad öppnar du IE i en systemanvändarkontext med psexec-verktyget.
  
     **psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"**

   - Så här säkerställer du att huvudmålservern kan kommunicera med konfigurationsservern:
  
     - Ändra proxyinställningarna i Internet Explorer för att kringgå huvudmålserverns IP-adress via proxyn.   
     Eller
     - Inaktivera proxyn på Huvudmålservern. 


## <a name="next-steps"></a>Nästa steg
- Felsöka [RDP-anslutning till Windows VM](../virtual-machines/windows/troubleshoot-rdp-connection.md)
- FelsökA [SSH-anslutning till Linux VM](../virtual-machines/linux/detailed-troubleshoot-ssh-connection.md)

Om du behöver mer hjälp, sedan lägga upp din fråga på [Site Recovery forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) eller lämna en kommentar i slutet av detta dokument. Vi har en aktiv gemenskap som ska kunna hjälpa dig.
