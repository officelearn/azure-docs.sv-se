---
title: Azures serie konsol för Linux | Microsoft Docs
description: Dubbelriktad serie konsol för Azure Virtual Machines och Virtual Machine Scale Sets.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: a9c1ca3ac55c1c995ac858e758d6930b49c5ea1c
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287018"
---
# <a name="azure-serial-console-for-linux"></a>Azures serie konsol för Linux

Serie konsolen i Azure Portal ger till gång till en text baserad konsol för virtuella Linux-datorer och instanser av skalnings uppsättningar för virtuella datorer. Den här seriella anslutningen ansluter till den ttyS0 serie porten för den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans, vilket ger åtkomst till den oberoende av nätverket eller operativ systemets tillstånd. Det går bara att komma åt serie konsolen med hjälp av Azure Portal och tillåts bara för de användare som har åtkomst rollen deltagare eller högre till den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

Serie konsolen fungerar på samma sätt för virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer. I det här dokumentet kommer alla omnämnanden för virtuella datorer att implicit inkludera skalnings uppsättnings instanser för virtuella datorer om inget annat anges.

Information om en dokumentation för Windows i serie konsolen finns i [serie konsol för Windows](../windows/serial-console.md).

> [!NOTE]
> Serie konsolen är allmänt tillgänglig i globala Azure-regioner. Det finns inte ännu i Azure government eller Azure Kina moln.


## <a name="prerequisites"></a>Krav

- Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste använda distributions modellen för resurs hantering. Klassiska distributioner stöds inte.

- Ditt konto som använder en serie konsol måste ha [rollen virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för den virtuella datorn och lagrings kontot för [startdiagnostik](boot-diagnostics.md)

- Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste ha en lösenordsbaserad användare. Du kan skapa en med den [Återställ lösenord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funktion av access-tillägg för virtuell dator. Välj **Återställ lösenord** från den **Support och felsökning** avsnittet.

- Startdiagnostik måste vara aktiverat på den virtuella datorn eller [](boot-diagnostics.md) den virtuella datorns skalnings uppsättnings instans.

    ![Inställningarna för startdiagnostik](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Inställningar som är tillgängliga för Linux-distributioner finns i [seriell konsol Linux-distributions tillgänglighet](#serial-console-linux-distribution-availability).

- Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste konfigureras för seriella utdata på `ttys0`. Det här är standardinställningen för Azure-avbildningar, men du behöver bara markera det här på anpassade avbildningar. Information [nedan](#custom-linux-images).


> [!NOTE]
> Serie konsolen kräver en lokal användare med ett konfigurerat lösen ord. Virtuella datorer eller skalnings uppsättningar för virtuella datorer som kon figurer ATS med en offentlig SSH-nyckel kan inte logga in i serie konsolen. Om du vill skapa en lokal användare med ett lösen ord använder du [tillägget VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), som är tillgängligt i portalen genom att välja **Återställ lösen ord** i Azure Portal och skapa en lokal användare med ett lösen ord.
> Du kan också återställa administratörs lösen ordet i ditt konto genom [att använda grub för att starta i enanvändarläge](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Tillgänglighet för Linux-distribution för serie konsol
För att serie konsolen ska fungera korrekt måste gäst operativ systemet konfigureras för att läsa och skriva konsol meddelanden till den seriella porten. De mest godkända [Azure Linux-distributionerna](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) har den seriella konsolen konfigurerad som standard. Om du väljer **seriell konsol** i avsnittet **support + fel sökning** i Azure Portal ger åtkomst till serie konsolen.

> [!NOTE]
> Om du inte ser något i serie konsolen ser du till att startdiagnostik är aktiverat på den virtuella datorn. Genom att trycka på **RETUR** åtgärdas ofta problem där ingenting visas i serie konsolen.

Distribution      | Åtkomst via seriekonsol
:-----------|:---------------------
Red Hat Enterprise Linux    | Seriell konsol åtkomst aktiverat som standard.
CentOS      | Seriell konsol åtkomst aktiverat som standard.
Ubuntu      | Seriell konsol åtkomst aktiverat som standard.
CoreOS      | Seriell konsol åtkomst aktiverat som standard.
SUSE        | Nyare SLES-avbildningar som är tillgängliga i Azure har åtkomst till serie konsol aktiverat som standard. Om du använder äldre versioner (10 eller tidigare) av SLES i Azure kan du läsa [KB-artikeln](https://www.novell.com/support/kb/doc.php?id=3456486) för att aktivera serie konsolen.
Oracle Linux        | Seriell konsol åtkomst aktiverat som standard.

### <a name="custom-linux-images"></a>Anpassade Linux-avbildningar
Om du vill aktivera en serie konsol för din anpassade Linux VM-avbildning aktiverar du konsol åtkomst i filen */etc/inittab* för att köra en terminal på `ttyS0`. Till exempel: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Du kan också behöva skapa en Getty on ttyS0. Detta kan göras med `systemctl start serial-getty@ttyS0.service`.

Du ska också lägga till ttyS0 som mål för seriella utdata. Mer information om hur du konfigurerar en anpassad avbildning för att arbeta med serie konsolen finns i system krav för att [skapa och ladda upp en Linux-VHD i Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Om du skapar en anpassad kernel bör du överväga att aktivera dessa kernel-flaggor: `CONFIG_SERIAL_8250=y` och `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Konfigurations filen finns vanligt vis i */Boot/* -sökvägen.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Vanliga scenarier för åtkomst till serie konsolen

Scenario          | Åtgärder i serie konsolen
:------------------|:-----------------------------------------
Skadad *FSTAB* -fil | Tryck på **RETUR** -tangenten för att fortsätta och Använd en text redigerare för att åtgärda *FSTAB* -filen. Du kan behöva vara i enanvändarläge för att göra det. Mer information finns i avsnittet om serie konsolen i [så här åtgärdar du fstab-problem](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) och [använder en serie konsol för att få åtkomst till grub och](serial-console-grub-single-user-mode.md)användarläge.
Felaktig brandväggsregler |  Om du har konfigurerat program varan iptables för att blockera SSH-anslutningen kan du använda en serie konsol för att interagera med den virtuella datorn utan SSH. Mer information finns på [sidan program varan iptables man](https://linux.die.net/man/8/iptables).<br>På samma sätt kan du, om brand väggen blockerar SSH-åtkomst, komma åt den virtuella datorn via serie konsolen och konfigurera om brand väggen. Mer information hittar du i [brand väggens dokumentation](https://firewalld.org/documentation/).
Filsystem skadade/kontroll | Information om hur du felsöker skadade fil system med hjälp av en serie konsol finns i avsnittet om serie konsol i den [virtuella Azure Linux-datorn](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) .
Problem med SSH-konfiguration | Komma åt seriekonsolen och ändra inställningarna. Seriell konsol kan användas oberoende av SSH-konfigurationen för en virtuell dator eftersom den inte kräver att den virtuella datorn har nätverks anslutning för att fungera. En fel söknings guide finns på [FELSÖKA SSH-anslutningar till en virtuell Azure Linux-dator som Miss lyckas, fel eller nekas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mer information finns i [detaljerade åtgärder för SSH-felsökning för problem som ansluter till en virtuell Linux-dator i Azure](./detailed-troubleshoot-ssh-connection.md)
Interagera med startprogrammet | Starta om den virtuella datorn från bladet för serie konsolen för att få åtkomst till GRUB på din virtuella Linux-dator. Mer information och distribution information finns i [använda serie konsolen för att komma åt grub och enanvändarläge](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Inaktivera serie konsolen

Som standard har alla prenumerationer åtkomst till seriell konsol. Du kan inaktivera serie konsolen antingen på prenumerations nivå eller skal uppsättnings nivå för virtuell dator/virtuell dator. Detaljerade anvisningar finns [i Aktivera och inaktivera Azures serie konsol](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Seriell konsol-säkerhet

### <a name="access-security"></a>Åtkomstsäkerhet
Åtkomst till seriekonsol är begränsad till användare som har en åtkomstroll av [virtuell Datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller högre för att den virtuella datorn. Om din Azure Active Directory-klient kräver multifaktorautentisering (MFA) och åtkomst till seriell konsol måste också MFA att Seriell konsol åtkomst sker via det [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data som skickas fram och tillbaka krypteras under överföringen.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsol för tillfället är inloggad på [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) loggarna för den virtuella datorn. Åtkomst till de här loggarna ägs och kontrolleras av administratören för Azure virtuella datorer.

> [!CAUTION]
> Inga lösenord för åtkomst för konsolen loggas. Men om kommandon körs i konsolen innehåller eller utgående lösenord, hemligheter, användarnamn eller någon annan form av personligt identifierbar information (PII), skrivs de till diagnostikloggar för VM-start. De skrivs tillsammans med alla andra synlig text, som en del av implementeringen av Seriell konsol rullar tillbaka funktion. Dessa loggar är cirkulär och endast personer med läsbehörighet till lagringskontot för diagnostik ha åtkomst till dem. Vi rekommenderar dock följa bästa praxis för att använda Remote Desktop för allt som kan omfatta hemligheter och/eller personligt identifierbar information.

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriekonsol och en annan användare begär har åtkomst till den samma virtuella datorn, kommer att kopplas från den första användaren och den andra användaren som är anslutna till samma session.

> [!CAUTION]
> Det innebär att en användare som är frånkopplad inte kommer att loggas ut. Möjligheten att genomdriva en utloggning vid från koppling (med SIGHUP eller liknande mekanism) finns fortfarande i översikten. För Windows finns en automatisk timeout som är aktive rad i SAC (administrations konsol). för Linux kan du dock konfigurera inställningen för timeout för Terminal. Om du vill göra det lägger du till `export TMOUT=600` i *. bash_profile* -eller *. Profile* -filen för den användare som du använder för att logga in i-konsolen. Den här inställningen tar bort sessionen efter 10 minuter.

## <a name="accessibility"></a>Hjälpmedel
Hjälpmedel är en viktig fokus för Azures serie konsol. Vi har därför säkerställt att serie konsolen är fullständigt tillgänglig.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd den **fliken** på tangentbordet för att navigera i gränssnittet Seriell konsol från Azure-portalen. Din plats kommer vara markerad på skärmen. Om du vill lämna seriekonsolfönstret fokus trycker du på **Ctrl**+**F6** på tangentbordet.

### <a name="use-serial-console-with-a-screen-reader"></a>Använda en-serie konsol med en skärm läsare
Seriekonsolen har inbyggt stöd för Skärmläsaren. Navigera med en skärmläsare aktiveras kan alternativ text för knappen är markerade som ska läsas upp av Skärmläsaren.

## <a name="known-issues"></a>Kända problem
Vi är medvetna om några problem med serie konsolen och den virtuella datorns operativ system. Här är en lista över de här problemen och stegen för att minska för virtuella Linux-datorer. Dessa problem och begränsningar gäller för både virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer. Om de inte matchar det fel du ser kan du läsa fel i vanliga fel med seriell konsol tjänsten i [vanliga](./serial-console-errors.md)fel i serie konsolen.

Problem                           |   Åtgärd
:---------------------------------|:--------------------------------------------|
Att trycka på **RETUR** när anslutningen popup-meddelandet inte orsakar en uppmaning som ska visas. | Mer information finns i [Hitting ange ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Det här problemet kan uppstå om du kör en anpassad virtuell dator, en härdnings apparat eller en GRUB-konfiguration som gör att Linux inte kan ansluta till den seriella porten.
Seriell konsol text tar bara upp en del av skärm storleken (ofta efter att ha använt en text redigerare). | Serie konsoler har inte stöd för förhandling om fönster storlek ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), vilket innebär att ingen SIGWINCH-signal skickas till uppdateringens skärm storlek och att den virtuella datorn inte kommer att ha någon kunskap om din Terminals storlek. Installera xterm eller ett liknande verktyg för att ge dig kommandot `resize` och kör `resize`.
Klistra in lång sträng fungerar inte. | Seriekonsolen begränsar längden på strängar som klistras in i terminalen för att 2048 tecken för att förhindra överbelastning serieport bandbredd.
Oförutsägbara inmatade tangenter i SLES BYOS-avbildningar. Tangent bords inskrivning känns bara igen sporadiskt. | Detta är ett problem med Plymouth-paketet. Plymouth bör inte köras i Azure eftersom du inte behöver en välkomst skärm och Plymouth stör plattforms möjligheten att använda serie konsol. Ta bort Plymouth med `sudo zypper remove plymouth` och starta sedan om. Du kan också ändra kernel-raden för GRUB-konfigurationen genom att lägga till `plymouth.enable=0` i slutet av raden. Du kan göra detta genom att [Redigera start posten vid start](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles), eller genom att redigera GRUB_CMDLINE_LINUX rad i `/etc/default/grub`, återskapa GRUB med `grub2-mkconfig -o /boot/grub2/grub.cfg`och sedan starta om.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F. Hur gör jag för att skicka feedback?**

A. Ge feedback genom att skapa ett GitHub-problem på https://aka.ms/serialconsolefeedback. Du kan också (mindre rekommenderas), kan du skicka feedback via azserialhelp@microsoft.com eller i kategorin virtuell dator för https://feedback.azure.com.

**F. har serie konsolen stöd för kopiera/klistra in?**

A. Ja. Använd **Ctrl**+**SKIFT**+**C** och **Ctrl**+**SKIFT** + **V** kopiera och klistra in i terminalen.

**F. kan jag använda en serie konsol i stället för en SSH-anslutning?**

A. Även om den här användningen kan verka tekniskt möjlig, är serie konsolen avsedd att användas främst som ett fel söknings verktyg i situationer där det inte går att ansluta via SSH. Vi rekommenderar att du använder serie konsolen som en SSH-ersättning av följande skäl:

- Serie konsolen har inte lika mycket bandbredd som SSH. Eftersom det är en endast text anslutning är det svårt att använda mer grafiska och omfattande interaktioner.
- Seriell konsol åtkomst är för närvarande möjligt bara med ett användar namn och lösen ord. Eftersom SSH-nycklar är mycket säkrare än kombinationer av användar namn/lösen ord, rekommenderar vi SSH över seriell konsol från ett säkerhets perspektiv för inloggning.

**F. Vem kan aktivera eller inaktivera en serie konsol för min prenumeration?**

A. Om du vill aktivera eller inaktivera seriekonsolen på en prenumeration hela-nivå, måste du ha skrivbehörighet till prenumerationen. Roller som har behörighet att skriva vara administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

**F. Vem har åtkomst till serie konsolen för min skalnings uppsättning för virtuella datorer/virtuella datorer?**

A. Du måste ha rollen virtuell dator deltagare eller högre för en virtuell dator eller skalnings uppsättning för virtuell dator för att få åtkomst till serie konsolen.

**F. min serie konsol visar inte något, vad gör jag?**

A. Avbildningen är antagligen felkonfigurerad för seriell konsolåtkomst. Information om hur du konfigurerar avbildningen för att aktivera en serie konsol finns [seriell konsol tillgänglighet för Linux-distribution](#serial-console-linux-distribution-availability).

**F. är serie konsolen tillgänglig för skalnings uppsättningar för virtuella datorer?**

A. Ja det är det! Se [serie konsolen för Virtual Machine Scale Sets](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**F. om jag konfigurerar en skalnings uppsättning för den virtuella datorn eller den virtuella datorn genom att bara använda SSH-nyckelalgoritm, kan jag fortfarande använda den seriella konsolen för att ansluta till min VM/virtuell dators skalnings uppsättnings instans?**

A. Ja. Eftersom serie konsolen inte kräver SSH-nycklar behöver du bara konfigurera en kombination av användar namn/lösen ord. Du kan göra det genom att välja **Återställ lösen ord** i Azure Portal och använda autentiseringsuppgifterna för att logga in i serie konsolen.

## <a name="next-steps"></a>Nästa steg
* Använd serie konsolen för att [komma åt grub och enanvändarläge](serial-console-grub-single-user-mode.md).
* Använd serie konsolen för [NMI-och SysRq-anrop](serial-console-nmi-sysrq.md).
* Lär dig hur du använder serie konsolen för att [Aktivera grub i olika distributioner](serial-console-grub-proactive-configuration.md) 
* Serie konsolen är också tillgänglig för [virtuella Windows-datorer](../windows/serial-console.md).
* Läs mer om [startdiagnostik](boot-diagnostics.md).

