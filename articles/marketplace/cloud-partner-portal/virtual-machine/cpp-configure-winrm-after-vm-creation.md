---
title: Konfigurera WinRM när den virtuella Azure-datorn har skapats | Azure Marketplace
description: Förklarar hur du konfigurerar Windows Remote Management (WinRM) när du har skapat en virtuell Azure-värd dator.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: 7d050b32b212f66623a24bcf87d40111fc5973a5
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77481382"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurera WinRM när en virtuell dator har skapats

I den här artikeln förklaras hur du konfigurerar en befintlig virtuell Azure-värd dator (VM) för att aktivera WinRM över HTTPS.  Den här konfigurationen gäller endast för Windows-baserade virtuella datorer och kräver följande två stegs process:

1. Aktivera port trafik för WinRM över HTTPS-protokollet.  Du konfigurerar den här inställningen för den virtuella datorn i Azure Portal.
2. Konfigurera den virtuella datorn för att aktivera WinRM genom att köra de angivna PowerShell-skripten.


## <a name="enabling-port-traffic"></a>Aktivera port trafik

WinRM över HTTPS-protokollet använder port 5986, som inte är aktive rad som standard på förkonfigurerade virtuella Windows-datorer som erbjuds på Azure Marketplace. Om du vill aktivera det här protokollet använder du följande steg för att lägga till en ny regel i nätverks säkerhets gruppen (NSG) med [Azure Portal](https://portal.azure.com).  Mer information om NSG: er finns i [säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Navigera till bladet med **virtuella datorer >**   <*vm-namn*>   **> Inställningar/nätverk**.
2.  Klicka på NSG namn (i det här exemplet **testvm11002**) för att visa dess egenskaper:

    ![Egenskaper för nätverks säkerhets grupp](./media/nsg-properties.png)
 
3. Under **Inställningar**väljer du **inkommande säkerhets regler** för att visa det här bladet.
4. Klicka på **+ Lägg** till för att skapa en ny regel som heter `WinRM_HTTPS` för TCP-port 5986.

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
