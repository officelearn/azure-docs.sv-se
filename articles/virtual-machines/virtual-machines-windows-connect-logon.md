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
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: db417fb72442ea8a5cd4ef882eb657b08bebaa0a


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Ansluta till och logga in på en virtuell Azure-dator som kör Windows
Du använder knappen **Anslut** på Azure Portal för att starta en fjärrskrivbordssession (RDP). Du börjar med att ansluta till den virtuella datorn och loggar sedan in.

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **Virtual Machines** på navmenyn.
3. Välj den virtuella datorn i listan.
4. Klicka på **Anslut** i bladet för den virtuella datorn.
   
    ![Skärmbild av Azure Portal som visar hur du ansluter till den virtuella datorn.](./media/virtual-machines-windows-connect-logon/connect.png)
   
   > [!TIP]
   > Om knappen **Anslut** på portalen är nedtonad och du inte är ansluten till Azure via [Express Route](../expressroute/expressroute-introduction.md) eller en [VPN-anslutning för plats till plats](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) måste du skapa och tilldela den virtuella datorn en offentlig IP-adress innan du kan använda RDP. Du kan läsa mer om [offentliga IP-adresser i Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Logga in på den virtuella datorn
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Nästa steg
Om du får problem när du försöker ansluta läser du [Felsöka anslutningar till fjärrskrivbord](virtual-machines-windows-troubleshoot-rdp-connection.md). Den här artikeln beskriver hur du diagnostiserar och löser vanliga problem.




<!--HONumber=Nov16_HO2-->


