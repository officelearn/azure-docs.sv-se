---
title: Seriekonsol för Azure-dator för Linux | Microsoft Docs
description: Dubbelriktad seriekonsol för virtuella Azure-datorer.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: f407d87249c44ad3a4773b2cd8fc85ee09506ceb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921424"
---
# <a name="virtual-machine-serial-console-for-linux"></a>Seriekonsol för virtuell dator för Linux

Seriekonsol för virtuell dator (VM) i Azure portal ger åtkomst till en textbaserad konsol för Linux-datorer. Den här seriell anslutning ansluter till den seriella porten COM1 för den virtuella datorn, som ger tillgång till den, oberoende av den virtuella datorns tillstånd för nätverk eller operativsystem. Åtkomst till seriekonsol för en virtuell dator kan du göra med Azure-portalen. Den tillåts endast för de användare som har en rollen virtuell Datordeltagare eller högre för den virtuella datorn.

Seriell konsol dokumentation för virtuella Windows-datorer finns i [seriekonsol för virtuell dator för Windows](../windows/serial-console.md).

> [!NOTE]
> Seriekonsol för virtuella datorer är allmänt tillgängligt i globala Azure-regioner. Det finns inte ännu i Azure government eller Azure Kina moln.


## <a name="prerequisites"></a>Förutsättningar

- Den virtuella datorn som du försöker komma åt en seriell konsol måste använda resource management-distributionsmodellen. Klassiska distributioner stöds inte.

- Den virtuella datorn som du försöker komma åt en seriell konsol måste ha [startdiagnostik](boot-diagnostics.md) aktiverat.

    ![Inställningarna för startdiagnostik](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Ett konto som använder en seriell konsol måste ha den [rollen virtuell Datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för den virtuella datorn och [startdiagnostik](boot-diagnostics.md) storage-konto:

- Den virtuella datorn som du försöker komma åt en seriell konsol måste ha en lösenordsbaserad konto. Du kan skapa en med den [Återställ lösenord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funktion av access-tillägg för virtuell dator. Välj **Återställ lösenord** från den **Support och felsökning** avsnittet.

- Inställningar som är specifika för Linux-distributioner, se [seriekonsolen tillgänglighet för Linux-distribution](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>Kom igång med seriekonsolen
Seriekonsol för virtuella datorer är bara tillgänglig via Azure portal:

  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. På menyn till vänster väljer **virtuella datorer**.

  1. Välj en virtuell dator i listan. Översiktssidan för den virtuella datorn öppnas.

  1. Rulla ned till den **Support och felsökning** och väljer **seriekonsolen**. Ett nytt fönster med seriell konsol öppnas och börjar anslutningen.

     ![Linux seriekonsolfönstret](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE]
> Seriekonsolen kräver en lokal användare med ett konfigurerat lösenord. Virtuella datorer som konfigurerats enbart med en offentlig SSH-nyckel kan inte logga in till seriekonsol. Om du vill skapa en lokal användare med ett lösenord, använda den [VMAccess-tillägget](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), som är tillgängligt i portalen genom att välja **Återställ lösenord** i Azure-portalen och skapa en lokal användare med ett lösenord.
> Du kan också återställa administratörslösenordet i ditt konto genom att [med GRUB starta i enanvändarläge](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Seriell konsol tillgänglighet för Linux-distribution
Gästoperativsystemet måste konfigureras för att läsa och skriva konsolmeddelanden att den seriella porten för seriekonsolen ska fungera korrekt. De flesta [godkända Azure Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) har seriekonsolen konfigureras som standard. Att välja **seriekonsolen** i den **Support och felsökning** på Azure portal ger åtkomst till seriekonsol.

Distribution      | Seriell konsolåtkomst
:-----------|:---------------------
Red Hat Enterprise Linux    | Seriell konsolåtkomst aktiverat som standard.
CentOS      | Seriell konsolåtkomst aktiverat som standard.
Ubuntu      | Seriell konsolåtkomst aktiverat som standard.
CoreOS      | Seriell konsolåtkomst aktiverat som standard.
SUSE        | Nyare SLES-avbildningar på Azure har seriell konsolåtkomst aktiverat som standard. Om du använder äldre versioner (10 eller tidigare) av SLES på Azure kan du läsa den [KB-artikel](https://www.novell.com/support/kb/doc.php?id=3456486) att aktivera Seriell konsol.
Oracle Linux        | Seriell konsolåtkomst aktiverat som standard.
Anpassade Linux-avbildningar     | Aktivera seriekonsol för en anpassad Linux VM-avbildning genom att aktivera åtkomst till konsolen i filen */etc/inittab* att köra en terminal på `ttyS0`. Till exempel: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Läs mer om hur du skapar anpassade avbildningar korrekt [skapa och ladda upp en VHD för Linux i Azure](https://aka.ms/createuploadvhd). Om du skapar en anpassad kernel, Överväg att aktivera dessa kernel-flaggor: `CONFIG_SERIAL_8250=y` och `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Konfigurationsfilen finns vanligtvis under den */boot/* sökväg.

> [!NOTE]
> Om du inte ser något i seriekonsolen, se till att startdiagnostik har aktiverats på den virtuella datorn.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Vanliga scenarier för att komma åt seriekonsolen

Scenario          | Åtgärder i seriekonsolen
:------------------|:-----------------------------------------
Bruten *FSTAB* fil | Tryck på den **RETUR** att fortsätta och använda en textredigerare för att åtgärda den *FSTAB* fil. Du kan behöva vara i enanvändarläge gör. Mer information finns i avsnittet seriekonsolen i [hur du löser problem med fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) och [Använd seriekonsol för att komma åt GRUB och enanvändarläge](serial-console-grub-single-user-mode.md).
Felaktig brandväggsregler |  Om du har konfigurerat iptables för att blockera SSH-anslutningen kan använda du Seriell konsol för att interagera med den virtuella datorn utan att behöva SSH. Mer information finns på den [iptables man sidan](https://linux.die.net/man/8/iptables). Om du firewalld blockerar SSH-åtkomst, kan du åtkomst till den virtuella datorn via seriekonsolen och konfigurera om firewalld. Mer information finns i den [firewalld dokumentation](https://firewalld.org/documentation/).
Filsystem skadade/kontroll | Se avsnittet seriekonsolen i [virtuell Linux-dator kan inte starta på grund av fel i filsystemet](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) för mer information om hur du felsöker skadat filsystem med hjälp av Seriell konsol.
Problem med SSH-konfigurationen | Komma åt seriekonsolen och ändra inställningarna. Seriell konsol kan användas oavsett SSH-konfigurationen för en virtuell dator som inte kräver att den virtuella datorn har nätverksanslutning ska fungera. Felsökningsguide för finns på [Felsök SSH-anslutningar till en virtuell Linux-dator som misslyckas, fel, eller avvisas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mer information finns på [detaljerad SSH felsökningssteg för problem med anslutning till en Linux-VM i Azure](./detailed-troubleshoot-ssh-connection.md)
Interagera med startprogrammet | Starta om den virtuella datorn från seriell konsol-bladet för att komma åt GRUB på Linux-VM. Mer information och -distribution-specifik information finns i [Använd seriekonsol för att komma åt GRUB och enanvändarläge](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Inaktivera seriekonsolen
Som standard har alla prenumerationer seriell konsolåtkomst är aktiverad för alla virtuella datorer. Du kan inaktivera seriekonsolen på prenumerationsnivån eller VM-nivå.

> [!NOTE]
> Om du vill aktivera eller inaktivera seriekonsol för en prenumeration, måste du ha skrivbehörighet till prenumerationen. Dessa behörigheter kan vara administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

### <a name="subscription-level-disable"></a>Prenumerationsnivå inaktivera
Seriell konsol kan inaktiveras för en hel prenumeration via den [inaktivera konsolen REST API-anrop](/rest/api/serialconsole/console/disableconsole). Du kan använda den **prova** funktionen som är tillgängliga på den här API-dokumentationssidan inaktiverar och aktiverar Seriell konsol för en prenumeration. Ange ditt prenumerations-ID för **subscriptionId**, ange **standard** för **standard**, och välj sedan **kör**. Azure CLI-kommandon är ännu inte tillgängliga.

![Testa REST-API](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Du kan också använda följande uppsättning bash-kommandon i Cloud Shell för att inaktivera, aktivera och visa inaktiverad status för seriekonsolen för en prenumeration:

* Hämta inaktiverad status för seriekonsolen för en prenumeration:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Inaktivera seriekonsol för en prenumeration:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Så här aktiverar Seriell konsol för en prenumeration:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

### <a name="vm-level-disable"></a>Inaktivera för VM-nivå
Seriell konsol kan inaktiveras för en specifik virtuell dator genom att inaktivera inställningen för den virtuella datorn boot diagnostics. Inaktivera startdiagnostik från Azure portal för att inaktivera seriekonsolen för den virtuella datorn.

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
> Det innebär att en användare som är frånkopplad inte kommer att loggas ut. Möjlighet att framtvinga en utloggning vid frånkoppling (med hjälp av SIGHUP eller liknande mekanism) är fortfarande i översikten. Det finns en automatisk tidsgräns har aktiverats i särskilda administrativa SAC (Console); för Windows Du kan dock konfigurera terminal timeout-inställningen för Linux. Du gör detta genom att lägga till `export TMOUT=600` i din *.bash_profile* eller *.profile* -filen för att användare kan du använda för att logga in på konsolen. Den här inställningen når tidsgränsen för sessionen efter 10 minuter.

## <a name="accessibility"></a>Hjälpmedel
Hjälpmedel är viktiga fokus för Azure seriekonsol. Vi säkerställt att seriekonsolen är helt tillgängliga för detta ändamål.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd den **fliken** på tangentbordet för att navigera i gränssnittet Seriell konsol från Azure-portalen. Din plats kommer vara markerad på skärmen. Om du vill lämna seriekonsolfönstret fokus trycker du på **Ctrl**+**F6** på tangentbordet.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Använda seriekonsolen med en skärmläsare
Seriekonsolen har inbyggt stöd för Skärmläsaren. Navigera med en skärmläsare aktiveras kan alternativ text för knappen är markerade som ska läsas upp av Skärmläsaren.

## <a name="errors"></a>Fel
Eftersom de flesta felen är tillfälliga kan försöker anslutningen ofta åtgärda dem. I följande tabell visas en lista över fel och åtgärder.

Fel                            |   Åtgärd
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta inställningarna för startdiagnostik för  *&lt;VMNAME&gt;*. Se till att startdiagnostik har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [startdiagnostik](boot-diagnostics.md) aktiverat.
Den virtuella datorn är i tillståndet stoppad frigjord. Starta den virtuella datorn och försök Seriell konsol-anslutning. | Den virtuella datorn måste vara i tillståndet igång för att komma åt seriekonsolen.
Du har inte behörighet att använda den här virtuella datorn med seriekonsolen. Kontrollera att du har minst deltagarbehörigheter för virtuell dator.| Seriell konsol-åtkomst kräver vissa behörigheter. Mer information finns i [krav](#prerequisites).
Det går inte att fastställa resursgruppen för startdiagnostiklagringskonto  *&lt;STORAGEACCOUNTNAME&gt;*. Kontrollera att startdiagnostik har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell konsol-åtkomst kräver vissa behörigheter. Mer information finns i [krav](#prerequisites).
Web socket är stängd eller kunde inte öppnas. | Du kan behöva godkänna `*.console.azure.com`. En mer detaljerad men längre metod är att godkänna den [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som ändras relativt regelbundet.
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Se till att startdiagnostik inte har en brandvägg för kontot. Ett lagringskonto för tillgänglig startdiagnostik är nödvändiga för seriekonsolen ska fungera.

## <a name="known-issues"></a>Kända problem
Vi är medvetna om några problem med seriell konsol. Här är en lista över dessa problem och åtgärder för problemlösning.

Problem                           |   Åtgärd
:---------------------------------|:--------------------------------------------|
Att trycka på **RETUR** när anslutningen popup-meddelandet inte orsakar en uppmaning som ska visas. | Mer information finns i [Hitting ange ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Det här problemet kan inträffa om du kör en anpassad virtuell dator, förstärkt installation eller GRUB konfiguration som orsakar Linux för att kunna ansluta ordentligt till den seriella porten.
Seriell konsol text tar endast upp en del av skärmstorlek (ofta när du använder en textredigerare). | Seriell konsoler stöder inte förhandling om fönsterstorlek ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), vilket innebär att det blir inga SIGWINCH signalen skickas till uppdatera skärmens storlek och den virtuella datorn har ingen kunskap om storleken på din terminal. Installera xterm eller ett liknande verktyg för att förse dig med den `resize` kommandot och kör sedan `resize`.
Klistra in lång sträng fungerar inte. | Seriekonsolen begränsar längden på strängar som klistras in i terminalen för att 2048 tecken för att förhindra överbelastning serieport bandbredd.
Seriell konsol fungerar inte med en brandvägg för storage-konto. | Seriell konsol avsiktligt fungerar inte med storage-konto brandväggar aktiverad på startdiagnostiklagringskonto.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**FRÅGOR OCH. Hur kan jag skicka feedback?**

A. Ge feedback genom att skapa ett GitHub-ärende på https://aka.ms/serialconsolefeedback. Du kan också (mindre rekommenderas), kan du skicka feedback via azserialhelp@microsoft.com eller i kategorin virtuell dator för https://feedback.azure.com.

**FRÅGOR OCH. Stöder seriekonsolen kopiera och klistra in?**

A. Ja. Använd **Ctrl**+**SKIFT**+**C** och **Ctrl**+**SKIFT** + **V** kopiera och klistra in i terminalen.

**FRÅGOR OCH. Kan jag använda seriekonsolen i stället för en SSH-anslutning?**

A. Medan den här användningen kan verka tekniskt möjligt, är seriekonsolen avsedd att användas som ett verktyg för felsökning i situationer där anslutning via SSH inte är möjligt. Vi rekommenderar mot att använda seriekonsolen som en SSH-ersättning av följande skäl:

- Seriekonsolen har inte så mycket bandbredd som SSH. Eftersom det är en anslutning med endast text, är mer omfattande GUI-interaktioner svåra.
- Seriell konsolåtkomst går för närvarande bara utförs med ett användarnamn och lösenord. Eftersom SSH-nycklar är mycket säkrare än kombinationer av användarnamn/lösenord, inloggningssäkerhet ur, rekommenderar vi att SSH via seriell konsol.

**FRÅGOR OCH. Vem kan aktivera eller inaktivera seriekonsol för min prenumeration?**

A. Om du vill aktivera eller inaktivera seriekonsolen på en prenumeration hela-nivå, måste du ha skrivbehörighet till prenumerationen. Roller som har behörighet att skriva vara administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

**FRÅGOR OCH. Vem som kan komma åt seriekonsolen för min virtuella dator?**

A. Du måste ha rollen virtuell Datordeltagare eller högre för en virtuell dator att få åtkomst till den Virtuella datorns Seriell konsol.

**FRÅGOR OCH. Min seriekonsolen inte visar någonting, vad gör jag?**

A. Avbildningen är antagligen felkonfigurerad för seriell konsolåtkomst. Information om hur du konfigurerar din avbildning om du vill aktivera seriekonsolen finns i [seriekonsolen tillgänglighet för Linux-distribution](#serial-console-linux-distribution-availability).

**FRÅGOR OCH. Är seriell konsol som är tillgänglig för VM-skalningsuppsättningar?**

A. För närvarande stöds inte åtkomst till seriekonsol för VM-skalningsuppsättningsinstanser.

**FRÅGOR OCH. Om jag har konfigurerat den virtuella datorn med hjälp av endast SSH-nyckelautentisering, kan jag fortfarande använda seriekonsolen för att ansluta till min virtuella dator?**

A. Ja. Eftersom seriekonsolen inte kräver SSH-nycklar, behöver du bara konfigurera en kombination av användarnamn/lösenord. Du kan göra det genom att välja **Återställ lösenord** i Azure-portalen och använder dessa autentiseringsuppgifter för att logga in till seriekonsol.

## <a name="next-steps"></a>Nästa steg
* Använd seriekonsol för att [åtkomst till GRUB och enanvändarläge](serial-console-grub-single-user-mode.md).
* Använd seriekonsol för [NMI och SysRq anrop](serial-console-nmi-sysrq.md).
* Lär dig hur du använder seriekonsol för att [aktivera GRUB i olika distributioner](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* Seriekonsolen är också tillgängligt för [Windows virtuella datorer](../windows/serial-console.md).
* Läs mer om [startdiagnostik](boot-diagnostics.md).

