---
title: Konfigurera WinRM när du har skapat för Azure-dator | Microsoft Docs
description: Förklarar hur du konfigurerar Windows Remote Management (WinRM) efter skapandet av en virtuell dator i Azure som värd.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 6350d5bde737c46cf14a9aef75a7ec57260a6afa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744608"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Konfigurera WinRM efter skapande av virtuella datorer

Den här artikeln förklarar hur du konfigurerar en befintlig Azure-baserad virtuell dator (VM) för att aktivera WinRM via HTTPS.  Den här konfigurationen gäller enbart för Windows-baserade virtuella datorer och kräver följande två steg:

1. Aktivera Porttrafik för WinRM via HTTPS-protokollet.  Du konfigurerar den här inställningen för den virtuella datorn i Azure-portalen.
2. Konfigurera den virtuella datorn för att aktivera WinRM genom att köra de angivna PowerShell-skript.


## <a name="enabling-port-traffic"></a>Aktivera Porttrafik

WinRM via HTTPS-protokollet använder port 5896, som inte är aktiverad som standard på förkonfigurerade Windows virtuella datorer tillhandahålls på Azure Marketplace. Om du vill aktivera det här protokollet, Använd följande steg för att lägga till en ny regel för nätverkssäkerhetsgrupp (NSG) med den [Azure-portalen](https://portal.azure.com).  Mer information om Nätverkssäkerhetsgrupper finns i [säkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Gå till bladet **virtuella datorer >**  <*vm-name*>  **> Inställningar/nätverk**.
2.  Klicka på NSG-namnet (i det här exemplet **testvm11002**) att visa dess egenskaper:

    ![Network security gruppegenskaper](./media/nsg-properties.png)
 
3. Under **inställningar**väljer **ingående säkerhetsregler** att visa det här bladet.
4. Klicka på **+ Lägg till** att skapa en ny regel som kallas `WinRM_HTTPS` för TCP-port 5986.

    ![Lägg till inkommande säkerhetsregel](./media/nsg-new-rule.png)

5. Klicka på **OK** när du är klar med värden.  Lista över ingående säkerhetsregler bör innehålla följande nya poster.

    ![Lista över inkommande säkerhetsregler](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Konfigurera virtuell dator för att aktivera WinRM 

Använd följande steg för att aktivera och konfigurera Windows Remote Management-funktionen på din Windows-VM.   

1. Upprätta en anslutning till fjärrskrivbord till din Azure-baserad virtuell dator.  Mer information finns i [hur du ansluter och logga in på Azure-datorer som kör Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  De återstående stegen ska köras på den virtuella datorn.
2. Hämta följande filer och spara dem i en mapp på den virtuella datorn:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Öppna den **PowerShell-konsolen** med förhöjd behörighet (**kör som administratör**). 
4. Kör följande kommando, tillhandahåller den nödvändiga parametern: det fullständigt kvalificerade domännamnet (FQDN) för den virtuella datorn: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![PowerShell-konfigurationsskript 1](./media/powershell-file1.png)

    Det här skriptet är beroende av de andra två filer som är i samma mapp.


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat WinRM, är du redo att [distribuera en virtuell dator från dess ingående virtuella hårddiskar](./cpp-deploy-vm-vhd.md).
