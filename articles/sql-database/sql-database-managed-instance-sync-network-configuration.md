---
title: Azure App Service - synkronisering nätverkskonfiguration
description: I den här artikeln beskrivs hur du synkroniserar nätverkskonfigurationen för Azure App Service hosting-abonnemang.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73687893"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synkronisering av nätverkskonfiguration för Azure App Service-värdplan

Det kan hända att även om du [har integrerat din app med ett Virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md)kan du inte upprätta anslutning till hanterad instans. En sak du kan prova är att uppdatera nätverkskonfigurationen för din serviceplan.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synkronisera nätverkskonfiguration för apptjänstvärdplan

Det gör du genom att följa dessa steg:  

1. Gå till apptjänstplanen för webbappar.

   ![app serviceplan](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klicka på **Nätverk** och klicka sedan **på Klicka här för att hantera**.

   ![hantera serviceplan](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Välj **ditt virtuella nätverk** och klicka på **Synkronisera nätverk**.

   ![synkronisera nätverk](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Vänta tills synkroniseringen är klar.
  
   ![synkroniseringen har gjorts](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Du är nu redo att försöka återupprätta anslutningen till den hanterade instansen.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ditt virtuella nätverk för hanterad instans finns i [VNet-arkitektur för hanterad instans](sql-database-managed-instance-connectivity-architecture.md) och [så här konfigurerar du befintligt VNet](sql-database-managed-instance-configure-vnet-subnet.md).
