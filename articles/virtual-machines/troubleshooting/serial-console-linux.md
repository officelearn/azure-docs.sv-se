---
title: Azure Serial Console för Linux | Microsoft-dokument
description: Dubbelriktad seriell konsol för virtuella Azure-datorer och skaluppsättningar för virtuella datorer.
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
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167020"
---
# <a name="azure-serial-console-for-linux"></a>Azure-seriekonsol för Linux

Seriekonsolen i Azure-portalen ger åtkomst till en textbaserad konsol för Linux virtuella datorer (VIRTUELLA datorer) och skaluppsättningar för virtuella datorer. Den här seriella anslutningen ansluter till ttys0 seriell port för den virtuella datorn eller den virtuella datorns skalningsuppsättningsinstans, vilket ger åtkomst till den oberoende av nätverks- eller operativsystemtillstånd. Seriell konsol kan endast nås med hjälp av Azure-portalen och är endast tillåten för de användare som har en åtkomstroll som Deltagare eller högre till den virtuella datorn eller skalan för virtuella datorer.

Seriekonsolen fungerar på samma sätt för virtuella datorer och skalningsuppsättningsinstanser för virtuella datorer och virtuella datorer. I det här dokumentet innehåller alla omnämnanden till virtuella datorer implicit infallna instanser av skalningsuppsättningar för virtuella datorer om inte annat anges.

Dokumentation för Serial Console för Windows finns i [Serial Console för Windows](../windows/serial-console.md).

> [!NOTE]
> Seriekonsolen är vanligtvis tillgänglig i globala Azure-regioner och i offentlig förhandsversion i Azure Government. Det är ännu inte tillgängligt i Azure China-molnet.


## <a name="prerequisites"></a>Krav

- Den virtuella datorn eller skalningsgruppsinstansen för virtuella datorer måste använda distributionsmodellen för resurshantering. Klassiska distributioner stöds inte.

- Ditt konto som använder seriell konsol måste ha rollen Deltagare i [virtuell dator](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för den virtuella datorn och [lagringskontot för startdiagnostik](boot-diagnostics.md)

- Den virtuella datorn eller den virtuella datorns skalningsgruppsinstans måste ha en lösenordsbaserad användare. Du kan skapa en med [återställningslösenordsfunktionen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) för vm-åtkomsttillägget. Välj **Återställ lösenord** i avsnittet Support + **felsökning.**

- Den virtuella datorn eller den virtuella datorns skalningsgruppsinstans måste ha [startdiagnostik](boot-diagnostics.md) aktiverad.

    ![Inställningar för startdiagnostik](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Inställningar som är specifika för Linux-distributioner finns i [Linux-distributionstillgänglighet för seriekonsolen](#serial-console-linux-distribution-availability).

- Den virtuella datorn eller den virtuella datorns skalningsgruppsinstans måste konfigureras för seriell utdata på `ttys0`. Detta är standard för Azure-avbildningar, men du vill dubbelkolla detta på anpassade avbildningar. Detaljer [nedan](#custom-linux-images).


> [!NOTE]
> Seriekonsolen kräver en lokal användare med ett konfigurerat lösenord. Virtuella datorer eller skalningsuppsättningar för virtuella datorer som endast konfigurerats med en SSH-offentlig nyckel kan inte logga in på seriekonsolen. Om du vill skapa en lokal användare med ett lösenord använder du [VMAccess-tillägget](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), som är tillgängligt i portalen genom att välja **Återställ lösenord** i Azure-portalen och skapa en lokal användare med ett lösenord.
> Du kan också återställa administratörslösenordet i ditt konto [genom att använda GRUB för att starta i enanvändarläge](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Tillgänglighet för Linux-distribution för seriekonsol
För att seriekonsolen ska fungera korrekt måste gästoperativsystemet konfigureras för att läsa och skriva konsolmeddelanden till den seriella porten. De flesta [godkända Azure Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) har den seriella konsolen konfigurerad som standard. Om du väljer **Seriell konsol** i avsnittet **Support + felsökning** i Azure-portalen får du tillgång till seriekonsolen.

> [!NOTE]
> Om du inte ser något i seriekonsolen kontrollerar du att startdiagnostik är aktiverat på den virtuella datorn. Att trycka **enter** åtgärdar ofta problem där ingenting visas i seriekonsolen.

Distribution      | Åtkomst via seriekonsol
:-----------|:---------------------
Red Hat Enterprise Linux    | Seriell konsolåtkomst aktiverad som standard.
CentOS      | Seriell konsolåtkomst aktiverad som standard.
Debian      | Seriell konsolåtkomst aktiverad som standard.
Ubuntu      | Seriell konsolåtkomst aktiverad som standard.
CoreOS      | Seriell konsolåtkomst aktiverad som standard.
SUSE        | Nyare SLES-avbildningar som är tillgängliga på Azure har seriell konsolåtkomst aktiverad som standard. Om du använder äldre versioner (10 eller tidigare) av SLES på Azure läser du [KB-artikeln](https://www.novell.com/support/kb/doc.php?id=3456486) för att aktivera seriell konsol.
Oracle Linux        | Seriell konsolåtkomst aktiverad som standard.

### <a name="custom-linux-images"></a>Anpassade Linux-bilder
Om du vill aktivera seriekonsolen för din anpassade Linux VM-avbildning aktiverar du `ttyS0`konsolåtkomst i filen */etc/inittab* för att köra en terminal på . Till exempel: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Du kan också behöva leka en getty på ttyS0. Detta kan göras `systemctl start serial-getty@ttyS0.service`med .

Du vill också lägga till ttys0 som mål för serieutdata. Mer information om hur du konfigurerar en anpassad avbildning så att den fungerar med seriekonsolen finns i de allmänna systemkraven på [Skapa och ladda upp en Virtuell Linux-fil i Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Om du skapar en anpassad kärna kan du `CONFIG_SERIAL_8250=y` överväga `CONFIG_MAGIC_SYSRQ_SERIAL=y`att aktivera dessa kärnflaggor: och . Konfigurationsfilen finns vanligtvis i sökvägen */boot/.*

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Vanliga scenarier för åtkomst till seriekonsolen

Scenario          | Åtgärder i seriekonsolen
:------------------|:-----------------------------------------
Bruten *FSTAB-fil* | Tryck på **Retur** för att fortsätta och använda en textredigerare för att åtgärda *FSTAB-filen.* Du kan behöva vara i enanvändarläge för att göra det. Mer information finns i avsnittet med seriekonsolen i [Hur du åtgärdar fstab-problem](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) och [använder seriell konsol för att komma åt GRUB- och single user-läge](serial-console-grub-single-user-mode.md).
Felaktiga brandväggsregler |  Om du har konfigurerat iptables för att blockera SSH-anslutning kan du använda seriell konsol för att interagera med den virtuella datorn utan att behöva SSH. Mer information finns på [iptables man sida](https://linux.die.net/man/8/iptables).<br>På samma sätt, om brandväggen blockerar SSH-åtkomst, kan du komma åt den virtuella datorn via seriell konsol och konfigurera om brandvägg. Mer information finns i [den brandväggsdokumentationen](https://firewalld.org/documentation/).
Filsystemet skadas/kontrolleras | Se seriekonsolen i [Azure Linux VM kan inte starta på grund av filsystemfel](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) för mer information om felsökning av skadade filsystem med seriell konsol.
Problem med SSH-konfiguration | Öppna seriekonsolen och ändra inställningarna. Seriell konsol kan användas oavsett SSH-konfigurationen för en virtuell dator eftersom den inte kräver att den virtuella datorn har nätverksanslutning för att fungera. En felsökningsguide finns på [Felsöka SSH-anslutningar till en Virtuell Azure Linux-dator som misslyckas, fel ut eller nekas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mer information finns på [detaljerade felsökningssteg för SSH för problem med anslutning till en Virtuell Linux-dator i Azure](./detailed-troubleshoot-ssh-connection.md)
Interagera med starthanterare | Starta om den virtuella datorn inifrån seriekonsolbladet för att komma åt GRUB på din Virtuella Linux-dator. Mer information och distributionsspecifik information finns i [Använda seriell konsol för att komma åt GRUB- och enanvändarläge](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Inaktivera seriekonsolen

Som standard har alla prenumerationer seriell konsolåtkomst aktiverad. Du kan inaktivera seriekonsolen på antingen prenumerationsnivå eller vm/virtuell datorskala. Detaljerade instruktioner finns i [Aktivera och inaktivera Azure Serial Console](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Seriell konsolsäkerhet

### <a name="access-security"></a>Åtkomstsäkerhet
Åtkomst till seriekonsolen är begränsad till användare som har en åtkomstroll [som deltagare i virtuell dator](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller högre till den virtuella datorn. Om din Azure Active Directory-klient kräver MFA (Multifaktorautentisering) behöver åtkomsten till seriekonsolen också MFA eftersom seriekonsolens åtkomst är via [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data som skickas fram och tillbaka krypteras på tråden.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsolen loggas för närvarande i [startdiagnostikloggarna](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) för den virtuella datorn. Åtkomst till dessa loggar ägs och kontrolleras av Azure-administratören för virtuella datorer.

> [!CAUTION]
> Inga åtkomstlösenord för konsolen loggas. Men om kommandon som körs i konsolen innehåller eller utdatalösenord, hemligheter, användarnamn eller någon annan form av personligt identifierbar information (PII), kommer de att skrivas till VM-startdiagnostikloggarna. De kommer att skrivas tillsammans med all annan synlig text, som en del av genomförandet av den seriella konsolens scroll back-funktion. Dessa loggar är cirkulära och endast personer med läsbehörighet till diagnostiklagringskontot har åtkomst till dem. Om du matar in dataellerkommandon som innehåller hemligheter eller personuppgifter rekommenderar vi att du använder SSH om det inte är nödvändigt med seriekonsolen.

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriekonsolen och en annan användare har begärt åtkomst till samma virtuella dator kopplas den första användaren från och den andra användaren ansluts till samma session.

> [!CAUTION]
> Det innebär att en användare som är frånkopplad inte loggas ut. Möjligheten att genomdriva en utloggning vid frånkoppling (med hjälp av SIGHUP eller liknande mekanism) finns fortfarande på översikten. För Windows finns en automatisk timeout aktiverad i Special Administrative Console (SAC); För Linux kan du dock konfigurera terminaltidsgränsen. Det gör du `export TMOUT=600` genom att lägga till i *profilfilen bash_profile* eller *.för* den användare som du använder för att logga in på konsolen. Den här inställningen tar time out sessionen efter 10 minuter.

## <a name="accessibility"></a>Hjälpmedel
Hjälpmedel är ett viktigt fokus för Azure Serial Console. Därför har vi sett till att seriekonsolen är fullt tillgänglig.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd **tabbtangenten** på tangentbordet för att navigera i det seriella konsolgränssnittet från Azure-portalen. Din plats markeras på skärmen. Om du vill lämna fokus i seriekonsolfönstret trycker du på **Ctrl**+**F6** på tangentbordet.

### <a name="use-serial-console-with-a-screen-reader"></a>Använda seriekonsol med en skärmläsare
Den seriella konsolen har skärmläsare stöd inbyggd. Om du navigerar med en skärmläsare påslagen kan alternativtexten för den markerade knappen läsas upp högt av skärmläsaren.

## <a name="known-issues"></a>Kända problem
Vi är medvetna om vissa problem med seriekonsolen och den virtuella datorns operativsystem. Här är en lista över dessa problem och steg för begränsning för virtuella Linux-datorer. Dessa problem och mildrande åtgärder gäller för både virtuella datorer och skalningsuppsättningsinstanser för virtuella datorer. Om dessa inte matchar felet du ser läser du de vanliga seriella konsolservicefelen vid [vanliga seriella konsolfel](./serial-console-errors.md).

Problem                           |   Åtgärd
:---------------------------------|:--------------------------------------------|
Om du trycker på **Retur** efter att anslutningsbanderollen inte visas visas en inloggningsfråga. | GRUB kanske inte är korrekt konfigurerad. Kör följande kommandon: `grub2-mkconfig -o /etc/grub2-efi.cfg` och/eller `grub2-mkconfig -o /etc/grub2.cfg`. Mer information finns i [Hitting enter gör ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Det här problemet kan uppstå om du kör en anpassad virtuell dator, härdad installation eller GRUB-config som gör att Linux misslyckas med att ansluta till den seriella porten.
Seriell konsoltext tar bara upp en del av skärmstorleken (ofta efter att du har använt en textredigerare). | Seriella konsoler stöder inte förhandlingar om fönsterstorlek[(RFC 1073),](https://www.ietf.org/rfc/rfc1073.txt)vilket innebär att det inte kommer att finnas någon SIGWINCH-signal som skickas för att uppdatera skärmstorleken och den virtuella datorn kommer inte att ha någon kunskap om terminalens storlek. Installera xterm eller ett liknande verktyg `resize` för att `resize`ge dig kommandot och kör sedan .
Att klistra in långa strängar fungerar inte. | Seriekonsolen begränsar längden på strängar som klistras in i terminalen till 2048 tecken för att förhindra överbelastning av den seriella portens bandbredd.
Oregelbunden tangentbordsinmatning i SLES BYOS-bilder. Tangentbordsingången känns bara igen sporadiskt. | Detta är ett problem med Plymouth-paketet. Plymouth bör inte köras i Azure eftersom du inte behöver en välkomstskärm och Plymouth stör plattformsmöjligheten att använda Serial Console. Ta bort `sudo zypper remove plymouth` Plymouth med och starta sedan om. Du kan också ändra kärnraden i GRUB-config genom att lägga till `plymouth.enable=0` till i slutet av raden. Du kan göra detta genom [att redigera startposten vid uppstart](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)eller `/etc/default/grub`genom att redigera `grub2-mkconfig -o /boot/grub2/grub.cfg`den GRUB_CMDLINE_LINUX raden i , återskapa GRUB med och sedan starta om.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F. Hur kan jag skicka feedback?**

A. Ge feedback genom att skapa https://aka.ms/serialconsolefeedbackett GitHub-problem på . Alternativt (mindre att föredra) kan azserialhelp@microsoft.com du skicka feedback https://feedback.azure.comvia eller i kategorin virtuell dator i .

**F. Stöder den seriella konsolen kopiering/inklistring?**

A. Ja. Använd **Ctrl**+**Skift**+**C** och **Ctrl**+**Skift**+**V** för att kopiera och klistra in terminalen.

**F. Kan jag använda seriekonsol i stället för en SSH-anslutning?**

A. Även om denna användning kan verka tekniskt möjligt, är den seriella konsolen avsedd att användas främst som ett felsökningsverktyg i situationer där anslutning via SSH inte är möjligt. Vi rekommenderar att du inte använder seriekonsolen som SSH-ersättning av följande skäl:

- Den seriella konsolen har inte lika mycket bandbredd som SSH. Eftersom det är en text-bara-anslutning, mer GUI-tunga interaktioner är svåra.
- Seriell konsolåtkomst är för närvarande endast möjlig med hjälp av ett användarnamn och lösenord. Eftersom SSH-nycklar är mycket säkrare än kombinationer av användarnamn/lösenord rekommenderar vi SSH via seriell konsol.

**F. Vem kan aktivera eller inaktivera seriell konsol för min prenumeration?**

A. Om du vill aktivera eller inaktivera seriekonsolen på en prenumerationsnivå måste du ha skrivbehörighet till prenumerationen. Roller som har skrivbehörighet omfattar administratörs- eller ägarroller. Anpassade roller kan också ha skrivbehörighet.

**F. Vem kan komma åt seriekonsolen för min VM/virtuell dator skala uppsättning?**

A. Du måste ha rollen Deltagare i virtuell dator eller högre för en skala för virtuell dator eller virtuell dator inställd för att komma åt seriekonsolen.

**F. Min seriell konsol visar ingenting, vad gör jag?**

A. Bilden är troligen felkonfigurerad för seriell konsolåtkomst. Information om hur du konfigurerar avbildningen så att den aktiverar seriekonsolen finns i [Tillgänglighet för Linux-distribution av seriekonsolen](#serial-console-linux-distribution-availability).

**F. Är seriekonsolen tillgänglig för skaluppsättningar för virtuella datorer?**

A. Ja det är det! Se [Seriekonsol för skalningsuppsättningar för virtuella datorer](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**F. Om jag ställer in min vm- eller virtuella datorskalauppsättning med endast SSH-nyckelautentisering, kan jag fortfarande använda seriekonsolen för att ansluta till min vm/virtual machine-skalningsuppsättningsinstans?**

A. Ja. Eftersom den seriella konsolen inte kräver SSH-nycklar behöver du bara konfigurera en kombination av användarnamn/lösenord. Du kan göra det genom att välja **Återställ lösenord** i Azure-portalen och använda dessa autentiseringsuppgifter för att logga in på seriekonsolen.

## <a name="next-steps"></a>Nästa steg
* Använd seriekonsolen för att [komma åt GRUB- och enanvändarläge](serial-console-grub-single-user-mode.md).
* Använd seriekonsolen för [NMI- och SysRq-samtal](serial-console-nmi-sysrq.md).
* Lär dig hur du använder seriekonsolen för att [aktivera GRUB i olika distributioner](serial-console-grub-proactive-configuration.md)
* Seriekonsolen är också tillgänglig för [virtuella Windows-datorer](../windows/serial-console.md).
* Läs mer om [startdiagnostik](boot-diagnostics.md).

