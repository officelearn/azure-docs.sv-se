---
title: Testa datavetenskapskod med Azure DevOps Services – Team Data Science Process
description: Data science-kodtestning på Azure med UCI-datauppsättningen för förutsägelse av vuxeninkomster med Team Data Science Process och Azure DevOps Services
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722067"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Testning av datavetenskapskod på Azure med Team Data Science Process och Azure DevOps Services
Den här artikeln innehåller preliminära riktlinjer för testning av kod i ett arbetsflöde för datavetenskap. Sådana tester ger dataforskare ett systematiskt och effektivt sätt att kontrollera kvaliteten och det förväntade resultatet av deras kod. Vi använder ett Team Data Science Process (TDSP) [projekt som använder UCI Vuxen inkomst datauppsättning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) som vi publicerade tidigare för att visa hur kodtestning kan göras. 

## <a name="introduction-on-code-testing"></a>Introduktion till kodtestning
"Enhetstestning" är en mångårig metod för mjukvaruutveckling. Men för datavetenskap är det ofta inte klart vad "enhetstestning" betyder och hur du ska testa kod för olika stadier av en livscykel för datavetenskap, till exempel:

* Förberedelse av data
* Granskning av datakvalitet
* Modellering
* Modelldistribution 

Den här artikeln ersätter termen "enhetstestning" med "kodtestning". Det refererar till testning som de funktioner som hjälper till att bedöma om koden för ett visst steg i en datavetenskap livscykel producerar resultat "som förväntat." Den person som skriver testet definierar vad som är "som förväntat", beroende på resultatet av funktionen - till exempel datakvalitetskontroll eller modellering.

Den här artikeln innehåller referenser som användbara resurser.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps för testramverket
I den här artikeln beskrivs hur du utför och automatiserar testning med hjälp av Azure DevOps. Du kan välja att använda alternativa verktyg. Vi visar också hur du konfigurerar en automatisk version med hjälp av Azure DevOps och build-agenter. För byggagenter använder vi virtuella Azure Data Science-datorer (DSVM).

## <a name="flow-of-code-testing"></a>Flöde av kodtestning
Det övergripande arbetsflödet för testkod i ett data science-projekt ser ut så här: 

![Flödesschema för kodtestning](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Detaljerade steg

Använd följande steg för att konfigurera och köra kodtestning och en automatiserad version med hjälp av en byggagent och Azure DevOps:

1. Skapa ett projekt i Visual Studio-skrivbordsprogrammet:

    ![Skärmen "Skapa nytt projekt" i Visual Studio](./media/code-test/create_project.PNG)

   När du har skapat projektet hittar du det i Solution Explorer i den högra rutan:
    
    ![Steg för att skapa ett projekt](./media/code-test/create_python_project_in_vs.PNG)

    ![Solution Explorer](./media/code-test/solution_explorer_in_vs.PNG)

1. Mata in projektkoden i Azure DevOps-projektkoddatabasen: 

    ![Databas för projektkod](./media/code-test/create_repo.PNG)

1. Anta att du har gjort vissa dataförberedelsearbete, till exempel datainmatning, funktionsteknik och att skapa etikettkolumner. Du vill vara säker på att koden genererar de resultat du förväntar dig. Här är en kod som du kan använda för att testa om databehandlingskoden fungerar som den ska:

    * Kontrollera att kolumnnamnen är rätt:
    
      ![Kod för matchande kolumnnamn](./media/code-test/check_column_names.PNG)

    * Kontrollera att svarsnivåerna är rätt:

      ![Kod för matchande nivåer](./media/code-test/check_response_levels.PNG)

    * Kontrollera att svarsprocenten är rimlig:

      ![Kod för svarsprocent](./media/code-test/check_response_percentage.PNG)

    * Kontrollera den saknade hastigheten för varje kolumn i data:
    
      ![Kod för saknad hastighet](./media/code-test/check_missing_rate.PNG)


1. När du har gjort databehandlingen och funktionen engineering arbete, och du har tränat en bra modell, se till att modellen du tränat kan få nya datauppsättningar korrekt. Du kan använda följande två tester för att kontrollera förutsägelsenivåer och fördelning av etikettvärden:

    * Kontrollera förutsägelsenivåer:
    
      ![Kod för kontroll av förutsägelsenivåer](./media/code-test/check_prediction_levels.PNG)

    * Kontrollera fördelningen av förutsägelsevärden:

      ![Kod för kontroll av förutsägelsevärden](./media/code-test/check_prediction_values.PNG)

1. Sätt alla testfunktioner tillsammans i ett Python-skript som heter **test_funcs.py:**

    ![Python-skript för testfunktioner](./media/code-test/create_file_test_func.PNG)


1. När testkoderna har förberetts kan du ställa in testmiljön i Visual Studio.

   Skapa en Python-fil som heter **test1.py**. Skapa en klass som innehåller alla tester du vill göra i den här filen. Följande exempel visar sex tester som förberetts:
    
    ![Python-fil med en lista över tester i en klass](./media/code-test/create_file_test1_class.PNG)

1. Dessa tester kan identifieras automatiskt om du sätter **codetest.testCase** efter ditt klassnamn. Öppna Test Explorer i den högra rutan och välj **Kör alla**. Alla tester kommer att köras sekventiellt och kommer att berätta om testet lyckas eller inte.

    ![Köra testerna](./media/code-test/run_tests.PNG)

1. Checka in koden till projektdatabasen med hjälp av Git-kommandon. Ditt senaste arbete kommer att återspeglas inom kort i Azure DevOps.

    ![Git-kommandon för incheckning av kod](./media/code-test/git_check_in.PNG)

    ![Senaste arbetet i Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Konfigurera automatisk byggande och test i Azure DevOps:

    a. I projektdatabasen väljer du **Bygg och släpp**och väljer sedan **+Nytt** för att skapa en ny byggprocess.

    ![Val för att starta en ny byggprocess](./media/code-test/create_new_build.PNG)

    b. Följ anvisningarna för att välja plats för källkod, projektnamn, databas och greninformation.
    
    ![Information om källa, namn, databas och gren](./media/code-test/fill_in_build_info.PNG)

    c. Välj en mall. Eftersom det inte finns någon Python-projektmall börjar du med att välja **Tom process**. 

    ![Lista över mallar och knappen "Tom process"](./media/code-test/start_empty_process_template.PNG)

    d. Namnge bygget och välj agenten. Du kan välja standard här om du vill använda en DSVM för att slutföra byggprocessen. Mer information om hur du ställer in agenter finns i [Skapa och frisläppa agenter](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Bygg- och agentval](./media/code-test/select_agent.PNG)

    e. Markera **+** i den vänstra rutan om du vill lägga till en aktivitet för den här byggfasen. Eftersom vi ska köra Python-skriptet **test1.py** för att slutföra alla kontroller, använder den här uppgiften ett PowerShell-kommando för att köra Python-kod.
    
    ![Fönstret "Lägg till uppgifter" med PowerShell markerat](./media/code-test/add_task_powershell.PNG)

    f. Fyll i den information som krävs i PowerShell-informationen, till exempel namn och version av PowerShell. Välj **Infogat skript** som typ. 
    
    I rutan under **Infogat skript**kan du skriva **python test1.py**. Kontrollera att miljövariabeln är korrekt konfigurerad för Python. Om du behöver en annan version eller kärna av Python kan du uttryckligen ange sökvägen som visas i bilden: 
    
    ![PowerShell-detaljer](./media/code-test/powershell_scripts.PNG)

    g. Välj **Spara & kö** för att slutföra byggpipelineprocessen.

    ![Knappen "Spara & kö"](./media/code-test/save_and_queue_build_definition.PNG)

Nu startar varje gång ett nytt åtagande flyttas till koddatabasen, byggprocessen automatiskt. (Här använder vi master som databas, men du kan definiera vilken gren som helst.) Processen kör **test1.py** filen i agentdatorn för att se till att allt som definieras i koden körs korrekt. 

Om aviseringarna är korrekt konfigurerade får du ett meddelande i e-postmeddelandet när versionen är klar. Du kan också kontrollera byggstatusen i Azure DevOps. Om det misslyckas, kan du kontrollera detaljerna i bygga och ta reda på vilken bit är bruten.

![E-postmeddelande om byggframgång](./media/code-test/email_build_succeed.PNG)

![Azure DevOps meddelande om att skapa lyckad](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Nästa steg
* Se [UCI:s databas](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) för inkomstprognos för konkreta exempel på enhetstester för datavetenskapliga scenarier.
* Följ föregående disposition och exempel från UCI inkomst förutsägelse scenario i din egen data science projekt.

## <a name="references"></a>Referenser
* [TDSP](https://aka.ms/tdsp)
* [Testverktyg för Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Testningsresurser för Azure DevOps](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
