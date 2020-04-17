---
title: Ansluta till en virtuell Windows Server-dator
description: Lär dig hur du ansluter och loggar in på en Windows-dator med Hjälp av Azure-portalen och resurshanterarens distributionsmodell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: c4f5df4a76792c64ab92bbe8d6e4b84080fbd8fe
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459193"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Ansluta och logga in på en virtuell Azure-dator som kör Windows
Du använder knappen **Anslut** på Azure Portal för att starta en fjärrskrivbordssession (RDP) från ett Windows-skrivbord. Först ansluter du till den virtuella datorn och sedan loggar du in.

Om du vill ansluta till en Virtuell Windows-dator från en Mac måste du installera en RDP-klient för Mac, till exempel [Microsoft Remote Desktop](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
1. Gå till [Azure-portalen](https://portal.azure.com/) för att ansluta till en virtuell dator. Sök efter och välj **Virtuella datorer**.
2. Välj den virtuella datorn i listan.
3. I början av sidan för den virtuella datorn väljer du **Anslut**.
4. På sidan **Anslut till virtuell dator** väljer du **RDP**och väljer sedan lämplig **IP-adress** och **Portnummer**. I de flesta fall bör standard-IP-adress och port användas. Välj **Ladda ned RDP-fil**. Om den virtuella datorn har en just-in-time-principuppsättning måste du först välja knappen **Begär åtkomst** för att begära åtkomst innan du kan hämta RDP-filen. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med hjälp av just-in-time-principen](../../security-center/security-center-just-in-time.md).
5. Öppna den nedladdade RDP-filen och välj **Anslut** när du tillfrågas. Du får en varning `.rdp` om att filen kommer från en okänd utgivare. Detta är normalt. Välj **Anslut** i fönstret **Anslutning till fjärrskrivbord** för att fortsätta.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd.](./media/connect-logon/rdp-warn.png)
3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange autentiseringsuppgifterna för ett konto på den virtuella datorn och välj sedan **OK**.
   
     **Lokalt konto**: Det här är vanligtvis det lokala kontots användarnamn och lösenord som du angav när du skapade den virtuella datorn. I det här fallet är domänen namnet på den virtuella datorn och är registrerad som *vmnamn*&#92;*användarnamn*.  
   
    **Domänen gick med i VM:** Om den virtuella datorn tillhör en domän anger du användarnamnet i formatet *Domän*&#92;*Användarnamn*. Kontot måste också antingen vara i gruppen administratörer eller ha beviljats behörighet för fjärråtkomst för den virtuella datorn.
   
    **Domänkontrollant**: Om den virtuella datorn är en domänkontrollant anger du användarnamnet och lösenordet för ett domänadministratörskonto för den domänen.
4. Välj **Ja** om du vill verifiera den virtuella datorns identitet och logga in färdigt.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Om **knappen Anslut** i portalen är nedtonad och du inte är ansluten till Azure via en [Express Route-](../../expressroute/expressroute-introduction.md) eller [Site-to-Site VPN-anslutning](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) måste du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda RDP. Mer information finns [i Offentliga IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Anslut till den virtuella datorn med PowerShell

 

Om du använder PowerShell och har Azure PowerShell-modulen `Get-AzRemoteDesktopFile` installerad kan du också ansluta med cmdlet, som visas nedan.

Det här exemplet startar omedelbart RDP-anslutningen och tar dig igenom liknande uppmaningar som ovan.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

Du kan också spara RDP-filen för framtida bruk.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta läser [du Felsöka fjärrskrivbordsanslutningar](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

