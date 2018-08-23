---
title: Med Visual Studio Publiceringsguiden för Azure-program | Microsoft Docs
description: Lär dig att konfigurera olika inställningar i Visual Studio Azure Application Publiceringsguiden
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 97d78bd61f7cf2a651fea1ac29e2a952a8f8ced3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057008"
---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>Med Visual Studio Publiceringsguiden för Azure-program

När du utvecklar ett webbprogram i Visual Studio kan du publicera programmet till en Azure-molntjänst med hjälp av den **publicera Azure-program** guiden.

> [!Note]
> Den här artikeln handlar om att distribuera till molntjänster, inte till webbplatser. Information om hur du distribuerar till webbplatser finns i [hur du distribuerar en Azure Web Site](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="accessing-the-publish-azure-application-wizard"></a>Åtkomst till guiden Publicera Azure-program

Du kan komma åt guiden Publicera Azure-program på två sätt beroende på vilken typ av Visual Studio-projekt som du har.

**Om du har ett Azure cloud service-projekt:**

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn väljer **publicera**.

**Om du har ett webbprogramsprojekt som inte är aktiverad för Azure:**

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn väljer **konvertera** > **konvertera till Azure-Molntjänstprojekt**. 

1. I **Solution Explorer**och högerklicka på det nyligen skapade Azure-projektet, på snabbmenyn väljer **publicera**.

## <a name="sign-in-page"></a>På inloggningssidan

![På inloggningssidan](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**Kontot** – Välj ett konto eller välj **Lägg till ett konto** i listrutan för kontot.

**Välj din prenumeration** – Välj prenumerationen som ska användas för distributionen.

## <a name="settings-page---common-settings-tab"></a>Inställningssidan - fliken vanliga inställningar

![Vanliga inställningar](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**Molntjänst** -med hjälp av listrutan, väljer du antingen ett befintligt moln tjänst eller välj  **&lt;Skapa nytt >**, och skapa en tjänst i molnet. Datacentret visas inom parentes för varje tjänst i molnet. Du rekommenderas att data datacenterplats för Molntjänsten vara samma som datacenterplats för storage-konto (avancerade inställningar).

**Miljö** – Välj antingen **produktion** eller **mellanlagring**. Välj mellanlagringsmiljön om du vill distribuera programmet i en testmiljö. 

**Konfigurace buildu** – Välj antingen **felsöka** eller **versionen**.

**Tjänstkonfigurationen** – Välj antingen **molnet** eller **lokala**.

**Aktivera Fjärrskrivbord för alla roller** – Välj det här alternativet om du vill kunna fjärransluta till tjänsten. Det här alternativet används främst för felsökning. Mer information finns i [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med hjälp av Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

**Aktivera webbdistribution för alla webbroller** – Välj det här alternativet för att aktivera webbdistribution för tjänsten. Du måste också välja den **aktivera Fjärrskrivbord för alla roller** alternativet att använda den här funktionen. Mer information finns i [publicerar en molntjänst med Visual Studio](vs-azure-tools-publishing-a-cloud-service.md).

## <a name="settings-page---advanced-settings-tab"></a>Sidan för inställningar - fliken Inställningar under Avancerat

![Avancerade inställningar](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**Distributionsetiketten** – Använd standardnamnet eller ange ett namn du väljer. Om du vill lägga till det datum då distributionsetiketten, lämnar du kryssrutan markerad. 

**Storage-konto** – Välj lagringskontot som ska användas för den här distributionen **&lt;Skapa nytt > att skapa ett lagringskonto. Datacentret visas inom parentes för varje lagringskonto. Du rekommenderas att datacenterplats för lagringskontot är samma som datacenterplats för Molntjänsten (vanliga inställningar).

Azure storage-kontot lagrar paketet för programdistributionen. När programmet distribueras tas paketet bort från lagringskontot.

**Ta bort distributionen vid misslyckande** – Välj det här alternativet för att distributionen tas bort om det uppstår några fel vid publicering. Om du vill upprätthålla en konstant virtuella IP-adress för din molntjänst bör detta vara avmarkerad.

**Uppdatering av programdistribution** – Välj det här alternativet om du vill distribuera endast uppdaterade komponenter. Den här typen av distribution kan vara snabbare än en fullständig distribution. Alternativet ska vara markerat om du vill upprätthålla en konstant virtuella IP-adress för din molntjänst. 

**Uppdatering av programdistribution - inställningar** – den här dialogrutan används för att specificera hur du vill att rollerna som ska uppdateras. Om du väljer **inkrementell uppdatering**, varje instans av ditt program har uppdaterats efter varandra, så att programmet alltid är tillgänglig. Om du väljer **samtidig uppdatering**, alla instanser av programmet uppdateras samtidigt. Samtidig uppdatering är snabbare, men tjänsten kanske inte är tillgängliga under uppdateringen.

![Distributionsinställningar](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**Aktivera IntelliTrace** – ange om du vill aktivera IntelliTrace. Med IntelliTrace, kan du logga omfattande felsökningsinformation för en rollinstans när den körs i Azure. Om du vill ta reda på orsaken ett problem kan använda du IntelliTrace-loggar för att gå igenom koden från Visual Studio som om den kördes i Azure. Läs mer om hur du använder IntelliTrace [felsökning en publicerade Azure-molntjänst med Visual Studio och IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md).

**Aktivera profilering** – ange om du vill aktivera prestanda-profilering. Visual Studio profiler kan du få en detaljerad analys av databaserad aspekter av hur din molntjänst körs. Mer information om hur du använder Visual Studio-profiler finns i [testa prestanda hos en Azure-molntjänst](./vs-azure-tools-performance-profiling-cloud-services.md).

**Aktivera Fjärrfelsökningsprogrammet för alla roller** – ange om du vill aktivera fjärrfelsökning. Mer information om hur du felsöker molntjänster med Visual Studio finns i [felsökning av en Azure-molntjänst eller virtuell dator i Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md).

## <a name="diagnostics-settings-page"></a>Inställningssidan för diagnostik

![Diagnostikinställningar](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

Diagnostik kan du felsöka en Azure-molntjänst (eller Azure-dator). Läs om hur diagnostik [konfigurera diagnostik för Azure Cloud Services och virtuella datorer](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Information om Application Insights finns i [vad är Application Insights?](./application-insights/app-insights-overview.md).

## <a name="summary-page"></a>Sammanfattningssida

![Sammanfattning](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**Målprofilen** – du kan välja att skapa en publiceringsprofil från inställningarna som du har valt. Du kan till exempel skapa en profil för en testmiljö och ett annat för produktion. Om du vill spara den här profilen, Välj den **spara** ikon. Guiden skapar profilen och sparar den i Visual Studio-projektet. Om du vill ändra namnet på profilen, öppna den **målprofilen** och välj sedan  **&lt;hantera... &gt;**.

   > [!Note]
   > Publiceringsprofilen som visas i Solution Explorer i Visual Studio och profilinställningarna skrivs till en fil med filnamnstillägget .azurePubxml. Inställningarna har sparats som attribut till XML-taggar.

## <a name="publishing-your-application"></a>Publicering av programmet

När du konfigurerar alla inställningar för distribution av ditt projekt väljer **publicera** längst ned i dialogrutan. Du kan övervaka statusen för processen i den **utdata** fönstret i Visual Studio.

## <a name="next-steps"></a>Nästa steg

- [Migrera och publicera en Webbapp till en Azure-molntjänst från Visual Studio](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)

- [Lär dig hur du använder Visual Studio för att publicera en Azure-molntjänst](./vs-azure-tools-publishing-a-cloud-service.md)

- [Felsöka en publicerad Azure-molntjänst med Visual Studio och IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)

- [Testa prestanda hos en Azure-molntjänst](./vs-azure-tools-performance-profiling-cloud-services.md)

- [Konfigurera diagnostik för Azure-molntjänster och virtuella datorer](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

- [Vad är Application Insights?](./application-insights/app-insights-overview.md)