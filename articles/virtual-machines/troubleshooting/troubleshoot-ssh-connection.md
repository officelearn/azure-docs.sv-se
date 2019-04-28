---
title: Felsök SSH-anslutningsproblem till en Azure-dator | Microsoft Docs
description: Så här felsöker du problem med till exempel ”SSH-anslutningen misslyckades” eller ”SSH-anslutningen nekades” för en Azure-dator som kör Linux.
keywords: SSH anslutningen nekades, ssh fel, azure ssh, SSH-anslutningen misslyckades
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 81e00c4a3b9490a05667d58952f7bdf8945bacdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61405279"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Felsök SSH-anslutningar till en virtuell Linux-dator som misslyckas, fel, eller nekas
Den här artikeln hjälper dig att hitta och åtgärda de problem som uppstår på grund av Secure Shell (SSH)-fel, SSH-anslutningsfel, eller SSH nekas när du försöker ansluta till en Linux-dator (VM). Du kan använda Azure-portalen, Azure CLI eller VM Access-tillägg för Linux för att felsöka och lösa problem med anslutning.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **få support**. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Snabbsteg för felsökning
Försök återansluta till den virtuella datorn efter varje steg i felsökningen.

1. [Återställa SSH-konfigurationen](#reset-config).
2. [Återställa autentiseringsuppgifterna](#reset-credentials) för användaren.
3. Kontrollera den [nätverkssäkerhetsgrupp](../../virtual-network/security-overview.md) regler tillåta SSH-trafik.
   * Se till att en [Nätverkssäkerhetsgrupp kopplad till regeln](#security-rules) finns för att tillåta SSH-trafik (som standard, TCP-port 22).
   * Du kan inte använda omdirigering av portar / mappning utan att använda en Azure load balancer.
4. Kontrollera den [VM resurshälsa](../../resource-health/resource-health-overview.md).
   * Se till att den virtuella datorn rapporterar är felfri.
   * Om du har [startdiagnostik aktiverat](boot-diagnostics.md), kontrollera den virtuella datorn inte rapporterar startfel i loggarna.
5. [Starta om den virtuella datorn](#restart-vm).
6. [Distribuera om den virtuella datorn](#redeploy-vm).

Läs vidare för mer detaljerad felsökning och förklaringar.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Tillgängliga metoder för att felsöka SSH-anslutningsproblem
Du kan återställa autentiseringsuppgifter eller SSH-konfigurationen med någon av följande metoder:

* [Azure-portalen](#use-the-azure-portal) – det är bra om du behöver snabbt återställa SSH-konfigurationen eller SSH-nyckeln och du inte har Azure-verktygen installerats.
* [Azure VM från Seriekonsol](https://aka.ms/serialconsolelinux) -VM seriekonsolen fungerar oavsett SSH-konfigurationen och ger dig en interaktiv konsol till den virtuella datorn. I själva verket ”kan inte SSH” situationer är särskilt seriekonsolen har utformats för att lösa. Mer information nedan.
* [Azure CLI](#use-the-azure-cli) – om du redan är på kommandoraden, snabbt återställa SSH-konfigurationen eller autentiseringsuppgifter. Om du arbetar med en klassisk virtuell dator kan du använda den [Azure klassiskt CLI](#use-the-azure-classic-cli).
* [Azure VMAccessForLinux-tillägget](#use-the-vmaccess-extension) – skapa och återanvända json definitionsfiler för att återställa SSH-konfiguration eller autentiseringsuppgifter.

När du har varje fel, försök att ansluta till den virtuella datorn igen. Om du fortfarande inte kan ansluta kan du försöka nästa steg.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen
Azure-portalen kan du snabbt återställa SSH-konfiguration eller autentiseringsuppgifter utan att installera några verktyg på din lokala dator.

Välj den virtuella datorn i Azure-portalen. Rulla ned till den **stöd + felsökning** och väljer **Återställ lösenord** som i följande exempel:

![Återställa SSH-konfigurationen eller autentiseringsuppgifter i Azure portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />Återställ SSH-konfiguration
Om du vill återställa SSH-konfigurationen, Välj `Reset configuration only` i den **läge** som i föregående skärmbild och sedan välja **uppdatering**. När den här åtgärden är klar försök komma åt den virtuella datorn igen.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Återställ SSH-autentiseringsuppgifter för en användare
Om du vill återställa autentiseringsuppgifterna för en befintlig användare, väljer du antingen `Reset SSH public key` eller `Reset password` i den **läge** avsnittet som i föregående skärmbild. Ange användarnamnet och en SSH-nyckel eller ett nytt lösenord och väljer sedan **uppdatering**.

Du kan också skapa en användare med sudo-behörighet på den virtuella datorn från den här menyn. Ange ett nytt användarnamn och tillhörande lösenord eller SSH-nyckel och välj sedan **uppdatering**.

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />Kontrollera säkerhetsregler

Använd [IP-flödesverifieringen](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) att bekräfta om en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska reglerna för effektiva säkerhetsgrupper för att säkerställa att inkommande ”Tillåt” NSG-regeln finns och är prioriterad för SSH-porten (standard 22). Mer information finns i [använda effektiva säkerhetsregler för felsökning av VM infrastrukturtrafiken rör](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Kontrollera Routning

Använda Network Watcher [nästa hopp](../../network-watcher/network-watcher-check-next-hop-portal.md) möjlighet att bekräfta att en väg inte förhindrar trafik dirigeras till eller från en virtuell dator. Du kan också gå igenom effektiva vägar för att se alla effektiva vägar för ett nätverksgränssnitt. Mer information finns i [använda effektiva vägar för felsökning av VM infrastrukturtrafiken rör](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Använda Azure VM-Seriekonsol
Den [Azure VM från Seriekonsol](./serial-console-linux.md) ger åtkomst till en textbaserad konsol för Linux-datorer. Du kan använda konsolen för att felsöka din SSH-anslutning i ett interaktivt gränssnitt. Se till att du har uppfyllt de [krav](./serial-console-linux.md#prerequisites) för att använda Seriekonsol och försök kommandona nedan för att ytterligare Felsök SSH-anslutning.

### <a name="check-that-ssh-is-running"></a>Kontrollera att SSH körs
Du kan använda följande kommando för att avgöra om SSH körs på den virtuella datorn:
```
$ ps -aux | grep ssh
```
Om det finns inga utdata, är SSH igång.

### <a name="check-which-port-ssh-is-running-on"></a>Kontrollera vilken port som SSH körs på
Du kan använda följande kommando för att kontrollera vilken port som SSH körs på:
```
$ sudo grep Port /etc/ssh/sshd_config
```
Dina utdata ser ut ungefär som:
```
Port 22
```

## <a name="use-the-azure-cli"></a>Använda Azure CLI
Om du inte redan gjort installera senast [Azure CLI](/cli/azure/install-az-cli2) och logga in på en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index).

Om du har skapat och laddat upp en anpassad avbildning för Linux-disk, se till att den [Microsoft Azure Linux Agent](../extensions/agent-windows.md) version 2.0.5 eller senare är installerat. För virtuella datorer som skapas med hjälp av avbildningar i galleriet, access-tillägg redan installerat och konfigurerat åt dig.

### <a name="reset-ssh-configuration"></a>Återställ SSH-konfiguration
Du kan först försöka återställa SSH-konfigurationen till standardvärden och SSH-servern på den virtuella datorn startas om. Detta ändras inte användarens kontonamn, lösenord och SSH-nycklar.
I följande exempel används [az vm användaren att återställa-ssh](/cli/azure/vm/user) att återställa SSH-konfigurationen på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställ SSH-autentiseringsuppgifter för en användare
I följande exempel används [az vm user update](/cli/azure/vm/user) att återställa autentiseringsuppgifterna för `myUsername` att värdet som anges i `myPassword`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Om du använder SSH-nyckelautentisering, kan du återställa SSH-nyckel för en viss användare. I följande exempel används **az vm komma åt set-linux-användare** att uppdatera SSH-nyckel som lagras i `~/.ssh/id_rsa.pub` för användaren med namnet `myUsername`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Använd VMAccess-tillägget
VM Access-tillägg för Linux läser i en json-fil som definierar åtgärder för att utföra. Dessa åtgärder omfattar återställer SSHD, när du återställer en SSH-nyckel eller att lägga till en användare. Du fortfarande använda Azure CLI för att anropa VMAccess-tillägget, men du kan återanvända json-filer mellan flera virtuella datorer om du vill. Den här metoden kan du skapa en databas som json-filer som sedan kan anropas för angivna scenarier.

### <a name="reset-sshd"></a>Återställ SSHD
Skapa en fil med namnet `settings.json` med följande innehåll:

```json
{
    "reset_ssh":"True"
}
```

Med Azure CLI kan du sedan anropa den `VMAccessForLinux` utökning av Återställ SSHD anslutningen genom att ange en json-fil. I följande exempel används [az vm-tilläggsuppsättningen](/cli/azure/vm/extension) återställa SSHD på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställ SSH-autentiseringsuppgifter för en användare
Om SSHD verkar fungera korrekt kan återställa du autentiseringsuppgifterna för en användare är. Om du vill återställa lösenordet för en användare skapar du en fil med namnet `settings.json`. I följande exempel återställs autentiseringsuppgifterna för `myUsername` att värdet som anges i `myPassword`. Ange följande rader i din `settings.json` fil, använda dina egna värden:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Eller om du vill återställa SSH-nyckel för en användare först skapa en fil med namnet `settings.json`. I följande exempel återställs autentiseringsuppgifterna för `myUsername` att värdet som anges i `myPassword`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Ange följande rader i din `settings.json` fil, använda dina egna värden:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

När du har skapat din json-fil, använder du Azure CLI för att anropa den `VMAccessForLinux` tillägg för att återställa dina SSH-autentiseringsuppgifterna genom att ange en json-fil. I följande exempel återställs autentiseringsuppgifter på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Använda den klassiska Azure CLI
Om du inte redan gjort [installerar Azure klassiska CLI och ansluter till din Azure-prenumeration](../../cli-install-nodejs.md). Kontrollera att du använder Resource Manager-läge på följande sätt:

```azurecli
azure config mode arm
```

Om du har skapat och laddat upp en anpassad avbildning för Linux-disk, se till att den [Microsoft Azure Linux Agent](../extensions/agent-windows.md) version 2.0.5 eller senare är installerat. För virtuella datorer som skapas med hjälp av avbildningar i galleriet, access-tillägg redan installerat och konfigurerat åt dig.

### <a name="reset-ssh-configuration"></a>Återställ SSH-konfiguration
SSHD-konfigurationen själva kan vara felkonfigurerad eller tjänsten påträffade ett fel. Du kan återställa SSHD att kontrollera att SSH-konfigurationen själva är giltig. När du återställer SSHD bör vara den första åtgärden som du vidta.

I följande exempel återställs SSHD på en virtuell dator med namnet `myVM` i resursgruppen med namnet `myResourceGroup`. Använd din egen virtuell dator och resursgrupp gruppnamn enligt följande:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställ SSH-autentiseringsuppgifter för en användare
Om SSHD verkar fungera korrekt kan återställa du lösenordet för en användare är. I följande exempel återställs autentiseringsuppgifterna för `myUsername` att värdet som anges i `myPassword`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Om du använder SSH-nyckelautentisering, kan du återställa SSH-nyckel för en viss användare. I följande exempel uppdateras SSH-nyckel som lagras i `~/.ssh/id_rsa.pub` för användaren med namnet `myUsername`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />Starta om en virtuell dator
Om du har återställa SSH-konfigurationen och användardata autentiseringsuppgifterna eller påträffade ett fel gör det, kan du starta om den virtuella datorn till underliggande problem med compute-adress.

### <a name="azure-portal"></a>Azure Portal
Om du vill starta om en virtuell dator med Azure portal, Välj den virtuella datorn och välj sedan **starta om** som i följande exempel:

![Starta om en virtuell dator i Azure portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
I följande exempel används [az vm restart](/cli/azure/vm) att starta om den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI
I följande exempel startar om den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />Distribuera om en virtuell dator
Du kan distribuera om en virtuell dator till en annan nod i Azure, som kan korrigera eventuella underliggande nätverksproblem. Information om hur du distribuerar om en virtuell dator finns i [distribuera om virtuell dator till nya Azure-nod](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> När den här åtgärden är klar differentierande diskdata går förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn uppdateras.
>
>

### <a name="azure-portal"></a>Azure Portal
Om du vill distribuera om en virtuell dator med Azure portal, Välj den virtuella datorn och bläddra ned till den **stöd + felsökning** avsnittet. Välj **omdistribuera** som i följande exempel:

![Distribuera om en virtuell dator i Azure portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Följande exempel används [az vm omdistribution](/cli/azure/vm) att distribuera om den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI
I följande exempel distribuerar om den virtuella datorn med namnet `myVM` i resursgruppen med namnet `myResourceGroup`. Använd dina egna värden enligt följande:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen
Prova de här stegen för att lösa de vanligaste SSH-anslutningsfel för virtuella datorer som har skapats med hjälp av den klassiska distributionsmodellen. Försök återansluta till den virtuella datorn efter varje steg.

* Återställ fjärråtkomst från den [Azure-portalen](https://portal.azure.com). Välj din virtuella dator på Azure-portalen och välj sedan **Återställ fjärråtkomst...** .
* Starta om den virtuella datorn. På den [Azure-portalen](https://portal.azure.com), Välj den virtuella datorn och välj **starta om**.

* Distribuera om den virtuella datorn till en ny Azure-nod. Information om hur du distribuera om en virtuell dator finns i [distribuera om virtuell dator till nya Azure-nod](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    När den här åtgärden är klar differentierande diskdata går förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn kommer att uppdateras.
* Följ instruktionerna i [återställa ett lösenord eller SSH för Linux-baserade virtuella datorer](../linux/classic/reset-access-classic.md) till:

  * Återställa lösenordet eller SSH-nyckel.
  * Skapa en *sudo* användarkonto.
  * Återställa SSH-konfigurationen.
* Kontrollera resurshälsa för den virtuella datorn om eventuella plattformsproblem.<br>
     Välj den virtuella datorn och rulla ned **inställningar** > **Kontrollera hälsa**.

## <a name="additional-resources"></a>Ytterligare resurser
* Om du är fortfarande inte SSH till den virtuella datorn när du har följt stegen efter, visa [mer detaljerad felsökning](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se ytterligare steg för att lösa problemet.
* Läs mer om hur du felsöker programåtkomst [Felsök åtkomst till ett program som körs på virtuella Azure-datorer](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Läs mer om hur du felsöker virtuella datorer som har skapats med hjälp av den klassiska distributionsmodellen [återställa ett lösenord eller SSH för Linux-baserade virtuella datorer](../linux/classic/reset-access-classic.md).
