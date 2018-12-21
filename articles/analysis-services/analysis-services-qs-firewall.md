---
title: Snabbstart – Konfigurera en brandvägg för en Analysis Services-server i Azure | Microsoft Docs
description: Lär dig hur du konfigurerar en brandvägg för en Analysis Services-serverinstans i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0351ab3213ac75559cc025f2ef71e2f098a7504c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993188"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Snabbstart: Konfigurera serverbrandväggen – portalen

Den här snabbstarten hjälper dig att konfigurera en brandvägg för Azure Analysis Services-servern. Att kunna aktivera en brandvägg och konfigurera IP-adressintervall enbart för de datorer som har åtkomst till servern är en viktig del i skyddet av din server och dina data.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Analysis Services-server i din prenumeration. Mer information finns i [Snabbstart: Skapa en server – portalen](analysis-services-create-server.md) eller [Snabbstart: Skapa en server – PowerShell](analysis-services-create-powershell.md)
- Ett eller flera IP-adressintervall för klientdatorer (om så behövs).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal 

[Logga in på portalen](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurera en brandvägg

1. Öppna sidan Översikt genom att klicka på servern. 
2. Klicka på **På** i **INSTÄLLNINGAR** > **Brandvägg** > **Aktivera brandvägg**.
3. Klicka på **På** i **Tillåt åtkomst från Power BI** om du vill ge DirectQuery åtkomst från Power BI-tjänsten.  
4. (Valfritt) Ange ett eller flera IP-adressintervall. Ange ett namn, och start- och slut-IP-adress för varje intervall. 
5. Klicka på **Spara**.

     ![Brandväggsinställningar](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort IP-adressintervallen eller inaktivera brandväggen när behovet inte längre finns.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten du har lärt dig hur du konfigurerar en brandvägg för servern. Nu när du har en server och har skyddat den med en brandvägg kan du lägga till en grundläggande exempeldatamodell till den från portalen. Att använda en exempelmodell är en bra idé om du vill lära dig mer om hur man konfigurerar modelldatabasroller och testar klientanslutningar. Fortsätt till och lägg till en exempelmodell om du vill lära dig mer.

> [!div class="nextstepaction"]
> [Självstudier: Lägg till en exempelmodell på servern](analysis-services-create-sample-model.md)
