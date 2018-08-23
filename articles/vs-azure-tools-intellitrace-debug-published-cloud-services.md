---
title: Felsöka en publicerad en Azure-molntjänst med Visual Studio och IntelliTrace | Microsoft Docs
description: Lär dig att felsöka en molntjänst med Visual Studio och IntelliTrace
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: 1deec539d3dfe65a2ac02a9f4cd2b94d9a78274a
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444350"
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>Felsöka en publicerad Azure-molntjänst med Visual Studio och IntelliTrace
Med IntelliTrace, kan du logga omfattande felsökningsinformation för en rollinstans när den körs i Azure. Om du vill ta reda på orsaken ett problem kan använda du IntelliTrace-loggar för att gå igenom koden från Visual Studio som om den kördes i Azure. I praktiken nyckel IntelliTrace poster för fjärrkörning av kod och miljödata när ditt Azure-program körs som en molntjänst i Azure och du kan upprepa inspelade data från Visual Studio. 

Du kan använda IntelliTrace om du har Visual Studio Enterprise installerad och din Azure-program mål .NET Framework 4 eller senare. IntelliTrace samlar in information för din Azure-roller. Virtuella datorer för rollerna alltid köra 64-bitars operativsystem.

Alternativt kan du använda [fjärrfelsökning](http://go.microsoft.com/fwlink/p/?LinkId=623041) att ansluta direkt till en molnbaserad tjänst som körs i Azure.

> [!IMPORTANT]
> IntelliTrace är avsedd för debug-scenarier och ska inte användas för en Produktionsdistribution.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Konfigurera ett Azure-program för IntelliTrace
Om du vill aktivera IntelliTrace för ett Azure-program, måste du skapa och publicera programmet från ett Visual Studio Azure-projekt. Du måste konfigurera IntelliTrace för ditt Azure-program innan du publicerar den till Azure. Om du publicerar ditt program utan att konfigurera IntelliTrace måste du publicera projektet. Mer information finns i [publicering av ett Azure cloud services-projekt med Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. När du är redo att distribuera dina Azure-program, kontrollera att dina projekt build mål är inställda på **felsöka**.

1. I **Solution Explorer**och högerklicka på projektet, Välj snabbmenyn **publicera**.
   
1. I den **publicera Azure-program** dialogrutan, Välj den Azure-prenumerationen och välj **nästa**.

1. I den **inställningar** väljer den **avancerade inställningar** fliken.

1. Aktivera den **aktivera IntelliTrace** alternativet för att samla in IntelliTrace-loggar för ditt program när den publiceras i molnet.
   
1. För att anpassa den grundläggande IntelliTrace-konfigurationen, Välj **inställningar** bredvid **aktivera IntelliTrace**.

    ![IntelliTrace-inställningslänken](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. I den **IntelliTrace inställningar** dialogrutan kan du ange vilka händelser som ska log, om du vill samla in information om anrop, vilka moduler och processer för att samla in loggar för och hur mycket utrymme för att allokera till inspelningen. Läs mer om IntelliTrace [felsökning med IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![IntelliTrace-inställningar](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

IntelliTrace-loggen är en cirkulär loggfil med den maximala storleken som anges i inställningarna för IntelliTrace (standardstorleken är 250 MB). IntelliTrace-loggar samlas in till en fil i filsystemet för den virtuella datorn. När du begär loggarna är en ögonblicksbild vidtas vid den tidpunkten i tid och laddas ned till den lokala datorn.

När Azure-Molntjänsten har publicerats till Azure kan du bestämma om IntelliTrace har aktiverats från Azure-nod i **Server Explorer**, enligt följande bild:

![Server Explorer - IntelliTrace-aktiverad](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Hämta IntelliTrace-loggar för en rollinstans
Med Visual Studio kan hämta du IntelliTrace-loggar för en rollinstans genom att följa dessa steg:

1. I **Server Explorer**, expandera den **molntjänster** nod, och leta upp rollinstans vars loggar som du vill hämta. 

1. Högerklicka på rollinstansen och välj snabbmenyn s **visa IntelliTrace-loggar**. 

    ![Visa menyalternativet för IntelliTrace-loggar](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. IntelliTrace-loggar laddas ned till en fil i en katalog på din lokala dator. Varje gång du begär IntelliTrace-loggar, skapas en ny ögonblicksbild. Medan loggarna hämtas, Visual Studio visar förloppet för åtgärden i den **Azure-aktivitetsloggen** fönster. I följande bild visas kan du expandera radobjekt för åtgärden att se mer information.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Du kan fortsätta att arbeta i Visual Studio medan hämtar IntelliTrace-loggar. När loggen har hämtats, öppnas den i Visual Studio.

> [!NOTE]
> IntelliTrace-loggar kan innehålla undantag som ramverket genererar och hanterar efteråt. Internt framework kod genereras undantagen som för att starta en roll, så du kan ignorera dem.
> 
> 

## <a name="next-steps"></a>Nästa steg
- [Alternativ för att distribuera Azure cloud services](vs-azure-tools-debugging-cloud-services-overview.md)
- [Publicera en Azure-molntjänst med Visual Studio](vs-azure-tools-publishing-a-cloud-service.md)