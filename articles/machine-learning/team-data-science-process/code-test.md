---
title: Datavetenskap kod testa på Azure med UCI vuxna intäkter förutsägelse dataset - teamet vetenskap och om Visual Studio Team Services
description: Datavetenskap kod testning med UCI vuxna intäkter förutsägelse data
services: machine-learning, team-data-science-process
documentationcenter: ''
author: weig
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2018
ms.author: weig
ms.openlocfilehash: f9465eadfea97ffb721a2bac2c9845460d566fd1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655619"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>Datavetenskap kod testning med UCI vuxna intäkter förutsägelse datauppsättningen
Den här artikeln ger preliminära riktlinjer för att testa koden i ett arbetsflöde för vetenskapliga data. Dessa tester ger datavetare ett systematiskt och effektivt sätt att kontrollera kvaliteten och förväntade resultatet av koden. Vi använder en Team Data vetenskap processen (TDSP) [projekt som använder UCI vuxna intäkter datauppsättningen](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) som vi tidigare publicerade för att visa hur koden testning kan göras. 

## <a name="introduction-on-code-testing"></a>Introduktion kod testning
”Enhet testning” är långsiktigt för programutveckling. Men för datavetenskap, är det ofta inte avmarkera vad som innebär och hur du bör testa kod för olika faser i en vetenskap livscykel data som:

* Förberedelse av data
* Data quality undersökning
* Modellering
* Modell-distribution 

Den här artikeln ersätter termen ”enhet testa” med ”kod testar”. Den hänvisar till testning som funktioner som hjälper dig för att utvärdera om koden för ett visst steg i en datavetenskap livscykel ger resultat ”som förväntat”. Den person som skriver testet definierar vad är ”som förväntat”, beroende på resultatet av funktionen, till exempel data quality kontroll eller modellering.

Den här artikeln innehåller referenser som användbara resurser.

## <a name="visual-studio-team-services-for-the-testing-framework"></a>Visual Studio Team Services för tester framework
Den här artikeln beskriver hur du utför och automatisera testning med hjälp av Visual Studio Team Services VSTS (). Du kan välja att använda alternativa verktyg. Vi också hur du ställer in en automatisk version med hjälp av VSTS och skapa agenter. Build-agenter använder vi datavetenskap virtuella datorer i Azure (DSVMs).

## <a name="flow-of-code-testing"></a>Flödet av kod för testning
Det totala arbetsflödet för tester kod i ett datavetenskap ser ut så här: 

![Flödesschema för att testa koden](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Detaljerade steg

Använd följande steg för att ställa in och köra koden testning och en automatisk build via ett build-agenten och VSTS:

1. Skapa ett projekt i Visual Studio-skrivbordsprogram:

    ![”Skapa nytt projekt” skärm i Visual Studio](./media/code-test/create_project.PNG)

   När du har skapat projektet hittar du den i Solution Explorer i den högra rutan:
    
    ![Steg för att skapa ett projekt](./media/code-test/create_python_project_in_vs.PNG)

    ![Solution Explorer](./media/code-test/solution_explorer_in_vs.PNG)

3. Feed projektet koden i VSTS projektet kod databasen: 

    ![Projektet databasen](./media/code-test/create_repo.PNG)

4. Anta att du har gjort vissa data förberedelse arbete, till exempel datapåfyllning, funktionen tekniker och skapa etikettkolumner. Du vill kontrollera att din kod genererar resultatet som du förväntar dig. Här är kod som du kan använda för att testa om databearbetning koden fungerar korrekt:

    * Kontrollera att rätt kolumnnamn:
    
      ![Koden för att matcha kolumnnamn](./media/code-test/check_column_names.PNG)

    * Kontrollera att rätt svarsnivåer:

      ![Koden för att matcha nivåer](./media/code-test/check_response_levels.PNG)

    * Kontrollera att i svarsprocent är rimliga:

      ![Koden för i svarsprocent](./media/code-test/check_response_percentage.PNG)

    * Kontrollera saknas frekvensen för varje kolumn i data:
    
      ![Koden saknas hastigheten](./media/code-test/check_missing_rate.PNG)


5. När du har gjort databehandling och funktionen engineering arbete, och du har tränat vara användbar, se till att poängsätta du har tränat modellen nya datamängder korrekt. Du kan använda följande två tester för att kontrollera förutsägelse nivåer och distribution av etikettvärden:

    * Kontrollera förutsägelse nivåer:
    
      ![Koden för att kontrollera förutsägelse nivåer](./media/code-test/check_prediction_levels.PNG)

    * Kontrollera distributionen av förutsägelse värden:

      ![Koden för att kontrollera förutsägelse värden](./media/code-test/check_prediction_values.PNG)

6. Placera alla testa funktioner tillsammans i en Python-skript som heter **test_funcs.py**:

    ![Python-skriptet för test-funktioner](./media/code-test/create_file_test_func.PNG)


7. När testet koder är förberett kan du konfigurera testmiljön i Visual Studio.

   Skapa en Python-fil som heter **test1.py**. Skapa en klass som innehåller alla tester som du vill göra i den här filen. I följande exempel visas sex tester förberedd:
    
    ![Python-fil med en lista över tester i en klass](./media/code-test/create_file_test1_class.PNG)

8. Dessa tester kan identifieras automatiskt om du placerar **codetest.testCase** efter din klassnamn. Öppna Utforskaren i Test i det högra fönstret och välj **kör alla**. Alla tester körs sekventiellt och talar om testet lyckas eller inte.

    ![Kör tester på](./media/code-test/run_tests.PNG)

9. Kontrollera i koden i projektet databasen med hjälp av Git-kommandon. Senaste arbetet visas snart i VSTS.

    ![Git-kommandon för kontrollerar i kod](./media/code-test/git_check_in.PNG)

    ![Senaste arbete i VSTS](./media/code-test/git_check_in_most_recent_work.PNG)

10. Konfigurera automatisk build och testa i VSTS:

    a. I projektet databasen, Välj **bygga och släpper**, och välj sedan **+ ny** att skapa en ny build-process.

       ![Alternativ för att starta en ny build-process](./media/code-test/create_new_build.PNG)

    b. Följ anvisningarna för att välja din kod källplats, projektnamn, databasen och gren information.
    
       ![Källa, namn, databasen och gren information](./media/code-test/fill_in_build_info.PNG)

    c. Välj en mall. Eftersom det finns ingen mall för Python-projekt, börja med att välja **tom process**. 

       ![Lista över mallar och ”tom process” knappen](./media/code-test/start_empty_process_template.PNG)

    d. Bygga och välj agenten. Du kan välja standard här om du vill använda en DSVM avslutas skapar. Mer information om inställningen agenter finns [bygga och släpper agenter](https://docs.microsoft.com/en-us/vsts/build-release/concepts/agents/agents?view=vsts).
    
       ![Bygg- och agent val](./media/code-test/select_agent.PNG)

    e. Välj **+** i den vänstra rutan, lägga till en aktivitet för den här build-fasen. Eftersom vi kör skriptet Python **test1.py** Slutför alla kontroller genom den här aktiviteten använder ett PowerShell-kommando för att köra Python-kod.
    
       ![”Lägg till uppgifter” rutan med PowerShell som valts](./media/code-test/add_task_powershell.PNG)

    f. Fyll i informationen som krävs, till exempel namn och version av PowerShell i PowerShell-information. Välj **infogat skript** som typen. 
    
       I rutan under **infogat skript**, du kan skriva **python test1.py**. Se till att miljövariabeln har ställts in korrekt för Python. Om du behöver en annan version eller kernel av Python, kan du uttryckligen ange sökvägen som visas i figur: 
    
       ![PowerShell-information](./media/code-test/powershell_scripts.PNG)

    g. Välj **Spara & kö** att avsluta processen för definition av version.

       ![Knappen ”Spara & kö”](./media/code-test/save_and_queue_build_definition.PNG)

Nu varje gång en ny commit skickas till databasen koden startar skapar automatiskt. (Exemplet använder här vi master som databasen, men du kan definiera varje gren.) Processen körs den **test1.py** filen i agentdator se till att allt som definierats i koden körs korrekt. 

Om aviseringar är inställda på rätt sätt meddelas du via e-post när versionen har slutförts. Du kan också kontrollera statusen i VSTS build. Om det misslyckas söker du klientkontrollen bygga och ta reda på vilken del har brutits.

![E-postmeddelande för att bygga lyckas](./media/code-test/email_build_succeed.PNG)

![VSTS aviseringar build lyckades](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Nästa steg
* Finns det [UCI intäkter förutsägelse databasen](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) konkreta exempel på kontroller för datavetenskap scenarier.
* Följ föregående disposition och exempel UCI intäkter förutsägelse scenariot i dina datavetenskap projekt.

## <a name="references"></a>Referenser
* [TDSP](https://aka.ms/tdsp)
* [Visual Studio Tools för testning](https://www.visualstudio.com/vs/features/testing-tools/)
* [VSTS testning resurser](https://www.visualstudio.com/team-services/)
* [Datavetenskap virtuella datorer](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)