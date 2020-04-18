---
title: Aktivera Azure Automation Start/Stop virtuella datorer under starttimmarslösning
description: I den här artikeln beskrivs hur du aktiverar Azure Automation Start/Stop VM-lösningen för dina virtuella Azure-datorer.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 2414567b74232d634fa0a34202691a8e43ae6135
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604752"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Aktivera Azure Automation Start/Stop VMs-lösning

Utför följande steg för att lägga till **start-/stopp-virtuella datorer under ledig tid** i ett nytt eller befintligt Automation-konto och länkad Log Analytics-arbetsyta. När du har slutfört introduktionsprocessen konfigurerar du variablerna för att anpassa lösningen.

>[!NOTE]
>Om du vill använda den här lösningen med klassiska virtuella datorer behöver du ett klassiskt kör som-konto, som inte skapas som standard. Instruktioner om hur du skapar ett klassiskt kör som-konto finns i [Skapa ett klassiskt kör som-konto](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Aktivera lösning

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter och välj **Automation-konton**.

3. På sidan Automation-konton väljer du ditt Automation-konto i listan.

4. Välj **Start/Stoppa virtuell dator** i Automation-kontot under **Relaterade resurser**. Härifrån kan du klicka på **Läs mer om och aktivera lösningen**. Om du redan har distribuerat en Start/Stop VM-lösning kan du välja den genom att klicka på **Hantera lösningen** och hitta den i listan.

   ![Aktivera från automationskonto](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Du kan också skapa den var som helst i Azure-portalen genom att klicka på **Skapa en resurs**. Skriv ett nyckelord som **Start** eller **Start/Stopp**på Marketplace-sidan. När du börjar skriva filtreras listan baserat på det du skriver. Du kan också skriva in ett eller flera nyckelord från lösningens fullständiga namn och sedan trycka på Retur. Välj **Start/Stoppa virtuella datorer under lediga timmar** från sökresultaten.

5. På sidan **Start/Stop-datorer under ledig tid** för den valda lösningen granskar du sammanfattningsinformationen och klickar sedan på **Skapa**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

6. Sidan Lägg till lösning visas. Du uppmanas att konfigurera lösningen innan du kan importera den till din Automation-prenumeration.

   ![Sidan Lägg till lösning för VM-hantering](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. Välj **Arbetsyta**på sidan Lägg till lösning . Välj en Log Analytics-arbetsyta som är länkad till samma Azure-prenumeration som Automation-kontot finns i. Om du inte har någon arbetsyta väljer du **Skapa ny arbetsyta**. Gör följande på sidan Logganalysarbetsyta:

   - Ange ett namn för den nya log analytics-arbetsytan, till exempel **ContosoLAWorkspace**.
   - Välj en **prenumeration** som du vill länka till genom att välja från listrutan, om den valda standardinställningen inte är lämplig.
   - För **Resursgrupp**kan du skapa en ny resursgrupp eller välja en befintlig.
   - Välj en **plats**.
   - Välj en **prisnivå**. Välj alternativet **Per GB (fristående).** Azure Monitor-loggar har uppdaterade [priser](https://azure.microsoft.com/pricing/details/log-analytics/) och per GB-nivå är det enda alternativet.

   > [!NOTE]
   > När du aktiverar lösningar stöds endast vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto.
   >
   > En lista över mappningspar som stöds finns i [Regionmappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

8. När du har lämnat den information som krävs på arbetsytan Log Analytics klickar du på **Skapa**. Du kan spåra dess förlopp under **Meddelanden** från menyn, som returnerar dig till sidan Lägg till lösning när du är klar.

9. Välj **Automation-konto**på sidan Lägg till lösning . Om du skapar en ny Log Analytics-arbetsyta kan du skapa ett nytt Automation-konto som ska kopplas till det, eller välja ett befintligt Automation-konto som inte redan är länkat till en Log Analytics-arbetsyta. Välj ett befintligt Automation-konto eller klicka på **Skapa ett Automation-konto**och ange följande information på sidan Lägg till automation-konto:
 
   - I fältet **namn** anger du namnet på Automation-kontot.

     Alla andra alternativ fylls i automatiskt baserat på den valda logganalysarbetsytan. Det går inte att ändra dessa alternativ. Ett Azure kör som-konto är standardmetoden för autentisering för runbooks som ingår i den här lösningen. När du har klickat på **OK**valideras konfigurationsalternativen och Automation-kontot skapas. Du kan spåra förloppet under **Meddelanden** på menyn.

10. Slutligen väljer du **Konfiguration**på sidan Lägg till lösning . Sidan Parametrar visas.

    ![Sidan Parametrar för lösning](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Här uppmanas du att:
  
   - Ange **namn på målresursgrupper**. Dessa värden är resursgruppsnamn som innehåller virtuella datorer som ska hanteras av den här lösningen. Du kan ange mer än ett namn och separera varje med hjälp av ett kommatecken (värden är inte skiftlägeskänsliga). Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen. Det här värdet lagras i **variablerna External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames.**
  
   - Ange **den vm-undantagslista (strängen)**. Det här värdet är namnet på en eller flera virtuella datorer från målresursgruppen. Du kan ange mer än ett namn och separera varje med hjälp av ett kommatecken (värden är inte skiftlägeskänsliga). Det går att använda ett jokertecken. Det här värdet lagras i **variabeln External_ExcludeVMNames.**
  
   - Välj ett **schema**. Välj ett datum och en tid för schemat. Ett återkommande dagligt schema skapas från och med den tid du valde. Det går inte att välja en annan region. Information om hur du konfigurerar schemat till den specifika tidszonen efter att lösningen har konfigurerats finns i [Ändra start- och avstängningsschemat](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Om du vill ta emot **e-postaviseringar** från en åtgärdsgrupp godkänner du standardvärdet **Ja** och anger en giltig e-postadress. Om du väljer **Nej** men bestämmer dig vid ett senare tillfälle att du vill få e-postmeddelanden kan du uppdatera [åtgärdsgruppen](../azure-monitor/platform/action-groups.md) som skapas med giltiga e-postadresser avgränsade med ett kommatecken. Du måste också aktivera följande varningsregler:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Standardvärdet för **Mål ResourceGroup-namn** är en **&ast;**. Detta är inriktat på alla virtuella datorer i en prenumeration. Om du inte vill att lösningen ska inriktas på alla virtuella datorer i din prenumeration måste det här värdet uppdateras till en lista över resursgruppsnamn innan schemana aktiveras.

11. När du har konfigurerat de första inställningarna som krävs för lösningen klickar du på **OK** för att stänga sidan Parametrar och välj **Skapa**. 

När alla inställningar har validerats distribueras lösningen till din prenumeration. Den här processen kan ta flera sekunder att slutföra och du kan spåra dess förlopp under **Meddelanden** från menyn.

> [!NOTE]
> Om du har en Azure Cloud Solution Provider-prenumeration (Azure CSP) går du i ditt Automation-konto i ditt Automation-konto till **Variabler** under **Delade resurser** och anger [**External_EnableClassicVMs**](automation-solution-vm-management.md#variables) variabeln till **False**. Detta hindrar lösningen från att leta efter klassiska vm-resurser.

## <a name="next-steps"></a>Nästa steg

Nu när du har lösningen aktiverad kan du [konfigurera](automation-solution-vm-management-config.md) den för att stödja dina vm-hanteringskrav.