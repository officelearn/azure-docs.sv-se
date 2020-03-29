---
title: Felsöka SSH-anslutningsproblem till en virtuell Azure-dator | Microsoft-dokument
description: Felsöka problem som "SSH-anslutning misslyckades" eller "SSH-anslutning nekad" för en Azure VM som kör Linux.
keywords: ssh-anslutning vägrade, ssh-fel, azure ssh, SSH-anslutning misslyckades
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: f221a0bdf579dbbf42ecf64e18803decfb718456
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060666"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Felsöka SSH-anslutningar till en virtuell Linux-dator som misslyckas, returnerar fel eller avvisas
Den här artikeln hjälper dig att hitta och korrigera de problem som uppstår på grund av Secure Shell (SSH) fel, SSH-anslutningsfel eller SSH vägras när du försöker ansluta till en Virtuell Linux-dator (VM). Du kan använda Azure-portalen, Azure CLI eller VM Access Extension för Linux för att felsöka och lösa anslutningsproblem.


Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Snabba felsökningssteg
Efter varje felsökningssteg kan du försöka ansluta till den virtuella datorn igen.

1. [Återställ SSH-konfigurationen](#reset-config).
2. [Återställ autentiseringsuppgifterna](#reset-credentials) för användaren.
3. Kontrollera att reglerna för [nätverkssäkerhetsgruppen](../../virtual-network/security-overview.md) tillåter SSH-trafik.
   * Kontrollera att det finns en regel för [nätverkssäkerhetsgrupp](#security-rules) för att tillåta SSH-trafik (som standard TCP-port 22).
   * Du kan inte använda portomdirigering/mappning utan att använda en Azure-belastningsutjämnare.
4. Kontrollera [hälsotillståndet för vm-resursen](../../resource-health/resource-health-overview.md).
   * Se till att den virtuella datorn rapporterar som felfri.
   * Om du har [aktiverat startdiagnostik](boot-diagnostics.md)kontrollerar du att den virtuella datorn inte rapporterar startfel i loggarna.
5. [Starta om den virtuella datorn](#restart-vm).
6. [Distribuera om den virtuella datorn](#redeploy-vm).

Fortsätt läsa för mer detaljerade felsökningssteg och förklaringar.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Tillgängliga metoder för felsökning av SSH-anslutningsproblem
Du kan återställa autentiseringsuppgifter eller SSH-konfiguration med någon av följande metoder:

* [Azure portal](#use-the-azure-portal) - bra om du behöver snabbt återställa SSH-konfigurationen eller SSH-nyckeln och du inte har Azure-verktygen installerade.
* [Azure VM Serial Console](https://aka.ms/serialconsolelinux) - den virtuella datorns seriella konsol fungerar oavsett SSH-konfiguration och ger dig en interaktiv konsol till din virtuella dator. Faktum är att "can't SSH" situationer är specifikt vad den seriella konsolen var utformad för att hjälpa till att lösa. Mer information nedan.
* [Azure CLI](#use-the-azure-cli) - om du redan är på kommandoraden, snabbt återställa SSH-konfigurationen eller autentiseringsuppgifter. Om du arbetar med en klassisk virtuell dator kan du använda [Azure classic CLI](#use-the-azure-classic-cli).
* [Azure VMAccessForLinux-tillägg](#use-the-vmaccess-extension) – skapa och återanvända json definition-filer för att återställa SSH-konfigurationen eller användarautentiseringsuppgifterna.

Försök ansluta till den virtuella datorn igen efter varje felsökningssteg. Om du fortfarande inte kan ansluta provar du nästa steg.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen
Azure-portalen är ett snabbt sätt att återställa SSH-konfigurationen eller användarautentiseringsuppgifterna utan att installera några verktyg på den lokala datorn.

Börja med att välja din virtuella dator i Azure-portalen. Bläddra ned till avsnittet **Support + Felsökning** och välj Återställ **lösenord** som i följande exempel:

![Återställa SSH-konfiguration eller autentiseringsuppgifter i Azure-portalen](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a><a id="reset-config" />Återställa SSH-konfigurationen
Om du vill återställa `Reset configuration only` SSH-konfigurationen väljer du i avsnittet **Läge** som i föregående skärmbild och väljer sedan **Uppdatera**. När den här åtgärden har slutförts försöker du komma åt den virtuella datorn igen.

### <a name="reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Återställa SSH-autentiseringsuppgifter för en användare
Om du vill återställa autentiseringsuppgifterna för en befintlig användare väljer du antingen `Reset SSH public key` eller `Reset password` i avsnittet **Läge** som i föregående skärmbild. Ange användarnamn och SSH-nyckel eller nytt lösenord och välj sedan **Uppdatera**.

Du kan också skapa en användare med sudo-behörigheter på den virtuella datorn från den här menyn. Ange ett nytt användarnamn och associerat lösenord eller SSH-nyckel och välj sedan **Uppdatera**.

### <a name="check-security-rules"></a><a id="security-rules" />Kontrollera säkerhetsregler

Använd [IP-flödeskontroll](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) för att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska effektiva regler för säkerhetsgrupper för att säkerställa inkommande "Tillåt" NSG-regel finns och prioriteras för SSH-port (standard 22). Mer information finns i [Använda effektiva säkerhetsregler för att felsöka vm-trafikflöde](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Kontrollera routning

Använd Network Watchers [nästa hopp-funktion](../../network-watcher/network-watcher-check-next-hop-portal.md) för att bekräfta att en rutt inte hindrar trafiken från att dirigeras till eller från en virtuell dator. Du kan också granska effektiva vägar för att se alla effektiva vägar för ett nätverksgränssnitt. Mer information finns i [Använda effektiva vägar för att felsöka vm-trafikflöde](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Använda Seriekonsolen för Azure VM
[Azure VM Serial Console](./serial-console-linux.md) ger åtkomst till en textbaserad konsol för virtuella Linux-datorer. Du kan använda konsolen för att felsöka SSH-anslutningen i ett interaktivt skal. Se till att du har uppfyllt [förutsättningarna](./serial-console-linux.md#prerequisites) för att använda Serial Console och prova kommandona nedan för att ytterligare felsöka din SSH-anslutning.

### <a name="check-that-ssh-is-running"></a>Kontrollera att SSH körs
Du kan använda följande kommando för att kontrollera om SSH körs på den virtuella datorn:

```console
ps -aux | grep ssh
```

Om det finns någon utgång är SSH igång.

### <a name="check-which-port-ssh-is-running-on"></a>Kontrollera vilken port SSH körs på

Du kan använda följande kommando för att kontrollera vilken port SSH körs på:

```console
sudo grep Port /etc/ssh/sshd_config
```

Din utgång kommer att se ut ungefär som:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Använda Azure CLI
Om du inte redan har gjort det installerar du den senaste [Azure CLI](/cli/azure/install-az-cli2) och loggar in på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

Om du har skapat och laddat upp en anpassad Linux-diskavbildning kontrollerar du att [Microsoft Azure Linux Agent](../extensions/agent-linux.md) version 2.0.5 eller senare är installerad. För virtuella datorer som skapats med galleriavbildningar är det här åtkomsttillägget redan installerat och konfigurerat åt dig.

### <a name="reset-ssh-configuration"></a>Återställa SSH-konfiguration
Du kan först försöka återställa SSH-konfigurationen till standardvärden och starta om SSH-servern på den virtuella datorn. Detta ändrar inte användarkontots namn, lösenord eller SSH-nycklar.
I följande exempel används [az vm-användaråterställning för](/cli/azure/vm/user) att `myVM` återställa `myResourceGroup`SSH-konfigurationen på den virtuella datorn som namnges i . Använd dina egna värden på följande sätt:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställa SSH-autentiseringsuppgifter för en användare
I följande exempel används [az vm-användaruppdatering](/cli/azure/vm/user) för att återställa autentiseringsuppgifterna för `myUsername` det värde som anges i `myPassword`, på den virtuella datorn som namnges `myVM` i `myResourceGroup`. Använd dina egna värden på följande sätt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Om du använder SSH-nyckelautentisering kan du återställa SSH-nyckeln för en viss användare. I följande exempel används **az vm access set-linux-user** `~/.ssh/id_rsa.pub` för att `myUsername`uppdatera SSH-nyckeln som lagras i för användaren med namnet , på den virtuella datorn som namnges `myVM` i `myResourceGroup`. Använd dina egna värden på följande sätt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Använda VMAccess-tillägget
VM Access Extension för Linux läser i en json-fil som definierar åtgärder som ska utföras. Dessa åtgärder omfattar återställning av SSHD, återställning av en SSH-nyckel eller tillägg av en användare. Du använder fortfarande Azure CLI för att anropa VMAccess-tillägget, men du kan återanvända json-filerna över flera virtuella datorer om så önskas. Med den här metoden kan du skapa en databas med json-filer som sedan kan anropas för givna scenarier.

### <a name="reset-sshd"></a>Återställ SSHD
Skapa en `settings.json` fil med namnet med följande innehåll:

```json
{
    "reset_ssh":"True"
}
```

Med Hjälp av Azure CLI `VMAccessForLinux` anropar du sedan tillägget för att återställa SSHD-anslutningen genom att ange din json-fil. I följande exempel används [az vm-tillägget för](/cli/azure/vm/extension) att `myVM` `myResourceGroup`återställa SSHD på den virtuella datorn som namnges i . Använd dina egna värden på följande sätt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställa SSH-autentiseringsuppgifter för en användare
Om SSHD verkar fungera korrekt kan du återställa autentiseringsuppgifterna för en givare. Om du vill återställa lösenordet för `settings.json`en användare skapar du en fil med namnet . I följande exempel återställs `myUsername` autentiseringsuppgifterna `myPassword`för det värde som anges i . Ange följande rader `settings.json` i filen med dina egna värden:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Eller för att återställa SSH-nyckeln för en `settings.json`användare, först skapa en fil med namnet . I följande exempel återställs `myUsername` autentiseringsuppgifterna `myPassword`för det värde `myVM` `myResourceGroup`som anges i , på den virtuella datorn som namnges i . Ange följande rader `settings.json` i filen med dina egna värden:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

När du har skapat json-filen använder `VMAccessForLinux` du Azure CLI för att anropa tillägget för att återställa SSH-användarautentiseringsuppgifterna genom att ange din json-fil. I följande exempel återställs autentiseringsuppgifterna på den virtuella datorn som namnges `myVM` i `myResourceGroup`. Använd dina egna värden på följande sätt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Använd Den klassiska Azure-CLI-et
Om du inte redan har gjort [det installerar du Den klassiska AZURE-CLI:en och ansluter till din Azure-prenumeration](../../cli-install-nodejs.md). Kontrollera att du använder Resource Manager-läget på följande sätt:

```azurecli
azure config mode arm
```

Om du har skapat och laddat upp en anpassad Linux-diskavbildning kontrollerar du att [Microsoft Azure Linux Agent](../extensions/agent-linux.md) version 2.0.5 eller senare är installerad. För virtuella datorer som skapats med galleriavbildningar är det här åtkomsttillägget redan installerat och konfigurerat åt dig.

### <a name="reset-ssh-configuration"></a>Återställa SSH-konfiguration
Själva SSHD-konfigurationen kan vara felkonfigurerad eller så uppstod ett fel för tjänsten. Du kan återställa SSHD för att se till att själva SSH-konfigurationen är giltig. Att återställa SSHD bör vara det första felsökningssteget du tar.

I följande exempel återställs SSHD `myVM` på en `myResourceGroup`virtuell dator som namnges i resursgruppen . Använd dina egna vm- och resursgruppnamn på följande sätt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställa SSH-autentiseringsuppgifter för en användare
Om SSHD verkar fungera korrekt kan du återställa lösenordet för en givare. I följande exempel återställs `myUsername` autentiseringsuppgifterna `myPassword`för det värde `myVM` `myResourceGroup`som anges i , på den virtuella datorn som namnges i . Använd dina egna värden på följande sätt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Om du använder SSH-nyckelautentisering kan du återställa SSH-nyckeln för en viss användare. I följande exempel uppdateras SSH-nyckeln `~/.ssh/id_rsa.pub` som lagras i för användaren med `myUsername`namnet , på den virtuella datorn som namnges `myVM` i `myResourceGroup`. Använd dina egna värden på följande sätt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a><a id="restart-vm" />Starta om en virtuell dator
Om du har återställt SSH-konfigurationen och användarautentiseringsuppgifterna, eller stött på ett fel i detta, kan du prova att starta om den virtuella datorn för att åtgärda underliggande beräkningsproblem.

### <a name="azure-portal"></a>Azure Portal
Om du vill starta om en virtuell dator med Azure-portalen väljer du den virtuella datorn och väljer sedan **Starta om** som i följande exempel:

![Starta om en virtuell dator i Azure-portalen](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
I följande exempel används [omstart av az-vm](/cli/azure/vm) för att starta om den virtuella datorn som nämns `myVM` i resursgruppen . `myResourceGroup` Använd dina egna värden på följande sätt:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

I följande exempel startas `myVM` den virtuella `myResourceGroup`datorn som nämns i resursgruppen . Använd dina egna värden på följande sätt:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a><a id="redeploy-vm" />Distribuera om en virtuell dator
Du kan distribuera om en virtuell dator till en annan nod i Azure, vilket kan korrigera eventuella underliggande nätverksproblem. Information om omdefördelning av en virtuell dator finns i [Distribuera om virtuell dator till ny Azure-nod](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> När den här åtgärden är klar går efemära diskdata förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn uppdateras.
>
>

### <a name="azure-portal"></a>Azure Portal
Om du vill distribuera om en virtuell dator med Azure-portalen väljer du den virtuella datorn och bläddrar ned till avsnittet **Support + Felsökning.** Välj **Omfördela** som i följande exempel:

![Distribuera om en virtuell dator i Azure-portalen](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
I följande exempel används [omdistribution av az vm](/cli/azure/vm) för `myVM` att distribuera `myResourceGroup`om den virtuella datorn som nämns i resursgruppen . Använd dina egna värden på följande sätt:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI

I följande exempel distribueras den `myVM` virtuella datorn som `myResourceGroup`nämns i resursgruppen . Använd dina egna värden på följande sätt:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Prova de här stegen för att lösa de vanligaste SSH-anslutningsfelen för virtuella datorer som har skapats med hjälp av den klassiska distributionsmodellen. Efter varje steg kan du försöka ansluta till den virtuella datorn igen.

* Återställ fjärråtkomst från [Azure-portalen](https://portal.azure.com). På Azure-portalen väljer du din virtuella dator och väljer sedan **Återställ fjärr...**.
* Starta om den virtuella datorn. På [Azure-portalen](https://portal.azure.com)väljer du din virtuella dator och väljer **Starta om**.

* Distribuera om den virtuella datorn till en ny Azure-nod. Information om hur du distribuerar om en virtuell dator finns i [Distribuera om virtuell dator till ny Azure-nod](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    När den här åtgärden är klar kommer efemära diskdata att gå förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn kommer att uppdateras.
* Följ instruktionerna i [Så här återställer du ett lösenord eller SSH för Linux-baserade virtuella datorer](../linux/classic/reset-access-classic.md) till:

  * Återställ lösenordet eller SSH-tangenten.
  * Skapa ett *sudo-användarkonto.*
  * Återställ SSH-konfigurationen.
* Kontrollera den virtuella datorns resurshälsa för eventuella plattformsproblem.<br>
     Välj din virtuella dator och bläddra nedåt **Inställningar** > **Kontrollera Hälsa**.

## <a name="additional-resources"></a>Ytterligare resurser
* Om du fortfarande inte kan SSH till din virtuella dator efter att ha följt stegen efter läser du [mer detaljerade felsökningssteg](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för att granska ytterligare steg för att lösa problemet.
* Mer information om felsökning av programåtkomst finns i [Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Mer information om felsökning av virtuella datorer som har skapats med den klassiska distributionsmodellen finns i [Så här återställer du ett lösenord eller SSH för Linux-baserade virtuella datorer](../linux/classic/reset-access-classic.md).
