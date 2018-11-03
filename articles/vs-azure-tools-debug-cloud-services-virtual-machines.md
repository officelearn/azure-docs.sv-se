---
title: Felsökning av en Azure-molntjänst eller virtuell dator i Visual Studio | Microsoft Docs
description: Felsöka en molntjänst eller virtuell dator i Visual Studio
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 9bbd7f72be6ef65a0cd4178b31f18a9765690837
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969470"
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Felsökning av en Azure-molntjänst eller virtuell dator i Visual Studio

Visual Studio ger dig olika alternativ för felsökning av Azure-molntjänster och virtuella datorer.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>Felsöka cloud Services på den lokala datorn

Du kan spara tid och pengar genom att använda Azure compute-emulatorn för att felsöka cloud Services på en lokal dator. Du kan felsöka en tjänst lokalt innan du distribuerar den för att förbättra tillförlitlighet och prestanda utan att betala för beräkningstiden. Men vissa kan det uppstå fel bara när du kör en molntjänst i Azure själva. Du kan felsöka de här felen om du aktiverar fjärrfelsökning när du publicerar din tjänst och bifoga felsökningsprogrammet till en rollinstans.

Emulatorn simulerar Azure Compute-tjänsten och körs i din lokala miljö så att du kan testa och felsöka cloud Services innan du distribuerar den. Emulatorn hanterar livscykeln för dina rollinstanser och ger åtkomst till simulerade resurser, till exempel lokal lagring. När du felsöker eller köra tjänsten från Visual Studio startar automatiskt emulatorn som ett program i bakgrunden och distribuerar sedan din tjänst till emulatorn. Du kan använda emulatorn för att visa din tjänst när den körs i den lokala miljön. Du kan köra en fullständig version eller expressversionen av emulatorn. (Från och med Azure 2.3, expressversionen av emulatorn kunna är standard.) Se [med Emulator Express för att köra och felsöka en molntjänst lokalt](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Felsöka cloud Services på den lokala datorn

1. På menyraden väljer **felsöka**, **Starta felsökning** att köra Azure cloud service-projekt. Alternativt kan du trycka på F5. Du ser ett meddelande om att Compute-emulatorn startas. När emulatorn startar bekräftar det att ikonen i systemfältet.

    ![Azure-emulatorn i systemfältet](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)

2. Visa användargränssnittet för compute-emulatorn genom att öppna snabbmenyn för Azure-ikonen i meddelandefältet och välj sedan **visa Compute Emulator UI**.

    Den vänstra rutan i Användargränssnittet visas de tjänster som för närvarande har distribuerats till compute-emulatorn och rollinstanser som varje tjänst körs. Du kan välja tjänsten eller roller att visa livscykel, loggning och diagnostikinformation i den högra rutan. Om du placerar fokus på den övre marginalen av ett fönster med en ingår så den att den fyller den högra rutan.

3. Gå igenom programmet genom att välja kommandon på den **felsöka** menyn och ange brytpunkter i koden. När du går igenom programmet i felsökningsprogrammet, uppdateras fönstren med aktuell status för programmet. När du stoppa felsökningen bort programdistributionen. Om ditt program innehåller en webbroll och du har ställt in egenskapen Start åtgärden att starta webbläsaren, startar Visual Studio ditt webbprogram i webbläsaren. Om du ändrar antalet instanser av en roll i tjänstkonfigurationen kan du stoppa Molntjänsten och starta sedan om felsökning så att du kan felsöka de här nya instanser av rollen.

    **Obs:** om du stoppar som körs eller om du felsöker din tjänst, lokala beräkningsemulatorn och storage-emulatorn inte stoppats. Du måste stänga dem explicit från meddelandefältet.

## <a name="debug-a-cloud-service-in-azure"></a>Felsöka en molntjänst i Azure

För att felsöka en molnbaserad tjänst från en fjärrdator, måste du aktivera funktionen explicit när du distribuerar din molntjänst så att nödvändiga tjänster (till exempel msvsmon.exe) är installerade på de virtuella datorer som kör dina rollinstanser. Om du inte aktivera fjärrfelsökning när du har publicerat tjänsten, måste du publicera tjänsten med fjärrfelsökning aktiverat.

Om du aktiverar fjärrfelsökning för en tjänst i molnet, inte det minskad prestanda eller ytterligare avgifter. Använd inte fjärrfelsökning på en tjänst för produktion, eftersom klienter som använder tjänsten kan påverkas negativt.

> [!NOTE]
> När du publicerar en molnbaserad tjänst från Visual Studio kan du aktivera **IntelliTrace** för alla roller i tjänsten som är riktade till .NET Framework 4 eller .NET Framework 4.5. Med hjälp av **IntelliTrace**, du kan granska händelser som inträffat under en rollinstans i förflutna och återskapa kontext från den tiden. Se [felsöka en publicerad molntjänst med IntelliTrace och Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) och [med IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Aktivera fjärrfelsökning för en molntjänst

1. Öppna snabbmenyn för Azure-projekt och välj sedan **publicera**.

2. Välj den **mellanlagring** miljö och **felsöka** konfiguration.

    Detta är endast en riktlinje. Du kan välja om du vill köra testmiljöer i en produktionsmiljö. Du kan dock negativt påverka användare om du aktiverar fjärrfelsökning i produktionsmiljön. Du kan välja Release-konfiguration, men konfigurationen Debug gör felsökning.

    ![Välj konfigurationen för felsökning](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)

3. Följ vanliga stegen, men välja den **aktivera Fjärrfelsökningsprogrammet för alla roller** kryssrutan på den **avancerade inställningar** fliken.

    ![Felsöka konfiguration](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Att bifoga felsökningsprogrammet till en molntjänst i Azure

1. I Server Explorer expanderar du noden för din molntjänst.

2. Öppna snabbmenyn för rollen eller rollinstans som du vill bifoga och välj sedan **bifoga felsökningsprogrammet**.

    Om du felsöker en roll, kopplar Visual Studio-felsökaren för varje instans av rollen. Felsökningsprogrammet bryter på en brytpunkt för den första rollinstansen som körs som rad med kod och uppfyller eventuella villkor för den brytpunkten. Om du felsöker en instans, debugger-ansluten till den instans och sidbrytningar på en brytpunkt när den specifika instansen körs den kodraden och uppfyller villkoren för den brytpunkten.

    ![Koppla felsökare](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)

3. När felsökningsprogrammet kopplar till en instans, felsöka som vanligt. Felsökningsprogrammet bifogar automatiskt lämpliga värdprocessen för din roll. Beroende på vad rollen som, bifogar felsökningsprogrammet w3wp.exe, WaWorkerHost.exe eller WaIISHost.exe. Expandera noden instans i Server Explorer för att verifiera processen som felsökningsprogrammet är ansluten. Se [Azure-Rollarkitektur](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) för mer information om Azure bearbetar.

    ![Välj dialogrutan kod](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

4. Öppna dialogrutan processer av, på menyraden, välja felsökning, Windows, processer för att identifiera de processer som felsökningsprogrammet är ansluten. (Tangentbord: Ctrl + Alt + Z) Om du vill koppla bort en särskild process, öppna dess snabbmenyn och välj sedan **koppla från processen**. Eller, leta rätt på instans-noden i Server Explorer, hitta processen, öppna dess snabbmenyn och välj sedan **koppla från processen**.

    ![Felsöka processer](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Undvika långa stopp vid brytpunkter när remote felsökning. Azure behandlar en process som har stoppats mer än ett par minuter som inte svarar och slutar skickar trafik till den instansen. Om du stoppar för länge kopplas msvsmon.exe från processen.

Om du vill koppla bort felsökning från alla processer i din instans eller roll, öppna snabbmenyn för rollen eller instans som du felsöker och välj sedan **koppla från felsökare**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Begränsningar för fjärrfelsökning i Azure

Från Azure SDK 2.3 har fjärrfelsökning följande begränsningar:

* Du kan inte publicera en molnbaserad tjänst där någon roll har mer än 25 instanser med fjärrfelsökning aktiverat.
* Felsökningsprogrammet använder portarna 30400 30424, 31400 31424 och 32400 32424. Om du försöker använda någon av dessa portar, du kan inte publicera din tjänst och någon av följande felmeddelanden visas i aktivitetsloggen för Azure:

  * Fel vid verifiering av .cscfg-filen mot .csdef-filen.
    Det reserverade intervall 'portintervallet ”för slutpunkten Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector av rollen 'rollen” överlappar ett redan definierade port eller ett intervall.
  * Tilldelningen misslyckades. Försök igen senare, försök att minska den Virtuella datorstorleken eller antalet rollinstanser, eller försök att distribuera till en annan region.

## <a name="debugging-azure-virtual-machines"></a>Felsökning Azure-datorer

Du kan felsöka program som körs på Azure virtual machines med hjälp av Server Explorer i Visual Studio. När du aktiverar fjärrfelsökning på Azure-datorer, installerar Azure remote felsökning tillägget på den virtuella datorn. Du kan sedan ansluta till processer på den virtuella datorn och felsöka som vanligt.

> [!NOTE]
> Virtuella datorer som skapats via Azure resource manager-stacken kan felsökas via en fjärranslutning med hjälp av Cloud Explorer i Visual Studio 2015. Mer information finns i [hantera Azure-resurser med Cloud Explorer](http://go.microsoft.com/fwlink/?LinkId=623031).

### <a name="to-debug-an-azure-virtual-machine"></a>Felsöka en Azure virtuell dator

1. I Server Explorer expanderar du noden för virtuella datorer och välj noden i den virtuella dator som du vill felsöka.

2. Öppna snabbmenyn och välj **aktivera felsökning**. När du tillfrågas om du är osäker på om du vill aktivera felsökning på den virtuella datorn, väljer **Ja**.

    Remote felsökning tillägget installeras på den virtuella datorn och aktivera felsökning i Azure.

    ![Aktivera fjärrfelsökning kommandot för virtuell dator](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure-aktivitetsloggen](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

3. När tillägget för fjärr-felsökning är klar installerar öppna snabbmenyn för den virtuella datorn och välj **bifoga felsökningsprogrammet...**

    Azure hämtar en lista över processer på den virtuella datorn och visar dem i Anslut för att bearbeta dialogrutan.

    ![Bifoga felsökningsprogrammet kommando](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

4. I den **koppla till Process** dialogrutan **Välj** att begränsa resultatlistan för att visa endast typerna av kod som du vill felsöka. Du kan felsöka 32-bitars eller 64-bitars förvaltad kod, inbyggd kod eller båda.

    ![Välj dialogrutan kod](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

5. Välj de processer som du vill felsöka på den virtuella datorn och välj sedan **bifoga**. Du kan till exempel välja w3wp.exe-processen om du vill felsöka en webbapp på den virtuella datorn. Se [felsöka en eller flera processer i Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) och [Azure-Rollarkitektur](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) för mer information.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Skapa ett webbprojekt och en virtuell dator för felsökning

Innan du publicerar din Azure-projekt, det kan vara bra att testa det i en innesluten miljö som har stöd för felsökning och testning av scenarier och där du kan installera testa och övervaka program. Ett sätt att köra dessa tester är felsöka din app på en virtuell dator.

Visual Studio ASP.NET-projekt erbjuder alternativ för att skapa en praktisk virtuell dator som du kan använda för att testa appen. Den virtuella datorn innehåller ofta nödvändiga slutpunkter som PowerShell, fjärrskrivbord och WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Du skapar ett webbprojekt och en virtuell dator för felsökning

1. Skapa ett nytt ASP.NET-webbprogram i Visual Studio.

2. I dialogrutan Nytt ASP.NET-projekt i avsnittet Azure väljer **VM** i listrutan listrutan. Lämna den **skapa fjärransluten resurser** kryssrutan är markerad. Välj **OK** att fortsätta.

    Den **Skapa virtuell dator på Azure** dialogrutan visas.

    ![Skapa dialogrutan för ASP.NET web-projekt](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Obs:** du att ombes att logga in på ditt Azure-konto om du inte redan är inloggad.

3. Välj olika inställningar för den virtuella datorn och välj sedan **OK**. Se [virtuella datorer](http://go.microsoft.com/fwlink/?LinkId=623033) för mer information.

    Det namn som du anger för DNS-namnet ska vara namnet på den virtuella datorn.

    ![Skapa virtuell dator på Azure dialogrutan](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure skapar den virtuella datorn och sedan etablerar och konfigurerar slutpunkter, till exempel Remote Desktop- och Web Deploy

4. När den virtuella datorn är fullständigt konfigurerad, väljer du den virtuella datorns nod i Server Explorer.

5. Öppna snabbmenyn och välj **aktivera felsökning**. När du tillfrågas om du är osäker på om du vill aktivera felsökning på den virtuella datorn, väljer **Ja**.

    Azure installerar remote felsökning tillägget till den virtuella datorn och aktivera felsökning.

    ![Aktivera fjärrfelsökning kommandot för virtuell dator](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure-aktivitetsloggen](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)

6. Publicera ditt projekt som beskrivs i [så här: distribuera en Web-projekt med ett klick i Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Eftersom du vill felsöka på den virtuella datorn på den **inställningar** för den **Publicera webbplats** väljer **felsöka** eftersom konfigurationen. Detta säkerställer att kortnamn är tillgängliga när du felsöker.

    ![Publiceringsinställningar](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)

7. I den **alternativ för Filpublicering**väljer **ta bort extra filer från destinationen** om projektet har redan distribuerats på en tidigare tidpunkt.

8. När du publicerar projektet på den virtuella datorns snabbmenyn i Server Explorer, Välj **bifoga felsökningsprogrammet...**

    Azure hämtar en lista över processer på den virtuella datorn och visar dem i Anslut för att bearbeta dialogrutan.

    ![Bifoga felsökningsprogrammet kommando](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)

9. I den **koppla till Process** dialogrutan **Välj** att begränsa resultatlistan för att visa endast typerna av kod som du vill felsöka. Du kan felsöka 32-bitars eller 64-bitars förvaltad kod, inbyggd kod eller båda.

    ![Välj dialogrutan kod](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)

10. Välj de processer som du vill felsöka på den virtuella datorn och välj sedan **bifoga**. Du kan till exempel välja w3wp.exe-processen om du vill felsöka en webbapp på den virtuella datorn. Se [felsöka en eller flera processer i Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) för mer information.

## <a name="next-steps"></a>Nästa steg

* Använd **Intellitrace** att samla in en logg över anrop och händelser från en server för versionen. Se [felsöka en publicerad molntjänst med IntelliTrace och Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).

* Använd **Azure Diagnostics** loggar detaljerad information från kod som körs i roller, oavsett om rollerna som körs i utvecklingsmiljön eller i Azure. Se [samla in data med hjälp av Azure Diagnostics](http://go.microsoft.com/fwlink/p/?LinkId=400450).
