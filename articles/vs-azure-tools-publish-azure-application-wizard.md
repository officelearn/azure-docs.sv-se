---
title: Med Visual Studio Publiceringsguiden för Azure-program | Microsoft Docs
description: Lär dig att konfigurera olika inställningar i Visual Studio publicera Azure guiden program
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 02d38f49a1bfe490acbcfee95a8a703cf0d7594f
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "31795987"
---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>Med Visual Studio Publiceringsguiden för Azure-program

När du utvecklar ett program i Visual Studio, kan du publicera programmet till en Azure-molntjänst med hjälp av den **publicera Azure-programmet** guiden.

> [!Note]
> Den här artikeln handlar om hur du distribuerar till molntjänster, inte till webbplatser. Information om hur du distribuerar till webbplatser finns [hur du distribuerar en Azure-webbplats](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false).

## <a name="accessing-the-publish-azure-application-wizard"></a>Åtkomst till guiden Publicera Azure-program

Du kan öppna guiden Publicera Azure-program på två sätt beroende på vilken typ av Visual Studio-projekt som du har.

**Om du har en Azure cloud service-projekt:**

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn, Välj **publicera**.

**Om du har ett webbprojekt för program som inte är aktiverad för Azure:**

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn, Välj **konvertera** > **konvertera till Azure Molntjänstprojekt**. 

1. I **Solution Explorer**, högerklicka på det nyligen skapade Azure-projektet och, från snabbmenyn väljer **publicera**.

## <a name="sign-in-page"></a>Inloggningssidan

![Inloggningssidan](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**Kontot** – Välj ett konto eller välj **Lägg till ett konto** i listrutan för kontot.

**Välj din prenumeration** -Välj prenumeration för din distribution.

## <a name="settings-page---common-settings-tab"></a>Inställningssidan - gemensamma inställningar

![Vanliga inställningar](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**Molntjänsten** -med hjälp av listrutan, Välj antingen en befintlig molnet tjänsten eller välj  **&lt;Skapa nytt >**, och skapa en tjänst i molnet. Datacenter visas inom parentes för varje tjänst i molnet. Du rekommenderas att data center platsen för Molntjänsten vara samma som data center platsen för lagringskontot (avancerade inställningar).

**Miljö** -väljer du antingen **produktion** eller **mellanlagring**. Välj mellanlagringsmiljön om du vill distribuera programmet i en testmiljö. 

**Skapa configuration** -väljer du antingen **felsöka** eller **versionen**.

**Tjänstkonfigurationen** -väljer du antingen **moln** eller **lokala**.

**Aktivera Fjärrskrivbord för alla roller** – Välj det här alternativet om du vill kunna fjärransluta till tjänsten. Det här alternativet används främst för felsökning. Mer information finns i [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services med Visual Studio](cloud-services/cloud-services-role-enable-remote-desktop-visual-studio.md).

**Aktivera webbdistribution för alla webbprogram roller** – Välj det här alternativet om du vill aktivera web distributionen för tjänsten. Du måste också välja den **aktivera Fjärrskrivbord för alla roller** alternativet att använda den här funktionen. Mer information finns i [publicering av en tjänst i molnet med hjälp av Visual Studio](vs-azure-tools-publishing-a-cloud-service.md).

## <a name="settings-page---advanced-settings-tab"></a>Inställningssidan - inställningar fliken Avancerat

![Avancerade inställningar](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**Distributionsetiketten** – Använd standardnamnet eller ange ett namn du väljer. Lägger till datumet i distributionsetiketten lämnar du kryssrutan är markerad. 

**Lagringskontot** -Välj lagringskonto för den här distributionen **&lt;Skapa nytt > Skapa ett lagringskonto. Datacenter visas inom parentes för varje storage-konto. Du rekommenderas att data center platsen för lagringskontot är densamma som data center platsen för Molntjänsten (vanliga inställningar).

Azure storage-konto lagras paketet för programdistributionen. När programmet distribueras tas paketet bort från lagringskontot.

**Ta bort distributionen på fel** – Välj det här alternativet för att distributionen tas bort om fel uppstår vid publicering. Ska den vara avmarkerad om du vill behålla en konstant virtuella IP-adressen för din molntjänst.

**Uppdatering av programdistribution** – Välj det här alternativet om du vill distribuera endast uppdaterade komponenter. Den här typen av distribution kan vara snabbare än en fullständig distribution. Detta ska kontrolleras om du vill behålla en konstant virtuella IP-adressen för din molntjänst. 

**Uppdatering av programdistribution - inställningar** -den här dialogrutan används för att specificera hur du vill att rollerna som ska uppdateras. Om du väljer **stegvis uppdatering**, varje instans av programmet uppdateras efter varandra, så att programmet alltid är tillgängligt. Om du väljer **samtidig uppdatering**, alla instanser av programmet uppdateras samtidigt. Samtidig uppdatering är snabbare, men tjänsten kanske inte är tillgängliga under uppdateringen.

![Inställningar för distribution](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**Aktivera IntelliTrace** -ange om du vill aktivera IntelliTrace. Med IntelliTrace, kan du logga omfattande felsökningsinformation för en rollinstans när den körs i Azure. Om du behöver ta reda på orsaken till ett problem kan du använda IntelliTrace-loggar för att stega igenom koden från Visual Studio som om den kördes i Azure. Läs mer om hur du använder IntelliTrace [felsökning en publicerade Azure cloud service med Visual Studio och IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md).

**Aktivera profilering** -ange om du vill aktivera prestanda profilering. Visual Studio-profiler kan du få en detaljerad analys av beräkningar aspekter av hur Molntjänsten körs. Mer information om hur du använder Visual Studio-profiler finns [testa prestanda för en Azure-molntjänst](./vs-azure-tools-performance-profiling-cloud-services.md).

**Aktivera Remote felsökare för alla roller** -ange om du vill aktivera fjärrfelsökning. Mer information om hur du felsöker molntjänster med hjälp av Visual Studio finns [felsökning en Azure-molntjänst eller en virtuell dator i Visual Studio](./vs-azure-tools-debug-cloud-services-virtual-machines.md).

## <a name="diagnostics-settings-page"></a>Sidan Inställningar för diagnostik

![Diagnostikinställningar](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

Diagnostiken kan du felsöka en Azure-molntjänst (eller virtuella Azure-datorn). Information om diagnostik finns [konfigurera diagnostik för virtuella datorer och Azure Cloud Services](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). Information om Application Insights finns [vad är Application Insights?](./application-insights/app-insights-overview.md).

## <a name="summary-page"></a>Sammanfattningssida

![Sammanfattning](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**Rikta profil** -du kan välja att skapa en publiceringsprofil från inställningarna som du har valt. Du kan till exempel skapa en profil för en testmiljö och en annan för produktion. Om du vill spara den här profilen, Välj den **spara** ikon. Guiden skapar profilen och sparar den i Visual Studio-projekt. Om du vill ändra namnet på profilen, öppna den **mål profil** listan och väljer sedan  **&lt;hantera... &gt;**.

   > [!Note]
   > Publiceringsprofilen som visas i Solution Explorer i Visual Studio och profilinställningarna skrivs till en fil med filnamnstillägget .azurePubxml. Inställningarna sparas som attribut till XML-taggar.

## <a name="publishing-your-application"></a>Publicering av programmet

När du konfigurerar alla inställningar för ditt projekt distribution, välja **publicera** längst ned i dialogrutan. Du kan övervaka statusen för processen i den **utdata** fönstret i Visual Studio.

## <a name="next-steps"></a>Nästa steg

- [Migrera och publicera ett program till en Azure-molntjänst från Visual Studio](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)

- [Lär dig använda Visual Studio för att publicera ett Azure-molntjänst](./vs-azure-tools-publishing-a-cloud-service.md)

- [Felsökning av en publicerad Azure cloud service med Visual Studio och IntelliTrace](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)

- [Testa prestanda för en Azure-molntjänst](./vs-azure-tools-performance-profiling-cloud-services.md)

- [Konfigurera diagnostik för Azure-molntjänster och virtuella datorer](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

- [Vad är Application Insights?](./application-insights/app-insights-overview.md)