---
title: Ansluta till en virtuell Windows Server-dator | Microsoft Docs
description: Lär dig hur du ansluter och logga in på en Windows-VM med hjälp av Azure portal och distributionsmodellen för Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: c14b6e16a16f325681722203eaa05a435b141208
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990614"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Hur du ansluter och logga in på Azure-datorer som kör Windows
Du använder knappen **Anslut** på Azure Portal för att starta en fjärrskrivbordssession (RDP) från ett Windows-skrivbord. Du ansluter till den virtuella datorn och sedan du loggar in på.

Om du vill ansluta till en virtuell Windows-dator från en Mac-dator, behöver du installera en RDP-klient för Mac som [Microsoft Remote Desktop](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2. På menyn till vänster väljer **virtuella datorer**.
3. Välj den virtuella datorn i listan.
4. Överst på sidan för den virtuella datorn, Välj **Connect**.
2. På den **Anslut till den virtuella datorn** väljer du lämplig IP-adress och port. I de flesta fall ska standard IP-adressen och porten användas. Välj **Ladda ned RDP-fil**. Om den virtuella datorn har en just-in-time-princip som angetts, måste du först välja den **begär åtkomst** knappen för att begära åtkomst innan du kan ladda ned RDP-filen. Mer information om just-in-time-principen finns i [hantera VM-åtkomst med hjälp av just i time-princip](../../security-center/security-center-just-in-time.md).
2. Öppna den nedladdade RDP-filen och välj **Anslut** när du tillfrågas. 
2. Du får en varning som den `.rdp` filen kommer från en okänd utgivare. Detta är normalt. I den **anslutning till fjärrskrivbord** väljer **Connect** att fortsätta.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd.](./media/connect-logon/rdp-warn.png)
3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange autentiseringsuppgifterna för ett konto på den virtuella datorn och välj sedan **OK**.
   
     **Lokalt konto**: Detta är vanligtvis lokala Kontoanvändarnamn och lösenord som du angav när du skapade den virtuella datorn. I det här fallet är domänen namnet på den virtuella datorn och är registrerad som *vmnamn*&#92;*användarnamn*.  
   
    **Domänansluten VM**: Om den virtuella datorn tillhör en domän kan du ange användarnamnet i formatet *domän*&#92;*användarnamn*. Kontot måste också antingen vara i gruppen administratörer eller ha beviljats behörighet för fjärråtkomst för den virtuella datorn.
   
    **Domänkontrollanten**: Om den virtuella datorn är en domänkontrollant, anger du användarnamn och lösenord för ett domänadministratörskonto för domänen.
4. Välj **Ja** att verifiera identiteten för den virtuella datorn och slutföra inloggning.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Om den **Connect** -knappen i portalen är nedtonad och du inte är ansluten till Azure via en [Express Route](../../expressroute/expressroute-introduction.md) eller [plats-till-plats VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) anslutning, behöver du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda RDP. Mer information finns i [offentliga IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Ansluta till den virtuella datorn med hjälp av PowerShell

Om du använder PowerShell och har installerat AzureRM-modulen kan du också ansluta med hjälp av den `Get-AzRemoteDesktopFile` cmdlet, enligt nedan.

Det här exemplet startas omedelbart RDP-anslutning, som tar dig igenom liknande frågor som ovan.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Du kan också spara RDP-filen för framtida användning.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Nästa steg
Om du har svårt att ansluta, se [felsöka fjärrskrivbordsanslutningar](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

