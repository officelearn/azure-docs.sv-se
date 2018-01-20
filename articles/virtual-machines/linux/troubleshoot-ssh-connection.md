---
title: "Felsökning av problem med SSH-anslutningen till en Azure VM | Microsoft Docs"
description: "Hur du felsöker problem, till exempel ”SSH-anslutningen misslyckades' eller 'SSH-anslutningen nekades' för en Azure-dator som kör Linux."
keywords: SSH anslutningen nekades, ssh fel, azure ssh, SSH-anslutning misslyckades
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: b7fe6dadb444ebbe6af6239562f507e451f9f605
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Felsökning av SSH-anslutningar till en Azure Linux-dator som misslyckas, fel, eller nekas
Det finns olika orsaker till att det uppstår fel på SSH (Secure Shell), SSH anslutningsfel eller SSH nekas när du försöker ansluta till en Linux-dator (VM). Den här artikeln hjälper dig att hitta och åtgärda problemen. Du kan använda Azure-portalen, Azure CLI eller tillägg för virtuell dator åtkomst för Linux för att felsöka och lösa anslutningsproblem med.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure och Stack Overflow-forum](http://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](http://azure.microsoft.com/support/options/) och välj **få support**. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](http://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Snabbsteg för felsökning
Försök ansluta till den virtuella datorn efter varje steg i felsökningen.

1. Återställ SSH-konfigurationen.
2. Återställa autentiseringsuppgifterna för användaren.
3. Kontrollera den [Nätverkssäkerhetsgruppen](../../virtual-network/virtual-networks-nsg.md) regler att SSH-trafik.
   * Se till att det finns en regel för Nätverkssäkerhetsgruppen så att SSH-trafik (som standard, TCP-port 22).
   * Du kan inte använda omdirigering av portar / mappning utan att använda en Azure belastningsutjämnare.
4. Kontrollera den [VM resurshälsa](../../resource-health/resource-health-overview.md). 
   * Se till att den virtuella datorn rapporteras som felfria.
   * Om du har aktiverat startdiagnostikinställningar Kontrollera den virtuella datorn inte rapporterar startfel i loggarna.
5. Starta om den virtuella datorn.
6. Distribuera den virtuella datorn.

Fortsätt läsa för mer detaljerad felsökning och förklaringar.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Tillgängliga metoder för att felsöka problem med SSH-anslutningen
Du kan återställa autentiseringsuppgifter eller SSH-konfigurationen genom att använda någon av följande metoder:

* [Azure-portalen](#use-the-azure-portal) – bra om du behöver att snabbt återställa SSH-konfigurationen eller SSH-nyckeln och du inte har Azure verktygen som installeras.
* [Azure CLI 2.0](#use-the-azure-cli-20) - om du redan är på kommandoraden, snabbt återställa SSH-konfigurationen eller autentiseringsuppgifter. Du kan också använda den [Azure CLI 1.0](#use-the-azure-cli-10)
* [Azure VMAccessForLinux-tillägget](#use-the-vmaccess-extension) – skapa och återanvända json definitionsfiler för att återställa SSH-konfigurationen eller autentiseringsuppgifter.

Försök ansluta till den virtuella datorn igen efter varje steg i felsökningen. Om du fortfarande inte kan ansluta försöker du med nästa steg.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen
Azure-portalen innehåller ett snabbt sätt att återställa SSH-konfigurationen eller autentiseringsuppgifter utan att installera verktyg som på den lokala datorn.

Välj den virtuella datorn i Azure-portalen. Rulla ned till den **stöd + felsökning** avsnittet och väljer **Återställ lösenord** som i följande exempel:

![Återställ SSH-konfigurationen eller autentiseringsuppgifter i Azure-portalen](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Återställ SSH-konfigurationen
Som ett första steg bör du välja `Reset configuration only` från den **läge** nedrullningsbara menyn som i föregående skärmbild och klicka på den **återställa** knappen. När den här åtgärden är klar försök komma åt den virtuella datorn igen.

### <a name="reset-ssh-credentials-for-a-user"></a>Återställ SSH-autentiseringsuppgifter för en användare
Om du vill återställa autentiseringsuppgifterna för en befintlig användare väljer du antingen `Reset SSH public key` eller `Reset password` från den **läge** nedrullningsbara menyn som i föregående skärmbild. Ange användarnamnet och en SSH-nyckel eller ett nytt lösenord och klicka sedan på den **återställa** knappen.

Du kan också skapa en användare med sudo-behörighet på den virtuella datorn från den här menyn. Ange ett nytt användarnamn och tillhörande lösenord eller SSH-nyckel och klicka sedan på den **återställa** knappen.

## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0
Om du inte redan gjort installera senaste [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in till en Azure med hjälp av [az inloggningen](/cli/azure/#login).

Om du har skapat och överföra en anpassad avbildning för Linux-disk, se till att den [Microsoft Azure Linux-agenten](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) version 2.0.5 eller senare är installerat. För virtuella datorer skapas med galleriavbildningar kan det här tillägget för åtkomst redan installeras och konfigureras för dig.

### <a name="reset-ssh-configuration"></a>Återställ SSH-konfiguration
Du kan först försök att återställa SSH-konfigurationen till standardvärden och SSH-servern på den virtuella datorn startas om. Observera att detta inte ändrar användarkontonamn, lösenord eller SSH-nycklar.
I följande exempel används [az vm användare återställa-ssh](/cli/azure/vm/user#reset-ssh) att återställa SSH-konfigurationen på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställ SSH-autentiseringsuppgifter för en användare
I följande exempel används [az vm uppdateringen](/cli/azure/vm/user#update) att återställa autentiseringsuppgifterna för `myUsername` med värdet som anges i `myPassword`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Om du använder SSH-nyckelautentisering, kan du återställa SSH-nyckeln för en viss användare. I följande exempel används **az vm komma åt set-linux-användare** att uppdatera SSH-nyckel som lagras i `~/.ssh/id_rsa.pub` för användare med namnet `myUsername`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Använd VMAccess-tillägget
Tillägget för VM-åtkomst för Linux läser i en json-fil som definierar åtgärder för att utföra. Dessa åtgärder omfattar återställer SSHD, när du återställer en SSH-nyckel eller lägger till en användare. Du fortfarande använda Azure CLI för att anropa VMAccess-tillägget, men du kan återanvända json-filerna över flera virtuella datorer om du vill. Den här metoden kan du skapa en databas för json-filer som sedan kan anropas för angivna scenarier.

### <a name="reset-sshd"></a>Återställ SSHD
Skapa en fil med namnet `settings.json` med följande innehåll:

```json
{  
    "reset_ssh":"True"
}
```

Med Azure CLI kan du sedan anropar den `VMAccessForLinux` tillägg för att återställa anslutningen SSHD genom att ange en json-fil. I följande exempel används [az vm-tillägget set](/cli/azure/vm/extension#set) att återställa SSHD på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställ SSH-autentiseringsuppgifter för en användare
Du kan återställa autentiseringsuppgifterna för en användare är om SSHD visas ska fungera korrekt. Om du vill återställa lösenordet för en användare skapar en fil med namnet `settings.json`. I följande exempel återställs autentiseringsuppgifterna för `myUsername` med värdet som anges i `myPassword`. Ange följande rader i din `settings.json` fil, använda egna värden:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Eller om du vill återställa SSH-nyckeln för en användare först skapa en fil med namnet `settings.json`. I följande exempel återställs autentiseringsuppgifterna för `myUsername` med värdet som anges i `myPassword`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Ange följande rader i din `settings.json` fil, använda egna värden:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

När du har skapat en json-fil, använder du Azure CLI för att anropa den `VMAccessForLinux` tillägg för att återställa SSH-autentiseringsuppgifter genom att ange en json-fil. I följande exempel återställs autentiseringsuppgifter på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-cli-10"></a>Använda Azure CLI 1.0
Om du inte redan har gjort [installera Azure CLI 1.0 och ansluta till din Azure-prenumeration](../../cli-install-nodejs.md). Kontrollera att du använder Resource Manager-läge på följande sätt:

```azurecli
azure config mode arm
```

Om du har skapat och överföra en anpassad avbildning för Linux-disk, se till att den [Microsoft Azure Linux-agenten](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) version 2.0.5 eller senare är installerat. För virtuella datorer skapas med galleriavbildningar kan det här tillägget för åtkomst redan installeras och konfigureras för dig.

### <a name="reset-ssh-configuration"></a>Återställ SSH-konfiguration
Hur SSHD kan vara felkonfigurerad eller ett fel uppstod i tjänsten. Du kan återställa SSHD att kontrollera att den SSH-konfigurationen är giltig. Återställer SSHD bör vara den första åtgärden som du vidta.

I följande exempel återställs SSHD på en virtuell dator med namnet `myVM` i resursgrupp med namnet `myResourceGroup`. Använd din egen VM- och gruppnamn enligt följande:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställ SSH-autentiseringsuppgifter för en användare
Om SSHD visas ska fungera korrekt, kan du återställa lösenordet för en användare är. I följande exempel återställs autentiseringsuppgifterna för `myUsername` med värdet som anges i `myPassword`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Om du använder SSH-nyckelautentisering, kan du återställa SSH-nyckeln för en viss användare. I följande exempel uppdateras SSH-nyckel som lagras i `~/.ssh/id_rsa.pub` för användare med namnet `myUsername`, på den virtuella datorn med namnet `myVM` i `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```


## <a name="restart-a-vm"></a>Starta om en virtuell dator
Om du har återställa SSH-konfigurationen och användarautentiseringsuppgifter eller påträffade ett fel när detta sker, kan du starta om den virtuella datorn till adressen underliggande beräkning problem.

### <a name="azure-portal"></a>Azure Portal
Om du vill starta om en virtuell dator med hjälp av Azure portal, Välj den virtuella datorn och klicka på den **starta om** knappen som i följande exempel:

![Starta om en virtuell dator i Azure-portalen](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
I följande exempel startar om den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
I följande exempel används [az vm omstart](/cli/azure/vm#restart) att starta om den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Distribuera om en VM
Du kan distribuera en virtuell dator till en annan nod i Azure, vilket kan korrigera eventuella underliggande nätverksproblem. Information om omdistribuera en virtuell dator finns [omdistribuera virtuell dator till en ny Azure nod](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> När den här åtgärden har slutförts tillfälliga data kommer att gå förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn kommer att uppdateras.
> 
> 

### <a name="azure-portal"></a>Azure Portal
Om du vill distribuera en virtuell dator med hjälp av Azure portal, Välj den virtuella datorn och rulla ned till den **stöd + felsökning** avsnitt. Klicka på den **omdistribuera** knappen som i följande exempel:

![Distribuera en virtuell dator i Azure-portalen](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli-10"></a>Azure CLI 1.0
I följande exempel återdistribuerar den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Följande exempel används [az vm Omdistributionen](/cli/azure/vm#redeploy) att distribuera om den virtuella datorn med namnet `myVM` i resursgrupp med namnet `myResourceGroup`. Använd egna värden enligt följande:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen
Gör så här för att lösa de vanligaste SSH-anslutningsfel för virtuella datorer som har skapats med hjälp av den klassiska distributionsmodellen. Försök ansluta till den virtuella datorn efter varje steg.

* Återställ fjärråtkomst från den [Azure-portalen](https://portal.azure.com). Välj Virtuellt Azure-portalen och klicka på den **Återställ fjärråtkomst...**  knappen.
* Starta om den virtuella datorn. På den [Azure-portalen](https://portal.azure.com), Välj den virtuella datorn och på den **starta om** knappen.
    
* Distribuera den virtuella datorn till en ny nod i Azure. Information om hur du distribuerar en virtuell dator finns [omdistribuera virtuell dator till en ny Azure nod](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
    När den här åtgärden har slutförts tillfälliga data kommer att gå förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn kommer att uppdateras.
* Följ instruktionerna i [hur du återställer ett lösenord eller SSH för Linux-baserade virtuella datorer](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) till:
  
  * Återställ lösenord eller SSH-nyckel.
  * Skapa en *sudo* användarkonto.
  * Återställ SSH-konfigurationen.
* Kontrollera den Virtuella datorresursens hälsotillstånd vid plattformsproblem.<br>
     Välj den virtuella datorn och rulla ned **inställningar** > **Kontrollera hälsan**.

## <a name="additional-resources"></a>Ytterligare resurser
* Om du fortfarande inte kan SSH till den virtuella datorn efter åtgärderna efter, se [mer detaljerad felsökning](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se ytterligare steg för att lösa problemet.
* Mer information om hur du felsöker programåtkomst finns [Felsök åtkomst till ett program som körs på en virtuell Azure-dator](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Mer information om felsökning av virtuella datorer som har skapats med hjälp av den klassiska distributionsmodellen finns [hur du återställer ett lösenord eller SSH för Linux-baserade virtuella datorer](classic/reset-access-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

