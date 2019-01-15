---
title: Återställa en lokal Windows-lösenord utan Azure-agenten | Microsoft Docs
description: Så här återställer du lösenordet för ett lokalt Windows-användarkonto när Azure-gästagenten inte är installerad eller fungerar på en virtuell dator
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: bb5d7306558f46f84d1f4a1b7a61332bf767479f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267053"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Återställa lokala Windows-lösenord för Azure VM offline
Du kan återställa det lokala Windows-lösenordet för en virtuell dator i Azure med hjälp av den [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) angivna Azure-gästagenten är installerad. Den här metoden är det primära sättet att återställa ett lösenord för en Azure-dator. Om du får problem med Azure-gästagenten svarar inte eller inte kunde installeras när du har överfört en anpassad avbildning, kan du manuellt kan du återställer en Windows-lösenord. Den här artikeln beskriver hur du återställer ett lokalt kontolösenord genom att koppla den virtuella käll-OS-disken till en annan virtuell dator. Stegen som beskrivs i den här artikeln gäller inte för Windows-domänkontrollanter. 

> [!WARNING]
> Använd endast den här processen som en sista utväg. Alltid försöka med att återställa ett lösenord med hjälp av den [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) första.
> 
> 

## <a name="overview-of-the-process"></a>Översikt över processen
Grundläggande stegen för att utföra en lokal återställning av lösenord för en Windows-dator i Azure när det finns ingen åtkomst till Azure-gästagenten är följande:

* Ta bort den Virtuella källdatorn. De virtuella diskarna bevaras.
* Koppla den Virtuella källdatorns OS-disken till en annan virtuell dator på samma plats i Azure-prenumerationen. Den här virtuella datorn kallas för Virtuellt felsökningsdatorn.
* Med Virtuellt felsökningsdatorn kan skapa vissa konfigurationsfiler på den Virtuella källdatorns OS-disken.
* Koppla bort den Virtuella datorns OS-disken från Virtuellt felsökningsdatorn.
* Använd en Resource Manager-mall för att skapa en virtuell dator med hjälp av den ursprungliga virtuella hårddisken.
* När den nya virtuella datorn startas, uppdatera lösenordet för den nödvändiga användaren konfigurationsfiler som du skapar.

> [!NOTE]
> Du kan automatisera följande processer:
>
> - Skapa Virtuellt felsökningsdatorn
> - Koppla OS-disken
> - Återskapa den ursprungliga virtuella datorn
> 
> Gör detta genom att använda den [Azure VM Recovery skript](https://github.com/Azure/azure-support-scripts/blob/master/VMRecovery/ResourceManager/README.md). Om du väljer att använda Azure VM Recovery skript använder du följande process i avsnittet ”detaljerade steg”:
> 1. Hoppa över steg 1 och 2 genom att använda skripten för att koppla OS-disken på den berörda virtuella datorn till en virtuell dator för återställning.
> 2. Följ steg 3 – 6 för att tillämpa åtgärder.
> 3. Hoppa över steg 7 – 9 genom att använda skripten för att återskapa den virtuella datorn.
> 4. Följ steg 10 och 11.

## <a name="detailed-steps"></a>Detaljerade steg

> [!NOTE]
> Stegen gäller inte för Windows-domänkontrollanter. Det fungerar bara på fristående server eller en server som är medlem i en domän.
> 
> 

Alltid försöka med att återställa ett lösenord med hjälp av den [Azure-portalen eller Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du försöker följande steg. Kontrollera att du har en säkerhetskopia av den virtuella datorn innan du börjar. 

1. Ta bort den berörda virtuella datorn i Azure-portalen. Den virtuella datorn endast tar bort metadata som referens för den virtuella datorn i Azure. De virtuella diskarna bevaras när den virtuella datorn tas bort:
   
   * Välj den virtuella datorn i Azure-portalen, klicka på *ta bort*:
     
     ![Ta bort befintlig virtuell dator](./media/reset-local-password-without-agent/delete_vm.png)
2. Koppla den Virtuella källdatorns OS-disken till Virtuellt felsökningsdatorn. Virtuellt felsökningsdatorn måste finnas i samma region som den Virtuella källdatorns OS-disken (till exempel `West US`):
   
   * Välj Virtuellt felsökningsdatorn i Azure-portalen. Klicka på *diskar* | *bifoga befintlig*:
     
     ![Bifoga befintlig disk](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Välj *VHD-filen* och välj sedan det lagringskonto som innehåller Virtuella källdatorn:
     
     ![Välj lagringskonto](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Markera behållaren som källa. Käll-behållaren är vanligtvis *virtuella hårddiskar*:
     
     ![Välj lagringsbehållare](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Välj OS-vhd för att ansluta. Klicka på *Välj* att slutföra processen:
     
     ![Välj källa för virtuell disk](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Anslut till felsökning VM med hjälp av fjärrskrivbord och se till att den Virtuella källdatorns OS-disken är synliga:
   
   * Välj Virtuellt felsökningsdatorn i Azure-portalen och klicka på *Connect*.
   * Öppna RDP-filen som hämtar. Ange användarnamnet och lösenordet för Virtuellt felsökningsdatorn.
   * I Utforskaren, leta efter datadisken bifogade. Om källan virtuell dators VHD är den enda data-disk som är ansluten till Virtuellt felsökningsdatorn, bör den vara F:-enhet:
     
     ![Visa anslutna disken](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Skapa `gpt.ini` i `\Windows\System32\GroupPolicy` på den Virtuella källdatorns enhet (om det finns gpt.ini, byta namn på att gpt.ini.bak):
   
   > [!WARNING]
   > Se till att du inte råkar skapar följande filer i C:\Windows operativsystemenheten för Virtuellt felsökningsdatorn. Skapa följande filer i operativsystemenhet för källan virtuell dator som är ansluten som en datadisk.
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
5. Skapa `scripts.ini` i `\Windows\System32\GroupPolicy\Machine\Scripts\Startup`. Kontrollera att dolda mappar visas. Om det behövs skapar den `Machine` eller `Scripts` mappar.
   
   * Lägg till följande rader i `scripts.ini` fil som du skapade:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Skapa scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Skapa `FixAzureVM.cmd` i `\Windows\System32` med följande innehållet och Ersätt `<username>` och `<newpassword>` med dina egna värden:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Du måste uppfylla krav på komplexitet konfigurerat lösenord för den virtuella datorn när du definierar det nya lösenordet.
7. Koppla från disken från datorn för felsökning i Azure-portalen:
   
   * Välj Virtuellt felsökningsdatorn i Azure-portalen, klicka på *diskar*.
   * Välj datadisken ansluten i steg 2, klickar du på *Detach*:
     
     ![Ta bort disken](./media/reset-local-password-without-agent/detach_disk.png)
8. Innan du skapar en virtuell dator måste du hämta URI som källa OS-disken:
   
   * Välj lagringskontot i Azure-portalen, klicka på *Blobar*.
   * Välj behållaren. Käll-behållaren är vanligtvis *virtuella hårddiskar*:
     
     ![Välj logglagringskontots blob](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Välj källa VM OS-VHD och klicka på den *kopia* knappen bredvid den *URL* namn:
     
     ![Kopiera disk URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Skapa en virtuell dator från den Virtuella källdatorns OS-disken:
   
   * Använd [Azure Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet) att skapa en virtuell dator från en specialiserad virtuell Hårddisk. Klicka på den `Deploy to Azure` knappen för att öppna Azure portal med mallbaserade informationen som fyllts i åt dig.
   * Om du vill behålla de tidigare inställningarna för den virtuella datorn, väljer *redigera mallen* att tillhandahålla ditt befintligt virtuellt nätverk, undernät, nätverkskort eller offentlig IP-adress.
   * I den `OSDISKVHDURI` parametern textruta, klistra in URI för källan VHD hämta i föregående steg:
     
     ![Skapa en virtuell dator från mall](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. När den nya virtuella datorn körs, ansluta till den virtuella datorn med hjälp av fjärrskrivbord med det nya lösenordet du angav i den `FixAzureVM.cmd` skript.
11. Ta bort följande filer att rensa miljön från din fjärrsession till den nya virtuella datorn:
    
    * From %windir%\System32
      * remove FixAzureVM.cmd
    * Från %windir%\System32\GroupPolicy\Machine\
      * ta bort scripts.ini
    * From %windir%\System32\GroupPolicy
      * ta bort gpt.ini (om gpt.ini fanns före och du bytt namn till gpt.ini.bak, Byt namn på bak-filen tillbaka till gpt.ini)

## <a name="next-steps"></a>Nästa steg
Om du fortfarande inte kan ansluta med hjälp av fjärrskrivbord, visas den [RDP felsökningsguide för](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den [detaljerad RDP felsökningsguide](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tittar på felsökning metoder i stället för specifika steg. Du kan också [öppna en supportförfrågan för Azure](https://azure.microsoft.com/support/options/) för praktisk hjälp.

