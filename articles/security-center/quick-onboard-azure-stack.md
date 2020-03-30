---
title: Inbyggda virtuella Azure Stack-datorer till Azure Security Center
description: Den här snabbstarten visar hur du etablerar virtuellt datortillägg för Azure Monitor, Update och Configuration Management på en virtuell Azure Stack-datorer.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73686523"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Snabbstart: Ombord på dina virtuella Azure Stack-datorer till Security Center
När du har lagt till din Azure-prenumeration kan du aktivera Security Center för att skydda dina virtuella datorer som körs på Azure Stack genom att lägga till tillägget **Azure Monitor, Update och Configuration Management** från Azure Stack marketplace.

Den här snabbstarten visar hur du lägger till tillägget **Azure Monitor, Update och Configuration Management** på en virtuell dator (Linux och Windows stöds båda) som körs på Azure Stack.

## <a name="prerequisites"></a>Krav
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Du måste ha en Azure-prenumeration på Security Centers standardnivå innan du startar den här snabbstarten. Läs [Publicera din Azure-prenumeration till Security Center Standard](security-center-get-started.md) för instruktioner om uppgradering. Du kan prova Security Center Standard-nivån utan kostnad i 30 dagar. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Välj arbetsyta i Azure Security Center

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. Välj **Security Center**på **Microsoft Azure-menyn** . **Security Center – Översikt** öppnas. 

   ![Översikt över Security Center][2]

3. På huvudmenyn i Security Center väljer du **Komma igång**.
4. Välj fliken **Kom igång**.

   ![Komma igång][3]

5. Klicka på **Konfigurera** under **Lägg till datorer som inte är Azure-datorer**. En lista över dina Log Analytics-arbetsytor visas. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbetsytan eller en annan arbetsyta som du vill att den virtuella Azure Stack-datorn ska rapportera säkerhetsdata till.

    ![Lägga till en dator som inte är en Azure-dator](./media/quick-onboard-windows-computer/non-azure.png)

   Bladet **Direct Agent** öppnas med en länk för att hämta agenten och nycklarna till ditt arbetsyte-ID som ska användas för att konfigurera agenten.

   >[!NOTE]
   > Du behöver INTE ladda ner agenten manuellt. Agenten installeras som ett VM-tillägg i stegen nedan.

6. Till höger om **arbetsyte-ID** väljer du ikonen för kopiera och klistra in ID:t i Anteckningar.

7. Till höger om **primärnyckel**, väljer du ikonen för kopiera och klistra in nyckeln i Anteckningar.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Lägg till tillägget för den virtuella datorn i dina befintliga virtuella Azure Stack-datorer
Nu måste du lägga till tillägget **azure monitor, update och configuration management** till de virtuella datorer som körs på din Azure Stack.

1. Logga in på din **Azure Stack-portal** på en ny webbläsarflik.
2. Gå till sidan **Virtuella datorer** och välj den virtuella dator som du vill skydda med Security Center. Information om hur du skapar en virtuell dator på Azure Stack finns i [den här snabbstarten för virtuella Windows-datorer](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) eller [den här snabbstarten för virtuella Linux-datorer](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Välj **Tillägg**. Listan över tillägg för virtuella datorer som är installerade på den här virtuella datorn visas.
4. Klicka på fliken **Lägg till.** Menybladet **Ny resurs** öppnas och visar listan över tillgängliga tillägg för virtuella datorer. 
5. Välj tillägget **Azure Monitor, Update och Configuration Management** och klicka på **Skapa**. Konfigurationsbladet **för installationstillägg** öppnas.

>[!NOTE]
> Om du inte ser tillägget **Azure Monitor, Update och Configuration Management** som anges på din marknadsplats kontaktar du din Azure Stack-operatör för att göra den tillgänglig.

6. Klistra in **arbetsyte-ID** och **arbetsytanyckel (primärnyckel)** som du kopierade till Anteckningar i föregående procedur på bladet **Installera tilläggskonfiguration.**
7. När du är klar med de nödvändiga konfigurationsinställningarna klickar du på **OK**.
8. När tilläggsinstallationen är klar visas dess status när **etableringen lyckades**. Det kan ta upp till en timme innan den virtuella datorn visas i Security Center-portalen.

Mer information om hur du installerar och konfigurerar agenten för Windows finns i [Ansluta Windows-datorer](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Felsökning av agentproblem för Linux finns i [Felsöka Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

Nu kan du övervaka dina virtuella Azure-datorer och datorer som inte är Azure-datorer på ett ställe. I Security Center-portalen på Azure under **Beräkna**har du en översikt över alla virtuella datorer och datorer tillsammans med deras rekommendationer. Security Center visar också alla identifieringar för dessa datorer i säkerhetsvarningar.

  ![Compute-bladet][6]

Det finns två typer av ikoner på **Compute**-bladet:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Datorer som inte är Azure-datorer 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM (Azure Stack virtuella datorer visas i den här gruppen)

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs kan du ta bort tillägget från den virtuella datorn via Azure Stack-portalen.

Så här tar du bort tillägget:

1. Öppna **Azure Stack Portal**.
2. Gå till sidan **Virtuella datorer** och välj den virtuella dator som du vill ta bort tillägget från.
3. Välj **Tillägg**väljer du tillägget **Microsoft.EnterpriseCloud.Monitoring**.
4. Klicka på **Avinstallera**och bekräfta valet genom att klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du etablerat azure monitor-, uppdaterings- och konfigurationshanteringstillägget på en virtuell dator som körs på Azure Stack. Om du vill läsa mer om hur du använder Security Center fortsätter du till självstudien om konfiguration av en säkerhetsprincip och utvärderar resursers säkerhet.

> [!div class="nextstepaction"]
> [Självstudie: Definiera och utvärdera säkerhetsprinciper](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
