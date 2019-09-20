---
title: Ansluta till en virtuell Windows Server-dator | Microsoft Docs
description: Lär dig hur du ansluter och loggar in på en virtuell Windows-dator med hjälp av Azure Portal och distributions modellen för Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 1980ee0bdea1316f6117d9deb7d2adb9df941768
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162619"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Ansluta och logga in på en virtuell Azure-dator som kör Windows
Du använder knappen **Anslut** på Azure Portal för att starta en fjärrskrivbordssession (RDP) från ett Windows-skrivbord. Först ansluter du till den virtuella datorn och sedan loggar du in.

Om du vill ansluta till en virtuell Windows-dator från en Mac måste du installera en RDP-klient för Mac, till exempel [Microsoft fjärrskrivbord](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2. På den vänstra menyn väljer du **Virtual Machines**.
3. Välj den virtuella datorn i listan.
4. Välj **Anslut**längst upp på sidan för den virtuella datorn.
2. På sidan **Anslut till virtuell dator** väljer du lämplig IP-adress och port. I de flesta fall ska standard-IP-adressen och porten användas. Välj **Ladda ned RDP-fil**. Om den virtuella datorn har en princip uppsättning för just-in-Time måste du först välja knappen **begär åtkomst** för att begära åtkomst innan du kan ladda ned RDP-filen. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med just-in-time-principen](../../security-center/security-center-just-in-time.md).
2. Öppna den nedladdade RDP-filen och välj **Anslut** när du tillfrågas. 
2. Du får en varning om att `.rdp` filen kommer från en okänd utgivare. Detta är normalt. I fönstret **anslutning till fjärrskrivbord** väljer du **Anslut** för att fortsätta.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd.](./media/connect-logon/rdp-warn.png)
3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange autentiseringsuppgifterna för ett konto på den virtuella datorn och välj sedan **OK**.
   
     **Lokalt konto**: Detta är vanligt vis det lokala kontots användar namn och lösen ord som du angav när du skapade den virtuella datorn. I det här fallet är domänen namnet på den virtuella datorn och är registrerad som *vmnamn*&#92;*användarnamn*.  
   
    Domänansluten **virtuell dator**: Om den virtuella datorn tillhör en domän anger du användar namnet i formatet *domän*&#92;*användar namn*. Kontot måste också antingen vara i gruppen administratörer eller ha beviljats behörighet för fjärråtkomst för den virtuella datorn.
   
    **Domänkontrollant:** Om den virtuella datorn är en domänkontrollant anger du användar namnet och lösen ordet för ett domän administratörs konto för domänen.
4. Välj **Ja** för att verifiera identiteten för den virtuella datorn och slutför inloggningen.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Om knappen **Anslut** på portalen är nedtonad och du inte är ansluten till Azure via en [Express Route](../../expressroute/expressroute-introduction.md) eller en VPN-anslutning från [plats till plats](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) måste du skapa och tilldela en offentlig IP-adress för din virtuella dator innan du kan använda RDP. Mer information finns i [offentliga IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Ansluta till den virtuella datorn med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du använder PowerShell och har installerat Azure PowerShell-modulen kan du också ansluta med hjälp av `Get-AzRemoteDesktopFile` cmdleten, som du ser nedan.

I det här exemplet startas RDP-anslutningen direkt, med hjälp av liknande prompter som ovan.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Du kan också spara RDP-filen för framtida användning.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta kan du läsa [Felsöka anslutningar till fjärr skrivbord](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

