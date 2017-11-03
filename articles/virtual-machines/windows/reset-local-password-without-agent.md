---
title: "Återställer ett lokalt Windows-lösenord utan Azure-agenten | Microsoft Docs"
description: "Hur du återställer lösenordet för ett lokalt Windows-användarkonto när Azure gästagenten inte har installerats eller fungerar på en virtuell dator"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/07/2017
ms.author: iainfou
ms.openlocfilehash: 880f5e5967298401fc2522124af3746d9906ffa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Hur du återställer lokala Windows-lösenord för Azure VM
Du kan återställa det lokala Windows-lösenordet för en virtuell dator i Azure med hjälp av [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) förutsatt att Azure gästagenten är installerad. Den här metoden är det vanligaste sättet att återställa ett lösenord för en Azure VM. Om du får problem med Azure gästagenten inte svarar eller inte kunde installeras efter överföring av en anpassad avbildning, du kan manuellt återställa en Windows-lösenord. Den här artikeln beskriver hur du återställer ett lokalt kontolösenord genom att koppla den virtuella käll-OS-disken till en annan virtuell dator. 

> [!WARNING]
> Endast använda den här processen som en sista utväg. Alltid ett försök att återställa ett lösenord med hjälp av den [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) första.
> 
> 

## <a name="overview-of-the-process"></a>Översikt över processen
Grundläggande steg för att utföra en lokal återställning av lösenord för en virtuell Windows-dator i Azure när det finns ingen åtkomst till Azure gästagenten är följande:

* Ta bort den Virtuella källdatorn. Virtuella diskar bevaras.
* Koppla källa VM OS-disken till en annan virtuell dator på samma plats i din Azure-prenumeration. Den här virtuella datorn kallas felsökning VM.
* Använd felsökning VM för att skapa vissa config-filer på käll-VM OS-disken.
* Koppla bort den virtuella datorn OS-disken från felsökning VM.
* Använd en Resource Manager-mall för att skapa en virtuell dator med hjälp av den ursprungliga virtuella disken.
* När den nya virtuella datorn startar uppdatera config-filer som du skapar lösenordet för användaren som krävs.

## <a name="detailed-steps"></a>Detaljerade steg
Alltid ett försök att återställa ett lösenord med hjälp av den [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan de försöker med följande steg. Kontrollera att du har en säkerhetskopia av den virtuella datorn innan du börjar. 

1. Ta bort de berörda VM i Azure-portalen. Den virtuella datorn endast tar bort metadata, referensen för den virtuella datorn i Azure. Virtuella diskar bevaras när den virtuella datorn tas bort:
   
   * Välj den virtuella datorn i Azure-portalen, klicka på *ta bort*:
     
     ![Ta bort en befintlig virtuell dator](./media/reset-local-password-without-agent/delete_vm.png)
2. Koppla källa VM OS-disken till Virtuellt datorn felsökning. Felsökning VM måste finnas i samma region som käll-VM OS-disken (som `West US`):
   
   * Välj felsökning VM i Azure-portalen. Klicka på *diskar* | *bifoga befintliga*:
     
     ![Bifoga den befintliga disken](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Välj *VHD-filen* och välj sedan det lagringskonto som innehåller ditt Virtuella källdatorn:
     
     ![Välj lagringskonto](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Markera behållaren som källa. Käll-behållaren är vanligtvis *virtuella hårddiskar*:
     
     ![Välj lagringsbehållare](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Välj OS-vhd att ansluta. Klicka på *Välj* att slutföra processen:
     
     ![Välj källa för virtuell disk](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Ansluta till den Virtuella datorn med hjälp av fjärrskrivbord felsökning och säkerställa källa VM OS-disken visas:
   
   * Välj felsökning VM i Azure-portalen och klicka på *Anslut*.
   * Öppna RDP-filen som laddas ned. Ange användarnamn och lösenord för felsökning VM.
   * I Utforskaren, leta efter datadisk bifogade. Om ursprungliga Virtuella datorns VHD finns endast data-disk som är ansluten till Virtuellt datorn felsökning, ska det vara F: enhet:
     
     ![Visa bifogade datadisk](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Skapa `gpt.ini` i `\Windows\System32\GroupPolicy` på käll-VM-enhet (om det finns gpt.ini, Byt namn till gpt.ini.bak):
   
   > [!WARNING]
   > Kontrollera att du inte oavsiktligt skapar följande filer i C:\Windows OS-enhet för felsökning VM. Skapa följande filer i OS-enhet för källan virtuell dator som är anslutna som en datadisk.
   > 
   > 
   
   * Lägg till följande rader i den `gpt.ini` fil som du skapade:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Skapa gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Skapa `scripts.ini` i `\Windows\System32\GroupPolicy\Machine\Scripts`. Kontrollera att dolda mappar som visas. Skapa vid behov på `Machine` eller `Scripts` mappar.
   
   * Lägg till följande rader i `scripts.ini` fil som du skapade:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Skapa scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Skapa `FixAzureVM.cmd` i `\Windows\System32` med följande innehållet och Ersätt `<username>` och `<newpassword>` med egna värden:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add

    ```

    ![Skapa FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    När du definierar det nya lösenordet måste du uppfylla krav på komplexitet konfigurerat lösenord för den virtuella datorn.
7. Koppla bort disken från felsökning VM i Azure-portalen:
   
   * Välj felsökning VM i Azure-portalen, klicka på *diskar*.
   * Välj datadisken ansluten i steg 2, klicka på *Detach*:
     
     ![Koppla bort disk](./media/reset-local-password-without-agent/detach_disk.png)
8. Innan du skapar en virtuell dator måste du hämta URI till din datakälla OS-disk:
   
   * Välj lagringskonto i Azure-portalen klickar du på *Blobbar*.
   * Välj behållare. Käll-behållaren är vanligtvis *virtuella hårddiskar*:
     
     ![Välj kontot lagringsblob](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Välj källa VM OS VHD och klicka på den *kopiera* knappen bredvid den *URL* namn:
     
     ![Kopiera disk URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Skapa en virtuell dator från käll-VM OS-disken:
   
   * Använd [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) att skapa en virtuell dator från en särskild virtuell Hårddisk. Klicka på den `Deploy to Azure` knappen för att öppna den Azure-portalen med mallbaserat information fylls i automatiskt.
   * Om du vill bevara de tidigare inställningarna för den virtuella datorn, väljer *redigera mallen* att ge dina befintliga VNet, undernät, nätverkskort eller offentlig IP-adress.
   * I den `OSDISKVHDURI` parametern textruta, klistra in URI för käll-VHD hämta i föregående steg:
     
     ![Skapa en virtuell dator från mall](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. När den nya virtuella datorn körs, ansluta till den virtuella datorn med hjälp av fjärrskrivbord med det nya lösenordet du angav i den `FixAzureVM.cmd` skript.
11. Ta bort följande filer att rensa miljön från din fjärrsession till den nya virtuella datorn:
    
    * Från %windir%\System32
      * ta bort FixAzureVM.cmd
    * Från %windir%\System32\GroupPolicy\Machine\
      * ta bort scripts.ini
    * Från %windir%\System32\GroupPolicy
      * ta bort gpt.ini (om gpt.ini fanns före och du bytt namn till gpt.ini.bak, Byt namn på filen .bak tillbaka till gpt.ini)

## <a name="next-steps"></a>Nästa steg
Om du fortfarande inte kan ansluta med hjälp av fjärrskrivbord, finns det [RDP felsökningsguiden](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den [detaljerad RDP felsökningsguide för](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tittar på felsökning metoder i stället för särskilda åtgärder. Du kan också [öppnar du ett Azure supportbegäran](https://azure.microsoft.com/support/options/) för praktiska hjälp.

