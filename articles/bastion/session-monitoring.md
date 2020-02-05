---
title: Övervakning och hantering av Azure skydds-sessioner | Microsoft Docs
description: I den här artikeln får du lära dig hur du väljer en pågående session och tvingar fram anslutningen eller tar bort den.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76981097"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Övervakning och hantering av sessioner för Azure-skydds

När skydds-tjänsten har tillhandahållits och distribuerats i det virtuella nätverket kan du använda den för att sömlöst ansluta till en virtuell dator i det här virtuella nätverket. När användare ansluter till arbets belastningar kan Azure skydds användas för att övervaka fjärrsessioner och vidta åtgärder för snabb hantering. Med övervakningen av Azure skydds-sessionen kan du se vilka användare som är anslutna till vilka virtuella datorer. Den visar IP-adressen som användaren anslöt från, hur länge de har varit anslutna och när de är anslutna. Med hjälp av sessionen kan du välja en pågående session och framtvinga-från koppling eller ta bort en session för att koppla bort användaren från den pågående sessionen.

## <a name="monitor"></a>Övervaka fjärrsessioner

1. I [Azure Portal](https://portal.azure.com)navigerar du till din Azure skydds-resurs och väljer **sessioner** från sidan Azure skydds.

   ![terminalserversessioner](./media/session-monitoring/sessions.png)
2. På sidan **sessioner** kan du se pågående fjärrsessioner på höger sida.

   ![Visa session](./media/session-monitoring/view-session.png)
3. Välj **Uppdatera** om du vill visa den uppdaterade listan med fjärrsessioner. När du väljer uppdatera kommer Azure skydds att hämta den senaste övervaknings informationen och uppdatera den i portalen.

   ![Meta](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Aktivera port 4443 för inkommande trafik från Gateway Manager för övervakning av sessionen för att fungera.
>

## <a name="view"></a>Ta bort eller tvinga bort en pågående fjärrsession

Du kan välja en eller flera sessioner och tvinga dem att koppla från dem. Följande steg visar hur du tar bort fjärrsessioner:

1. Gå till Azure skydds-resursen och välj **sessioner** från sidan Azure skydds.

   ![sökning](./media/session-monitoring/navigate.png)
2. När du har valt sessioner visas en lista över fjärrsessioner.

   ![lista sessioner](./media/session-monitoring/list.png)
3. Välj en speciell fjärrsession, välj sedan tre ellipser på höger sida av raden session och välj sedan **ta bort**.

   ![delete](./media/session-monitoring/delete.png)
4. När du väljer Ta bort kommer fjärrsessionen att kopplas från och användaren visas i meddelandet "du har kopplats från" i fjärrsessionen.

   ![Koppla från](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).