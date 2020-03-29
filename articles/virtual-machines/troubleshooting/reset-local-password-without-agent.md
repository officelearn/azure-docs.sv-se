---
title: Återställa ett lokalt Windows-lösenord utan Azure-agent | Microsoft-dokument
description: Så här återställer du lösenordet för ett lokalt Windows-användarkonto när Azure-gästagenten inte är installerad eller fungerar på en virtuell dator
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: becbf88aeda164f7d916cbc1f1ace89262cc1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921631"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Återställa det lokala Windows-lösenordet för en frånkopplad virtuell Azure-dator
Du kan återställa det lokala Windows-lösenordet för en virtuell dator i Azure med hjälp av [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) förutsatt att Azure-gästagenten är installerad. Den här metoden är det primära sättet att återställa ett lösenord för en Virtuell Azure.This method is the primary way to reset a password for an Azure VM. Om du stöter på problem med att Azure-gästagenten inte svarar eller inte installerar efter att du har laddat upp en anpassad avbildning kan du återställa ett Windows-lösenord manuellt. I den här artikeln beskrivs hur du återställer ett lokalt kontolösenord genom att bifoga den virtuella källoperativsystemet till en annan virtuell dator. De steg som beskrivs i den här artikeln gäller inte för Windows-domänkontrollanter. 

> [!WARNING]
> Använd endast den här processen som en sista utväg. Försök alltid att återställa ett lösenord med [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) först.

## <a name="overview-of-the-process"></a>Översikt över processen
De viktigaste stegen för att utföra en lokal lösenordsåterställning för en Windows-virtuell dator i Azure när det inte finns någon åtkomst till Azure-gästagenten är följande:

1. Stoppa den berörda virtuella datorn.
1. Skapa en ögonblicksbild för OS-disken för den virtuella datorn.
1. Skapa en kopia av OS-disken från ögonblicksbilden.
1. Bifoga och montera den kopierade OS-disken på en annan Windows-virtuell dator och skapa sedan några konfigurationsfiler på disken. Filerna hjälper dig att återställa lösenordet.
1. Avmontera och koppla bort den kopierade OS-disken från den virtuella felsökningsdatorn.
1. Byt os-disken mot den berörda virtuella datorn.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Detaljerade steg för den virtuella datorn med Resurshanterarens distribution

> [!NOTE]
> Stegen gäller inte för Windows-domänkontrollanter. Det fungerar bara på fristående server eller en server som är medlem i en domän.

Försök alltid att återställa ett lösenord med [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du försöker med följande steg. Kontrollera att du har en säkerhetskopia av den virtuella datorn innan du börjar.

1. Ta en ögonblicksbild för OS-disken för den berörda virtuella datorn, skapa en disk från ögonblicksbilden och bifoga sedan disken till en felsöka virtuell dator. Mer information finns i [Felsöka en Virtuell Windows-dator genom att koppla OS-disken till en återställnings-VM med Azure-portalen](troubleshoot-recovery-disks-portal-windows.md).
2. Anslut till den virtuella felsökningsdatorn med fjärrskrivbord.
3. Skapa `gpt.ini` `\Windows\System32\GroupPolicy` på käll-VM:s enhet (om gpt.ini finns byter du namn till gpt.ini.bak):
   
   > [!WARNING]
   > Se till att du inte av misstag skapar följande filer i C:\Windows, OS-enheten för felsökningsdatorn. Skapa följande filer i OS-enheten för källdatorn som är ansluten som en datadisk.
   
   * Lägg till följande `gpt.ini` rader i filen som du skapade:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Skapa gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. Skapa `scripts.ini` `\Windows\System32\GroupPolicy\Machines\Scripts\`i . Kontrollera att dolda mappar visas. Om det behövs `Machine` `Scripts` skapar du mapparna eller.
   
   * Lägg till följande `scripts.ini` rader filen du skapade:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Skapa scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. Skapa `FixAzureVM.cmd` `\Windows\System32` med följande innehåll, `<username>` `<newpassword>` ersätt och med dina egna värderingar:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Skapa FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Du måste uppfylla de konfigurerade lösenordskomplexitetskraven för den virtuella datorn när du definierar det nya lösenordet.

6. I Azure-portalen kan du koppla från disken från den virtuella felsökningsdatorn.

7. [Ändra OS-disken för den berörda virtuella datorn](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. När den nya virtuella datorn har körts ansluter du till den `FixAzureVM.cmd` virtuella datorn med fjärrskrivbord med det nya lösenordet som du angav i skriptet.

9. Ta bort följande filer från fjärrsessionen till den nya virtuella datorn för att rensa miljön:
    
    * Från %windir%\System32
      * ta bort FixAzureVM.cmd
    * Från %windir%\System32\GroupPolicy\Machine\Scripts
      * ta bort scripts.ini
    * Från %windir%\System32\GroupPolicy
      * ta bort gpt.ini (om gpt.ini fanns tidigare, och du döpte om den till gpt.ini.bak, byt namn på .bak-filen tillbaka till gpt.ini)

## <a name="detailed-steps-for-classic-vm"></a>Detaljerade steg för klassisk virtuell dator

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Stegen gäller inte för Windows-domänkontrollanter. Det fungerar bara på fristående server eller en server som är medlem i en domän.

Försök alltid att återställa ett lösenord med [Azure-portalen eller Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) innan du försöker med följande steg. Kontrollera att du har en säkerhetskopia av den virtuella datorn innan du börjar. 

1. Ta bort den berörda virtuella datorn i Azure-portalen. Om du tar bort den virtuella datorn tas bara metadata bort, referensen för den virtuella datorn i Azure. De virtuella diskarna behålls när den virtuella datorn tas bort:
   
   * Välj den virtuella datorn i Azure-portalen och klicka sedan på *Ta bort:*
     
     ![Ta bort befintlig virtuell dator](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Koppla käll-VM:s OS-disk till den virtuella felsökningsdatorn. Den virtuella felsökningsdatorn måste vara i samma region som källdatorns OS-disk `West US`(t.ex.
   
   1. Välj felsöka vm i Azure-portalen. Klicka på *Diskar* | *Bifoga befintliga:*
     
      ![Koppla befintlig disk](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Välj *VHD-fil* och välj sedan det lagringskonto som innehåller källdatorns virtuella dator:
     
      ![Välj lagringskonto](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Markera rutan *Visa klassiska lagringskonton*och markera källbehållaren. Källbehållaren är vanligtvis *virtuella:*
     
      ![Välj lagringsbehållare](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Välj lagringsbehållare](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Välj det virtuella operativsystemet som ska kopplas. Klicka på *Välj* för att slutföra processen:
     
      ![Välj virtuell källdisk](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Klicka på Ok om du vill bifoga disken

      ![Koppla befintlig disk](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Anslut till felsökningsdatorn med Fjärrskrivbord och se till att källdatorns OS-disk är synlig:

   1. Välj felsöka virtuell dator i Azure-portalen och klicka på *Anslut*.

   2. Öppna RDP-filen som hämtas. Ange användarnamn och lösenord för den virtuella felsökningsdatorn.

   3. Leta efter den datadisk du har bifogat i Utforskaren. Om käll-VM:s virtuella dator är den enda datadisk som är ansluten till den virtuella felsökningsdatorn bör det vara F:-enheten:
     
      ![Visa bifogad datadisk](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Skapa `gpt.ini` `\Windows\System32\GroupPolicy` på källdatorns enhet `gpt.ini` (om det `gpt.ini.bak`finns, byt namn till):
   
   > [!WARNING]
   > Kontrollera att du inte av misstag skapar `C:\Windows`följande filer i , OS-enheten för felsökningsdatorn. Skapa följande filer i OS-enheten för källdatorn som är ansluten som en datadisk.
   
   * Lägg till följande `gpt.ini` rader i filen som du skapade:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Skapa gpt.ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Skapa `scripts.ini` `\Windows\System32\GroupPolicy\Machines\Scripts\`i . Kontrollera att dolda mappar visas. Om det behövs `Machine` `Scripts` skapar du mapparna eller.
   
   * Lägg till följande `scripts.ini` rader filen du skapade:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Skapa scripts.ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. Skapa `FixAzureVM.cmd` `\Windows\System32` med följande innehåll, `<username>` `<newpassword>` ersätt och med dina egna värderingar:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Skapa FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Du måste uppfylla de konfigurerade lösenordskomplexitetskraven för den virtuella datorn när du definierar det nya lösenordet.

7. I Azure-portalen kan du koppla från disken från den virtuella felsökningsdatorn:
   
   1. Välj felsöka vm i Azure-portalen, klicka på *Diskar*.
   
   2. Markera den datadisk som är bifogad i steg 2, klicka på **Koppla bort**och klicka sedan på **OK**.

     ![Koppla bort disk](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Koppla bort disk](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Skapa en virtuell dator från källdatorns OS-disk:
   
     ![Skapa en virtuell dator från mall](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Skapa en virtuell dator från mall](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Skapa en virtuell dator från mall](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Slutför upplevelsen skapa virtuella datorer

1. När den nya virtuella datorn har körts ansluter du till den `FixAzureVM.cmd` virtuella datorn med fjärrskrivbord med det nya lösenordet som du angav i skriptet.

2. Ta bort följande filer från fjärrsessionen till den nya virtuella datorn för att rensa miljön:
    
    * Från`%windir%\System32`
      * Ta bort`FixAzureVM.cmd`
    * Från`%windir%\System32\GroupPolicy\Machine\Scripts`
      * Ta bort`scripts.ini`
    * Från`%windir%\System32\GroupPolicy`
      * ta `gpt.ini` bort `gpt.ini` (om det fanns tidigare, `gpt.ini.bak`och du `.bak` döpte `gpt.ini`om den till , byta namn på filen tillbaka till )

## <a name="next-steps"></a>Nästa steg
Om du fortfarande inte kan ansluta med Fjärrskrivbord läser du [felsökningsguiden för FJÄRRSKRIVBORDS-program](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). I den [detaljerade felsökningsguiden för fjärrskrivbordsprogrammen](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) finns felsökningsmetoder i stället för specifika steg. Du kan också [öppna en Azure-supportbegäran för](https://azure.microsoft.com/support/options/) praktisk hjälp.
