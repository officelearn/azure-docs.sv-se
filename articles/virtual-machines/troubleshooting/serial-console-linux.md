---
title: Azure Virtual Machine Serial Console | Microsoft Docs
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
ms.date: 09/11/2018
ms.author: harijay
ms.openlocfilehash: ce799f4201a560077c5bb1b943a9e587a71806f2
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856351"
---
# <a name="virtual-machine-serial-console"></a>Virtual Machine Serial Console


Virtual Machine Serial Console i Azure ger åtkomst till en textbaserad konsol för Linux-datorer. Den här seriell anslutning är att den seriella porten COM1 för den virtuella datorn, som ger tillgång till den virtuella datorn som är oberoende av en virtuell dators nätverks- eller operating system-tillstånd. Åtkomsten till seriekonsol för en virtuell dator för närvarande kan endast göras via Azure portal och tillåts endast för de användare som har VM-deltagare eller senare åtkomst till den virtuella datorn. 

Seriell konsol dokumentation för Windows-datorer samt [Klicka här](../windows/serial-console.md).

> [!Note] 
> Seriekonsol för virtuella datorer är allmänt tillgängligt i globala Azure-regioner. I det här läget ännu Seriekonsolen inte finns tillgänglig i Azure Government eller Azure Kina moln.


## <a name="prerequisites"></a>Förutsättningar 

* Du måste använda resource management-distributionsmodellen. Klassiska distributioner stöds inte. 
* Den virtuella datorn måste ha [startdiagnostik](boot-diagnostics.md) aktiverat – se skärmbilden nedan.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Azure-konto med seriekonsolen måste ha [deltagarrollen](../../role-based-access-control/built-in-roles.md) för den virtuella datorn och [startdiagnostik](boot-diagnostics.md) storage-konto. 
* Den virtuella datorn som du är acessing Seriell konsol måste också ha ett konto för lösenordsbaserad. Du kan skapa en med den [Återställ lösenord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funktionerna i VM access-tillägg - se skärmbilden nedan.

    ![](./media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Inställningar som är specifika för Linux-distributioner, se [komma åt seriekonsolen för Linux](#Serial-Console-Linux-distro-availability)



## <a name="get-started-with-serial-console"></a>Kom igång med Seriekonsol
Seriekonsol för virtuella datorer bara kan nås via [Azure-portalen](https://portal.azure.com). Se till att du har uppfyllt de [krav](#prerequisites) ovan. Nedan följer du stegen för att komma åt seriekonsol för virtuella datorer via portalen:

  1. Öppna Azure portal
  1. (Hoppa över detta om den virtuella datorn har en användare som använder lösenordsautentisering) Lägg till en användare med autentisering med användarnamn/lösenord genom att klicka på bladet ”Återställ lösenord”
  1. Välj virtuella datorer i den vänstra menyn.
  1. Klicka på den virtuella datorn i listan. Översiktssidan för den virtuella datorn öppnas.
  1. Rulla ned till avsnittet om Support och felsökning och klicka på alternativet ”seriekonsolen”. Ett nytt fönster med seriell konsol öppnas och starta anslutningen.

![](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### 

> [!NOTE] 
> Seriell konsol kräver en lokal användare med ett lösenord som har konfigurerats. Virtuella datorer som endast konfigurerats med en offentlig SSH-nyckel kan inte logga in till seriekonsol för tillfället. Om du vill skapa en lokal användare med lösenord, använda den [VM Access-tillägg](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), tillgänglig i portalen genom att klicka på ”Återställ lösenord” i portalen och skapa en lokal användare med ett lösenord.
> Du kan också återställa administratörslösenordet i ditt konto genom att [med GRUB för att släppa i enanvändarläge](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distro-availability"></a>Seriell konsol Linux-distribution tillgänglighet
Gästoperativsystemet måste konfigureras för att läsa och skriva konsolmeddelanden att den seriella porten för seriekonsol för att fungera korrekt. De flesta [godkända Azure Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) har seriekonsolen konfigureras som standard. Att klicka på avsnittet Seriekonsolen i Azure portal ger åtkomst till konsolen. 

Distribution      | Seriell åtkomst till konsolen
:-----------|:---------------------
Red Hat Enterprise Linux    | Red Hat Enterprise Linux-avbildningar på Azure har åtkomst till konsolen aktiverad som standard. 
CentOS      | CentOS-avbildningarna i Azure har åtkomst till konsolen aktiverad som standard. 
Ubuntu      | Ubuntu-avbildningar som är tillgängliga på Azure har åtkomst till konsolen aktiverad som standard.
CoreOS      | CoreOS-avbildningarna i Azure har åtkomst till konsolen aktiverad som standard.
SUSE        | Nyare SLES-avbildningar på Azure har åtkomst till konsolen aktiverad som standard. Om du använder äldre versioner (10 eller nedan) för SLES på Azure, följer du de [KB-artikel](https://www.novell.com/support/kb/doc.php?id=3456486) att aktivera Seriell konsol. 
Oracle Linux        | Oracle Linux-avbildningar på Azure har åtkomst till konsolen aktiverad som standard.
Anpassade Linux-avbildningar     | Aktivera seriekonsol för en anpassad Linux VM-avbildning genom att aktivera åtkomst till konsolen i `/etc/inittab` att köra en terminal på `ttyS0`. Här är ett exempel att lägga till det i filen inittab: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Läs mer om hur du skapar anpassade avbildningar korrekt [skapa och ladda upp en VHD för Linux i Azure](https://aka.ms/createuploadvhd). Om du skapar en anpassad kernel vissa kernel-flaggor att tänka på att aktivera är `CONFIG_SERIAL_8250=y` och `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Konfigurationsfilen finns ofta under /boot/ för ytterligare utforskning.

## <a name="common-scenarios-for-accessing-serial-console"></a>Vanliga scenarier för att komma åt seriekonsol 
Scenario          | Åtgärder i seriekonsol                
:------------------|:-----------------------------------------
Bruten FSTAB-filen | `Enter` för att fortsätta och åtgärda fstab-filen med hjälp av en textredigerare. Du kan behöva vara i enanvändarläge för detta. Se [hur du löser problem med fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) och [med Seriekonsol för att komma åt GRUB och enanvändarläge](serial-console-grub-single-user-mode.md) att komma igång.
Felaktig brandväggsregler | Få åtkomst till seriell konsol och åtgärda iptables. 
Filsystem skadade/kontroll | Få åtkomst till seriell konsol och Återställ filsystem. 
Problem med SSH/RDP-konfigurationen | Komma åt Seriell konsol och ändra inställningarna. 
Nätverket låsa system| Åtkomst seriekonsolen via portalen för att hantera system. 
Interagera med startprogrammet | Åtkomst GRUB via seriekonsolen. Gå till [med Seriekonsol för att komma åt GRUB och enanvändarläge](serial-console-grub-single-user-mode.md) att komma igång. 

## <a name="disable-serial-console"></a>Inaktivera Seriekonsol
Som standard har alla prenumerationer seriell konsolåtkomst är aktiverad för alla virtuella datorer. Du kan inaktivera seriekonsolen på prenumerationsnivån eller VM-nivå.

> [!Note] 
> För att aktivera eller inaktivera seriekonsol för en prenumeration, måste du ha skrivbehörighet till prenumerationen. Detta omfattar, men är inte begränsat till administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

### <a name="subscription-level-disable"></a>Prenumerationsnivå inaktivera
Seriell konsol kan inaktiveras för en hel prenumeration genom att via den [inaktivera konsolen REST API-anrop](https://aka.ms/disableserialconsoleapi). Du kan använda ”prova” funktionerna som är tillgängliga på sidan API-dokumentationen om du inaktiverar och aktiverar Seriekonsol för en prenumeration. Ange din `subscriptionId`, ”standard” i den `default` fältet och klicka på Kör. Azure CLI-kommandon är inte tillgängliga ännu och anländer vid ett senare tillfälle. [Testa REST API-anrop här](https://aka.ms/disableserialconsoleapi).

![](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Du kan också använda uppsättningen kommandon nedan i Cloud Shell (bash-kommandon visas) för att inaktivera, aktivera och visa inaktiverat status för seriell konsol för en prenumeration. 

* Hämta inaktiverad status för seriell konsol för en prenumeration:
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
Seriell konsol kan inaktiveras för specifika virtuella datorer genom att inaktivera inställningen för den virtuella datorn boot diagnostics. Helt enkelt inaktivera startdiagnostik från Azure-portalen och seriekonsolen kommer att inaktiveras för den virtuella datorn.

## <a name="serial-console-security"></a>Seriell konsol-säkerhet 

### <a name="access-security"></a>Åtkomstsäkerhet 
Åtkomst till seriell konsol är begränsat till användare som har [VM deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) eller över åtkomst till den virtuella datorn. Om din AAD-klient kräver Multi-Factor Authentication och sedan åtkomst till seriell konsol måste också MFA eftersom dess åtkomst sker via [Azure-portalen](https://portal.azure.com).

### <a name="channel-security"></a>Kanalsäkerhet
Alla data som skickas fram och tillbaka krypteras under överföringen.

### <a name="audit-logs"></a>Granskningsloggar
All åtkomst till seriekonsol för tillfället är inloggad på [startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) loggarna för den virtuella datorn. Åtkomst till de här loggarna ägs och kontrolleras av administratören för Azure virtuella datorer.  

>[!CAUTION] 
Även om inga lösenord för åtkomst för konsolen loggas om kommandon körs i konsolen innehåller eller utgående lösenord, hemligheter, användarnamn eller någon annan form av personligt identifierbar Information (PII), skrivs de till startdiagnostik för virtuell dator loggar, tillsammans med alla andra synlig text, som en del av implementeringen av Seriell konsol scrollback funktioner. Dessa loggar är cirkulär och endast personer med läsbehörighet till lagringskontot för diagnostik har åtkomst till dem, men vi rekommenderar att du följer bästa praxis för att använda SSH-konsolen för allt som kan omfatta hemligheter och/eller personligt identifierbar information. 

### <a name="concurrent-usage"></a>Samtidig användning
Om en användare är ansluten till seriell konsol och en annan användare begär har åtkomst till den samma virtuella datorn, kommer att kopplas från den första användaren och den andra användaren som är anslutna på ett sätt som den första användaren ställa upp och låta den fysiska konsolen och en ny användare som sitter.

>[!CAUTION] 
Det innebär att den användare som kopplas inte kommer att loggas ut! Möjlighet att framtvinga en utloggning vid frånkoppling (via SIGHUP eller liknande mekanism) är fortfarande i översikten. För Windows finns en automatisk tidsgräns har aktiverats i SAC, men för Linux du kan konfigurera terminal timeoutinställning. För att göra detta helt enkelt till `export TMOUT=600` i din .bash_profile eller .profile för användaren du logga in i konsolen med, för att tidsgränsen för sessionen efter 10 minuter.

## <a name="accessibility"></a>Hjälpmedel
Hjälpmedel är viktiga fokus för Azure seriekonsol. Därför har vi sett att seriekonsolen är tillgänglig för de med visuella objekt och hörselnedsättning samt personer som inte kanske kan använda en mus.

### <a name="keyboard-navigation"></a>Tangentbordsnavigering
Använd den `tab` på tangentbordet för att flytta runt Seriell konsol-gränssnittet i Azure-portalen. Din plats kommer vara markerad på skärmen. Om du vill lämna fokus på bladet för seriell konsol, trycker du på `Ctrl + F6` på tangentbordet.

### <a name="use-serial-console-with-a-screen-reader"></a>Använda Seriell konsol med en skärmläsare
Seriell konsol levereras med stöd för Skärmläsaren inbyggda. Navigera med en skärmläsare aktiveras kan alternativ text för knappen är markerade som ska läsas upp av Skärmläsaren.

## <a name="errors"></a>Fel
De flesta felen är tillfälliga sin natur och anslutningsförsök görs seriekonsolen ofta åtgärdar dessa. Tabellen nedan visar en lista över fel och åtgärder

Fel                            |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta inställningarna för startdiagnostik för '<VMNAME>'. Se till att startdiagnostik har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. | Kontrollera att den virtuella datorn har [startdiagnostik](boot-diagnostics.md) aktiverat. 
Den virtuella datorn är i tillståndet stoppad frigjord. Starta den virtuella datorn och försök Seriell konsol-anslutning. | Virtuell dator måste vara i tillståndet igång för att komma åt seriekonsolen
Du har inte behörighet att använda den här virtuella datorn seriekonsolen. Kontrollera att du har minst deltagarbehörigheter för virtuell dator.| Seriell konsolåtkomst kräver vissa behörighet att komma åt. Se [åtkomstbehörigheter](#prerequisites) information
Det går inte att fastställa resursgruppen för startdiagnostiklagringskonto '<STORAGEACCOUNTNAME>'. Kontrollera att startdiagnostik har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell konsolåtkomst kräver vissa behörighet att komma åt. Se [åtkomstbehörigheter](#prerequisites) information
Web socket är stängd eller kunde inte öppnas. | Du kan behöva godkänna `*.console.azure.com`. En mer detaljerad men längre metod är att godkänna den [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/en-us/download/details.aspx?id=41653), som ändras relativt regelbundet.
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Kontrollera att startdiagnostik inte har en brandvägg för kontot. Ett lagringskonto för tillgänglig startdiagnostik är nödvändigt för seriekonsolen ska fungera.

## <a name="known-issues"></a>Kända problem 
Vi är medvetna om några problem med seriell konsol. Här är en lista över dessa problem och åtgärder för problemlösning.

Problem                           |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Träffa ange när anslutningen popup-meddelandet inte visas en logg i Kommandotolken | Finns på följande sida: [Hitting ange ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Detta kan inträffa om du använder en anpassad virtuell dator, förstärkt installation eller GRUB konfiguration som orsakar Linux för att kunna ansluta ordentligt till den seriella porten.
Seriell konsol text tar endast upp en del av skärmstorlek (ofta när du använder en textredigerare) | Seriell konsoler stöder inte förhandling om fönsterstorlek ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), vilket innebär att det blir inga SIGWINCH signalen skickas till uppdatera skärmens storlek och den virtuella datorn har ingen kunskap om storleken på din terminal. Vi rekommenderar instaling xterm eller några andra liknande verktyg som ger kommandot 'storleksändring'. Köra, ändra storlek på' åtgärda detta.
Klistra in mycket långa strängar fungerar inte | Seriell konsol begränsar längden på strängar som klistras in i terminalen för att 2048 tecken. Det här är att förhindra att överbelasta serieport bandbredd.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
**FRÅGOR OCH. Hur kan jag skicka feedback?**

A. Ge feedback som ett problem genom att gå till https://aka.ms/serialconsolefeedback. Du kan också (mindre rekommenderas), skicka feedback via azserialhelp@microsoft.com eller i kategori för virtuell dator av http://feedback.azure.com

**FRÅGOR OCH. Seriell konsol som har stöd för kopiera/klistra in?**

A. Ja sker. Använd Ctrl + Skift + C och Ctrl + Skift + V för att kopiera och klistra in i terminalen.

**FRÅGOR OCH. Kan jag använda seriekonsolen i stället för en SSH-anslutning?**

A. Medan det kan verka tekniskt möjligt, är seriell konsol avsedd att användas som ett verktyg för felsökning i situationer där anslutning via SSH inte är möjligt. Vi rekommenderar mot att använda seriekonsolen som en SSH-ersättning av två skäl:

1. Seriell konsol har inte så mycket bandbredd som SSH - anslutningen endast text, är så att flera GUI omfattande interaktioner blir svårt i Seriell konsol.
1. Seriell konsolåtkomst är för närvarande endast av användarnamn och lösenord. SSH-nycklar är mycket säkrare än kombinationer av användarnamn/lösenord, så ur säkerhetssynpunkt inloggningen rekommenderar vi SSH via seriell konsol.

**FRÅGOR OCH. Vem kan aktivera eller inaktivera seriekonsol för min prenumeration?**

A. För att aktivera eller inaktivera Seriell konsol på en prenumeration hela-nivå, måste du ha skrivbehörighet till prenumerationen. Roller som har behörighet att skriva omfattar, men är inte begränsad till administratörer eller ägare. Anpassade roller kan också ha skrivbehörighet.

**FRÅGOR OCH. Vem som kan komma åt seriekonsol för min virtuella dator?**

A. Du måste ha deltagarbehörighet inom nivå eller högre till en virtuell dator för att komma åt den Virtuella datorns Seriell konsol. 

**FRÅGOR OCH. Min seriekonsolen inte visas allt, hur gör jag?**

A. Avbildningen är antagligen felkonfigurerad för seriell konsolåtkomst. Se [åtkomst Seriekonsol för Linux](#Access-Serial-Console-for-Linux) mer information om hur du konfigurerar din avbildning om du vill aktivera Seriell konsol.

**FRÅGOR OCH. Är seriell konsol för Virtual Machine Scale Sets?**

A. Åtkomst till seriekonsol för VM-skalningsuppsättningsinstanser stöds inte för tillfället.

**FRÅGOR OCH. Jag konfigurera min virtuella dator med hjälp av endast SSH-nyckelautentisering, kan jag fortsätta att använda Seriell konsol för att ansluta till min virtuella dator?** A. Ja. Seriell konsol kräver inte SSH-nycklar, så att allt du behöver göra en kombination av användarnamn/lösenord. Du kan göra detta genom att använda ”Återställ lösenord”-bladet i portalen och använda dessa autentiseringsuppgifter för att logga in på Seriell konsol.

## <a name="next-steps"></a>Nästa steg
* Använd Seriekonsol för att [starta i GRUB och ange enanvändarläge](serial-console-grub-single-user-mode.md)
* Använd Seriekonsol för [NMI och SysRq anrop](serial-console-nmi-sysrq.md)
* Seriekonsolen är också tillgängligt för [Windows](../windows/serial-console.md) virtuella datorer
* Läs mer om [startdiagnostik](boot-diagnostics.md)