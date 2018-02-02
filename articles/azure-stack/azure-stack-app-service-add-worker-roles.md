---
title: "Skala ut arbetsroller i Apptjänster - Azure Stack | Microsoft Docs"
description: "Detaljerade riktlinjer för att skala Azure Stack Apptjänster"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: a9be9011062f07d59842d417bf6761ec81c39275
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>Apptjänst Azure stacken: lägga till fler infrastruktur eller worker-roller
*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*  

Det här dokumentet innehåller instruktioner om hur du skalar Apptjänst på Azure-stacken infrastruktur- och arbetsroller roller. Den innehåller anvisningar för att skapa ytterligare arbetsroller för program i alla storlekar.

> [!NOTE]
> Om miljön i Azure-stacken inte har fler än 96 GB RAM-minne kan det vara svårt att lägga till ytterligare kapacitet.

Apptjänst på Azure-stacken, som standard stöder lediga och delade worker nivåer. Om du vill lägga till andra worker-nivåer, som du behöver lägga till flera arbetsroller.

Om du inte är säker på vad som har distribuerats med App Service på Azure-stacken installation kan du granska ytterligare information i den [Apptjänst på Azure-stacken översikt](azure-stack-app-service-overview.md).

Azure Apptjänst Azure stacken distribuerar alla roller med hjälp av virtuella datorer och drar därmed nytta av funktioner för skalning av arbetsbelastningen. Därför sker alla skalning av worker-nivåer via App Service administratören.

Lägg till ytterligare arbetare direkt i App Service-administratören Resource Provider.

1. Logga in på Azure Stack-administrationsportalen som tjänstadministratör.

2. Bläddra till **Apptjänster**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klicka på **roller**. Här kan du se uppdelning av alla roller för App Service som distribueras.

4. Högerklicka på raden i vilken du vill skala och klicka sedan på **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klicka på **skalning**, Välj antalet instanser som du vill skala till och klicka sedan på **spara**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Apptjänst Azure stacken ska nu lägga till ytterligare virtuella datorer, konfigurera dem, installera nödvändig programvara och markeras som redo när processen har slutförts. Den här processen kan ta ungefär 80 minuter.

7. Du kan övervaka förloppet för av nya roller genom att visa arbetare i den **roller** bladet.

Arbetare blir tillgängliga för användare att distribuera arbetsbelastningen till dem när de är helt distribuerade och redo. Nedan visas ett exempel på tillgängliga flera prisnivåer som standard. Om det finns inga tillgängliga arbetare för en viss worker-nivå, är möjlighet att välja motsvarande prisnivån inte tillgänglig.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Om du vill skala ut Management lägga klientdelen eller utgivare roller till utskalningsdistribution motsvarande skaluppsättning för virtuell dator. Vi lägger till möjligheten att skala ut rollerna via App Service-hantering i en framtida version.

Om du vill skala ut Management, klientdelen eller utgivare roller följer du samma steg att välja rätt roll. Domänkontrollanter distribueras inte som Skaluppsättningar och därför två bör distribueras vid installationen för alla Produktionsdistribution.

### <a name="next-steps"></a>Nästa steg

[Konfigurera distributionskällor](azure-stack-app-service-configure-deployment-sources.md)
