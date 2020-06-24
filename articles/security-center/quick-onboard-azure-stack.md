---
title: Publicera Azure Stack virtuella datorer i Azure Security Center
description: Den här snabb starten visar hur du etablerar tillägget Azure Monitor, uppdatering och konfigurations hantering för virtuella datorer på en Azure Stack virtuella datorer.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: memildin
ms.openlocfilehash: 1f0a11616fe996f02d6464d23724156906176041
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771163"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Snabb start: publicera dina Azure Stack virtuella datorer i Security Center
När du har publicerat din Azure-prenumeration kan du aktivera Security Center för att skydda dina virtuella datorer som körs på Azure Stack genom att lägga till tillägget **Azure Monitor, uppdatering och konfigurations hantering** för virtuella datorer från Azure Stack Marketplace.

Den här snabb starten visar hur du lägger till tillägget **Azure Monitor, uppdatering och konfigurations hantering** för virtuella datorer på en virtuell dator (Linux och Windows stöds båda) som körs på Azure Stack.

## <a name="prerequisites"></a>Krav
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Du måste ha en Azure-prenumeration på Security Center standard nivån innan du startar den här snabb starten. Läs [Publicera din Azure-prenumeration till Security Center Standard](security-center-get-started.md) för instruktioner om uppgradering. Du kan prova Security Center standard nivån kostnads fritt i 30 dagar. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Välj din arbets yta i Azure Security Center

1. Logga in på [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. Välj **Security Center**på **Microsoft Azure** -menyn. **Security Center – Översikt** öppnas. 

   ![Översikt över Security Center][2]

3. På huvudmenyn i Security Center väljer du **Komma igång**.
4. Välj fliken **Kom igång**.

   ![Kom igång][3]

5. Klicka på **Konfigurera** under **Lägg till datorer som inte är Azure-datorer**. En lista över dina Log Analytics-arbetsytor visas. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbets ytan eller en annan arbets yta som du vill att den Azure Stack virtuella datorn ska rapportera säkerhets data till.

    ![Lägga till en dator som inte är en Azure-dator](./media/quick-onboard-windows-computer/non-azure.png)

   Bladet **Direct agent** öppnas med en länk för att ladda ned agenten och nycklarna för ditt arbetsyte-ID som ska användas för att konfigurera agenten.

   >[!NOTE]
   > Du behöver inte hämta agenten manuellt. Agenten installeras som ett VM-tillägg i stegen nedan.

6. Till höger om **arbetsyte-ID** väljer du ikonen för kopiera och klistra in ID:t i Anteckningar.

7. Till höger om **primärnyckel**, väljer du ikonen för kopiera och klistra in nyckeln i Anteckningar.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Lägg till tillägget för virtuell dator till dina befintliga Azure Stack virtuella datorer
Nu måste du lägga till tillägget **Azure Monitor, uppdatering och konfigurations hantering** för virtuella datorer på de virtuella datorerna som körs på din Azure Stack.

1. Logga in på din **Azure Stack** -Portal på en ny flik i webbläsaren.
2. Gå till sidan **virtuella datorer** och välj den virtuella dator som du vill skydda med Security Center. Information om hur du skapar en virtuell dator på Azure Stack finns i [den här snabb starten för virtuella Windows-datorer](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) eller [den här snabb starten för virtuella Linux-datorer](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Välj **Tillägg**. Listan över virtuella dator tillägg som är installerade på den här virtuella datorn visas.
4. Klicka på fliken **Lägg till** . Bladet **Ny resurs** öppnas och visar en lista över tillgängliga tillägg för virtuella datorer. 
5. Välj tillägget **Azure Monitor, uppdatering och konfigurations hantering** och klicka på **skapa**. Bladet **installation Extension** -konfiguration öppnas.

>[!NOTE]
> Om du inte ser tillägget **Azure Monitor, uppdatering och konfigurations hantering** som visas på din Marketplace, kan du kontakta din Azure Stack-operatör för att göra det tillgängligt.

6. På bladet **Installera tilläggs** konfiguration klistrar du in **arbets ytans ID** och den **arbets ytans nyckel (primär nyckel)** som du kopierade i anteckningar i föregående procedur.
7. När du är färdig med nödvändiga konfigurations inställningar klickar du på **OK**.
8. När tillägget har slutförts visas statusen som **etableringen har slutförts**. Det kan ta upp till en timme innan den virtuella datorn visas i Security Center portalen.

Mer information om hur du installerar och konfigurerar agenten för Windows finns i [ansluta Windows-datorer](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Information om Linux-felsökning av agent problem finns i [Felsöka Azure Log Analytics Linux-agenten](../azure-monitor/platform/agent-linux-troubleshoot.md).

Nu kan du övervaka dina virtuella Azure-datorer och datorer som inte är Azure-datorer på ett ställe. I Security Center Portal på Azure, under **Compute**, har du en översikt över alla virtuella datorer och datorer tillsammans med deras rekommendationer. Security Center även upptäcka all identifiering av de här datorerna i säkerhets aviseringar.

  ![Compute-bladet][6]

Det finns två typer av ikoner på **Compute**-bladet:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Datorer som inte är Azure-datorer 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Virtuell Azure-dator (Azure Stack virtuella datorer visas i den här gruppen)

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs kan du ta bort tillägget från den virtuella datorn via Azure Stack-portalen.

Ta bort tillägget:

1. Öppna **Azure Stack-portalen**.
2. Gå till sidan **virtuella datorer** och välj den virtuella dator som du vill ta bort tillägget från.
3. Välj **tillägg**, Välj tillägget **Microsoft. EnterpriseCloud. Monitoring**.
4. Klicka på **Avinstallera**och bekräfta valet genom att klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du allokerat tillägget Azure Monitor, uppdatering och konfigurations hantering på en virtuell dator som körs på Azure Stack. Om du vill läsa mer om hur du använder Security Center fortsätter du till självstudien om konfiguration av en säkerhetsprincip och utvärderar resursers säkerhet.

> [!div class="nextstepaction"]
> [Självstudie: Definiera och utvärdera säkerhetsprinciper](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
