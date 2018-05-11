---
title: Ansluta till en virtuell Windows Server-dator | Microsoft Docs
description: Lär dig hur du ansluter till och loggar in på en virtuell Windows-dator med hjälp av Azure Portal och Resource Manager-distributionsmodellen.
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
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Ansluta till och logga in på en virtuell Azure-dator som kör Windows
Du använder knappen **Anslut** på Azure Portal för att starta en fjärrskrivbordssession (RDP) från ett Windows-skrivbord. Du börjar med att ansluta till den virtuella datorn och loggar sedan in.

Om du försöker ansluta till en virtuell Windows-dator från en Mac måste du installera en RDP-klient för Mac som [Microsoft Remote Desktop](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **Virtuella datorer** på menyn till vänster.
3. Välj den virtuella datorn i listan.
4. Överst på sidan för den virtuella datorn klickar du på den ![Bild av anslutningsknappen.](./media/connect-logon/connect.png) till.
2. I den **Anslut till den virtuella datorn** , markera lämpliga alternativ och klickar på **ladda ned RDP-filen**.
2. Öppna den hämta RDP-filen och klicka på **Anslut** när du tillfrågas. 
2. Du får en varning att `.rdp`-filen kommer från en okänd utgivare. Detta är normalt. Fortsätt genom att klicka på **Anslut** i fjärrskrivbordsfönstret.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd.](./media/connect-logon/rdp-warn.png)
3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange autentiseringsuppgifterna för ett konto på den virtuella datorn och klicka sedan på **Ok**.
   
     **Lokalt konto**: detta är vanligtvis det lokala kontots användarnamn och lösenord som du angav när du skapade den virtuella datorn. I det här fallet är domänen namnet på den virtuella datorn och är registrerad som *vmnamn*&#92;*användarnamn*.  
   
    **Domänansluten VM**: Om den virtuella datorn tillhör en domän, anger du användarnamnet i formatet *Domän*&#92;*Användarnamn*. Kontot måste också antingen vara i gruppen administratörer eller ha beviljats behörighet för fjärråtkomst för den virtuella datorn.
   
    **Domänkontroller**: om den virtuella datorn är en domänkontroller, anger di användarnamn och lösenord för ett domänadministratörskonto för den domänen.
4. Klicka på **Ja** för att verifiera den virtuella datorns identitet och slutföra inloggning.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Om knappen **Anslut** på portalen är nedtonad och du inte är ansluten till Azure via [Express Route](../../expressroute/expressroute-introduction.md) eller en [VPN-anslutning för plats till plats](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) måste du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda RDP. Du kan läsa mer om [offentliga IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 


## <a name="next-steps"></a>Nästa steg
Om du får problem när du försöker ansluta läser du [Felsöka anslutningar till fjärrskrivbord](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här artikeln beskriver hur du diagnostiserar och löser vanliga problem.

