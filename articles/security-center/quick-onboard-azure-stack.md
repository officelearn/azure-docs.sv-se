---
title: Azure Security Center-Snabbstart – publicera dina Azure Stack-datorer till Security Center | Microsoft Docs
description: Den här snabbstarten visar hur du etablerar tillägget Azure Monitor-, Update- och konfigurationshantering för virtuell dator på en Azure Stack-datorer.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
editor: ''
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 7a630acee079301b95e7e05f5c5333dd116abb68
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59563802"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>Snabbstart:  Publicera dina Azure Stack-datorer till Security Center
När du har publicerat dina Azure-prenumeration kan du aktivera Security Center för att skydda dina virtuella datorer som körs på Azure Stack genom att lägga till den **Azure Monitor-, Update- och konfigurationshantering** tillägg för virtuell dator från den Azure Stack marketplace.

Den här snabbstarten visar hur du lägger till den **Azure Monitor-, Update- och konfigurationshantering** tillägg för virtuell dator på en virtuell dator (Linux och Windows både stöds) som körs på Azure Stack.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Du måste ha en Azure-prenumeration på Security Center Standard-nivån innan du påbörjar den här snabbstarten. Läs [Publicera din Azure-prenumeration till Security Center Standard](security-center-get-started.md) för instruktioner om uppgradering. Du kan prova Security Center Standard-nivån utan kostnad under 30 dagar. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Välj din arbetsyta i Azure Security Center

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center – Översikt** öppnas. 

   ![Översikt över Security Center][2]

3. På huvudmenyn i Security Center väljer du **Komma igång**.
4. Välj fliken **Kom igång**.

   ![Kom igång][3]

5. Klicka på **Konfigurera** under **Lägg till datorer som inte är Azure-datorer**. En lista över dina Log Analytics-arbetsytor visas. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbetsytan eller någon annan arbetsyta som du vill att Azure Stack-VM till rapporten säkerhetsdata till.

    ![Lägga till en dator som inte är en Azure-dator](./media/quick-onboard-windows-computer/non-azure.png)

   Den **Direktagent** bladet öppnas med en länk för att ladda ned agenten och nycklar för arbetsyte-ID för användning vid konfigureringen av agenten.

   >[!NOTE]
   > Du behöver inte ladda ned agenten manuellt. Agenten installeras som ett VM-tillägg i stegen nedan.

6. Till höger om **arbetsyte-ID** väljer du ikonen för kopiera och klistra in ID:t i Anteckningar.

7. Till höger om **primärnyckel**, väljer du ikonen för kopiera och klistra in nyckeln i Anteckningar.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Lägga till tillägget för virtuell dator i dina befintliga Azure Stack-datorer
Du måste nu lägga till den **Azure Monitor-, Update- och konfigurationshantering** tillägg för virtuell dator till de virtuella datorerna som körs på Azure Stack.

1. I en ny webbläsarflik, logga in på din **Azure Stack** portal.
2. Gå till den **virtuella datorer** väljer du den virtuella dator som du vill skydda med Security Center. Information om hur du skapar en virtuell dator på Azure Stack finns i [den här snabbstarten för Windows-datorer](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) eller [den här snabbstarten för virtuella Linux-datorer](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Välj **Tillägg**. Listan över tillägg för virtuell dator som installerats på den här virtuella datorn visas.
4. Klicka på den **Lägg till** fliken. Den **ny resurs** appmenyns blad öppnas och visar en lista över tillgängliga VM-tillägg. 
5. Välj den **Azure Monitor-, Update- och konfigurationshantering** tillägget och klickar på **skapa**. Den **installera tillägget** konfigurationsbladet öppnas.

>[!NOTE]
> Om du inte ser den **Azure Monitor-, Update- och konfigurationshantering** tillägg som anges i din marketplace kontaktar du din Azure Stack-operatör att göra den tillgänglig.

6. På den **installera tillägget** konfigurationsbladet, klistra in den **arbetsyte-ID** och **Arbetsytenyckel (primärnyckel)** som du kopierade till anteckningar i föregående procedur.
7.  När du är klar tillhandahåller de nödvändiga konfigurationsinställningarna, klickar du på **OK**.
8. När installationen av tillägget har slutförts visas statusen som **etableringen är klar**. Det kan ta upp till en timme för den virtuella datorn visas i Security Center-portalen.

Mer information om installation och konfiguration av agenten för Windows finns [ansluta Windows-datorer](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Linux felsökning av problem med agentens finns i [felsöka Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md).

Nu kan du övervaka dina virtuella Azure-datorer och datorer som inte är Azure-datorer på ett ställe. I Security Center portal på Azure, under **Compute**, har du en överblick över alla virtuella datorer och datorer tillsammans med sina rekommendationer. Security Center hämtar också alla identifiering för datorerna i säkerhetsaviseringar.

  ![Compute-bladet][6]

Det finns två typer av ikoner på **Compute**-bladet:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Datorer som inte är Azure-datorer 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Virtuell Azure-dator (virtuella datorer i Azure Stack visas i den här gruppen)

## <a name="clean-up-resources"></a>Rensa resurser
När den inte längre behövs kan du ta bort tillägget från den virtuella datorn via Azure Stack-portalen.

Ta bort tillägget:

1. Öppna den **Azure Stack-portalen**.
2. Gå till **virtuella datorer** väljer du den virtuella datorn från vilken du vill ta bort tillägget.
3. Välj **tillägg**, markerar du tillägget **Microsoft.EnterpriseCloud.Monitoring**.
4. Klicka på **avinstallera**, och bekräfta valet genom att klicka på **Ja**.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har etablerat du Azure Monitor-, Update- och konfigurationshantering tillägget på en virtuell dator som körs på Azure Stack. Om du vill läsa mer om hur du använder Security Center fortsätter du till självstudien om konfiguration av en säkerhetsprincip och utvärderar resursers säkerhet.

> [!div class="nextstepaction"]
> [Självstudier: Definiera och utvärdera säkerhetsprinciper](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
