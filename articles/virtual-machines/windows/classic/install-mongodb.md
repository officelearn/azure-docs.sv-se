---
title: "Installera MongoDB på en Windows-dator i Azure | Microsoft Docs"
description: "Lär dig hur du installerar MongoDB på en Azure VM som skapats med den klassiska distributionsmodellen som kör Windows Server."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
ms.openlocfilehash: 6b5af18d02fd508a21cdc21b38b1c16e79f07ecb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="install-mongodb-on-a-windows-vm-in-azure"></a>Installera MongoDB på en Windows-dator i Azure
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md).  Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Om du vill installera och konfigurera MongoDB med hjälp av Resource Manager-distributionsmodellen, se [i den här artikeln](../install-mongodb.md).

[MongoDB] [ MongoDB] är en populär öppen källkod, högpresterande NoSQL-databas. Den här artikeln hjälper dig att skapa en Windows Server virtuell dator (VM) med hjälp av den [Azure-portalen][AzurePortal]. Du skapar och bifogar en datadisk till den virtuella datorn innan du installerar och konfigurerar MongoDB. Om du har en befintlig virtuell dator i Azure som du vill använda kan du hoppa direkt till [installera och konfigurera MongoDB](#install-and-run-mongodb-on-the-virtual-machine).

## <a name="create-a-virtual-machine-running-windows-server"></a>Skapa en virtuell dator som kör Windows Server
Följ instruktionerna för att skapa en virtuell dator.

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> Du kan lägga till en slutpunkt för MongoDB när du skapar den virtuella datorn och konfigurera den på följande sätt: namnet som **Mongo**, använda **TCP** som protokoll, och Ställ in både offentliga och privata portar på  **27017**.
>
>

## <a name="attach-a-data-disk"></a>Anslut en datadisk
Ansluta en datadisk för att ge lagring för den virtuella datorn, och sedan initiera så att Windows kan använda den. Om du redan har en datadisk, kan du koppla den befintliga disken eller du kan koppla en tom disk.

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

Anvisningar för att initiera disken, finns i ”så här: initierar en ny datadisk i Windows Server” i [hur du kopplar en datadisk till en virtuell dator för Windows](attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Installera och köra MongoDB på den virtuella datorn
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Sammanfattning
I den här självstudiekursen beskrivs hur du skapar en virtuell dator som kör Windows Server från en annan dator att ansluta till den och ansluta en datadisk.  Du också fått lära dig hur du installerar och konfigurerar MongoDB på Windows-baserad virtuell dator. Du kan nu komma åt MongoDB på Windows-baserad virtuell dator, genom att följa de avancerade ämnena i det [MongoDB-dokumentation][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/

