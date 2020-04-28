---
title: Azure App Service-synkronisera nätverks konfiguration
description: Den här artikeln beskriver hur du synkroniserar nätverks konfigurationen för Azure App Service värd plan.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: e7263d6a7716caf9f53e8496c6fb02b6d17b5509
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73687893"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synkronisera nätverks konfiguration för Azure App Service värd plan

Det kan hända att även om du [integrerar din app med en Azure-Virtual Network](../app-service/web-sites-integrate-with-vnet.md)kan du inte upprätta en anslutning till en hanterad instans. En sak som du kan prova är att uppdatera nätverks konfigurationen för din tjänst plan.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synkronisera nätverks konfiguration för App Service värd plan

Det gör du genom att följa dessa steg:  

1. Gå till webbappar App Service plan.

   ![App Service-plan](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klicka på **nätverk** och sedan på **Klicka här för att hantera**.

   ![hantera service plan](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Välj ditt **VNet** och klicka på **Synkronisera nätverk**.

   ![Synkronisera nätverk](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Vänta tills synkroniseringen är färdig.
  
   ![synkronisering har slutförts](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Nu kan du försöka återupprätta anslutningen till din hanterade instans.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ditt VNet för hanterad instans finns i [hanterad instans VNet-arkitektur](sql-database-managed-instance-connectivity-architecture.md) och [hur du konfigurerar befintligt VNet](sql-database-managed-instance-configure-vnet-subnet.md).
