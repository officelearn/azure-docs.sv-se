---
title: Uppgradera till standard nivå – Azure Security Center
description: Den här snabb starten visar hur du uppgraderar till Security Center standard pris nivån för ytterligare säkerhet.
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
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 550c9ff57b9c558f2f175165c7f06ead45991be9
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226028"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Snabbstart: Publicera din Azure-prenumeration till Security Center Standard
Azure Security Center erbjuder enhetlig säkerhetshantering och skydd mot hot i olika hybridmolnarbetsbelastningar. Även om den kostnads fria nivån erbjuder begränsad säkerhet för dina Azure-resurser, utökar standard nivån dessa funktioner till lokala och andra moln. Med Security Center Standard kan du hitta och åtgärda säkerhetsproblem, tillämpa åtkomst- och programkontroller för att blockera skadlig aktivitet, upptäcka hot med analys och intelligens och svara snabbt under attacker. Du kan prova Security Center Standard utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

I den här artikeln ska du uppgradera till standard nivån för ytterligare säkerhet och installera Log Analytics-agenten på dina virtuella datorer för att övervaka säkerhets problem och hot.

## <a name="prerequisites"></a>Krav
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Om du vill uppgradera en prenumeration till standard nivån måste du tilldelas rollen som prenumerations ägare, prenumerations deltagare eller säkerhets administratör.

## <a name="enable-your-azure-subscription"></a>Aktivera din Azure-prenumeration

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).

1. Välj **Security Center**på **Microsoft Azure** -menyn. **Security Center översikt** öppnas.

   ![Översikt över Security Center][2]

**Security Center – Översikt** ger en enhetlig överblick över säkerhetstillståndet för dina hybridarbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för arbetsbelastningarna samt identifiera och minimera risker. Security Center aktiverar automatiskt Azure-prenumerationer som inte tidigare har registrerats av dig eller någon annan prenumerationsanvändare på den kostnadsfria nivån.

Du kan visa och filtrera listan över prenumerationer genom att klicka på menyalternativet **Prenumerationer**. Security Center börjar nu utvärdera säkerheten för prenumerationerna för att identifiera säkerhetsproblem. Om du vill anpassa typerna av utvärderingar kan du ändra säkerhetsprincipen. En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav.

Inom några minuter efter att du har startat Security Center för första gången kanske du ser:

- **Rekommendationer** för sätt att förbättra säkerheten för dina Azure-prenumerationer. Om du klickar på panelen **Rekommendationer** öppnas en rangordnad lista.
- En inventering av resurserna **beräkning och appar**, **nätverk**, **datasäkerhet** och **identitet och åtkomst** som nu utvärderas av Security Center tillsammans med säkerhetspositionen för var och en.

Om du vill dra full nytta av Security Center måste du slutföra stegen nedan för att uppgradera till standard nivån och installera Log Analytics agenten.


## <a name="upgrade-to-the-standard-tier"></a>Uppgradera till standardnivån

Du måste uppgradera till standardnivån för att kunna använda snabbstart och självstudier i Security Center. Det finns en kostnadsfri utvärderingsversion av Security Center Standard. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). 

1. Välj **komma igång**från Security Center marginal List.
 
   ![Komma igång](./media/security-center-get-started/get-started-upgrade-tab.png)

    På fliken **Uppgradera** visas de prenumerationer och arbets ytor som är berättigade till onboarding.

1. Från listan **Välj arbets ytor för att aktivera standard nivån i** listan väljer du de arbets ytor som ska uppgraderas.


    > [!TIP]
    > Om du väljer en arbets yta som är berättigad till en kostnads fri utvärderings version påbörjar nästa steg en utvärderings version. Om arbets ytorna inte är berättigade till en utvärderings version kommer den att uppgraderas och kostnaderna börjar.

1. Välj **Uppgradera** för att uppgradera de valda arbets ytorna till standard nivån.


## <a name="automate-data-collection"></a>Automatisera datainsamling
Security Center samlar in data från dina virtuella Azure-datorer och icke-Azure-datorer för att övervaka säkerhetsproblem och hot. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din arbets yta för analys. Som standard skapar Security Center en ny arbetsyta till dig.

När automatisk etablering har Aktiver ATS installerar Security Center Log Analytics agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas. Automatisk försörjning rekommenderas starkt.

Så här aktiverar du automatisk etablering av Log Analytics agent:

1. Under Security Center huvud menyn väljer du **pris & inställningar**.
1. På prenumerations raden klickar du på den prenumeration som du vill ändra inställningarna för.
1. På fliken **Datainsamling** anger du **Automatisk etablering** till **På**.
1. Välj **Spara**.
---
  ![Aktivera automatisk försörjning][6]

Med de här nya kunskaperna om dina virtuella datorer i Azure kan Security Center ge dig ytterligare rekommendationer som relaterar till systemuppdateringsstatus, OS-säkerhetskonfigurationer, slutpunktsskydd samt generera fler säkerhetsaviseringar.

  ![Rekommendationer][8]

## <a name="clean-up-resources"></a>Rensa resurser
De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabb starter och självstudier kan du fortsätta att köra standard-nivån och behålla automatisk etablering aktive rad. Om du inte tänker fortsätta eller vill återgå till den kostnadsfria nivån:

1. Gå tillbaka till Security Center huvud menyn och välj **pris & inställningar**.
2. Klicka på den prenumeration som du vill ändra till den kostnads fria nivån.
3. Välj **pris nivå** och välj **kostnads fri** för att ändra prenumerationen från standard nivån till den kostnads fria nivån.
5. Välj **Spara**.

Om du vill avaktivera automatisk etablering:

1. Gå tillbaka till Security Center huvud menyn och välj **pris & inställningar**.
2. Rensa den prenumeration som du vill inaktivera automatisk etablering på.
3. På fliken **Datainsamling** anger du **Automatisk etablering** till **Av**.
4. Välj **Spara**.

>[!NOTE]
> Om du inaktiverar automatisk etablering tas inte Log Analytics agenten bort från virtuella Azure-datorer där agenten har etablerats. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du uppgraderat till standard nivån och etablerade Log Analytics agent för enhetlig säkerhets hantering och skydd mot hot i dina hybrid moln arbets belastningar. Om du vill lära dig mer om att använda Security Center fortsätter du till snabbstarten för publicering av Windows-datorer som är lokala och i andra moln.

> [!div class="nextstepaction"]
> [Snabbstart: Publicera Windows-datorer till Azure Security Center](quick-onboard-windows-computer.md)

Vill du optimera och Spara på dina moln utgifter?

> [!div class="nextstepaction"]
> [Börja analysera kostnaderna med Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
