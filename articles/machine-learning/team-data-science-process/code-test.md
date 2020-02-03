---
title: Testa data science kod med Azure-tjänster för DevOps - Team Data Science Process
description: Data science kod testning på Azure med UCI vuxet inkomst förutsägelse datauppsättningen med Team Data Science Process och Azure DevOps-tjänsterna
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 9612114bb368898ccf31b2c8692869b84544b652
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722067"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Data science kod testning på Azure med Team Data Science Process och Azure DevOps-tjänsterna
Den här artikeln ger preliminär riktlinjer för att testa koden i ett arbetsflöde för datavetenskap. Sådant test ger datatekniker ett systematiskt och effektivt sätt att kontrollera kvaliteten och förväntade resultatet av sin kod. Vi använder ett TDSP-projekt (Team data science process) [som använder den data uppsättning för den sexuella](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) värde som vi publicerade tidigare för att visa hur kod testning kan göras. 

## <a name="introduction-on-code-testing"></a>Introduktion till code testning
”Enhetstestning” är värnar om praxis för programutveckling. Men för data vetenskap är det ofta inte uppenbart vad "enhets testning" innebär och hur du bör testa kod för olika faser i en data vetenskaps livs cykel, till exempel:

* Förberedelse av data
* Data quality undersökning
* Modellering
* Modelldistribution 

Den här artikeln ersätter termen ”enhet testning” med ”testning av kod”. Den refererar till testning som de funktioner som hjälper dig för att utvärdera om koden för ett visst steg i en livscykeln för datavetenskap ger resultat ”som förväntat”. Den person som skriver testet definierar vad är ”som förväntat”, beroende på resultatet av funktionen – till exempel data kvalitetskontroll eller modellering.

Den här artikeln innehåller referenser som användbara resurser.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps för testning framework
Den här artikeln beskriver hur du utför och automatisera testning med Azure DevOps. Du kan välja att använda andra verktyg. Vi visar också hur man ställer in en automatisk build med hjälp av Azure DevOps och agenter. Build-agenter, kan vi använda Azure virtuella datorer för datavetenskap (Dsvm).

## <a name="flow-of-code-testing"></a>Flödet av kod testning
Det totala arbetsflödet för testning koden i ett datavetenskapsprojekt ser ut så här: 

![Flödesschema för testning av kod](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Detaljerade steg

Använd följande steg för att konfigurera och köra kod testning och en automatisk build med hjälp av en skapandeagent och Azure DevOps:

1. Skapa ett projekt i Visual Studio-skrivbordsprogram:

    ![”Skapa nytt projekt” skärm i Visual Studio](./media/code-test/create_project.PNG)

   När du har skapat ditt projekt, hittar du det i Solution Explorer i den högra rutan:
    
    ![Anvisningar för att skapa ett projekt](./media/code-test/create_python_project_in_vs.PNG)

    ![Solution Explorer](./media/code-test/solution_explorer_in_vs.PNG)

1. Skicka projektkoden till kodlagringsplatsen för Azure DevOps-projekt: 

    ![Lagringsplatsen för projektet](./media/code-test/create_repo.PNG)

1. Anta att du har gjort vissa förberedelse av data, som datainmatning, funktionsframställning och skapa etikettkolumner. Du vill kontrollera att din kod genererar resultat som du förväntar dig. Här följer ett kodexempel som du kan använda för att testa om databearbetning koden fungerar korrekt:

    * Kontrollera att kolumnnamnen är rätt:
    
      ![Kod för att matcha kolumnnamn](./media/code-test/check_column_names.PNG)

    * Kontrollera att svarsnivåer är rätt:

      ![Kod för att matcha nivåer](./media/code-test/check_response_levels.PNG)

    * Kontrollera att i svarsprocent är rimliga:

      ![Koden för svarsprocent](./media/code-test/check_response_percentage.PNG)

    * Kontrollera saknas frekvensen för varje kolumn i data:
    
      ![Kod för saknas](./media/code-test/check_missing_rate.PNG)


1. När du har gjort databehandlingen och funktionen engineering arbete, och du har tränat en bra modell, se till att poängsätta du tränade modellen nya datauppsättningar på rätt sätt. Du kan använda följande två tester för att kontrollera förutsägelse nivåer och distribution av etikettvärden:

    * Kontrollera förutsägelse nivåer:
    
      ![Kod för att kontrollera förutsägelse nivåer](./media/code-test/check_prediction_levels.PNG)

    * Kontrollera distributionen av förutsägelse värden:

      ![Kod för att kontrollera värdena för förutsägelse](./media/code-test/check_prediction_values.PNG)

1. Sätt ihop alla test funktioner i ett Python-skript med namnet **test_funcs. py**:

    ![Python-skript för att testa functions](./media/code-test/create_file_test_func.PNG)


1. När test-koder bereds, kan du ställa in testmiljön i Visual Studio.

   Skapa en python-fil med namnet **test1.py**. Skapa en klass som innehåller alla tester som du vill göra i den här filen. I följande exempel visas sex tester förberett:
    
    ![Python-fil med en lista över tester i en klass](./media/code-test/create_file_test1_class.PNG)

1. Dessa tester kan identifieras automatiskt om du anger **codetest. testCase** efter ditt klass namn. Öppna test Utforskaren i den högra rutan och välj **Kör alla**. Alla tester körs sekventiellt och talar om testet lyckas eller inte.

    ![Kör testerna](./media/code-test/run_tests.PNG)

1. Kontrollera i din kod till lagringsplatsen för projektet genom att använda Git-kommandon. Senaste arbetet visas snart i Azure DevOps.

    ![Git-kommandon för att kontrollera i kod](./media/code-test/git_check_in.PNG)

    ![Senaste arbete i Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Konfigurera automatisk build och testa i Azure DevOps:

    a. I projekt databasen väljer du **build och release**och väljer sedan **+ ny** för att skapa en ny versions process.

    ![Val för att starta en ny versions process](./media/code-test/create_new_build.PNG)

    b. Följ anvisningarna för att välja din kod för källplats, projektnamn, lagringsplatsen och grenen information.
    
    ![Käll-, namn-, lagrings-och förgrenings information](./media/code-test/fill_in_build_info.PNG)

    c. Välj en mall. Eftersom det inte finns någon python-projektmall börjar du med att välja **tom process**. 

    ![Lista över mallar och knappen "tom process"](./media/code-test/start_empty_process_template.PNG)

    d. Namnge versionen och välj agenten. Du kan välja standardvärdet här om du vill använda en DSVM för att slutföra Bygg processen. Mer information om hur du ställer in agenter finns i [skapa och släppa agenter](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Build-och agent-val](./media/code-test/select_agent.PNG)

    e. Välj **+** i det vänstra fönstret för att lägga till en uppgift för den här build-fasen. Eftersom vi ska köra python-skriptet **test1.py** för att slutföra alla kontroller, använder den här uppgiften ett PowerShell-kommando för att köra python-kod.
    
    ![Fönstret Lägg till aktiviteter med PowerShell valt](./media/code-test/add_task_powershell.PNG)

    f. Fyll i nödvändig information, till exempel namn och version av PowerShell i PowerShell-information. Välj **infogat skript** som typ. 
    
    I rutan under **infogat skript**kan du skriva python- **test1.py**. Kontrol lera att miljövariabeln är korrekt konfigurerad för python. Om du behöver en annan version eller kernel av python kan du uttryckligen ange sökvägen så som visas i bilden: 
    
    ![PowerShell-information](./media/code-test/powershell_scripts.PNG)

    g. Klicka på **spara & kö** för att slutföra processen för att bygga pipelinen.

    ![Knappen Spara & kö](./media/code-test/save_and_queue_build_definition.PNG)

Varje gång en ny allokering skickas till kodlagringsplatsen, startas nu skapandeprocessen automatiskt. (Här använder vi Master som lagrings plats, men du kan definiera vilken gren som helst.) Processen kör **test1.py** -filen på agent datorn för att kontrol lera att allting som definieras i koden körs på rätt sätt. 

Om aviseringar har ställts in korrekt, kommer du att meddelas via e-post när bygget har slutförts. Du kan också kontrollera status för build i Azure DevOps. Om det misslyckas kan du kontrollera informationen för versionen och ta reda på vilka delar är bruten.

![E-postmeddelande för en lyckad build](./media/code-test/email_build_succeed.PNG)

![Meddelande om Azure DevOps för en lyckad build](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Nästa steg
* Se [lagrings platsen](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) för den enmängds inkomst förutsägelse för konkreta exempel på enhets test för data vetenskaps scenarier.
* Följ föregående disposition och exempel från UCI inkomst förutsägelse scenariot i din egen dataforskningsprojekt.

## <a name="references"></a>Referenser
* [TDSP](https://aka.ms/tdsp)
* [Test verktyg för Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Test resurser för Azure DevOps](https://www.visualstudio.com/team-services/)
* [Data vetenskaps Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
