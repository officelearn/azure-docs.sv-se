---
title: Azure App Service – synkronisera nätverkskonfiguration | Microsoft Docs
description: Den här artikeln beskrivs hur du synkroniserar din nätverkskonfiguration för Azure App Service-värdplan.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein, bonova, carlrab
manager: digimobile
origin.date: 12/13/2018
ms.date: 04/29/2019
ms.openlocfilehash: 0d7920080fd61389741fbe785f5141003bef5251
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61314728"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Synkronisera nätverkskonfiguration för Azure App Service-värdplan

Det kan hända att även om du [integrerat din app med Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), du kan inte upprätta anslutning till hanterad instans. En sak som du ska uppdatera nätverkskonfigurationen för din serviceplan.

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>Synkronisera nätverkskonfiguration för App Service-värdplanen

Det gör du genom att följa dessa steg:  

1. Gå till web apps App Service-plan.

   ![apptjänstplan](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. Klicka på **nätverk** och klicka sedan på **Klicka här för att hantera**.

   ![hantera service-plan](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. Välj din **VNet** och klicka på **synkronisera nätverk**.

   ![Synkronisera nätverk](./media/sql-database-managed-instance-sync-networking/sync.png)

4. Vänta tills synkroniseringen är klar.
  
   ![synkronisering är klar](./media/sql-database-managed-instance-sync-networking/sync-done.png)

Du är nu redo att testa att återupprätta anslutningen till din hanterade instans.

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar ditt virtuella nätverk för hanterad instans finns i [arkitektur för hanterad instans VNet](sql-database-managed-instance-connectivity-architecture.md) och [så här konfigurerar du befintliga virtuella nätverket](sql-database-managed-instance-configure-vnet-subnet.md).
