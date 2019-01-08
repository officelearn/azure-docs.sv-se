---
title: Så här felsöker du vanliga problem när VHD skapas | Microsoft Docs
description: Svar på vanliga felsökningsfrågor och problem när VHD skapas.
services: Azure Marketplace
documentationcenter: ''
author: v-miclar
manager: ''
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: e58a5f8901b2ab53d47095e1d5fd232783e3b1ba
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079144"
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Så här felsöker du vanliga problem som uppstod vid skapande av virtuell Hårddisk
Den här artikeln är för att hjälpa en Azure Marketplace-utgivare och/eller en medadministratör som kan uppstår ett problem eller har frågor när du publicerar eller hantera deras VM-lösningar.

1. Hur kan jag ändra namnet på värden?
   
    När datorn har skapats kan kan inte användare uppdatera namnet på värden.
2. Så här återställer du Fjärrskrivbordstjänsten eller dess lösenord för inloggning?
   
   * [Referens för Windows VM](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Referens för virtuell Linux-dator](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Hur du skapar nya ssh certifikat?
   
   Se länken: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Så här konfigurerar du en öppen VPN-certifikatet?
   
   Se länken: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Vad är Supportpolicy för att köra serverprogramvara från Microsoft i Microsoft Azure VM-miljön (infrastructure-as-a-service)?
   
   Se länken: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. Har ingen unik identifierare i virtuella datorer?
   
   Azure kodar Azure VM unikt ID i varje virtuell dator. Mer information finns i den här bloggen och dokumentation.
7. I en virtuell dator Hur hanterar jag det anpassa skripttillägget startåtgärden?
   
   Se länken: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Hur du skapar en virtuell dator från Azure-portalen med den virtuella Hårddisken som har överförts till premium storage?
   
   Vi stöder inte den här funktionen ännu.
9. Stöds en 32-bitars-app i Azure Marketplace?
   
   Se länken Mer information om support-principen: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Varje gång jag försöker skapa en avbildning från min virtuella hårddiskar, visas felmeddelandet ”. Virtuell Hårddisk har redan registrerats med avbildningslagringsplats som resursen ”i PowerShell. Jag har skapat inte en avbildning innan eller var det jag hittade bilder med det här namnet i Azure. Hur löser jag det här problemet?
    
    Det här problemet uppstår vanligen om användaren etablerade en virtuell dator från den här virtuella Hårddisken och det finns ett lås på den virtuella Hårddisken. Kontrollera att det finns inga VM som har allokerats från den här virtuella Hårddisken. Om felet kvarstår lyft ett supportärende med den här länken eller från publiceringen portal (information ges i svaret på frågan 11).