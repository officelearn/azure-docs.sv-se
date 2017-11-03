---
title: "Installera MySQL på en virtuell dator OpenSUSE | Microsoft Docs"
description: "Lär dig att installera MySQL på en OpenSUSE VMirtual för Linux-dator i Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
ms.openlocfilehash: 01b798a25575b66f89057315ce80d6cc0cde53b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Installera MySQL på en virtuell dator som kör OpenSUSE Linux i Azure
[MySQL] [ MySQL] är en populär, öppen källkod SQL-databas. Den här kursen visar hur du skapar en virtuell dator som kör OpenSUSE Linux och sedan installera MySQL.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Skapa en virtuell dator som kör OpenSUSE Linux
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>Installera och köra MySQL på den virtuella datorn
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>Nästa steg
Mer information om MySQL finns i [MySQL dokumentationen][MySQLDocs].

[MySQLDocs]:http://dev.mysql.com/doc/index-topic.html
[MySQL]:http://www.mysql.com

