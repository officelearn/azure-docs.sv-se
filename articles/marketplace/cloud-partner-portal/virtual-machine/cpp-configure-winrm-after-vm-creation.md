---
title: Konfigurera WinRM när den virtuella Azure-datorn har skapats | Azure Marketplace
description: Förklarar hur du konfigurerar Windows Remote Management (WinRM) när du har skapat en virtuell Azure-värd dator.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: 3a67371ce6f951a9e446ab639ea5b59248b79565
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144145"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurera WinRM när en virtuell dator har skapats

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ instruktionerna i [skapa ett erbjudande för virtuell Azure-dator](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) för att hantera dina migrerade erbjudanden.

I den här artikeln förklaras hur du konfigurerar en befintlig virtuell Azure-värd dator (VM) för att aktivera WinRM över HTTPS.  Den här konfigurationen gäller endast för Windows-baserade virtuella datorer och kräver följande två stegs process:

1. Aktivera port trafik för WinRM över HTTPS-protokollet.  Du konfigurerar den här inställningen för den virtuella datorn i Azure Portal.
2. Konfigurera den virtuella datorn för att aktivera WinRM genom att köra de angivna PowerShell-skripten.


## <a name="enabling-port-traffic"></a>Aktivera port trafik

WinRM över HTTPS-protokollet använder port 5986, som inte är aktive rad som standard på förkonfigurerade virtuella Windows-datorer som erbjuds på Azure Marketplace. Om du vill aktivera det här protokollet använder du följande steg för att lägga till en ny regel i nätverks säkerhets gruppen (NSG) med [Azure Portal](https://portal.azure.com).  Mer information om NSG: er finns i [säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview).

1. Navigera till bladet med **virtuella datorer >**   < *VM-namn* >   **> inställningar/nätverk**.
2. Klicka på NSG namn (i det här exemplet **testvm11002**) för att visa dess egenskaper:

    ![Egenskaper för nätverks säkerhets grupp](./media/nsg-properties.png)
 
3. Under **Inställningar**väljer du **inkommande säkerhets regler** för att visa det här bladet.
4. Klicka på **+ Lägg** till för att skapa en `WinRM_HTTPS` ny regel som kallas för TCP-port 5986.

    ![Lägg till säkerhets regel för inkommande nätverk](./media/nsg-new-rule.png)

5. Klicka på **OK** när du har angett värden.  Listan över inkommande säkerhets regler ska innehålla följande nya poster.

    ![Lista över inkommande nätverks säkerhets regler](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Konfigurera VM för att aktivera WinRM 

Använd följande steg för att aktivera och konfigurera Windows Remote Management-funktionen på din virtuella Windows-dator.   

1. Upprätta en fjärr skrivbords anslutning till den virtuella Azure-värdbaserade datorn.  Mer information finns i [så här ansluter du och loggar in på en virtuell Azure-dator som kör Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  De återstående stegen kommer att köras på den virtuella datorn.
2. Ladda ned följande filer och spara dem i en mapp på den virtuella datorn:
    - [ConfigureWinRM. ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [Makecert. exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf. cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Öppna **PowerShell-konsolen** med utökade privilegier (**Kör som administratör**). 
4. Kör följande kommando för att tillhandahålla den nödvändiga parametern: det fullständigt kvalificerade domän namnet (FQDN) för din virtuella dator: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![PowerShell-konfigurations skript 1](./media/powershell-file1.png)

    Det här skriptet beror på de andra två filerna i samma mapp.


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat WinRM är du redo att [distribuera den virtuella datorn från dess komponenters hård diskar](./cpp-deploy-vm-vhd.md).
