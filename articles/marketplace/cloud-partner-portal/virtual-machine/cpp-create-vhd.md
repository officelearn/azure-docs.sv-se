---
title: Skapa en Azure-kompatibel virtuell hård disk för Azure Marketplace
description: Förklarar hur du skapar en virtuell hård disk för ett erbjudande för virtuella datorer på Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 642c6964aaad8d6e8750fca67efb11eb3feaf19d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147014"
---
# <a name="create-an-azure-compatible-vhd"></a>Skapa en Azure-kompatibel virtuell hård disk

> [!IMPORTANT]
> Från och med 13 april 2020 börjar vi flytta hanteringen av din virtuella Azure-dator till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [skapa tekniska till gång till Azure-datorer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

Den här artikeln beskriver de steg som krävs för att skapa en virtuell hård disk (VHD) för en virtuell dator (VM)-erbjudande på Azure Marketplace.  Den innehåller också bästa praxis för olika aspekter, till exempel att använda Remote Desktop Protocol (RDP), välja en storlek för den virtuella datorn, installera de senaste Windows-uppdateringarna och generalisera VHD-avbildningen.  Följande avsnitt fokuserar främst på Windows-baserade virtuella hård diskar. Mer information om hur du skapar Linux-baserade virtuella hård diskar finns i [Linux on distributioner som har godkänts av Azure](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Vi rekommenderar starkt att du följer anvisningarna i det här avsnittet för att använda Azure för att skapa en virtuell dator som innehåller ett förkonfigurerat, godkänt operativ system.  Om detta inte är kompatibelt med din lösning är det möjligt att skapa och konfigurera en lokal virtuell dator med ett godkänt operativ system.  Du kan sedan konfigurera och förbereda den för uppladdning enligt beskrivningen i [förbereda en virtuell Windows-VHD eller VHDX för att ladda upp till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).


## <a name="select-an-approved-base"></a>Välj en godkänd bas
Operativ systemets virtuella hård disk för din VM-avbildning måste baseras på en Azure-godkänd bas avbildning som innehåller Windows Server eller SQL Server.
Börja med att skapa en virtuell dator från någon av följande avbildningar som finns på Microsoft Azure-portalen:

-    Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-    [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, Web)
-    [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, standard, Web)

> [!TIP]
> Om du använder den aktuella Azure Portal eller PowerShell, har Windows Server-avbildningar som publicerats den 8 september 2014 och senare godkänts.

Alternativt erbjuder Azure ett antal godkända Linux-distributioner.  En aktuell lista finns i [Linux on distributioner som har godkänts av Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Skapa en virtuell dator i Azure Portal 

I Microsoft [Azure Portal](https://ms.portal.azure.com/)skapar du bas avbildningen med hjälp av följande steg.

1. Logga in på portalen med Microsoft-konto för den Azure-prenumeration som du vill publicera ditt virtuella dator erbjudande.
2. Skapa en ny resurs grupp och ange **resurs gruppens namn**, **prenumeration**och **plats för resurs gruppen**.  Mer information finns i [Hantera resurs grupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Klicka på **virtuella datorer** på den vänstra meny raden för att visa sidan med information om virtuella datorer. 
4. På den nya sidan klickar du på **+ Lägg** till för att visa **Compute** -bladet.  Om du inte ser VM-typen på den första skärmen kan du söka efter namnet på den virtuella bas datorn, till exempel:

    ![Compute-bladet för den nya virtuella datorn](./media/publishvm_014.png)

5. När du har valt en lämplig virtuell avbildning anger du följande värden:
   * På bladet **grundläggande** inställningar anger du ett **namn** för den virtuella datorn, mellan 1-15 alfanumeriska tecken. (Det här exemplet `DemoVm009`använder.)
   * Ange ett **användar namn** och ett starkt **lösen ord**som används för att skapa ett lokalt konto på den virtuella datorn.  (Används `adminUser` här.)  Lösen ordet måste vara 8-123 tecken långt och uppfylla tre av följande fyra komplexitets krav: en gemen bokstav, en versal, en siffra och ett specialtecken. Mer information finns i [krav på användar namn och lösen ord](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Välj den resurs grupp som du skapade ( `DemoResourceGroup`här).
   * Välj en **plats** för Azure-datacenter (här `West US`).
   * Spara värdena genom att klicka på **OK** . 

6. Välj storleken på den virtuella dator som ska distribueras med följande rekommendationer:
   * Om du planerar att utveckla den virtuella hård disken lokalt spelar det ingen roll. Överväg att använda en av de mindre virtuella datorerna.
   * Om du planerar att utveckla avbildningen i Azure bör du överväga att använda en av de rekommenderade VM-storlekarna för den valda avbildningen.
   * Information om priser finns i den **rekommenderade pris nivå** väljaren som visas på portalen. Den visar de tre rekommenderade storlekarna som tillhandahålls av utgivaren. (Här är utgivaren Microsoft.)

   ![Storleks blad för ny virtuell dator](./media/publishvm_015.png)

7. I bladet **Inställningar** anger du alternativet **Använd hanterad disk** till **Nej**.  På så sätt kan du hantera den nya virtuella hård disken manuellt. ( **Inställnings** bladet gör det också möjligt att ändra andra ändringar av lagrings-och nätverks alternativ, till exempel genom att välja **Premium (SSD)** i **disk typ**.)  Fortsätt genom att klicka på **OK** .

    ![Bladet inställningar för ny virtuell dator](./media/publishvm_016.png)

8. Klicka på **Sammanfattning** och granska dina val. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **OK**.

    ![Sammanfattnings bladet för den nya virtuella datorn](./media/publishvm_017.png)

Azure påbörjar etableringen av den virtuella dator som du har angett.  Du kan följa förloppet genom att klicka på fliken **Virtual Machines** till vänster.  När den har skapats ändras statusen till **körs**.  Nu kan du [ansluta till den virtuella datorn](./cpp-connect-vm.md).


## <a name="next-steps"></a>Nästa steg

Om du har problem med att skapa din nya Azure-baserade virtuella hård disk kan du läsa [vanliga problem när du skapar en virtuell hård disk](./cpp-common-vhd-creation-issues.md).  Annars måste du [ansluta till de virtuella datorer](./cpp-connect-vm.md) som du skapade i Azure. 
