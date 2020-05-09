---
title: Aktivera Azure Automation Starta/stoppa virtuella datorer när de inte används-lösning
description: Den här artikeln beskriver hur du aktiverar Azure Automation starta/stoppa virtuell dator vid låg belastnings lösning för dina virtuella Azure-datorer.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: ed9937576334fcba6707f2737e92c3cddce2d7f7
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864223"
---
# <a name="enable-azure-automation-startstop-vms-solution"></a>Aktivera Azure Automation starta/stoppa VM-lösning

Utför följande steg för att lägga till **Starta/stoppa virtuella datorer när de inte används** -lösningen i ett nytt eller befintligt Automation-konto och länkade Log Analytics-arbetsytan. När du har slutfört onboarding-processen konfigurerar du variablerna för att anpassa lösningen.

>[!NOTE]
>Om du vill använda den här lösningen med klassiska virtuella datorer behöver du ett klassiskt kör som-konto, som inte skapas som standard. Anvisningar om hur du skapar ett klassiskt kör som-konto finns i [skapa ett klassiskt kör som-konto](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="enable-solution"></a>Aktivera lösning

1. Logga in på Azure- [portalen](https://portal.azure.com).

2. Sök efter och välj **Automation-konton**.

3. På sidan Automation-konton väljer du ditt Automation-konto i listan.

4. Från Automation-kontot väljer du **Starta/stoppa virtuell dator** under **relaterade resurser**. Härifrån kan du klicka på **Läs mer om och aktivera lösningen**. Om du redan har distribuerat en lösning för att starta/stoppa virtuell dator kan du välja den genom att klicka på **Hantera lösningen** och söka efter den i listan.

   ![Aktivera från Automation-konto](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Du kan också skapa den från var som helst i Azure Portal genom att klicka på **skapa en resurs**. På sidan Marketplace skriver du ett nyckelord, till exempel **Starta** eller **Starta/stoppa**. När du börjar skriva filtreras listan baserat på det du skriver. Alternativt kan du skriva in ett eller flera nyckelord från det fullständiga namnet på lösningen och trycka på RETUR. Välj **Starta/stoppa virtuella datorer när de inte används** från Sök resultaten.

5. På sidan **Starta/stoppa virtuella datorer när de inte används** för den valda lösningen granskar du sammanfattnings informationen och klickar sedan på **skapa**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

6. Sidan Lägg till lösning visas. Du uppmanas att konfigurera lösningen innan du kan importera den till din Automation-prenumeration.

   ![Sidan för att lägga till hantering av virtuella datorer](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

7. På sidan Lägg till lösning väljer du **arbets yta**. Välj en Log Analytics arbets yta som är länkad till samma Azure-prenumeration som Automation-kontot finns i. Om du inte har en arbets yta väljer du **Skapa ny arbets yta**. Utför följande steg på sidan Log Analytics arbets yta:

   - Ange ett namn för den nya Log Analytics-arbetsytan, till exempel **ContosoLAWorkspace**.
   - Välj en **prenumeration** som du vill länka till genom att välja i list rutan, om det valda standardvärdet inte är tillämpligt.
   - För **resurs grupp**kan du skapa en ny resurs grupp eller välja en befintlig.
   - Välj en **plats**.
   - Välj en **pris nivå**. Välj alternativet **per GB (fristående)** . Azure Monitors loggar har uppdaterat [priser](https://azure.microsoft.com/pricing/details/log-analytics/) och nivån per GB är det enda alternativet.

   > [!NOTE]
   > När du aktiverar lösningar stöds endast vissa regioner för att länka en Log Analytics-arbetsyta och ett Automation-konto.
   >
   > En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

8. När du har angett nödvändig information på sidan Log Analytics arbets yta klickar du på **skapa**. Du kan följa förloppet under **meddelanden** på menyn, som återgår till sidan Lägg till lösning när du är färdig.

9. På sidan Lägg till lösning väljer du **Automation-konto**. Om du skapar en ny Log Analytics arbets yta kan du skapa ett nytt Automation-konto som ska associeras med det, eller välja ett befintligt Automation-konto som inte redan är kopplat till en Log Analytics arbets yta. Välj ett befintligt Automation-konto eller klicka på **skapa ett Automation-konto**och ange följande information på sidan Lägg till Automation-konto:
 
   - I fältet **namn** anger du namnet på Automation-kontot.

     Alla andra alternativ fylls i automatiskt baserat på den Log Analytics arbets ytan som valts. Dessa alternativ kan inte ändras. Ett Azure kör som-konto är standardmetoden för autentisering för runbooks som ingår i den här lösningen. När du klickar på **OK**verifieras konfigurations alternativen och automation-kontot skapas. Du kan spåra förloppet under **Meddelanden** på menyn.

10. På sidan Lägg till lösning väljer du till sist **konfiguration**. Sidan parametrar visas.

    ![Parameter sidan för lösningen](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Här uppmanas du att:
  
   - Ange **mål ResourceGroup namn**. Dessa värden är resurs grupp namn som innehåller virtuella datorer som ska hanteras av den här lösningen. Du kan ange mer än ett namn och avgränsa dem med ett kommatecken (värden är inte Skift läges känsliga). Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen. Det här värdet lagras i **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** variabler.
  
   - Ange **undantags listan för den virtuella datorn (sträng)**. Det här värdet är namnet på en eller flera virtuella datorer från mål resurs gruppen. Du kan ange mer än ett namn och avgränsa dem med ett kommatecken (värden är inte Skift läges känsliga). Användning av jokertecken stöds. Det här värdet lagras i variabeln **External_ExcludeVMNames** .
  
   - Välj ett **schema**. Välj datum och tid för ditt schema. Ett dagligt schema som pågår kommer att skapas från och med den tid som du har valt. Det går inte att välja en annan region. Information om hur du konfigurerar schemat till din speciella tidszon efter att du har konfigurerat lösningen finns i [ändra schemat för start och avstängning](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).
  
   - Acceptera standardvärdet **Ja** och ange en giltig e-postadress för att ta emot **e-postaviseringar** från en åtgärds grupp. Om du väljer **Nej** men väljer ett senare datum som du vill ta emot e-postaviseringar kan du uppdatera den [Åtgärds grupp](../azure-monitor/platform/action-groups.md) som skapas med giltiga e-postadresser, avgränsade med kommatecken. Du måste också aktivera följande aviserings regler:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Standardvärdet för **mål ResourceGroup namn** är ett **&ast;**. Detta riktar sig till alla virtuella datorer i en prenumeration. Om du inte vill att lösningen ska rikta alla virtuella datorer i din prenumeration måste det här värdet uppdateras till en lista över resurs grupp namn innan du aktiverar scheman.

11. När du har konfigurerat de första inställningarna som krävs för lösningen klickar du på **OK** för att stänga sidan parametrar och väljer **skapa**. 

När alla inställningar har verifierats distribueras lösningen till din prenumeration. Den här processen kan ta flera sekunder att slutföra och du kan följa förloppet under **meddelanden** på menyn.

> [!NOTE]
> Om du har en prenumeration på Azure Cloud Solution Provider (Azure CSP) efter att distributionen har slutförts går du till **variabler** under **delade resurser** i ditt Automation-konto och anger [External_EnableClassicVMs](automation-solution-vm-management.md#variables) variabeln till **falskt**. Detta stoppar lösningen från att leta efter klassiska VM-resurser.

## <a name="next-steps"></a>Nästa steg

Nu när lösningen är aktive rad kan du [Konfigurera](automation-solution-vm-management-config.md) den så att den stöder dina krav för hantering av virtuella datorer.