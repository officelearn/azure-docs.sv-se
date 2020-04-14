---
title: Skapa en Azure-kompatibel hårddisk för Azure Marketplace
description: Förklarar hur du skapar en virtuell hårddisk för ett erbjudande om virtuella datorer på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 99d2bc95c1dd837bfc3bcabcead28777b7e6f746
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273944"
---
# <a name="create-an-azure-compatible-vhd"></a>Skapa en Azure-kompatibel virtuell hårddisk

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi den rörliga hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i Skapa dina tekniska resurser för [din virtuella Azure-dator](https://aka.ms/AzureVMTechAsset) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs de steg som krävs för att skapa en virtuell hårddisk (VHD) för ett virtuellt datorerbjudande (VM) på Azure Marketplace.  Den innehåller också metodtips för olika aspekter, till exempel att använda RDP (Remote Desktop Protocol), välja en storlek för den virtuella datorn, installera de senaste Windows-uppdateringarna och generalisera VHD-avbildningen.  Följande avsnitt fokuserar huvudsakligen på windowsbaserade virtuella hårddiskar; Mer information om hur du skapar Linux-baserade virtuella hårddiskar finns i [Linux på distributioner som stöds av Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Vi rekommenderar starkt att du följer vägledningen i det här avsnittet för att använda Azure för att skapa en virtuell dator som innehåller ett förkonfigurerat, godkänt operativsystem.  Om detta inte är kompatibelt med din lösning är det möjligt att skapa och konfigurera en lokal virtuell dator med hjälp av ett godkänt operativsystem.  Du kan sedan konfigurera och förbereda den för överföring enligt beskrivningen i [Förbered en Windows VHD eller VHDX för att ladda upp till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Välj en godkänd bas
Operativsystemet VHD för vm-avbildningen måste baseras på en Azure-godkänd basavbildning som innehåller Windows Server eller SQL Server.
Börja med att skapa en virtuell dator från en av följande avbildningar, som finns på Microsoft Azure-portalen:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Företag, Standard, Webb)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Företag, Standard, Webb)

> [!TIP]
> Om du använder den aktuella Azure-portalen eller PowerShell godkänns Windows Server-avbildningar som publicerades den 8 september 2014 och senare.

Alternativt erbjuder Azure en rad godkända Linux-distributioner.  En aktuell lista finns i [Linux på distributioner som stöds av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Skapa virtuell dator i Azure-portalen 

Skapa basavbildningen i Microsoft [Azure-portalen](https://ms.portal.azure.com/)med hjälp av följande steg.

1. Logga in på portalen med Microsoft-kontot för den Azure-prenumeration som du vill publicera ditt VM-erbjudande.
2. Skapa en ny resursgrupp och ange **resursgruppsnamn,** **prenumeration**och **resursgruppsplats**.  Mer vägledning finns i [Hantera resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Klicka på **virtuella datorer** i det vänstra menyfältet för att visa informationssidan för virtuella datorer. 
4. På den här nya sidan klickar du på **+Lägg** till för att visa **beräkningsbladet.**  Om du inte ser typen virtuell dator på den första skärmen kan du söka efter namnet på den virtuella basdatorn, till exempel:

    ![Beräkningsblad för ny virtuell dator](./media/publishvm_014.png)

5. När du har valt rätt virtuell avbildning anger du följande värden:
   * På bladet **Grunderna** anger du ett **namn** för den virtuella datorn, mellan 1-15 alfanumeriska tecken. (I det `DemoVm009`här exemplet används .)
   * Ange ett **användarnamn** och ett starkt **lösenord**som används för att skapa ett lokalt konto på den virtuella datorn.  (Här `adminUser` används.)  Lösenordet måste vara 8-123 tecken långt och uppfylla tre av följande fyra komplexitetskrav: ett gemener, ett versalstecken, ett tal och ett specialtecken. Mer information finns i [Krav på användarnamn och lösenord](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Välj den resursgrupp som `DemoResourceGroup`du skapade (här).
   * Välj en Azure **Datacenter-plats** (här). `West US`
   * Spara dessa värden genom att klicka på **OK.** 

6. Välj storleken på den virtuella datorn som ska distribueras med hjälp av följande rekommendationer:
   * Om du planerar att utveckla den virtuella hårddisken lokalt spelar storleken ingen roll. Överväg att använda en av de mindre virtuella datorerna.
   * Om du planerar att utveckla avbildningen i Azure kan du överväga att använda en av de rekommenderade vm-storlekarna för den valda avbildningen.
   * Information om priser finns i väljaren **Rekommenderade prisnivåer** som visas på portalen. Den visar de tre rekommenderade storlekarna som tillhandahålls av utgivaren. (Här är utgivaren Microsoft.)

   ![Storlek blad av nya VM](./media/publishvm_015.png)

7. I bladet **Inställningar** ställer du in alternativet **Använd hanterad disk** på **Nej**.  På så sätt kan du manuellt hantera den nya virtuella hårddisken. (Med bladet **Inställningar** kan du också ändra andra ändring av lagrings- och nätverksalternativen, till exempel välja **Premium (SSD)** i **disktyp**.)  Klicka på **OK** för att fortsätta.

    ![Inställningsblad för ny virtuell dator](./media/publishvm_016.png)

8. Klicka på **Sammanfattning** och granska dina val. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **OK**.

    ![Sammanfattning blad av ny virtuell dator](./media/publishvm_017.png)

Azure börjar etablera den virtuella datorn som du har angett.  Du kan spåra dess framsteg genom att klicka på fliken **Virtuella datorer** till vänster.  När den har skapats ändras statusen till **Kör**.  Då kan du [ansluta till den virtuella datorn](./cpp-connect-vm.md).


## <a name="next-steps"></a>Nästa steg

Om du har haft problem med att skapa din nya Azure-baserade virtuella hårddisk läser du [Vanliga problem när den virtuella hårddisken skapas](./cpp-common-vhd-creation-issues.md).  Annars måste du [sedan ansluta till de virtuella datorer som](./cpp-connect-vm.md) du skapade på Azure. 
