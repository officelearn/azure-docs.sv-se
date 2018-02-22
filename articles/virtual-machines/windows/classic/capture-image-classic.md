---
title: "Hämta en avbildning av en Windows Azure-dator | Microsoft Docs"
description: Avbilda en virtuell Windows-dator skapad med den klassiska distributionsmodellen.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: ee6371faa9c3cc72104ba0eb05d17226679fa420
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Avbilda en virtuell Azure Windows-dator skapad med den klassiska distributionsmodellen
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Resource Manager modellinformation finns i [samla in en hanterad avbildning av en generaliserad virtuell dator i Azure](../capture-image-resource.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Den här artikeln visar hur du avbildar en Azure-dator som kör Windows, så du kan använda den som en bild för att skapa andra virtuella datorer. Den här avbildningen innehåller operativsystemdisken och eventuella hårddiskar som är kopplade till den virtuella datorn. Det finns inget nätverkskonfigurationer, så du behöver konfigurera nätverkskonfigurationer när du skapar de andra virtuella datorer som använder avbildningen.

Azure lagrar bilden under **VM-avbildningar (klassisk)**, **Compute** tjänst som visas när du visar alla Azure-tjänster. Det här är samma plats där alla bilder som du har överfört lagras. Mer information om avbildningar finns [om avbildningar för virtuella datorer](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Innan du börjar
Dessa instruktioner förutsätter att du redan skapat en virtuell Azure-dator och konfigurerat operativsystemet, inklusive bifoga datadiskar. Om du inte gjort det ännu, finns i följande artiklar för information om hur du skapar och förbereder den virtuella datorn:

* [Skapa en virtuell dator från en avbildning](createportal.md)
* [Så här kopplar du en datadisk till en virtuell dator](attach-disk.md)
* Se till att serverroller som stöds med Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Den här processen tar bort den ursprungliga virtuella datorn när den är klar.
>
>

Innan du gör en avbildning av en virtuell Azure-dator, rekommenderas det att säkerhetskopiera den virtuella måldatorn. Virtuella Azure-datorer kan säkerhetskopieras med Azure Backup. Mer information finns i [Säkerhetskopiera virtuella Azure-datorer](../../../backup/backup-azure-arm-vms.md). Det finns andra lösningar från certifierade partner. Om du vill ta reda på vad som finns för närvarande kan du söka på Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Avbilda den virtuella datorn
1. I den [Azure-portalen](http://portal.azure.com), **Anslut** till den virtuella datorn. Instruktioner finns i [så att logga in på en virtuell dator som kör Windows Server][How to sign in to a virtual machine running Windows Server].
2. Öppna ett kommandotolksfönster som administratör.
3. Ändra katalogen till `%windir%\system32\sysprep`, och sedan köra sysprep.exe.
4. Den **systemförberedelseverktyget** dialogrutan visas. Gör följande:

   * I **Rensa systemåtgärd**väljer **ange System Out of Box Experience (OOBE)** och se till att **Generalize** är markerad. Mer information om att använda Sysprep finns [så att använda Sysprep: en introduktion][How to Use Sysprep: An Introduction].
   * I **avstängningsalternativ**väljer **avstängning**.
   * Klicka på **OK**.

   ![Kör Sysprep](./media/capture-image/SysprepGeneral.png)
5. Sysprep stängs den virtuella datorn, som ändrar status för den virtuella datorn i Azure portal och **stoppad**.
6. I Azure-portalen klickar du på **virtuella datorer (klassisk)** och välj den virtuella dator som du vill samla in. Den **VM-avbildningar (klassisk)** grupp visas under **Compute** när du visar **alla tjänster**.

7. Klicka på kommandofältet **avbilda**.

   ![Avbilda virtuell dator](./media/capture-image/CaptureVM.png)

   Den **avbilda den virtuella datorn** dialogrutan visas.

8. I **avbildningsnamn**, Skriv ett namn för den nya avbildningen. I **avbildningsetiketten**, ange en etikett för den nya avbildningen.

9. Klicka på **jag har kört Sysprep på den virtuella datorn**. Den här kryssrutan refererar till åtgärder med Sysprep i steg 3-5. En avbildning _måste_ vara generaliserad med Sysprep innan du lägger till en Windows Server-avbildning till en uppsättning anpassade avbildningar.

10. När avbildningen har slutförts, den nya avbildningen blir tillgängligt i den **Marketplace**i den **Compute**, **VM-avbildningar (klassisk)** behållare.

    ![Avbildningen lyckades](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Nästa steg
Bilden är redo att användas för att skapa virtuella datorer. Om du vill göra det, ska du skapa en virtuell dator genom att välja den **alla tjänster** menyalternativet längst ned i tjänster menyn och sedan **VM-avbildningar (klassisk)** i den **Compute** grupp. Instruktioner finns i [skapa en virtuell dator från en avbildning](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
