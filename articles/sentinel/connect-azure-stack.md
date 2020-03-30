---
title: Ombord på dina virtuella Azure Stack-datorer till Azure Sentinel | Microsoft-dokument
description: Den här artikeln visar hur du etablerar azure monitor-, uppdaterings- och konfigurationshanteringstillägget för virtuella datorer på Virtuella Azure Stack-datorer och börjar övervaka dem med Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588526"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Ansluta virtuella Azure Stack-datorer till Azure Sentinel




Med Azure Sentinel kan du övervaka dina virtuella datorer som körs på Azure och Azure Stack på ett ställe. Om du vill vara ombord på dina Azure Stack-datorer i Azure Sentinel måste du först lägga till tillägget för den virtuella datorn i dina befintliga virtuella Azure Stack-datorer. 

När du har anslutit Azure Stack-datorer väljer du från ett galleri med instrumentpaneler som visar insikter baserat på dina data. Dessa instrumentpaneler kan enkelt anpassas efter dina behov.



## <a name="add-the-virtual-machine-extension"></a>Lägg till tillägget för den virtuella datorn 

Lägg till tillägget **Azure Monitor, Update och Configuration Management** till de virtuella datorer som körs på din Azure Stack. 

1. Logga in på din [Azure Stack-portal](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)på en ny webbläsarflik .
2. Gå till sidan **Virtuella datorer** och välj den virtuella dator som du vill skydda med Azure Sentinel. Information om hur du skapar en virtuell dator på Azure Stack finns i [Skapa en virtuell Windows-server med Azure Stack-portalen](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) eller [Skapa en virtuell Linux-server med hjälp av Azure Stack-portalen](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Välj **Tillägg**. Listan över tillägg för virtuella datorer som är installerade på den här virtuella datorn visas.
4. Klicka på fliken **Lägg till.** Menybladet **Ny resurs** öppnas och visar listan över tillgängliga tillägg för virtuella datorer. 
5. Välj tillägget **Azure Monitor, Update och Configuration Management** och klicka på **Skapa**. Konfigurationsfönstret **för installationstillägg** öppnas.

   ![Inställningar för Azure Monitor, Update och Configuration Management](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Om du inte ser tillägget **Azure Monitor, Update och Configuration Management** som anges på din marknadsplats kontaktar du din Azure Stack-operatör för att göra den tillgänglig.

6. På Azure Sentinel-menyn väljer du **Inställningar för arbetsyta** följt av **Avancerat**och kopierar **arbetsyte-ID** och **Arbetsytenyckeln (primärnyckel)**. 
1. I **tilläggsfönstret** För Azure Stack Install klistrar du in dem i de angivna fälten och klickar på **OK**.
1. När tilläggsinstallationen är klar visas dess status när **etableringen lyckades**. Det kan ta upp till en timme för den virtuella datorn att visas i Azure Sentinel-portalen.

Mer information om hur du installerar och konfigurerar agenten för Windows finns i [Ansluta Windows-datorer](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Felsökning av agentproblem för Linux finns i [Felsöka Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

I Azure Sentinel-portalen på Azure, under **Virtuella datorer,** har du en översikt över alla virtuella datorer och datorer tillsammans med deras status. 

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs kan du ta bort tillägget från den virtuella datorn via Azure Stack-portalen.

Så här tar du bort tillägget:

1. Öppna **Azure Stack Portal**.
2. Gå till sidan **Virtuella datorer** och välj den virtuella dator som du vill ta bort tillägget från.
3. Välj **Tillägg**väljer du tillägget **Microsoft.EnterpriseCloud.Monitoring**.
4. Klicka på **Avinstallera**och bekräfta ditt val.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- Strömma data från [vanliga felformatsinstallationer](connect-common-event-format.md) till Azure Sentinel.
