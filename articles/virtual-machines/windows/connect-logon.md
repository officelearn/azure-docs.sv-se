---
title: Ansluta till en virtuell Windows Server-dator | Microsoft Docs
description: "Lär dig hur du ansluter till och loggar in på en virtuell Windows-dator med hjälp av Azure Portal och Resource Manager-distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 88431377a36d5bc36220c630f0c8d4a46ab4a434
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Ansluta till och logga in på en virtuell Azure-dator som kör Windows
Du använder knappen **Anslut** på Azure Portal för att starta en fjärrskrivbordssession (RDP) från ett Windows-skrivbord. Du börjar med att ansluta till den virtuella datorn och loggar sedan in.

Om du försöker ansluta till en virtuell Windows-dator från en Mac måste du installera en RDP-klient för Mac som [Microsoft Remote Desktop](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **Virtual Machines** på navmenyn.
3. Välj den virtuella datorn i listan.
4. Klicka på **Anslut** i bladet för den virtuella datorn.
   
    ![Skärmbild av Azure Portal som visar hur du ansluter till den virtuella datorn.](./media/connect-logon/connect.png)
   
   > [!TIP]
   > Om knappen **Anslut** på portalen är nedtonad och du inte är ansluten till Azure via [Express Route](../../expressroute/expressroute-introduction.md) eller en [VPN-anslutning för plats till plats](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) måste du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda RDP. Du kan läsa mer om [offentliga IP-adresser i Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Logga in på den virtuella datorn
[!INCLUDE [virtual-machines-log-on-win-server](../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Nästa steg
Om du får problem när du försöker ansluta läser du [Felsöka anslutningar till fjärrskrivbord](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här artikeln beskriver hur du diagnostiserar och löser vanliga problem.


