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
ms.date: 08/07/2018
ms.author: harijay
ms.openlocfilehash: e74ee48f0adc0d8ba0d2ea91b5d82415601f9405
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702426"
---
# <a name="virtual-machine-serial-console-preview"></a>Virtual Machine Serial Console (förhandsversion) 


Virtual Machine Serial Console i Azure ger åtkomst till en textbaserad konsol för Linux och Windows-datorer. Den här seriell anslutning är att COM1 seriell port på den virtuella datorn och ger åtkomst till den virtuella datorn och inte är relaterade till nätverk för virtuella datorer / drift systemtillstånd. Åtkomst till seriekonsol för en virtuell dator kan klar för närvarande endast via Azure-portalen och tillåts endast för de användare som har VM-deltagare eller senare åtkomst till den virtuella datorn. 

Seriell konsol dokumentation för Windows-datorer samt [Klicka här](../windows/serial-console.md).

> [!Note] 
> Förhandsversioner görs tillgängliga för dig på villkor att du godkänner användningsvillkoren. Mer information finns i [Microsoft Azure kompletterande användningsvillkor för förhandsversioner av Microsoft Azure.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Den här tjänsten är för närvarande i **förhandsversion** och åtkomst till seriekonsol för virtuella datorer är tillgänglig för globala Azure-regioner. I det här läget är seriell konsol inte tillgängliga Azure Government, Azure Tyskland och Azure Kina-molnet.


## <a name="prerequisites"></a>Förutsättningar 

* Du måste använda resource management-distributionsmodellen. Klassiska distributioner stöds inte. 
* Den virtuella datorn måste ha [startdiagnostik](boot-diagnostics.md) aktiverat – se skärmbilden nedan.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

* Azure-konto med seriekonsolen måste ha [deltagarrollen](../../role-based-access-control/built-in-roles.md) för den virtuella datorn och [startdiagnostik](boot-diagnostics.md) storage-konto. 
* Den virtuella datorn som du är acessing Seriell konsol måste också ha ett konto för lösenordsbaserad. Du kan skapa en med den [Återställ lösenord](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) funktionerna i VM access-tillägg - se skärmbilden nedan.

    ![](../media/virtual-machines-serial-console/virtual-machine-serial-console-reset-password.png)

* Inställningar som är specifika för Linux-distributioner, se [komma åt seriekonsolen för Linux](#access-serial-console-for-linux)



## <a name="get-started-with-serial-console"></a>Kom igång med Seriekonsol
Seriekonsol för virtuella datorer bara kan nås via [Azure-portalen](https://portal.azure.com). Nedan visas stegen för att komma åt seriekonsol för virtuella datorer via portalen 

  1. Öppna Azure portal
  2. Välj virtuella datorer i den vänstra menyn.
  3. Klicka på den virtuella datorn i listan. Översiktssidan för den virtuella datorn öppnas.
  4. Rulla ned till avsnittet om Support och felsökning och klicka på alternativet för seriekonsol (förhandsversion). Ett nytt fönster med seriell konsol öppnas och starta anslutningen.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE] 
> Seriell konsol kräver en lokal användare med ett lösenord som har konfigurerats. Virtuella datorer som endast konfigurerats med en offentlig SSH-nyckel har inte åtkomst till seriekonsol för tillfället. Om du vill skapa en lokal användare med lösenord, använda den [VM Access-tillägg](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension) (finns också i portalen genom att klicka på ”Återställ lösenord”) och skapa en lokal användare med ett lösenord.

## <a name="disable-serial-console"></a>Inaktivera Seriekonsol
Som standard har alla prenumerationer seriell konsolåtkomst är aktiverad för alla virtuella datorer. Du kan inaktivera seriekonsolen på prenumerationsnivån eller VM-nivå.

### <a name="subscription-level-disable"></a>Prenumerationsnivå inaktivera
Seriell konsol kan inaktiveras för en hel prenumeration genom att via den [inaktivera konsolen REST API-anrop](https://aka.ms/disableserialconsoleapi). Du kan använda ”prova” funktionerna som är tillgängliga på sidan API-dokumentationen om du inaktiverar och aktiverar Seriekonsol för en prenumeration. Ange din `subscriptionId`, ”standard” i den `default` fältet och klicka på Kör. Azure CLI-kommandon är inte tillgängliga ännu och anländer vid ett senare tillfälle. [Testa REST API-anrop här](https://aka.ms/disableserialconsoleapi).

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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

### <a name="disable-feature"></a>Inaktivera funktionen
Seriell konsol-funktionen kan inaktiveras för specifika virtuella datorer genom att inaktivera inställningen för den virtuella datorn boot diagnostics.

## <a name="common-scenarios-for-accessing-serial-console"></a>Vanliga scenarier för att komma åt seriekonsol 
Scenario          | Åtgärder i seriekonsol                
:------------------|:-----------------------------------------
Bruten FSTAB-filen | `Enter` för att fortsätta och åtgärda fstab-filen med hjälp av en textredigerare. Du kan behöva vara i enanvändarläge för detta. Se [hur du löser problem med fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) och [med Seriekonsol för att komma åt GRUB och enanvändarläge](serial-console-grub-single-user-mode.md) att komma igång.
Felaktig brandväggsregler | Få åtkomst till seriell konsol och åtgärda iptables. 
Filsystem skadade/kontroll | Få åtkomst till seriell konsol och Återställ filsystem. 
Problem med SSH/RDP-konfigurationen | Komma åt Seriell konsol och ändra inställningarna. 
Nätverket låsa system| Åtkomst seriekonsolen via portalen för att hantera system. 
Interagera med startprogrammet | Åtkomst GRUB via seriekonsolen. Gå till [med Seriekonsol för att komma åt GRUB och enanvändarläge](serial-console-grub-single-user-mode.md) att komma igång. 

## <a name="access-serial-console-for-linux"></a>Åtkomst Seriekonsol för Linux
Gästoperativsystemet måste konfigureras för att läsa och skriva konsolmeddelanden att den seriella porten för seriekonsol för att fungera korrekt. De flesta [godkända Azure Linux-distributioner](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) har seriekonsolen konfigureras som standard. Att klicka på avsnittet Seriekonsolen i Azure portal ger åtkomst till konsolen. 

Distribution      | Seriell åtkomst till konsolen
:-----------|:---------------------
Red Hat Enterprise Linux    | Red Hat Enterprise Linux-avbildningar på Azure har åtkomst till konsolen aktiverad som standard. 
CentOS      | CentOS-avbildningarna i Azure har åtkomst till konsolen aktiverad som standard. 
Ubuntu      | Ubuntu-avbildningar som är tillgängliga på Azure har åtkomst till konsolen aktiverad som standard.
CoreOS      | CoreOS-avbildningarna i Azure har åtkomst till konsolen aktiverad som standard.
SUSE        | Nyare SLES-avbildningar på Azure har åtkomst till konsolen aktiverad som standard. Om du använder äldre versioner (10 eller nedan) för SLES på Azure, följer du de [KB-artikel](https://www.novell.com/support/kb/doc.php?id=3456486) att aktivera Seriell konsol. 
Oracle Linux        | Oracle Linux-avbildningar på Azure har åtkomst till konsolen aktiverad som standard.
Anpassade Linux-avbildningar     | Aktivera åtkomst till konsolen i /etc/inittab att köra en terminal på ttyS0 om du vill aktivera seriekonsol för en anpassad Linux VM-avbildning. Här är ett exempel att lägga till det i filen inittab: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Läs mer om hur du skapar anpassade avbildningar korrekt [skapa och ladda upp en VHD för Linux i Azure](https://aka.ms/createuploadvhd).

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
## <a name="known-issues"></a>Kända problem 
Eftersom vi är fortfarande i förhandsstadium för seriell konsolåtkomst, arbetar vi via några kända problem. Nedan visas i listan över dem med möjliga lösningar:

Problem                           |   Åtgärd 
:---------------------------------|:--------------------------------------------|
Det finns inget alternativ med VM scale set-instans från seriell konsol |  Åtkomst till seriekonsol för VM-skalningsuppsättningsinstanser stöds inte vid tidpunkten för förhandsversionen.
Träffa ange när anslutningen popup-meddelandet inte visas en logg i Kommandotolken | Finns på följande sida: [Hitting ange ingenting](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Detta kan inträffa om du använder en anpassad virtuell dator, förstärkt installation eller GRUB konfiguration som orsakar Linux för att kunna ansluta ordentligt till den seriella porten.
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Kontrollera att startdiagnostik inte har en brandvägg för kontot. Ett lagringskonto för tillgänglig startdiagnostik är nödvändigt för seriekonsolen ska fungera.


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 
**FRÅGOR OCH. Hur kan jag skicka feedback?**

A. Ge feedback som ett problem genom att gå till https://aka.ms/serialconsolefeedback. Du kan också mindre (rekommenderas) skicka feedback via azserialhelp@microsoft.com eller i kategori för virtuell dator av http://feedback.azure.com

**FRÅGOR OCH. Jag kan inte komma åt seriekonsolen var kan jag registrera ett supportärende?**

A. Den här förhandsversionsfunktionen täcks via Azure-förhandsversioner. Stöd för detta hanteras bäst via kanaler som nämns ovan. 

## <a name="next-steps"></a>Nästa steg
* Använd Seriekonsol för att [starta i GRUB och ange enanvändarläge](serial-console-grub-single-user-mode.md)
* Använd Seriekonsol för [NMI och SysRq anrop](serial-console-nmi-sysrq.md)
* Seriekonsolen är också tillgängligt för [Windows](../windows/serial-console.md) virtuella datorer
* Läs mer om [startdiagnostik](boot-diagnostics.md)