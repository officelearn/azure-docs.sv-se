---
title: Snabb start – konfigurera Azure Analysis Services server-brandvägg | Microsoft Docs
description: Den här snabb starten hjälper dig att konfigurera en brand vägg för en Azure Analysis Services-server med hjälp av Azure Portal.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 48618815519fad31bff5d6a8d2d2edc82535f437
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697895"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Snabbstart: Konfigurera serverbrandvägg – portalen

Den här snabbstarten hjälper dig att konfigurera en brandvägg för Azure Analysis Services-servern. Att kunna aktivera en brandvägg och konfigurera IP-adressintervall enbart för de datorer som har åtkomst till servern är en viktig del i skyddet av din server och dina data.

## <a name="prerequisites"></a>Krav

- En Analysis Services-server i din prenumeration. Mer information finns i [Snabbstart: Skapa en server – Portal](analysis-services-create-server.md) eller [Snabbstart: Skapa en server – PowerShell](analysis-services-create-powershell.md)
- Ett eller flera IP-adressintervall för klientdatorer (om så behövs).
- Vissa scenarier där Power BI Premium ansluter till Azure Analysis Services, inklusive data import (uppdatering) och sid brytnings rapporter, stöds för närvarande inte även om Tillåt åtkomst från Power BI har Aktiver ATS. Det vanligaste scenariot med att använda Live Connect från Power BI Premium stöds. Alla Power BI Pros scenarier stöds.


## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal 

[Logga in på portalen](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurera en brandvägg

1. Öppna sidan Översikt genom att klicka på servern. 
2. I **Inställningar**  >  **brand vägg**  >  **Aktivera brand vägg**, klickar du på **på**.
3. Klicka på **På** i **Tillåt åtkomst från Power BI** om du vill ge DirectQuery åtkomst från Power BI-tjänsten.  
4. (Valfritt) Ange ett eller flera IP-adressintervall. Ange ett namn, och start- och slut-IP-adress för varje intervall. Brand Väggs regelns namn får inte vara längre än 128 tecken och får bara innehålla versaler, gemener, siffror, under streck och bindestreck. Blank steg och andra specialtecken är inte tillåtna.
5. Klicka på **Spara**.

     ![Brandväggsinställningar](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort IP-adressintervallen eller inaktivera brandväggen när behovet inte längre finns.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten du har lärt dig hur du konfigurerar en brandvägg för servern. Nu när du har en server och har skyddat den med en brandvägg kan du lägga till en grundläggande exempeldatamodell till den från portalen. Att använda en exempelmodell är en bra idé om du vill lära dig mer om hur man konfigurerar modelldatabasroller och testar klientanslutningar. Fortsätt till och lägg till en exempelmodell om du vill lära dig mer.

> [!div class="nextstepaction"]
> [Självstudier: Lägg till en exempelmodell till servern](analysis-services-create-sample-model.md)
