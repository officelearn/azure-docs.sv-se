---
title: "Läs in testa ditt program med hjälp av Visual Studio Team Services | Microsoft Docs"
description: "Lär dig hur belastningstest Azure Service Fabric-program med hjälp av Visual Studio Team Services."
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: fc743585-0d1b-483f-981d-493f4552ac07
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: e8e270ce865d4da3ee219958b308db2c1c89b11b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>Läs in testa ditt program med hjälp av Visual Studio Team Services
Den här artikeln visar hur du använder Microsoft Visual Studio belastningen test funktioner belastningstest ett program. Den använder en serverdel för Azure Service Fabric-tillståndskänslig service och en frontwebb för tillståndslösa tjänsten. Exempelprogram som används här är ett flygplan plats simulatorn. Du kan ange ett flygplan ID, avvikelse tid och mål. Programmets serverdel bearbetar begäranden och klientdelen som visas på en karta flygplan som matchar villkoren.

Följande diagram illustrerar Service Fabric-program som du ska testa.

![Diagram över exempelprogram flygplan plats][0]

## <a name="prerequisites"></a>Krav
Innan du börjar måste du göra följande:

* Skaffa ett Visual Studio Team Services-konto. Du kan få ett kostnadsfritt [Visual Studio Team Services](https://www.visualstudio.com).
* Hämta och installera Visual Studio 2013 eller Visual Studio 2015. Den här artikeln används Visual Studio 2015 Enterprise edition, men Visual Studio 2013 och andra utgåvor ska fungera på samma sätt.
* Distribuera programmet till en fristående miljö. Se [hur du distribuerar program till ett kluster med hjälp av Visual Studio](service-fabric-publish-app-remote-cluster.md) information om detta.
* Förstå användningsmönster för ditt program. Den här informationen används för att simulera belastningen mönstret.
* Förstå mål för att testa din belastningen. Detta hjälper dig att tolka och analys av testresultatet belastningen.

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>Skapa och köra project webbprestanda och belastningstest
### <a name="create-a-web-performance-and-load-test-project"></a>Skapa ett webbprogram prestanda och belastningstest projekt
1. Öppna Visual Studio 2015. Välj **filen** > **ny** > **projekt** menyraden för att öppna den **nytt projekt** dialogrutan.
2. Expandera den **Visual C#** nod och välj **Test** > **project webbprestanda och belastningstest**. Namnge projektet och välj sedan den **OK** knappen.

    ![Skärmbild som visar dialogrutan Nytt projekt][1]

    Du bör se ett nytt webbprogram prestanda och belastningstest projekt i Solution Explorer.

    ![Skärmdump av Solution Explorer visar det nya projektet][2]

### <a name="record-a-web-performance-test"></a>Registrera ett webbtest prestanda
1. Öppna projektet .webtest.
2. Välj den **Lägg till registrering** ikon för att starta en session för registrering i webbläsaren.

    ![Skärmbild som visar ikonen Lägg till inspelning i en webbläsare][3]

    ![Skärmbild av knappen post i en webbläsare][4]
3. Bläddra till Service Fabric-programmet. Panelen inspelning ska visa webbegäranden.

    ![Skärmbild av webbförfrågningar på panelen Spela in][5]
4. Utför en sekvens med åtgärder som du förväntar dig användare att utföra. Dessa åtgärder används som ett mönster för att generera belastningen.
5. När du är klar väljer du den **stoppa** för att stoppa inspelningen.

    ![Skärmbild av knappen Avbryt][6]

    .Webtest-projekt i Visual Studio bör har tagit emot ett antal begäranden. Dynamiska parametrar ersätts automatiskt. Nu kan du ta bort några extra, upprepade beroende-begäranden som inte är en del av Testscenario.
6. Spara projektet och välj sedan den **kör testa** kommandot för att köra prestandatester web lokalt och kontrollera att allt fungerar korrekt.

    ![Skärmbild av kommandot Kör Test][7]

### <a name="parameterize-the-web-performance-test"></a>Parameterstyra prestanda webbtestet
Du skapa en parameter utifrån webbtestet prestanda genom att konvertera det till en kodad web prestandatest och sedan redigera koden. Alternativt kan binda du webbtestet prestanda till en datalista så att testet går igenom data. Se [generera och kör en kodad web prestandatest](https://msdn.microsoft.com/library/ms182552.aspx) för information om hur du konverterar webbprestanda testa ett kodat test. Se [lägga till en datakälla i en web prestandatest](https://msdn.microsoft.com/library/ms243142.aspx) för information om hur du binda data till en webbtjänst prestandatest.

I det här exemplet ska vi konvertera prestanda webbtestet till en kodad test så att du kan ersätta flygplan-ID med en GUID som genererats och lägga till fler begäranden att skicka flygplan till olika platser.

### <a name="create-a-load-test-project"></a>Skapa ett projekt för test av belastningen
Ett projekt för test av belastningen består av ett eller flera av de scenarier som beskrivs av web prestandatester och enhetstest, tillsammans med ytterligare angivna belastningen testa inställningar. Följande steg visar hur du skapar ett projekt för test av belastningen:

1. Välj på snabbmenyn i projektets webbprestanda och belastningstest **Lägg till** > **belastningstest**. I den **belastningen testa** guiden välja den **nästa** för att konfigurera inställningar för testet.
2. I den **mönster för att läsa in** väljer du om du vill att en konstant användarbelastningen eller en steg-belastning som börjar med ett fåtal användare och ökar användare över tid.

    Om du har en bra uppskattning av användaren belastningen och vill se hur det aktuella systemet utför, Välj **konstant ladda**. Om målet är att lära dig om utförs konsekvent under olika belastningar, Välj **steg belastningen**.
3. I den **testa blandning** väljer du den **Lägg till** och välj test som du vill ska ingå i testet belastningen. Du kan använda den **Distribution** kolumnen till att ange vilken procentandel av totalt antal tester som körs för varje test.
4. I den **inställningar** ange testperioden belastningen.

   > [!NOTE]
   > Den **testa iterationer** alternativet är tillgängligt endast när du kör ett belastningstest lokalt med hjälp av Visual Studio.
   >
   >
5. I den **plats** avsnitt i **inställningar**, ange platsen där begäranden om belastning test genereras. Guiden kan du uppmanas att logga in på ditt Team Services-konto. Logga in och sedan välja en geografisk plats. När du är klar väljer du den **Slutför** knappen.
6. När testet belastningen har skapats kan öppna .loadtest projektet och välj den aktuella köra inställning, till exempel **inställningar** > **kör Settings1 [Active]**. Då öppnas kör inställningarna i den **egenskaper** fönster.
7. I den **resultat** avsnitt i den **inställningar** egenskapsfönstret för den **tidsinställning information lagring** inställningen ska ha **ingen** som dess Standardvärdet. Ändra värdet till **alla enskilda uppgifter** få mer information om belastningen testresultaten. Se [ladda testning](https://www.visualstudio.com/load-testing.aspx) för mer information om hur du ansluter till Visual Studio Team Services och kör ett belastningstest.

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>Kör testet belastningen med hjälp av Visual Studio Team Services
Välj den **kör belastningen testa** kommando för att starta testet körs.

![Skärmbild av kommandot Kör belastningstest][8]

## <a name="view-and-analyze-the-load-test-results"></a>Visa och analysera testresultaten belastning
Testa fortlöper som belastningen, prestanda informationen läggs till i diagram. Du bör se något som liknar följande diagram.

![Skärmbild som visar prestandadiagram för testresultat][9]

1. Välj den **hämta rapport** länken längst upp på sidan. När rapporten har hämtats kan välja den **visa rapporten** knappen.

    På den **diagram** visas diagram för olika prestandaräknare. På den **sammanfattning** fliken övergripande testresultaten visas. Den **tabeller** visar det totala antalet skickade och misslyckade belastningstester.
2. Välj numret länkar på den **Test** > **misslyckades** och **fel** > **antal** kolumner att visa fel information.

    Den **detaljer** fliken visas virtuella användar- och testa scenariot information för misslyckade begäranden. Data kan vara användbar om testet belastningen innehåller flera scenarier.

Se [analysera ladda Testa resultaten i vyn diagram för att läsa in testa Analyzer](https://www.visualstudio.com/load-testing.aspx) testresultat för mer information om hur du visar belastning.

## <a name="automate-your-load-test"></a>Automatisera dina belastningstest
Visual Studio Team Services ladda Test ger API: er som hjälper dig att hantera belastningstester och analysera resultaten i ett Team Services-konto. Se [moln belastningen testning Rest-API: er](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx) för mer information.

## <a name="next-steps"></a>Nästa steg
* [Övervakning och diagnos av tjänster i en inställning för utveckling av lokal dator](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png
