---
title: "Felsökning av vanliga problem vid skapandet av VHD | Microsoft Docs"
description: "Svar på vanliga felsökning frågor och problem under skapande av virtuell Hårddisk."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c4e88a9fbb15dd90d619b159ae1065dfacc1907f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Felsökning av vanliga problem som påträffas under skapande av virtuell Hårddisk
Den här artikeln för att hjälpa en Azure Marketplace utgivaren och/eller medadministratör som kan ha ett problem eller har frågor när du publicerar eller hantera sina virtuella solution(s).

1. Hur ändrar namnet på värddatorn?
   
    När du har skapat VM kan inte användare uppdatera namnet på värden.
2. Så här återställer du tjänsten Remote Desktop eller dess inloggningslösenord?
   
   * [Referens för Windows VM](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Referens för Linux VM](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Hur du skapar nya ssh certifikat?
   
   Mer information finns på länken: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Så här konfigurerar du en öppen VPN-certifikat?
   
   Mer information finns på länken: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Vad är Supportpolicy för att köra Microsoft server-program i virtuella Microsoft Azure-miljön (infrastructure-as-a-service)?
   
   Mer information finns på länken: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Har ingen unik identifierare i virtuella datorer?
   
   Azure kodar Azure VM unika ID på varje virtuell dator. Mer information finns i den här bloggen och dokumentationen.
7. På en virtuell dator hur kan jag hantera tillägg för anpassat skript i Start-uppgiften?
   
   Mer information finns på länken: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Så här skapar du en virtuell dator från Azure-portalen med den virtuella Hårddisken som har överförts till premium-lagring?
   
   Vi stöder inte den här funktionen ännu.
9. Är en 32-bitars-app som stöds i Azure Marketplace?
   
   Hittar du på länken för information om stöd för principen: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. När jag försöker skapa en avbildning från min virtuella hårddiskar, visas felmeddelandet ”. Virtuell Hårddisk är redan registrerad med avbildningslagringsplats som resursen ”i PowerShell. Skapade inte någon bild innan eller hittade I en bild med det här namnet i Azure. Hur lösa problemet?
    
    Vanligtvis inträffa om användaren etableras en virtuell dator från den här virtuella Hårddisken och det finns ett lås på den virtuella Hårddisken. Kontrollera att det finns ingen VM allokerats från den här virtuella Hårddisken. Om felet kvarstår fortfarande sedan du höja ett supportärende med hjälp av den här länken eller från publicering portal rörande detta (information ges i svaret för frågan 11).