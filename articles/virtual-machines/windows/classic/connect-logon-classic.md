---
title: Logga in på en klassisk Azure-VM | Microsoft Docs
description: Använda Azure portal för att logga in på en Windows-dator som skapats med den klassiska distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: bcbebc216dbd63acfb33cf72ba774d088149a3a7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Logga in på en virtuell Windows-dator med Azure Portal
I Azure portal ska du använda den **Anslut** för att starta en fjärrskrivbordssession och logga in på en virtuell Windows-dator.

Vill du ansluta till en Linux-VM? Se [så att logga in på en virtuell dator som kör Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om hur du loggar in på en virtuell dator med hjälp av Resource Manager-modellen finns [här](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn
1. Logga in på Azure Portal.
2. Klicka på den virtuella dator som du vill komma åt. Namnet visas i den **alla resurser** fönstret.

    ![Virtuella-machine-platser](./media/connect-logon/azureportaldashboard.png)

3. Klicka på **Anslut** i kommandofältet på instrumentpanelen för virtuell dator.

    ![Ansluta ikonen för den virtuella datorn](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Logga in på den virtuella datorn
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Nästa steg
* Om den **Anslut** är inaktiverad eller du har andra problem med anslutningen till fjärrskrivbordet, pröva att återställa konfigurationen. Klicka på **Återställ fjärråtkomst** från instrumentpanelen för virtuell dator.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* För problem med ditt lösenord, pröva att återställa den. Klicka på **Återställ lösenord** längs vänster kant för den virtuella datorn instrumentpanelen under **stöd + felsökning**.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Om dessa tips fungerar inte eller inte är vad du behöver kan du se [felsökning av anslutning till fjärrskrivbord till en Windows-baserad Azure-dator](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här artikeln beskriver hur du diagnostiserar och löser vanliga problem.
