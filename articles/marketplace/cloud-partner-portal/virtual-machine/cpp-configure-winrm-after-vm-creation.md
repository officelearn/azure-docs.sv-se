---
title: Konfigurera WinRM efter att Virtuella Azure-datorer har skapats | Azure Marketplace
description: Förklarar hur du konfigurerar Windows Remote Management (WinRM) efter att en virtuell dator med Azure har skapats.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: b80325594eedb87293c31de3236bb4690eb89e05
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273026"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurera WinRM när virtuell dator har skapats

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Skapa ett Azure Virtual Machine-erbjudande](https://aka.ms/CreateAzureVMoffer) för att hantera dina migrerade erbjudanden.

I den här artikeln beskrivs hur du konfigurerar en befintlig Azure-värdbaserad virtuell dator (VM) för att aktivera WinRM via HTTPS.  Den här konfigurationen gäller endast för Windows-baserade virtuella datorer och kräver följande tvåstegsprocess:

1. Aktivera porttrafik för WinRM via HTTPS-protokollet.  Du konfigurerar den här inställningen för den virtuella datorn i Azure-portalen.
2. Konfigurera den virtuella datorn så att WinRM kan aktiveras genom att köra de medföljande PowerShell-skripten.


## <a name="enabling-port-traffic"></a>Aktivera porttrafik

WinRM-protokollet via HTTPS använder port 5986, som som standard inte är aktiverad på förkonfigurerade Virtuella Windows-datorer som erbjuds på Azure Marketplace. Om du vill aktivera det här protokollet använder du följande steg för att lägga till en ny regel i nätverkssäkerhetsgruppen (NSG) med [Azure-portalen](https://portal.azure.com).  Mer information om NSG finns i [Säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).

1. Navigera till bladet **Virtuella datorer >**   < *vm-namn* >   **> Inställningar/Nätverk**.
2. Klicka på NSG-namnet (i det här exemplet **testvm11002)** för att visa dess egenskaper:

    ![Egenskaper för nätverkssäkerhetsgrupp](./media/nsg-properties.png)
 
3. Under **Inställningar**väljer du **Regler för inkommande säkerhet för** att visa det här bladet.
4. Klicka på **+Lägg till** `WinRM_HTTPS` om du vill skapa en ny regel som kallas för TCP-port 5986.

    ![Lägga till säkerhetsregel för inkommande nätverk](./media/nsg-new-rule.png)

5. Klicka på **OK** när du är klar med att ange värden.  Listan över inkommande säkerhetsregler bör innehålla följande nya poster.

    ![Lista över säkerhetsregler för inkommande nätverk](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Konfigurera virtuell dator för att aktivera WinRM 

Använd följande steg för att aktivera och konfigurera Windows Remote Management-funktionen på din virtuella Windows-dator.   

1. Upprätta en anslutning till fjärrskrivbord till den virtuella datorn med Azure.Establish a Remote Desktop connection to your Azure-hosted VM.  Mer information finns [i Så här ansluter och loggar du in på en virtuell Azure-dator som kör Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  De återstående stegen körs på den virtuella datorn.
2. Hämta följande filer och spara dem i en mapp på den virtuella datorn:
    - [KonfigureraWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Öppna **PowerShell-konsolen** med förhöjda privilegier (**Kör som administratör**). 
4. Kör följande kommando och ange den parameter som krävs: det fullständigt kvalificerade domännamnet (FQDN) för den virtuella datorn: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Powershell-konfigurationsskript 1](./media/powershell-file1.png)

    Skriptet beror på att de andra två filerna finns i samma mapp.


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat WinRM är du redo att [distribuera den virtuella datorn från dess ingående virtuella hårddiskar](./cpp-deploy-vm-vhd.md).
