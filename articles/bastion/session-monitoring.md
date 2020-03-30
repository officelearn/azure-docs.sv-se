---
title: Övervakning och hantering av Azure Bastion-session | Microsoft-dokument
description: I den här artikeln får du lära dig hur du väljer en pågående session och tvångskoppling eller ta bort den.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76981097"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Sessionsövervakning och hantering för Azure Bastion

När Bastion-tjänsten har etablerats och distribuerats i det virtuella nätverket kan du använda den för att sömlöst ansluta till valfri virtuell dator i det här virtuella nätverket. När användare ansluter till arbetsbelastningar kan Azure Bastion användas för att övervaka fjärrsessioner och vidta snabbhanteringsåtgärder. Med Azure Bastion-sessionsövervakning kan du visa vilka användare som är anslutna till vilka virtuella datorer. Den visar IP som användaren anslutit från, hur länge de har anslutits och när de är anslutna. Med sessionshanteringsupplevelsen kan du välja en pågående session och tvinga bort eller ta bort en session för att koppla bort användaren från den pågående sessionen.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Övervaka fjärrsessioner

1. I [Azure-portalen](https://portal.azure.com)navigerar du till din Azure Bastion-resurs och väljer **Sessioner** på sidan Azure Bastion.

   ![Sessioner](./media/session-monitoring/sessions.png)
2. På sidan **Sessioner** kan du se de pågående fjärrsessionerna till höger.

   ![visa session](./media/session-monitoring/view-session.png)
3. Välj **Uppdatera** om du vill visa den uppdaterade listan över fjärrsessioner. När du väljer Uppdatera hämtar Azure Bastion den senaste övervakningsinformationen och uppdaterar den i portalen.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Aktivera port 4443 för inkommande trafik från Gateway Manager för sessionsövervakning för att fungera.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Ta bort eller tvinga bort en pågående fjärrsession

Du kan välja en uppsättning sessions och tvinga bort dem. Följande steg visar hur du tar bort fjärrsessioner:

1. Navigera till din Azure Bastion-resurs och välj **Sessioner** på sidan Azure Bastion.

   ![navigate](./media/session-monitoring/navigate.png)
2. När du har valt Sessioner visas en lista över fjärrsessioner.

   ![lista sessioner](./media/session-monitoring/list.png)
3. Markera en viss fjärrsession, markera sedan de tre ellipserna till höger på sessionsraden och välj sedan **Ta bort**.

   ![delete](./media/session-monitoring/delete.png)
4. När du väljer Ta bort kopplas fjärrsessionen från och användaren visas meddelandet "Du har kopplats från" i fjärrsessionen.

   ![koppla från](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Nästa steg

Läs [Vanliga frågor om Bastion](bastion-faq.md).