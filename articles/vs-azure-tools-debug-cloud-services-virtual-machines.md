---
title: "Felsökning av en Azure-molntjänst eller en virtuell dator i Visual Studio | Microsoft Docs"
description: "Felsökning av en molnbaserad tjänst eller en virtuell dator i Visual Studio"
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: ghogen
editor: 
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: a303e080bc847daf023eed2e9ba1ffc31e340160
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Felsökning av en Azure-molntjänst eller en virtuell dator i Visual Studio
Visual Studio finns olika alternativ för felsökning av Azure-molntjänster och virtuella datorer.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>Felsöka din molntjänst på den lokala datorn
Du kan spara tid och pengar med hjälp av Azure compute emulator felsöka din molntjänst på en lokal dator. Du kan felsöka en tjänst lokalt innan du distribuerar det för att förbättra pålitligheten och prestandan utan att betala för beräkning tid. Men vissa kan det uppstå fel bara när du kör en molnbaserad tjänst i Azure sig själv. Du kan felsöka dessa fel om du aktiverar fjärrfelsökning när du publicerar din tjänst och sedan koppla felsökaren till en rollinstans.

Emulatorn simulerar Azure Compute-tjänsten och körs i din lokala miljö så att du kan testa och felsöka din molntjänst innan du distribuerar den. Emulatorn hanterar livscykeln för dina rollinstanser och ger åtkomst till simulerade resurser, till exempel lokal lagring. När du felsöker eller köra tjänsten från Visual Studio startar automatiskt emulatorn som en bakgrundsprogrammet och sedan distribuerar tjänsten till emulatorn. Du kan använda emulatorn för att visa din tjänst när den körs i den lokala miljön. Du kan köra en fullständig version eller emulatorns uttrycklig version. (Från och med Azure 2.3 emulatorns uttrycklig version kunna är standard.) Se [med hjälp av emulatorn att köra och felsöka en tjänst i molnet lokalt](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Felsöka din molntjänst på den lokala datorn
1. På menyraden, väljer du **felsöka**, **Start Debugging** köra projektet Azure cloud service. Alternativt kan du trycka på F5. Du ser ett meddelande som Compute Emulator startas. När emulatorn startar bekräftar den ikonen i systemfältet.

    ![Azure-emulatorn i systemfältet](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)
2. Visa användargränssnittet för beräkningsemulatorn genom att öppna snabbmenyn för Azure-ikonen i meddelandefältet och välj sedan **visa Compute Emulator UI**.

    Den vänstra rutan i Gränssnittet visas de tjänster som för närvarande har distribuerats till beräkningsemulatorn och rollinstanser som varje tjänst körs. Du kan välja tjänster eller roller för att visa livscykel, loggning och diagnostisk information i den högra rutan. Om du placerar fokus i den övre marginalen på ett fönster i ingår så det att den fyller den högra rutan.
3. Stega igenom programmet genom att välja kommandon på den **felsöka** -menyn och ange brytpunkter i koden. Eftersom du gå igenom program i felsökaren har fönstren uppdaterats med den aktuella statusen för programmet. När du stoppar felsökning tas programdistributionen bort. Om ditt program innehåller en webbroll och du har ställt in egenskapen startade åtgärden att starta webbläsaren, startar Visual Studio ditt webbprogram i webbläsaren. Om du ändrar antalet instanser av en roll i konfigurationen för tjänsten, måste du stoppa Molntjänsten och sedan starta om felsökningen om så att du kan felsöka de här nya instanser av rollen.

    **Obs:** när du stoppar körs eller felsöka din tjänst, lokala beräkningsemulatorn och storage-emulatorn inte stoppats. Du måste stoppa dem uttryckligen från meddelandefältet.

## <a name="debug-a-cloud-service-in-azure"></a>Felsöka en molnbaserad tjänst i Azure
Om du vill felsöka en molnbaserad tjänst från en fjärrdator måste du aktivera funktionen explicit när du distribuerar din molntjänst så att nödvändiga tjänster (till exempel msvsmon.exe) är installerade på de virtuella datorer som kör rollinstanser av. Om du inte aktiverar fjärrfelsökning när du publicerade tjänsten, måste du publicera om tjänsten med fjärrfelsökning aktiverad.

Om du aktiverar fjärrfelsökning för en tjänst i molnet, inte den minskad prestanda eller ytterligare avgifter. Du bör inte använda fjärrfelsökning för en tjänst för produktion, eftersom klienter som använder tjänsten kan påverkas negativt.

> [!NOTE]
> När du publicerar en molnbaserad tjänst från Visual Studio kan du aktivera **IntelliTrace** för alla roller i tjänsten som är riktade till .NET Framework 4 eller .NET Framework 4.5. Med hjälp av **IntelliTrace**, kan du granska händelser som inträffade i en rollinstans i förflutna och återskapa kontext än den tiden. Se [felsökning en publicerade molntjänst med IntelliTrace och Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) och [med IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).
>
>

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Aktivera fjärrfelsökning för en tjänst i molnet
1. Öppna snabbmenyn för Azure-projekt och välj sedan **publicera**.
2. Välj den **mellanlagring** miljö och **felsöka** konfiguration.

    Detta är endast en rekommendation. Du kan välja om du vill köra din testmiljöer i en produktionsmiljö. Du kan dock negativt påverka användare om du aktiverar fjärrfelsökning i produktionsmiljön. Du kan välja versionen konfiguration, men Debug-konfigurationen är felsökning.

    ![Välj Debug-konfiguration](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)
3. Gör vanligt, men väljer den **aktivera fjärråtkomst felsökare för alla roller** kryssrutan på den **avancerade inställningar** fliken.

    ![Felsöka konfiguration](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Att koppla felsökaren till en molnbaserad tjänst i Azure
1. I Server Explorer expanderar du noden för din molntjänst.
2. Öppna snabbmenyn för rollen eller rollinstans som du vill bifoga och välj sedan **koppla felsökare**.

    Om du felsöka en roll bifogar Visual Studio-felsökaren för varje instans av rollen. Felsökningsprogrammet bryts på en brytpunkt för den första rollinstansen som kör den kodraden och uppfyller eventuella villkor för att brytpunkt. Om du felsöka en instans, felsökare bifogar till endast instans och sidbrytningar på en brytpunkt när den specifika instansen körs den kodraden och uppfyller villkoren för den brytpunkt.

    ![Koppla felsökare](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)
3. När felsökningen kopplas till en instans, felsöka som vanligt. Felsökningsprogrammet bifogar automatiskt lämpliga värdprocessen för din roll. Beroende på vad rollen är, bifogar felsökningsprogrammet w3wp.exe, WaWorkerHost.exe eller WaIISHost.exe. Expandera noden instans i Server Explorer för att verifiera den process som felsökare. Se [Azure rollen arkitektur](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) mer information om Azure processer.

    ![Välj typ i dialogrutan kod](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
4. Öppna dialogrutan processer, på menyraden, välja Debug, Windows, processer för att identifiera de processer som felsökare. (Tangentbord: Ctrl + Alt + Z) Om du vill koppla från en specifik process, öppna dess snabbmenyn och välj sedan **koppla från processen**. Eller, hitta noden instans i Server Explorer, hitta processen, öppna dess snabbmenyn och välj sedan **koppla från processen**.

    ![Felsöka processer](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Undvika lång stopp vid brytpunkter när remote felsökning. Azure behandlar en process som har stoppats för längre än några minuter som inte svarar och stoppar skickar trafik till den instansen. Om du stoppar för länge lossa msvsmon.exe från processen.
>
>

Öppna snabbmenyn för rollen eller instans som du felsöker och välj sedan om du vill koppla från felsökning från alla processer i din instans eller rollen **frånkoppling felsökare**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Begränsningar för fjärrfelsökning i Azure
Från Azure SDK 2.3 har fjärrfelsökning följande begränsningar.

* Med fjärrfelsökning aktiverad, kan du publicera en molnbaserad tjänst där någon roll har mer än 25 instanser.
* Felsökningsprogrammet använder portarna 30400 30424, 31400 31424 och 32400 32424. Om du försöker använda någon av dessa portar du kommer inte att kunna publicera din tjänst och någon av följande felmeddelanden visas i aktivitetsloggen för Azure:

  * Det gick inte att verifiera .cscfg-filen mot .csdef-filen.
    Det reserverade intervall 'portintervallet' för slutpunkten Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector av rollen 'rollen' överlappar ett redan definierade porten eller det.
  * Det gick inte att allokera. Försök igen senare, försök att minska den Virtuella datorstorleken eller antalet rollinstanser, eller försök att distribuera till en annan region.

## <a name="debugging-azure-virtual-machines"></a>Felsökning av virtuella Azure-datorer
Du kan felsöka program som körs på virtuella Azure-datorer med hjälp av Server Explorer i Visual Studio. När du aktiverar fjärrfelsökning i en virtuell Azure-dator, installerar Azure remote felsökning tillägget på den virtuella datorn. Du kan sedan ansluta till processer på den virtuella datorn och felsöka som vanligt.

> [!NOTE]
> Virtuella datorer som skapats via Azure resource manager-stacken kan felsökas via fjärranslutning med hjälp av Cloud Explorer i Visual Studio 2015. Mer information finns i [hantera Azure-resurser med Cloud Explorer](http://go.microsoft.com/fwlink/?LinkId=623031).
>
>

### <a name="to-debug-an-azure-virtual-machine"></a>Felsöka en virtuell Azure-dator
1. I Server Explorer expanderar du noden virtuella datorer och välj noden för den virtuella dator som du vill felsöka.
2. Öppna snabbmenyn och välj **aktivera felsökning**. När du tillfrågas om du är säker på om du vill aktivera felsökning på den virtuella datorn, Välj **Ja**.

    Azure installerar remote felsökning tillägget på den virtuella datorn att aktivera felsökning.

    ![Aktivera felsökning kommando för virtuell dator](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure-aktivitetsloggen](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)
3. När fjärråtkomst felsökning tillägget har installerats, öppna snabbmenyn för den virtuella datorn och välj **koppla felsökare...**

    Azure hämtar en lista över processer på den virtuella datorn och visar dem i Attach processen i dialogrutan.

    ![Koppla felsökare kommando](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)
4. I den **koppla till Process** dialogrutan **Välj** vill begränsa resultatlistan för att visa endast typerna av kod som du vill felsöka. Du kan felsöka 32 - eller 64-bitars hanterad kod, maskinkod eller båda.

    ![Välj typ i dialogrutan kod](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
5. Välj de processer som du vill felsöka på den virtuella datorn och välj sedan **bifoga**. Du kan till exempel välja w3wp.exe-processen om du vill felsöka ett webbprogram på den virtuella datorn. Se [felsöka en eller flera processer i Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) och [Azure rollen arkitektur](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) för mer information.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Skapa ett webbprojekt och en virtuell dator för felsökning
Innan du publicerar ditt Azure-projekt kan det vara användbart att testa den i en innesluten miljö som har stöd för felsökning och testa scenarier och där du kan installera testning och övervaka program. Ett sätt att göra detta är att felsöka din app på en virtuell dator.

Visual Studio ASP.NET-projekt erbjuda ett alternativ för att skapa en praktisk virtuell dator som du kan använda för att testa appen. Den virtuella datorn innehåller vanligtvis behövs slutpunkter som PowerShell, fjärrskrivbord och WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Så här skapar du ett webbprojekt och en virtuell dator för felsökning
1. Skapa ett nytt ASP.NET-webbprogram i Visual Studio.
2. Välj i dialogrutan Nytt ASP.NET-projekt i avsnittet Azure **virtuella** i den nedrullningsbara listrutan. Lämna den **skapa fjärresurser** kryssrutan är markerad. Välj **OK** att fortsätta.

    Den **Skapa virtuell dator på Azure** dialogrutan visas.

    ![ASP.NET web project dialogrutan Skapa](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Obs:** blir du ombedd att logga in på ditt Azure-konto om du inte redan är inloggad.

1. Välj olika inställningar för den virtuella datorn och välj sedan **OK**. Se [virtuella datorer](http://go.microsoft.com/fwlink/?LinkId=623033) för mer information.

    Det namn du anger för DNS-namn ska vara namnet på den virtuella datorn.

    ![Skapa virtuell dator på Azure dialogrutan](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure skapar den virtuella datorn och sedan etablerar och konfigurerar slutpunkter, till exempel Remote Desktop- och webbdistribution
2. När den virtuella datorn har konfigurerats helt, markerar du den virtuella datorns nod i Server Explorer.
3. Öppna snabbmenyn och välj **aktivera felsökning**. När du tillfrågas om du är säker på om du vill aktivera felsökning på den virtuella datorn, Välj **Ja**.

    Azure installerar remote felsökning tillägget till den virtuella datorn att aktivera felsökning.

    ![Aktivera felsökning kommando för virtuell dator](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure-aktivitetsloggen](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)
4. Publicera dina projekt som beskrivs i [så här: distribuera en Web Project med ett klick i Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Eftersom du vill felsöka på den virtuella datorn på den **inställningar** sida av den **Publicera webbplats** väljer **Debug** som konfigurationen. Detta säkerställer att kortnamn är tillgängliga när du felsöker.

    ![Inställningar för publicering](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)
5. I den **alternativ för Filpublicering**väljer **ta bort extra filer från destinationen** om projektet har redan distribuerats vid en tidigare tidpunkt.
6. När projektet publicerar på snabbmenyn för den virtuella datorn i Server Explorer markerar **koppla felsökare...**

    Azure hämtar en lista över processer på den virtuella datorn och visar dem i Attach processen i dialogrutan.

    ![Koppla felsökare kommando](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)
7. I den **koppla till Process** dialogrutan **Välj** vill begränsa resultatlistan för att visa endast typerna av kod som du vill felsöka. Du kan felsöka 32 - eller 64-bitars hanterad kod, maskinkod eller båda.

    ![Välj typ i dialogrutan kod](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
8. Välj de processer som du vill felsöka på den virtuella datorn och välj sedan **bifoga**. Du kan till exempel välja w3wp.exe-processen om du vill felsöka ett webbprogram på den virtuella datorn. Se [felsöka en eller flera processer i Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) för mer information.

## <a name="next-steps"></a>Nästa steg
* Använd **Intellitrace** att samla in en logg över anrop och händelser från en server för versionen. Se [felsökning en publicerade molntjänst med IntelliTrace och Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).
* Använd **Azure Diagnostics** logga detaljerad information från kod som körs inom roller, oavsett om rollerna som körs i en utvecklingsmiljö eller i Azure. Se [samlar in loggningsdata med hjälp av Azure-diagnostik](http://go.microsoft.com/fwlink/p/?LinkId=400450).
