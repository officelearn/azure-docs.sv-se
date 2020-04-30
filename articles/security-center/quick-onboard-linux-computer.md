---
title: Publicera Linux-datorer till Azure Security Center | Microsoft Docs
description: I den här snabbstarten får du veta hur du publicerar dina Linux-datorer till Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: memildin
ms.openlocfilehash: 3b7181550fd76f158ec04e9779f4301bd74484e1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80435968"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Snabbstart: Publicera Linux-datorer till Azure Security Center
När du har publicerat dina Azure-prenumerationer kan du aktivera Security Center för Linux-resurser som körs utanför Azure, till exempel lokalt eller i andra moln, genom att tillhandahålla en agent. Agenten kallas för Log Analytics agent, men den kallas även OMS-agenten.

Den här snabb starten visar hur du installerar agenten på en Linux-dator.

## <a name="prerequisites"></a>Krav
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Du måste vara på Security Center standard pris nivån innan du påbörjar den här snabb starten. Läs [Publicera din Azure-prenumeration till Security Center Standard](security-center-get-started.md) för instruktioner om uppgradering. Du kan prova Security Center standard utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-linux-computer"></a>Lägga till ny Linux-dator

1. Logga in på [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. Välj **Security Center**på **Microsoft Azure** -menyn. **Security Center – Översikt** öppnas.

   ![Översikt över Security Center][2]

3. På huvudmenyn i Security Center väljer du **Komma igång**.
4. Välj fliken **Kom igång** . ![kom igång][3]

5. Klicka på **Konfigurera** under **Lägg till datorer som inte är Azure-datorer** så visas en lista över dina Log Analytics-arbetsytor. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbetsytan eller någon annan arbetsyta som du vill använda.

    ![Lägga till en dator som inte är en Azure-dator](./media/quick-onboard-linux-computer/non-azure.png)

6. På sidan **Direktagent**, under **LADDA NED OCH INTEGRERA AGENT FOR LINUX** väljer du **kopieringsknappen** för att kopiera kommandot *wget*.

7. Öppna Anteckningar och klistra in det här kommandot. Spara den här filen på en plats som kan nås från din Linux-dator.

## <a name="install-the-agent"></a>Installera agenten

1. På Linux-datorn öppnar du filen du sparade tidigare. Markera hela innehållet, kopiera, öppna en terminalkonsol och klistra in kommandot.
2. När installationen är klar kan du kontrollera att *omsagent* är installerat genom att köra kommandot *pgrep*. Kommandot returnerar PIP (process-ID:t) *omsagent* som det visas nedan:

   ![Installera agenten][5]

Loggarna för agenten finns på: */var/opt/Microsoft/omsagent/\<arbetsyte-ID>/log/*

  ![Loggar för agent][6]

Efter en stund visas den nya Linux-datorn i Security Center. Det kan ta upp till 30 minuter.

Nu kan du övervaka dina virtuella Azure-datorer och datorer som inte är Azure-datorer på ett ställe. Under **Compute** (Beräkna) har du en översikt över alla virtuella datorer och datorer tillsammans med rekommendationer. I varje kolumn finns en typ av rekommendationer. Färgen representerar den virtuella datorns eller datorns aktuella säkerhetsstatus för den rekommendationen. Security Center visar också eventuella identifieringar för dessa datorer i Säkerhetsaviseringar.

  ![Compute-blad][7] Det finns två typer av ikoner på **Compute**-bladet:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Datorer som inte är Azure-datorer

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Rensa resurser
När den inte längre behövs kan du ta bort agenten från Linux-datorn.

Så här tar du bort agenten:

1. Ladda ned Linux-agenten [universal script](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) till datorn.
2. Kör paketets SH-fil med argumentet *--purge* på datorn, som fullständigt tar bort agenten och dess konfiguration.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Nästa steg
I den här snabb starten etablerade du agenten på en Linux-dator. Om du vill läsa mer om hur du använder Security Center fortsätter du till självstudien om konfiguration av en säkerhetsprincip och utvärderar resursers säkerhet.

> [!div class="nextstepaction"]
> [Självstudie: Definiera och utvärdera säkerhetsprinciper](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/get-started.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
