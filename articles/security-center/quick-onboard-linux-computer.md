---
title: Azure Security Center-snabbstart – Publicera dina Linux-datorer till Security Center | Microsoft Docs
description: I den här snabbstarten får du veta hur du publicerar dina Linux-datorer till Security Center.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: 05e4bed0f9b4dfb6d1879408085447ef53db8655
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
ms.locfileid: "29530174"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Snabbstart: Publicera Linux-datorer till Azure Security Center
När du har publicerat dina Azure-prenumerationer kan du aktivera Security Center för Linux-resurser som körs utanför Azure, till exempel lokalt eller i andra moln, genom att etablera Linux-agenten.

I den här snabbstarten får du lära dig att installera Linux-agenten på en Linux-dator.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Innan du startar den här snabbstarten måste du ha standardnivån i Security Center. Läs [Publicera din Azure-prenumeration till Security Center Standard](security-center-get-started.md) för instruktioner om uppgradering. Du kan prova Security Center Standard utan kostnad under de första 60 dagarna.

## <a name="add-new-linux-computer"></a>Lägga till ny Linux-dator

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center – Översikt** öppnas.

 ![Översikt över Security Center][2]

3. På huvudmenyn i Security Center väljer du **Kom igång med avancerad säkerhet**.
4. Välj **Vill du lägga till datorer som inte är Azure-datorer?**.
   ![Kom igång med avancerad säkerhet][3]

5. Vid **Lägg till datorer som inte är Azure-datorer** visas en lista över dina Log Analytics-arbetsytor. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbetsytan eller någon annan arbetsyta som du vill använda.

    ![Lägga till en dator som inte är en Azure-dator][4]

6.  På sidan **Direktagent**, under **LADDA NED OCH INTEGRERA AGENT FOR LINUX** väljer du **kopieringsknappen** för att kopiera kommandot *wget*.

7.  Öppna Anteckningar och klistra in det här kommandot. Spara den här filen på en plats som kan nås från din Linux-dator.

## <a name="install-the-agent"></a>Installera agenten

1.  På Linux-datorn öppnar du filen du sparade tidigare. Markera hela innehållet, kopiera, öppna en terminalkonsol och klistra in kommandot.
2.  När installationen är klar kan du kontrollera att *omsagent* är installerat genom att köra kommandot *pgrep*. Kommandot returnerar PIP (process-ID:t) *omsagent* som det visas nedan:

  ![Installera agenten][5]

Loggarna för Security Center-agenten för Linux finns på: */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Loggar för agent][6]

Efter en stund visas den nya Linux-datorn i Security Center. Det kan ta upp till 30 minuter.

Nu kan du övervaka dina virtuella Azure-datorer och datorer som inte är Azure-datorer på ett ställe. Under **Compute** har du en översikt över alla virtuella datorer och datorer tillsammans med rekommendationer. I varje kolumn finns en typ av rekommendationer. Färgen representerar den virtuella datorns eller datorns aktuella säkerhetsstatus för den rekommendationen. Security Center visar också eventuella identifieringar för dessa datorer i Säkerhetsaviseringar.

  ![Compute-blad][7] Det finns två typer av ikoner på **Compute**-bladet:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Icke-Azure-dator

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Rensa resurser
När den inte längre behövs kan du ta bort agenten från Linux-datorn.

Så här tar du bort agenten:

1. Ladda ned Linux-agenten [universal script](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) till datorn.
2. Kör paketets SH-fil med argumentet *--purge* på datorn, som fullständigt tar bort agenten och dess konfiguration.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten etablerade du agenten på en Linux-dator. Om du vill läsa mer om hur du använder Security Center fortsätter du till självstudien om konfiguration av en säkerhetsprincip och utvärderar resursers säkerhet.

> [!div class="nextstepaction"]
> [Självstudie: Definiera och utvärdera säkerhetsprinciper](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
