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
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012367"
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

1. Klicka på den **Anslut** på egenskapssidan för virtuell dator. 
2. I den **Anslut till den virtuella datorn** , hålla väljer lämpliga alternativ och klickar på **ladda ned RDP-filen**.
2. Öppna den hämta RDP-filen och klicka på **Anslut** när du tillfrågas. 
2. Du får en varning att `.rdp`-filen kommer från en okänd utgivare. Detta är normalt. Fortsätt genom att klicka på **Anslut** i fjärrskrivbordsfönstret.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd.](./media/connect-logon/rdp-warn.png)
3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange autentiseringsuppgifterna för ett konto på den virtuella datorn och klicka sedan på **Ok**.
   
     **Lokalt konto**: detta är vanligtvis det lokala kontots användarnamn och lösenord som du angav när du skapade den virtuella datorn. I det här fallet är domänen namnet på den virtuella datorn och är registrerad som *vmnamn*&#92;*användarnamn*.  
   
    **Domänansluten VM**: Om den virtuella datorn tillhör en domän, anger du användarnamnet i formatet *Domän*&#92;*Användarnamn*. Kontot måste också antingen vara i gruppen administratörer eller ha beviljats behörighet för fjärråtkomst för den virtuella datorn.
   
    **Domänkontroller**: om den virtuella datorn är en domänkontroller, anger di användarnamn och lösenord för ett domänadministratörskonto för den domänen.
4. Klicka på **Ja** för att verifiera den virtuella datorns identitet och slutföra inloggning.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet.](./media/connect-logon/cert-warning.png)

## <a name="next-steps"></a>Nästa steg
* Om den **Anslut** är inaktiverad eller du har andra problem med anslutningen till fjärrskrivbordet, pröva att återställa konfigurationen. Klicka på **Återställ fjärråtkomst** från instrumentpanelen för virtuell dator.

    ![Återställ fjärråtkomst](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* För problem med ditt lösenord, pröva att återställa den. Klicka på **Återställ lösenord** längs vänster kant för den virtuella datorn instrumentpanelen under **stöd + felsökning**.

    ![Återställ lösenord](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Om dessa tips fungerar inte eller inte är vad du behöver kan du se [felsökning av anslutning till fjärrskrivbord till en Windows-baserad Azure-dator](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Den här artikeln beskriver hur du diagnostiserar och löser vanliga problem.
