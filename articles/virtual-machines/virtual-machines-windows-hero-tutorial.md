---
title: "Skapa din första virtuella Windows-dator | Microsoft Docs"
description: "Lär dig hur du skapar din första virtuella Windows-dator med hjälp av Azure Portal."
keywords: Virtuell Windows-dator, skapa en virtuell dator, virtuell dator, konfigurera en virtuell dator
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 785e17eb-4a13-4f06-b70f-4bd496d0ec5d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: hero-article
ms.date: 01/03/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 425637599df40ac3be23984785e4f3292d72978d
ms.openlocfilehash: 7c7a300b620b1e7bd0cd1b816e575f2d57fee80a


---
# <a name="create-your-first-windows-virtual-machine-in-the-azure-portal"></a>Skapa din första virtuella Windows-dator på Azure Portal
I den här självstudiekursen ser du hur enkelt det är att skapa en virtuell Windows-dator (VM) på bara några minuter med hjälp av Azure Portal.  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Välja VM-avbildning från Marketplace
Vi använder en Windows Server 2016 Datacenter-avbildning som exempel, men det är bara en av de många avbildningar som är tillgängliga i Azure. Vilka avbildningsalternativ som är tillgängliga beror på din prenumeration. Exempelvis kan vissa skrivbordsavbildningar vara tillgängliga för [MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Börja längst upp till vänster och klicka på **Nytt** > **Beräkna** > **Windows Server 2016 Datacenter**.
   
    ![Skärmbild som visar de tillgängliga avbildningarna för virtuella Azure-datorer på portalen](./media/virtual-machines-windows-hero-tutorial/marketplace-new.png)
3. Kontrollera att **Resource Manager** är valt i **Välj en distributionsmodell** på bladet **Windows Server 2016 Datacenter**. Klicka på **Skapa**.
   
    ![Skärmbild som visar distributionsmodellen som ska användas för den virtuella datorn](./media/virtual-machines-windows-hero-tutorial/deployment-model.png)

## <a name="create-the-windows-virtual-machine"></a>Skapa den virtuella Windows-datorn
När du har valt avbildningen kan du använda standardinställningarna och snabbt skapa den virtuella datorn.

1. På bladet **Grundläggande inställningar** anger du ett **namn** för den virtuella datorn. I det här exemplet är *HeroVM* namnet på den virtuella datorn. Namnet måste vara mellan 1 och 15 tecken långt och får inte innehålla specialtecken.
2. Ange ett **användarnamn** och ett starkt **lösenord** som ska användas för att skapa ett lokalt konto på den virtuella datorn. Det lokala kontot används för att logga in på och hantera den virtuella datorn. I det här exemplet är *azureuser* användarnamnet.
   
    Lösenordet måste innehålla mellan 8 och 123 tecken och uppfylla tre av följande fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken. Läs mer om [krav för användarnamn och lösenord](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

3. Välj en befintlig [resursgrupp](../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny. I det här exemplet är *HeroVMRG* namnet på resursgruppen.

4. Välj en **Plats** för ett Azure-datacenter. I det här exemplet är *USA, östra** platsen. 

4. När du är klar klickar du på **OK** för att fortsätta till nästa avsnitt. 
   
    ![Skärmbild som visar bladet Grundläggande inställningar där du kan konfigurera en virtuell Azure-dator](./media/virtual-machines-windows-hero-tutorial/basics-blade.png)
5. Välj en [storlek](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för den virtuella datorn och fortsätt genom att klicka på **Välj**. I det här exemplet är *DS1_V2 Standard* VM-storleken.
   
    ![Skärmbild av bladet Storlek som visar de tillgängliga storlekarna för virtuella datorer i Azure](./media/virtual-machines-windows-hero-tutorial/size-blade.png)
6. På bladet **Inställningar** kan du ändra lagrings- och nätverksalternativen. I den här självstudiekursen accepterar du standardinställningarna. Om du valde en VM-storlek som stöder det kan du prova Azure Premium Storage genom att välja **Premium (SSD)** i **Disktyp**. När du har gjort önskade ändringar klickar du på **OK**.
   
    ![Skärmbild av bladet Inställningar där du kan konfigurera valfria funktioner för en virtuell Azure-dator](./media/virtual-machines-windows-hero-tutorial/settings-blade.png)
7. Klicka på **Sammanfattning** och granska dina val. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **OK**.
   
    ![Skärmbild av sidan Sammanfattning som visar de valda konfigurationsalternativen för den virtuella Azure-datorn](./media/virtual-machines-windows-hero-tutorial/summary-blade.png)
8. Medan Azure skapar den virtuella datorn kan du följa förloppet genom att klicka på **Virtual Machines** till vänster. När den virtuella datorn har skapats ändras status till **Körs**.

## <a name="connect-to-the-virtual-machine-and-sign-on"></a>Ansluta till den virtuella datorn och logga in
1. Klicka på **Virtual Machines** till vänster.
2. Välj den virtuella datorn i listan.
3. Klicka på **Anslut** i bladet för den virtuella datorn. När du gör det skapas och hämtas en RDP-fil (Remote Desktop Protocol) som fungerar som en genväg för att ansluta till din dator. Du kan spara filen på skrivbordet för enkel åtkomst. **Öppna** den här filen när du vill ansluta till den virtuella datorn.
   
    ![Skärmbild av Azure Portal som visar hur du ansluter till den virtuella datorn](./media/virtual-machines-windows-hero-tutorial/connect.png)
4. En varning visas som anger att RDP-filen kommer från en okänd utgivare. Detta är normalt. Fortsätt genom att klicka på **Anslut** i fjärrskrivbordsfönstret.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)
5. I fönstret Windows-säkerhet anger du användarnamnet och lösenordet för det lokala kontot som du skapade när du skapade den virtuella datorn. Användarnamnet anges som *vm-namn*&#92;*användarnamn*. Klicka på **OK**.
   
    ![Skärmbild av hur du anger den virtuella datorns namn, användarnamn och lösenord](./media/virtual-machines-windows-hero-tutorial/credentials.png)
6. Du får en varning om att certifikatet inte kan verifieras. Detta är normalt. Klicka på **Ja** för att verifiera den virtuella datorns identitet och slutföra inloggning.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)

Om du får problem när du försöker ansluta läser du [Felsöka anslutningar till fjärrskrivbord till en Windows-baserad virtuell dator i Azure](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Nu kan du arbeta med den virtuella datorn som med andra servrar.

## <a name="optional-stop-the-vm"></a>Valfritt: Stoppa den virtuella datorn
Det är en bra idé att stoppa den virtuella datorn så att du inte debiteras när du inte använder den. Klicka på **Stoppa** och klicka sedan på **Ja**.

![Skärmbild som visar knappen för att stoppa en virtuell dator](./media/virtual-machines-windows-hero-tutorial/stop-vm.png)

Klicka på knappen **Starta** när du vill starta om den virtuella datorn och börja använda den igen.

## <a name="next-steps"></a>Nästa steg
* [Installera IIS](virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att experimentera med din nya VM. Den här kursen visar också hur du öppnar port 80 för inkommande webbtrafik med en nätverkssäkerhetsgrupp (NSG). 
* Du kan också [skapa en virtuell Windows-dator med hjälp av PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [skapa en virtuell Linux-dator](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med Azure CLI.
* Om du är intresserad av att automatisera distributioner kan du läsa [Skapa en virtuell Windows-dator med hjälp av en Resource Manager-mall](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Jan17_HO2-->


