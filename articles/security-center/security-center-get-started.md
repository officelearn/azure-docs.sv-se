---
title: Azure Security Center-snabbstart – Publicera din Azure-prenumeration till Security Center Standard | Microsoft Docs
description: I den här snabbstarten får du lära dig att uppgradera till Security Centers Standard-prisnivå för ytterligare säkerhet.
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
ms.date: 06/26/2018
ms.author: terrylan
ms.openlocfilehash: d10cef33ef0c325d41c9539107b9a4cab5e916d8
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059862"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Snabbstart: Publicera din Azure-prenumeration till Security Center Standard
Azure Security Center erbjuder enhetlig säkerhetshantering och skydd mot hot i olika hybridmolnarbetsbelastningar. På den kostnadsfria nivån erbjuds endast begränsad säkerhet för dina Azure-resurser, medan Standard-nivån utökar funktionerna till lokala resurser och andra moln. Med Security Center Standard kan du hitta och åtgärda säkerhetsproblem, tillämpa åtkomst- och programkontroller för att blockera skadlig aktivitet, upptäcka hot med analys och intelligens och svara snabbt under attacker. Du kan prova Security Center Standard utan kostnad under de första 60 dagarna.

I den här artikeln uppgraderar du till Standard-nivån för utökad säkerhet och installerar Microsoft Monitoring Agent på dina virtuella datorer för att övervaka säkerhetsproblem och hot.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Om du vill uppgradera en prenumeration till standardnivån måste du vara tilldelad rollen som prenumerationsägare, prenumerationsdeltagare eller säkerhetsadministratör.

## <a name="enable-your-azure-subscription"></a>Aktivera din Azure-prenumeration

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center – Översikt** öppnas.

 ![Översikt över Security Center][2]

**Security Center – Översikt** ger en enhetlig överblick över säkerhetstillståndet för dina hybridarbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för arbetsbelastningarna samt identifiera och minimera risker. Security Center aktiverar automatiskt Azure-prenumerationer som inte tidigare har registrerats av dig eller någon annan prenumerationsanvändare på den kostnadsfria nivån.

Du kan visa och filtrera listan över prenumerationer genom att klicka på menyalternativet **Prenumerationer**. Security Center börjar nu utvärdera säkerheten för prenumerationerna för att identifiera säkerhetsproblem. Om du vill anpassa typerna av utvärderingar kan du ändra säkerhetsprincipen. En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav.

Inom några minuter efter att du har startat Security Center för första gången kanske du ser:

- **Rekommendationer** för sätt att förbättra säkerheten för dina Azure-prenumerationer. Om du klickar på panelen **Rekommendationer** öppnas en rangordnad lista.
- En inventering av resurserna **beräkning och appar**, **nätverk**, **datasäkerhet** och **identitet och åtkomst** som nu utvärderas av Security Center tillsammans med säkerhetspositionen för var och en.

Om du vill dra full nytta av Security Center måste du slutföra stegen nedan för att uppgradera till standardnivån och installera Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Uppgradera till standardnivån
Du måste uppgradera till standardnivån för att kunna använda snabbstart och självstudier i Security Center. De första 60 dagarna är kostnadsfria, och du kan återgå till den kostnadsfria nivån när som helst.

1. På huvudmenyn i Security Center väljer du **Kom igång med avancerad säkerhet**.

2. Under **Kom igång med avancerad säkerhet** listar Security Center prenumerationer och arbetsytor som är behöriga för registrering. Välj en prenumeration i listan.

  ![Välj en prenumeration][4]

3. **Säkerhetsprincip** ger information om resursgrupperna i prenumerationen. **Prissättning** öppnas också.
4. Under **Prissättning** väljer du **Standard** för att uppgradera från den kostnadsfria nivån till standardnivån och klickar på **Spara**.

  ![Välj standard][5]

Nu när du har uppgraderat till standardnivån har du åtkomst till flera funktioner i Security Center, som **anpassningsbara programkontroller**, **just-in-time-åtkomst till virtuell dator**, **säkerhetsaviseringar**, **hotinformation**, **automatiserade spelböcker** med mera. Lägg märke till att säkerhetsaviseringar endast visas när Security Center identifierar skadlig aktivitet.

  ![Säkerhetsaviseringar][7]

## <a name="automate-data-collection"></a>Automatisera datainsamling
Security Center samlar in data från dina virtuella Azure-datorer och icke-Azure-datorer för att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys. Som standard skapar Security Center en ny arbetsyta till dig.

När automatisk etablering är aktiverat installerar Security Center Microsoft Monitoring Agent på alla virtuella Azure-datorer som stöds och alla nya som skapas. Automatisk försörjning rekommenderas starkt.

Så här aktiverar du automatisk försörjning för Microsoft Monitoring Agent:

1. På huvudmenyn i Security Center väljer du **Säkerhetsprincip**.
2. Välj prenumerationen.
3. Under **Säkerhetsprincip** väljer du **Datainsamling**.
4. Under **Datainsamling** väljer du **På** för att aktivera automatisk etablering.
5. Välj **Spara**.

  ![Aktivera automatisk försörjning][6]

Med de här nya kunskaperna om dina virtuella datorer i Azure kan Security Center ge dig ytterligare rekommendationer som relaterar till systemuppdateringsstatus, OS-säkerhetskonfigurationer, slutpunktsskydd samt generera fler säkerhetsaviseringar.

  ![Rekommendationer][8]

## <a name="clean-up-resources"></a>Rensa resurser
De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du tänker fortsätta med att arbeta med efterföljande snabbstarter och självstudier ska du fortsätta att köra Standard-nivån och ha automatisk etablering aktiverad. Om du inte tänker fortsätta eller vill återgå till den kostnadsfria nivån:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. Välj den prenumeration eller princip du vill ska återgå till den kostnadsfria nivån. **Säkerhetsprincip** öppnas.
3. Under **PRINCIPKOMPONENTER** väljer du **Prisnivå**.
4. Välj **Kostnadsfri** om du vill byta prenumeration från Standard-nivån till den kostnadsfria nivån.
5. Välj **Spara**.

Om du vill avaktivera automatisk etablering:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. Välj den prenumeration du vill avaktivera automatisk etablering för.
3. Under **Säkerhetsprincip – Datainsamling** väljer du **Av** under **Registrering** för att inaktivera automatisk etablering.
4. Välj **Spara**.

>[!NOTE]
> Inaktivering av automatisk etablering tar inte bort Microsoft Monitoring Agent från virtuella Azure-datorer där agenten har etablerats. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du uppgraderat till standardnivån och etablerat Microsoft Monitoring Agent för enhetlig säkerhetshantering och skydd mot hot i dina hybridmolnarbetsbelastningar. Om du vill lära dig mer om att använda Security Center fortsätter du till snabbstarten för publicering av Windows-datorer som är lokala och i andra moln.

> [!div class="nextstepaction"]
> [Snabbstart: Publicera Windows-datorer till Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
