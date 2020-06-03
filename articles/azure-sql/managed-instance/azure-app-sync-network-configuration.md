---
title: Synkronisera nätverks konfiguration för Azure App Service
titleSuffix: Azure SQL Managed Instance
description: Den här artikeln beskriver hur du synkroniserar nätverks konfigurationen för Azure App Service hosting plan med din Azure SQL-hanterade instans.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: 8aca5a4f76504f1e9c33f55101f697ea6b3b6856
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309917"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Synkronisera nätverks konfiguration för Azure App Service värd plan med Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Det kan hända att även om du [integrerar din app med en Azure-Virtual Network](../../app-service/web-sites-integrate-with-vnet.md)kan du inte upprätta en anslutning till SQL-hanterad instans. Uppdaterar eller synkroniserar nätverks konfigurationen för din tjänst plan kan lösa det här problemet. 

## <a name="sync-network-configuration"></a>Synkronisera nätverkskonfiguration 

Det gör du genom att följa dessa steg:  

1. Gå till webbappar App Service plan.

   ![Skärm bild av App Service plan](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. Välj **nätverk** och välj sedan **Klicka här för att hantera**.

   ![Skärm bild av hantera tjänste plan](./media/azure-app-sync-network-configuration/manage-plan.png)

3. Välj ditt **VNet** och klicka på **Synkronisera nätverk**.

   ![Skärm bild av Sync-nätverk](./media/azure-app-sync-network-configuration/sync.png)

4. Vänta tills synkroniseringen är färdig.
  
   ![Skärm bild av synkronisering färdig](./media/azure-app-sync-network-configuration/sync-done.png)

Nu kan du försöka återupprätta anslutningen till din SQL-hanterade instans.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ditt VNet för SQL-hanterad instans finns i [SQL Managed instance VNet-arkitektur](connectivity-architecture-overview.md) och [hur du konfigurerar befintligt VNet](vnet-existing-add-subnet.md).
