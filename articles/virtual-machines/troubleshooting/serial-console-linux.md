---
title: Azures serie konsol för Linux | Microsoft Docs
description: Dubbelriktad serie konsol för Azure Virtual Machines och Virtual Machine Scale Sets.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 4e0c91096d5efdcc9639a7127126d8e4b89ef068
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090149"
---
# <a name="azure-serial-console-for-linux"></a>Azures serie konsol för Linux

Serie konsolen i Azure Portal ger till gång till en text baserad konsol för virtuella Linux-datorer och instanser av skalnings uppsättningar för virtuella datorer. Den här seriella anslutningen ansluter till den ttyS0 serie porten för den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans, vilket ger åtkomst till den oberoende av nätverket eller operativ systemets tillstånd. Det går bara att komma åt serie konsolen med hjälp av Azure Portal och tillåts bara för de användare som har åtkomst rollen deltagare eller högre till den virtuella datorn eller skalnings uppsättningen för den virtuella datorn.

Serie konsolen fungerar på samma sätt för virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer. I det här dokumentet kommer alla omnämnanden för virtuella datorer att implicit inkludera skalnings uppsättnings instanser för virtuella datorer om inget annat anges.

Information om en dokumentation för Windows i serie konsolen finns i [serie konsol för Windows](../windows/serial-console.md).

> [!NOTE]
> Serie konsolen är allmänt tillgänglig i globala Azure-regioner. Det finns inte ännu i Azure government eller Azure Kina moln.


## <a name="prerequisites"></a>Förutsättningar

- Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste använda distributions modellen för resurs hantering. Klassiska distributioner stöds inte.

- Ditt konto som använder en serie konsol måste ha [rollen virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) för den virtuella datorn och [](boot-diagnostics.md) lagrings kontot för startdiagnostik

- Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste ha en lösenordsbaserad användare. Du kan skapa en med den [Återställ lösenord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funktion av access-tillägg för virtuell dator. Välj **Återställ lösenord** från den **Support och felsökning** avsnittet.

- Startdiagnostik måste vara aktiverat på den virtuella datorn eller [](boot-diagnostics.md) den virtuella datorns skalnings uppsättnings instans.

    ![Inställningarna för startdiagnostik](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Inställningar som är tillgängliga för Linux-distributioner finns i [seriell konsol Linux-distributions tillgänglighet](#serial-console-linux-distribution-availability).

- Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste konfigureras för seriella utdata på `ttys0`. Det här är standardinställningen för Azure-avbildningar, men du behöver bara markera det här på anpassade avbildningar. Information [nedan](#custom-linux-images).


## <a name="get-started-with-the-serial-console"></a>Kom igång med serie konsolen
Serie konsolen för virtuella datorer och skalnings uppsättningen för virtuella datorer kan bara nås via Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Serie konsol för Virtual Machines
Serie konsolen för virtuella datorer är lika enkel som att klicka på **seriell konsol** i avsnittet **support och fel sökning** i Azure Portal.
  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. Gå till **alla resurser** och välj en virtuell dator. Översikts sidan för den virtuella datorn öppnas.

  1. Rulla ned till den **Support och felsökning** och väljer **seriekonsolen**. Ett nytt fönster med seriell konsol öppnas och börjar anslutningen.

     ![Fönstret för seriella Linux-konsol](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Serie konsol för Virtual Machine Scale Sets
Serie konsolen är tillgänglig per instans för skalnings uppsättningar för virtuella datorer. Du måste gå till den enskilda instansen av en skalnings uppsättning för virtuella datorer innan du ser knappen **seriell konsol** . Om den virtuella datorns skalnings uppsättning inte har startdiagnostik aktive rad, se till att du uppdaterar modell för skalnings uppsättning för virtuella datorer för att aktivera startdiagnostik, och uppgradera sedan alla instanser till den nya modellen för att få åtkomst till serie konsolen.
  1. Öppna [Azure-portalen](https://portal.azure.com).

  1. Gå till **alla resurser** och välj en skalnings uppsättning för virtuell dator. Översikts sidan för skalnings uppsättningen för den virtuella datorn öppnas.

  1. Navigera till **instanser**

  1. Välj en instans för skalnings uppsättning för virtuell dator

  1. I avsnittet **support och fel sökning** väljer du **seriell konsol**. Ett nytt fönster med seriell konsol öppnas och börjar anslutningen.

     ![Serie konsol för skalnings uppsättning för virtuella Linux-datorer](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> Serie konsolen kräver en lokal användare med ett konfigurerat lösen ord. Virtuella datorer eller skalnings uppsättningar för virtuella datorer som kon figurer ATS med en offentlig SSH-nyckel kan inte logga in i serie konsolen. Om du vill skapa en lokal användare med ett lösen ord använder du [tillägget VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), som är tillgängligt i portalen genom att välja **Återställ lösen ord** i Azure Portal och skapa en lokal användare med ett lösen ord.
> Du kan också återställa administratörs lösen ordet i ditt konto genom [att använda grub för att starta i enanvändarläge](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Tillgänglighet för Linux-distribution för serie konsol
För att serie konsolen ska fungera korrekt måste gäst operativ systemet konfigureras för att läsa och skriva konsol meddelanden till den seriella porten. De mest godkända [Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) -distributionerna har den seriella konsolen konfigurerad som standard. Om du väljer **seriell konsol** i avsnittet **support + fel sökning** i Azure Portal ger åtkomst till serie konsolen.

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
Om du vill aktivera en serie konsol för din anpassade Linux VM-avbildning aktiverar du konsol åtkomst i filen */etc/inittab* för att köra `ttyS0`en terminal på. Till exempel: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`.

Du ska också lägga till ttyS0 som mål för seriella utdata. Mer information om hur du konfigurerar en anpassad avbildning för att arbeta med serie konsolen finns i system krav för att [skapa och ladda upp en Linux-VHD i Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Om du skapar en anpassad kernel bör du överväga att aktivera dessa kernel- `CONFIG_SERIAL_8250=y` flaggor `CONFIG_MAGIC_SYSRQ_SERIAL=y`: och. Konfigurations filen finns vanligt vis i */Boot/* -sökvägen.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Vanliga scenarier för åtkomst till serie konsolen

Scenario          | Åtgärder i serie konsolen
:------------------|:-----------------------------------------
Skadad *FSTAB* -fil | Tryck på **RETUR** -tangenten för att fortsätta och Använd en text redigerare för att åtgärda *FSTAB* -filen. Du kan behöva vara i enanvändarläge för att göra det. Mer information finns i avsnittet om serie konsolen i [så här åtgärdar du fstab-problem](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) och [använder en serie konsol för att få åtkomst till grub och](serial-console-grub-single-user-mode.md)användarläge.
Felaktig brandväggsregler |  Om du har konfigurerat program varan iptables för att blockera SSH-anslutningen kan du använda en serie konsol för att interagera med den virtuella datorn utan SSH. Mer information finns på [sidan program varan iptables man](https://linux.die.net/man/8/iptables).<br>På samma sätt kan du, om brand väggen blockerar SSH-åtkomst, komma åt den virtuella datorn via serie konsolen och konfigurera om brand väggen. Mer information hittar du i [brand väggens dokumentation](https://firewalld.org/documentation/).
Filsystem skadade/kontroll | Information om hur du felsöker skadade fil system med hjälp av en serie konsol finns i avsnittet om serie konsol i den [virtuella Azure Linux-datorn](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) .
Problem med SSH-konfiguration | Komma åt seriekonsolen och ändra inställningarna. Seriell konsol kan användas oberoende av SSH-konfigurationen för en virtuell dator eftersom den inte kräver att den virtuella datorn har nätverks anslutning för att fungera. En fel söknings guide finns på [FELSÖKA SSH-anslutningar till en virtuell Azure Linux-dator som Miss lyckas, fel eller nekas](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mer information finns i [detaljerade åtgärder för SSH-felsökning för problem som ansluter till en virtuell Linux-dator i Azure](./detailed-troubleshoot-ssh-connection.md)
Interagera med startprogrammet | Starta om den virtuella datorn från bladet för serie konsolen för att få åtkomst till GRUB på din virtuella Linux-dator. Mer information och distribution information finns i [använda serie konsolen för att komma åt grub och enanvändarläge](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Inaktivera serie konsolen
Som standard har alla prenumerationer åtkomst till seriell konsol. Du kan inaktivera serie konsolen antingen på prenumerations nivå eller skal uppsättnings nivå för virtuell dator/virtuell dator. Observera att startdiagnostik måste vara aktiverat på en virtuell dator för att serie konsolen ska fungera.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Inaktivera skalnings uppsättning på VM/virtuell dator
Serie konsolen kan inaktive ras för en angiven virtuell dator eller en virtuell dators skalnings uppsättning genom att inaktivera inställningen för startdiagnostik. Inaktivera startdiagnostik från Azure Portal för att inaktivera serie konsolen för den virtuella datorn eller skalnings uppsättningen för den virtuella datorn. Om du använder en seriell konsol på en skalnings uppsättning för virtuella datorer måste du uppgradera de virtuella datorernas skalnings uppsättnings instanser till den senaste modellen.

> [!NOTE]
> Om du vill aktivera eller inaktivera seriekonsol för en prenumeration, måste du ha skrivbehörighet till prenumerationen. Dessa behörigheter inkluderar administratörs-eller ägar roller. Anpassade roller kan också ha skrivbehörighet.

### <a name="subscription-level-disable"></a>Prenumerationsnivå inaktivera
Seriell konsol kan inaktiveras för en hel prenumeration via den [inaktivera konsolen REST API-anrop](/rest/api/serialconsole/console/disableconsole). Den här åtgärden kräver åtkomst till deltagar nivå eller över till prenumerationen. Du kan använda den **prova** funktionen som är tillgängliga på den här API-dokumentationssidan inaktiverar och aktiverar Seriell konsol för en prenumeration. Ange ditt prenumerations-ID för **subscriptionId**, ange **standard** som standard och välj sedan **Kör**. Azure CLI-kommandon är ännu inte tillgängliga.

Om du vill återaktivera en serie konsol för en prenumeration använder du [REST API anrop för att aktivera konsolen](/rest/api/serialconsole/console/enableconsole).

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
> Det innebär att en användare som är frånkopplad inte kommer att loggas ut. Möjligheten att genomdriva en utloggning vid från koppling (med SIGHUP eller liknande mekanism) finns fortfarande i översikten. För Windows finns en automatisk timeout som är aktive rad i SAC (administrations konsol). för Linux kan du dock konfigurera inställningen för timeout för Terminal. Det gör du genom att `export TMOUT=600` lägga till i filen *. bash_profile* eller *. Profile* för den användare som du använder för att logga in i-konsolen. Den här inställningen tar bort sessionen efter 10 minuter.

## <a name="accessibility"></a>Hjälpmedel
Hjälpmedel är en viktig fokus för Azures serie konsol. Vi har därför säkerställt att serie konsolen är fullständigt tillgänglig.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd den **fliken** på tangentbordet för att navigera i gränssnittet Seriell konsol från Azure-portalen. Din plats kommer vara markerad på skärmen. Om du vill lämna seriekonsolfönstret fokus trycker du på **Ctrl**+**F6** på tangentbordet.

### <a name="use-serial-console-with-a-screen-reader"></a>Använda en-serie konsol med en skärm läsare
Seriekonsolen har inbyggt stöd för Skärmläsaren. Navigera med en skärmläsare aktiveras kan alternativ text för knappen är markerade som ska läsas upp av Skärmläsaren.

## <a name="errors"></a>Fel
Eftersom de flesta felen är tillfälliga kan försöker anslutningen ofta åtgärda dem. I följande tabell visas en lista över fel och åtgärder. Dessa fel och begränsningar gäller för både virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer.

Fel                            |   Åtgärd
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta inställningarna för startdiagnostik för  *&lt;VMNAME&gt;* . Se till att startdiagnostik har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [startdiagnostik](boot-diagnostics.md) aktiverat.
Den virtuella datorn är i tillståndet stoppad frigjord. Starta den virtuella datorn och försök Seriell konsol-anslutning. | Den virtuella datorn måste vara i ett startat tillstånd för att få åtkomst till serie konsolen.
Du har inte de behörigheter som krävs för att använda den här virtuella datorn i serie konsolen. Kontrollera att du har minst deltagarbehörigheter för virtuell dator.| Åtkomst till serie konsolen kräver vissa behörigheter. Mer information finns i [krav](#prerequisites).
Det går inte att fastställa resursgruppen för startdiagnostiklagringskonto  *&lt;STORAGEACCOUNTNAME&gt;* . Kontrollera att startdiagnostik har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Åtkomst till serie konsolen kräver vissa behörigheter. Mer information finns i [krav](#prerequisites).
Web socket är stängd eller kunde inte öppnas. | Du kan behöva godkänna `*.console.azure.com`. En mer detaljerad men längre metod är att godkänna den [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som ändras relativt regelbundet.
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Se till att startdiagnostik inte har en konto brand vägg. Ett lagringskonto för tillgänglig startdiagnostik är nödvändiga för seriekonsolen ska fungera.

## <a name="known-issues"></a>Kända problem
Vi är medvetna om några problem med seriell konsol. Här är en lista över dessa problem och åtgärder för problemlösning. Dessa problem och begränsningar gäller för både virtuella datorer och instanser av skalnings uppsättningar för virtuella datorer.

Problem                           |   Åtgärd
:---------------------------------|:--------------------------------------------|
Att trycka på **RETUR** när anslutningen popup-meddelandet inte orsakar en uppmaning som ska visas. | Mer information finns i [Hitting ange ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Det här problemet kan uppstå om du kör en anpassad virtuell dator, en härdnings apparat eller en GRUB-konfiguration som gör att Linux inte kan ansluta till den seriella porten.
Seriell konsol text tar bara upp en del av skärm storleken (ofta efter att ha använt en text redigerare). | Serie konsoler har inte stöd för förhandling om fönster storlek ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), vilket innebär att ingen SIGWINCH-signal skickas till uppdateringens skärm storlek och att den virtuella datorn inte kommer att ha någon kunskap om din Terminals storlek. Installera xterm eller ett liknande verktyg för att tillhandahålla `resize` kommandot och kör `resize`sedan.
Klistra in lång sträng fungerar inte. | Seriekonsolen begränsar längden på strängar som klistras in i terminalen för att 2048 tecken för att förhindra överbelastning serieport bandbredd.
Seriell konsol fungerar inte med en lagrings konto brand vägg. | Seriell konsol efter design fungerar inte med lagrings kontots brand väggar aktiverade på lagrings kontot för startdiagnostik.
Seriell konsol fungerar inte med ett lagrings konto med hjälp av Azure Data Lake Storage Gen2 med hierarkiska namn områden. | Detta är ett känt problem med hierarkiska namn områden. Du kan undvika detta genom att kontrol lera att lagrings kontot för startdiagnostik för den virtuella datorn inte har skapats med hjälp av Azure Data Lake Storage Gen2. Det här alternativet kan bara anges när lagrings kontot skapas. Du kan behöva skapa ett separat lagrings konto för startdiagnostik utan Azure Data Lake Storage Gen2 aktiverat för att undvika det här problemet.
Oförutsägbara inmatade tangenter i SLES BYOS-avbildningar. Tangent bords inskrivning känns bara igen sporadiskt. | Detta är ett problem med Plymouth-paketet. Plymouth bör inte köras i Azure eftersom du inte behöver en välkomst skärm och Plymouth stör plattforms möjligheten att använda serie konsol. Ta bort Plymouth `sudo zypper remove plymouth` med och starta sedan om. Du kan också ändra kernel-raden för grub-konfigurationen genom `plymouth.enable=0` att lägga till i slutet av raden. Du kan göra detta genom att [Redigera start posten vid start](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles), eller genom att redigera GRUB_CMDLINE_LINUX-raden i `/etc/default/grub`, återskapa grub med `grub2-mkconfig -o /boot/grub2/grub.cfg`och sedan starta om.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**FRÅGOR OCH. Hur kan jag skicka feedback?**

A. Ge feedback genom att skapa ett GitHub- https://aka.ms/serialconsolefeedback problem på. Du kan också (mindre rekommenderas), kan du skicka feedback via azserialhelp@microsoft.com eller i kategorin virtuell dator för https://feedback.azure.com.

**FRÅGOR OCH. Stöder seriekonsolen kopiera och klistra in?**

A. Ja. Använd **Ctrl**+**SKIFT**+**C** och **Ctrl**+**SKIFT** + **V** kopiera och klistra in i terminalen.

**FRÅGOR OCH. Kan jag använda en serie konsol i stället för en SSH-anslutning?**

A. Även om den här användningen kan verka tekniskt möjlig, är serie konsolen avsedd att användas främst som ett fel söknings verktyg i situationer där det inte går att ansluta via SSH. Vi rekommenderar att du använder serie konsolen som en SSH-ersättning av följande skäl:

- Serie konsolen har inte lika mycket bandbredd som SSH. Eftersom det är en endast text anslutning är det svårt att använda mer grafiska och omfattande interaktioner.
- Seriell konsol åtkomst är för närvarande möjligt bara med ett användar namn och lösen ord. Eftersom SSH-nycklar är mycket säkrare än kombinationer av användar namn/lösen ord, rekommenderar vi SSH över seriell konsol från ett säkerhets perspektiv för inloggning.

**FRÅGOR OCH. Vem kan aktivera eller inaktivera en serie konsol för min prenumeration?**

A. Om du vill aktivera eller inaktivera seriekonsolen på en prenumeration hela-nivå, måste du ha skrivbehörighet till prenumerationen. Roller som har behörighet att skriva vara administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

**FRÅGOR OCH. Vem har åtkomst till serie konsolen för min skalnings uppsättning för virtuella datorer/virtuella datorer?**

A. Du måste ha rollen virtuell dator deltagare eller högre för en virtuell dator eller skalnings uppsättning för virtuell dator för att få åtkomst till serie konsolen.

**FRÅGOR OCH. Min seriekonsolen inte visar någonting, vad gör jag?**

A. Avbildningen är antagligen felkonfigurerad för seriell konsolåtkomst. Information om hur du konfigurerar avbildningen för att aktivera en serie konsol finns [seriell konsol tillgänglighet för Linux-distribution](#serial-console-linux-distribution-availability).

**FRÅGOR OCH. Är seriell konsol som är tillgänglig för VM-skalningsuppsättningar?**

A. Ja det är det! Se [serie konsolen för Virtual Machine Scale Sets](#serial-console-for-virtual-machine-scale-sets)

**FRÅGOR OCH. Kan jag fortfarande använda den seriella konsolen för att ansluta till min VM/virtuell dators skalnings uppsättnings instans om jag konfigurerar den virtuella datorn eller den virtuella datorns skalnings uppsättning med endast autentisering med SSH-nyckel?**

A. Ja. Eftersom serie konsolen inte kräver SSH-nycklar behöver du bara konfigurera en kombination av användar namn/lösen ord. Du kan göra det genom att välja **Återställ lösen ord** i Azure Portal och använda autentiseringsuppgifterna för att logga in i serie konsolen.

## <a name="next-steps"></a>Nästa steg
* Använd serie konsolen för att [komma åt grub och enanvändarläge](serial-console-grub-single-user-mode.md).
* Använd serie konsolen för [NMI-och SysRq-anrop](serial-console-nmi-sysrq.md).
* Lär dig hur du använder serie konsolen för att [Aktivera grub i olika distributioner](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* Serie konsolen är också tillgänglig för [virtuella Windows-datorer](../windows/serial-console.md).
* Läs mer om [startdiagnostik](boot-diagnostics.md).

