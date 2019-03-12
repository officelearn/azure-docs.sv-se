---
title: Skapa en Azure-kompatibel VHD för Azure Marketplace | Microsoft Docs
description: Beskriver hur du skapar en virtuell Hårddisk till en virtuell dator på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: bd085804f532811e71d421e3f683c50c1c3416cb
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575494"
---
# <a name="create-an-azure-compatible-vhd"></a>Skapa en Azure-kompatibel VHD

Den här artikeln beskriver steg behövs för att skapa en virtuell hårddisk (VHD) till en virtuell dator (VM) i Azure Marketplace.  Den innehåller också Metodtips för olika områden, till exempel med Remote Desktop Protocol (RDP), att välja en storlek för den virtuella datorn, installerar de senaste uppdateringarna för Windows och generalisera den VHD-avbildningen.  I följande avsnitt fokuserar främst på windows-baserade virtuella hårddiskar; Mer information om hur du skapar Linux-baserade virtuella hårddiskar finns [Linux på distributioner som godkänts av Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Vi rekommenderar starkt att du följer riktlinjerna i det här avsnittet använder Azure för att skapa en virtuell dator som innehåller en förkonfigurerad, godkända operativsystemet.  Om det inte är kompatibel med din lösning, är det möjligt att skapa och konfigurera en lokal virtuell dator med hjälp av ett godkända operativsystem.  Du kan konfigurera och förbereda den för att ladda upp, enligt beskrivningen i [förbereda en Windows-VHD eller VHDX för att överföra till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Välj en godkänd base
Operativsystemet VHD för din avbildning måste baseras på en Azure-godkänd Källavbildning som innehåller Windows Server eller SQL Server.
Om du vill börja skapa en virtuell dator från någon av följande avbildningar som finns i Microsoft Azure portal:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/), [2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/), [2008 R2 SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Om du använder den aktuella Azure-portalen eller PowerShell, har Windows Server-avbildningar publicerade den 8 September 2014 och senare godkänts.

Azure erbjuder också ett antal godkända Linux-distributioner.  En aktuell lista finns i [Linux på distributioner som godkänts av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Skapa virtuell dator i Azure portal 

I Microsofts [Azure-portalen](https://ms.portal.azure.com/), skapa basavbildningen med följande steg.

1. Logga in på portalen med Microsoft-konto för Azure-prenumeration du vill publicera ditt erbjudande för virtuell dator.
2. Skapa en ny resursgrupp och ange din **Resursgruppsnamn**, **prenumeration**, och **resursgruppsplats**.  Mer information finns i [hantera resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Klicka på **virtuella datorer** i den vänstra menyraden för att visa sidan med virtuella datorer. 
4. I den nya sidan, klickar du på **+ Lägg till** att visa den **Compute** bladet.  Om du inte ser typ av virtuell dator på den första skärmen, kan du söka efter namnet på din grundläggande virtuell dator, till exempel:

    ![Compute-blad för nya virtuella datorn](./media/publishvm_014.png)

5. När du har valt rätt virtuella avbildningen kan du ange följande värden:
  * På den **grunderna** bladet anger du en **namn** för den virtuella datorn, mellan 1 och 15 alfanumeriska tecken. (Det här exemplet används `DemoVm009`.)
  * Ange en **användarnamn** och ett starkt **lösenord**, som används för att skapa ett lokalt konto på den virtuella datorn.  (Här `adminUser` används.)  Lösenordet måste innehålla mellan 8 och 123 tecken och uppfylla tre av följande fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken. Mer information finns i [krav på användarnamn och lösenord](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
  * Välj den resursgrupp som du skapade (här `DemoResourceGroup`).
  * Välj en Azure-Datacenter **plats** (här `West US`).
  * Klicka på **OK** att spara värdena. 

6.  Välj storleken på den virtuella datorn ska distribueras med hjälp av följande rekommendationer:
  * Om du planerar att utveckla VHD lokala roll storleken ingen. Överväg att använda en av de mindre virtuella datorerna.
  * Om du planerar att utveckla avbildningen i Azure bör du överväga att använda en av de storlekar som rekommenderas för den valda avbildningen.
  * Information om priser finns i den **rekommenderade prisnivåer** väljare som visas i portalen. Den visar de tre storlekar som rekommenderas av utgivaren. (Här är utgivaren Microsoft.)

    ![Bladet storlek för nya virtuella datorn](./media/publishvm_015.png)

7. I den **inställningar** , anger du den **Använd Managed Disk** alternativet att **nr**.  På så sätt kan du hantera den nya virtuella Hårddisken manuellt. (Den **inställningar** bladet kan du ändra andra ändringar även de lagring och nätverk alternativ, till exempel välja **Premium (SSD)** i **disktyp**.)  Klicka på **OK** att fortsätta.

    ![Bladet med inställningar för nya virtuella datorn](./media/publishvm_016.png)

8. Klicka på **Sammanfattning** och granska dina val. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **OK**.

    ![Bladet sammanfattning för nya virtuella datorn](./media/publishvm_017.png)

Azure börjar etablering av den virtuella datorn som du har angett.  Du kan spåra förloppet genom att klicka på **virtuella datorer** fliken till vänster.  När den har skapats ändras status till **kör**.  I det här läget kan du [ansluta till den virtuella datorn](./cpp-connect-vm.md).


## <a name="next-steps"></a>Nästa steg

Om du har stött svårt att skapa din nya Azure-baserad virtuell Hårddisk finns i [vanliga problem vid skapande av virtuell Hårddisk](./cpp-common-vhd-creation-issues.md).  I annat fall bredvid måste du [ansluta till de virtuella datorerna](./cpp-connect-vm.md) du skapade i Azure. 
