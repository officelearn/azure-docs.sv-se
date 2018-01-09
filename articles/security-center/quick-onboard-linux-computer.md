---
title: Azure Security Center Quickstart - publicera Linux-datorer till Security Center | Microsoft Docs
description: "Den här snabbstarten visar hur du integrerar dina Linux-datorer till Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: terrylan
ms.openlocfilehash: 365afd2199b1b8b2e70d882f6a729384edbdffbc
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Snabbstartsguide: Publicera Linux-datorer till Azure Security Center
När du hanterar dina Azure-prenumerationer du kan aktivera Security Center för Linux-resurser som körs utanför Azure, för exempel på lokalt eller i andra moln genom att etablera Linux-agenten.

Den här snabbstarten visar hur du installerar Linux-agenten på en Linux-dator.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Du måste vara på Security Center Standard prisnivån innan du påbörjar den här snabbstartsguide. Se [Onboard din Azure-prenumeration till Security Center Standard](security-center-get-started.md) för uppgraderingen. Du kan försöka Security Center Standard utan kostnad för de första 60 dagarna.

## <a name="add-new-linux-computer"></a>Lägg till nya Linux-datorn

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center - översikt** öppnas.

 ![Översikt över Security Center][2]

3. Välj under huvudmenyn Security Center **Onboarding till avancerad säkerhet**.
4. Välj **du vill lägga till Azure-datorer**.
   ![Publicera till avancerad säkerhet][3]

5. På **lägga till nya Azure-datorer**, visas en lista över logganalys-arbetsytor. Listan innehåller, om tillämpligt, standardarbetsytan skapade av Security Center när automatisk etablering har aktiverats. Välj den här arbetsytan eller en annan arbetsyta som du vill använda.

    ![Lägg till Azure-dator][4]

6.  På den **direkt Agent** sidan under **ladda ned och publicera AGENT för LINUX**, Välj den **kopiera** för att kopiera den *wget* kommando.

7.  Öppna Anteckningar och klistra in det här kommandot. Spara filen på en plats som kan nås från Linux-dator.

## <a name="install-the-agent"></a>Installera agenten

1.  Öppna filen som tidigare sparats på Linux-dator. Markera hela innehållet från, kopia, öppna konsolen terminal och klistra in kommandot.
2.  När installationen är klar kan du kontrollera att den *omsagent* har installerats genom att köra den *pgrep* kommando. Kommandot returnerar den *omsagent* PID (Process-ID) enligt nedan:

  ![Installera agenten][5]

I Security Center-Agent för Linux-loggarna finns på: */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Loggar för agent][6]

Efter en tid kan det ta upp till 30 minuter, den nya Linux-datorn visas i Security Center.

Nu kan du övervaka dina virtuella Azure-datorer och Azure-datorer på samma plats. Under **Compute**, du har en översikt över alla virtuella datorer och datorer samt rekommendationer. Varje kolumn representerar en uppsättning rekommendationer. Färgen som representerar den Virtuella datorns eller datorns aktuella säkerhetsstatus för denna rekommendation. Security Center hämtar också alla identifieringar för datorerna i säkerhetsaviseringar.

  ![Compute-bladet][7] det finns två typer av ikoner på den **Compute** bladet:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Icke-Azure-dator

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Rensa resurser
När de inte längre behövs kan du ta bort agenten från Linux-datorn.

Ta bort agenten:

1. Hämta Linux-agenten [universal skriptet](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) till datorn.
2. Kör .sh samlingsfilen med den *--Rensa* argumentet på datorn som helt tar bort agenten och dess konfiguration.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Nästa steg
I den här snabbstartsguide etablerats agenten på en Linux-dator. Mer information om hur du använder Security Center fortsätta kursen för att konfigurera en säkerhetsprincip och utvärdera säkerheten för dina resurser.

> [!div class="nextstepaction"]
> [Självstudier: Definiera och utvärdera säkerhetsprinciper](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
