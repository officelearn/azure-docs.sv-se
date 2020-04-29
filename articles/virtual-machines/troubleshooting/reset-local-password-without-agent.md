---
title: Återställa ett lokalt Windows-lösenord utan Azure-agent | Microsoft Docs
description: Återställa lösen ordet för ett lokalt Windows-användarkonto när Azures gästa Gent inte är installerad eller fungerar på en virtuell dator
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77921631"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Återställa det lokala Windows-lösenordet för en frånkopplad virtuell Azure-dator
Du kan återställa det lokala Windows-lösenordet för en virtuell dator i Azure med hjälp av [Azure Portal eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) förutsatt att Azures gästa Gent är installerad. Den här metoden är det primära sättet att återställa ett lösen ord för en virtuell Azure-dator. Om du stöter på problem med att Azures gästa Gent inte svarar eller om du inte vill installera efter att du har laddat upp en anpassad avbildning kan du manuellt återställa ett Windows-lösenord. Den här artikeln beskriver hur du återställer ett lokalt konto lösen ord genom att koppla den virtuella käll operativ system disken till en annan virtuell dator. De steg som beskrivs i den här artikeln gäller inte för Windows-domänkontrollanter. 

> [!WARNING]
> Använd endast den här processen som en sista utväg. Försök alltid att återställa ett lösen ord med [Azure Portal eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) först.

## <a name="overview-of-the-process"></a>Översikt över processen
De grundläggande stegen för att utföra en lokal lösen ords återställning för en virtuell Windows-dator i Azure när det inte finns någon åtkomst till Azures gästa Gent är följande:

1. Stoppa den virtuella datorn som påverkas.
1. Skapa en ögonblicks bild för den virtuella datorns OS-disk.
1. Skapa en kopia av OS-disken från ögonblicks bilden.
1. Anslut och montera den kopierade OS-disken till en annan virtuell Windows-dator och skapa sedan några konfigurationsfiler på disken. Filerna kan hjälpa dig att återställa lösen ordet.
1. Demontera och koppla från den kopierade OS-disken från den virtuella fel söknings datorn.
1. Byt OS-disk för den berörda virtuella datorn.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Detaljerade steg för den virtuella datorn med Resource Manager-distribution

> [!NOTE]
> Stegen gäller inte för Windows-domänkontrollanter. Den fungerar bara på fristående server eller en server som är medlem i en domän.

Försök alltid att återställa ett lösen ord med hjälp av [Azure Portal eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du provar följande steg. Kontrol lera att du har en säkerhets kopia av den virtuella datorn innan du börjar.

1. Ta en ögonblicks bild för OS-disken för den berörda virtuella datorn, skapa en disk från ögonblicks bilden och Anslut sedan disken till en Felsök virtuell dator. Mer information finns i [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure Portal](troubleshoot-recovery-disks-portal-windows.md).
2. Anslut till den virtuella fel söknings datorn med hjälp av fjärr skrivbord.
3. `\Windows\System32\GroupPolicy` Skapa `gpt.ini` på den virtuella käll hård disken (om GPT. ini finns, Byt namn till GPT. ini. bak):
   
   > [!WARNING]
   > Se till att du inte av misstag skapar följande filer i C:\Windows, OS-enheten för fel sökning av virtuell dator. Skapa följande filer i OS-enheten för din virtuella käll dator som är ansluten som en datadisk.
   
   * Lägg till följande rader i `gpt.ini` filen som du skapade:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Skapa GPT. ini](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. Skapa `scripts.ini` i `\Windows\System32\GroupPolicy\Machines\Scripts\`. Kontrol lera att dolda mappar visas. Om det behövs skapar du `Machine` - `Scripts` eller-mapparna.
   
   * Lägg till följande rader i `scripts.ini` filen du skapade:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Skapa skript. ini](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. Skapa `FixAzureVM.cmd` i `\Windows\System32` med följande innehåll och Ersätt `<username>` och `<newpassword>` med dina egna värden:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Skapa FixAzureVM. cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Du måste uppfylla de konfigurerade lösen ords komplexitets kraven för den virtuella datorn när du definierar det nya lösen ordet.

6. I Azure Portal kopplar du bort disken från den virtuella fel söknings datorn.

7. [Ändra OS-disken för den berörda virtuella datorn](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. När den nya virtuella datorn har körts ansluter du till den virtuella datorn med hjälp av fjärr skrivbord med det nya lösen `FixAzureVM.cmd` ordet som du angav i skriptet.

9. Ta bort följande filer från fjärrsessionen till den nya virtuella datorn för att rensa miljön:
    
    * Från%windir%\System32
      * ta bort FixAzureVM. cmd
    * Från%windir%\System32\GroupPolicy\Machine\Scripts
      * ta bort scripts. ini
    * Från%windir%\System32\GroupPolicy
      * ta bort GPT. ini (om GPT. ini fanns före, och du har bytt namn på den till GPT. ini. bak, Byt namn på bak-filen till GPT. ini)

## <a name="detailed-steps-for-classic-vm"></a>Detaljerade anvisningar för klassisk virtuell dator

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Stegen gäller inte för Windows-domänkontrollanter. Den fungerar bara på fristående server eller en server som är medlem i en domän.

Försök alltid att återställa ett lösen ord med hjälp av [Azure Portal eller Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) innan du provar följande steg. Kontrol lera att du har en säkerhets kopia av den virtuella datorn innan du börjar. 

1. Ta bort den virtuella datorn som påverkas i Azure Portal. Att ta bort den virtuella datorn tar bara bort metadata, referens för den virtuella datorn i Azure. De virtuella diskarna bevaras när den virtuella datorn tas bort:
   
   * Välj den virtuella datorn i Azure Portal och klicka sedan på *ta bort*:
     
     ![Ta bort befintlig virtuell dator](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Anslut den virtuella käll datorns OS-disk till den virtuella fel söknings datorn. Den virtuella fel söknings datorn måste finnas i samma region som den virtuella käll datorns OS- `West US`disk (till exempel):
   
   1. Välj den virtuella fel söknings datorn i Azure Portal. Klicka på *diskar* | *bifoga befintliga*:
     
      ![Bifoga befintlig disk](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. Välj *VHD-fil* och välj sedan det lagrings konto som innehåller den virtuella käll datorn:
     
      ![Välj lagringskonto](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. Markera kryss rutan *Visa klassiska lagrings konton*och välj sedan käll behållaren. Käll behållaren är vanligt vis *virtuella hård diskar*:
     
      ![Välj lagrings behållare](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Välj lagrings behållare](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. Välj den virtuella OS-disk som ska bifogas. Klicka på *Välj* för att slutföra processen:
     
      ![Välj virtuell käll disk](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Klicka på OK för att koppla disken

      ![Bifoga befintlig disk](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Anslut till fel söknings datorn med hjälp av fjärr skrivbord och se till att den virtuella käll datorns OS-disk är synlig:

   1. Välj den virtuella fel söknings datorn i Azure Portal och klicka på *Anslut*.

   2. Öppna den RDP-fil som laddas ned. Ange användar namn och lösen ord för den virtuella datorn för fel sökning.

   3. Leta efter den datadisk som du har anslutit i Utforskaren. Om den virtuella datorns virtuella hård disk är den enda datadisk som är ansluten till den virtuella fel söknings datorn ska den vara enheten F:
     
      ![Visa ansluten datadisk](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Skapa `gpt.ini` `\Windows\System32\GroupPolicy` på den virtuella käll datorn (om `gpt.ini` den finns, Byt namn till `gpt.ini.bak`):
   
   > [!WARNING]
   > Se till att du inte av misstag skapar följande filer i `C:\Windows`, OS-enheten för fel sökning av virtuell dator. Skapa följande filer i OS-enheten för din virtuella käll dator som är ansluten som en datadisk.
   
   * Lägg till följande rader i `gpt.ini` filen som du skapade:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Skapa GPT. ini](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. Skapa `scripts.ini` i `\Windows\System32\GroupPolicy\Machines\Scripts\`. Kontrol lera att dolda mappar visas. Om det behövs skapar du `Machine` - `Scripts` eller-mapparna.
   
   * Lägg till följande rader i `scripts.ini` filen du skapade:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Skapa skript. ini](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. Skapa `FixAzureVM.cmd` i `\Windows\System32` med följande innehåll och Ersätt `<username>` och `<newpassword>` med dina egna värden:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Skapa FixAzureVM. cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Du måste uppfylla de konfigurerade lösen ords komplexitets kraven för den virtuella datorn när du definierar det nya lösen ordet.

7. Ta bort disken från den virtuella fel söknings datorn i Azure Portal:
   
   1. Välj den virtuella fel söknings datorn i Azure Portal och klicka på *diskar*.
   
   2. Välj den data disk som är ansluten i steg 2, klicka på **Koppla från**och klicka sedan på **OK**.

     ![Koppla från disk](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Koppla från disk](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Skapa en virtuell dator från den virtuella käll datorn OS-disk:
   
     ![Skapa en virtuell dator från en mall](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Skapa en virtuell dator från en mall](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Skapa en virtuell dator från en mall](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Slutför upplevelsen för att skapa en virtuell dator

1. När den nya virtuella datorn har körts ansluter du till den virtuella datorn med hjälp av fjärr skrivbord med det nya lösen `FixAzureVM.cmd` ordet som du angav i skriptet.

2. Ta bort följande filer från fjärrsessionen till den nya virtuella datorn för att rensa miljön:
    
    * Som`%windir%\System32`
      * ta bort`FixAzureVM.cmd`
    * Som`%windir%\System32\GroupPolicy\Machine\Scripts`
      * ta bort`scripts.ini`
    * Som`%windir%\System32\GroupPolicy`
      * ta `gpt.ini` bort ( `gpt.ini` om den fanns före, och du byter namn på `gpt.ini.bak`den till, `.bak` byter du namn `gpt.ini`på filen tillbaka till)

## <a name="next-steps"></a>Nästa steg
Om du fortfarande inte kan ansluta med hjälp av fjärr skrivbord går du till [fel söknings guiden för RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). [Detaljerad fel söknings guide för RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) söker efter fel söknings metoder i stället för särskilda steg. Du kan också [öppna en support förfrågan för Azure](https://azure.microsoft.com/support/options/) för praktisk hjälp.
