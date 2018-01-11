---
title: Azure Security Center Quickstart - publicera dina Windows-datorer till Security Center | Microsoft Docs
description: "Den här snabbstarten visar hur du etablerar Microsoft Monitoring Agent på en Windows-dator."
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
ms.openlocfilehash: 50cbbca9181d67bc41632a4650c76b9636a72356
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Snabbstartsguide: Publicera Windows-datorer till Azure Security Center
När du hanterar dina Azure-prenumerationer du kan aktivera Security Center för resurser som körs utanför Azure, för exempel på lokalt eller i andra moln genom att etablera Microsoft Monitoring Agent.

Den här snabbstarten visar hur du installerar Microsoft Monitoring Agent på en Windows-dator.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Du måste vara på Security Center Standard prisnivån innan du påbörjar den här snabbstartsguide. Se [Onboard din Azure-prenumeration till Security Center Standard](security-center-get-started.md) för uppgraderingen. Du kan försöka Security Center Standard utan kostnad för de första 60 dagarna.

## <a name="add-new-windows-computer"></a>Lägga till nya Windows-dator

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center - översikt** öppnas.

 ![Översikt över Security Center][2]

3. Välj under huvudmenyn Security Center **Onboarding till avancerad säkerhet**.
4. Välj **du vill lägga till Azure-datorer**.

   ![Publicera till avancerad säkerhet][3]

5. På **lägga till nya Azure-datorer**, visas en lista över logganalys-arbetsytor. Listan innehåller, om tillämpligt, standardarbetsytan skapade av Security Center när automatisk etablering har aktiverats. Välj den här arbetsytan eller en annan arbetsyta som du vill använda.

    ![Lägg till Azure-dator][4]

  Den **direkt Agent** blad öppnas med en länk för att ladda ned en Windows-agenten och nycklar för ditt arbetsyte-ID som ska användas i Konfigurera agenten.

6.  Välj den **ladda ned Windows Agent** länk som gäller för din typ av processor att ladda ned installationsfilen.

7.  Till höger om **arbetsyte-ID**, väljer du ikonen för kopiera och klistra in det ID: T i anteckningar.

8.  Till höger om **primärnyckel**, väljer du ikonen för kopiera och klistra in nyckeln i anteckningar.

## <a name="install-the-agent"></a>Installera agenten
Nu måste du installera den hämta filen på måldatorn.

1. Kopiera filen till måldatorn och kör installationsprogrammet.
2. På den **Välkommen** väljer **nästa**.
3. På den **licensvillkoren** , Läs licensvillkoren och sedan välja **jag accepterar**.
4. På den **målmappen** sidan, ändra eller behålla standardinstallationsmappen och väljer sedan **nästa**.
5. På den **installationsalternativ för Agent** väljer att ansluta agenten till Azure logganalys (OMS) och välj sedan **nästa**.
6. På den **Azure logganalys** och klistra in den **arbetsyte-ID** och **Arbetsytenyckel (primärnyckel)** som du kopierade i anteckningar i föregående procedur.
7. Om datorn ska rapportera till logganalys-arbetsytan i Azure Government molnet, väljer **Azure som tillhör amerikanska myndigheter** formuläret i **Azure-molnet** listrutan.  Om datorn måste kommunicera via en proxyserver till Log Analytics-tjänsten, Välj **Avancerat** och ange en URL och portnummer för proxyservern.
8. Välj **nästa** när du har slutfört att tillhandahålla de nödvändiga konfigurationsinställningarna.

  ![Installera agenten][5]

9. På den **klar att installera** granskar du dina val och välj sedan **installera**.
10. På den **konfigurationen slutförts** väljer **Slutför**

När du är klar visas **Microsoft Monitoring Agent** i **Kontrollpanelen**. Du kan granska konfigurationen av det och kontrollera att agenten är ansluten.

Mer information om att installera och konfigurera agenten finns [ansluta Windows-datorer](../log-analytics/log-analytics-agent-windows.md#install-the-agent-using-setup).

Nu kan du övervaka dina virtuella Azure-datorer och Azure-datorer på samma plats. Under **Compute**, du har en översikt över alla virtuella datorer och datorer samt rekommendationer. Varje kolumn representerar en uppsättning rekommendationer. Färgen som representerar den Virtuella datorns eller datorns aktuella säkerhetsstatus för denna rekommendation. Security Center hämtar också alla identifieringar för datorerna i säkerhetsaviseringar.

  ![Compute-bladet][6]

Det finns två typer av ikoner på den **Compute** bladet:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Icke-Azure-dator

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs kan du ta bort agenten från Windows-dator.

Ta bort agenten:

1. Öppna **Kontrollpanelen**.
2. Öppna **Program och funktioner**.
3. I **Program och funktioner** väljer du **Microsoft Monitoring Agent** och klickar på **Avinstallera**.

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten etablerats Microsoft Monitoring Agent på en Windows-dator. Mer information om hur du använder Security Center fortsätta kursen för att konfigurera en säkerhetsprincip och utvärdera säkerheten för dina resurser.

> [!div class="nextstepaction"]
> [Självstudier: Definiera och utvärdera säkerhetsprinciper](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
