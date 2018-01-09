---
title: Azure Security Center Quickstart - publicera din Azure-prenumeration till Security Center Standard | Microsoft Docs
description: "Den här snabbstarten visar hur du uppgraderar till Security Center Standard prisnivån för ytterligare säkerhet."
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
ms.openlocfilehash: ac4e3b36b08223f7e3b54850ed53a8185e85f0d2
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Snabbstartsguide: Publicera din Azure-prenumeration till Security Center Standard
Azure Security Center ger enhetlig säkerhetsskydd för hantering och hot över hybrid cloud arbetsbelastningar. Även om den kostnadsfria nivån erbjuder begränsad säkerhet för dina Azure resurser, utökar standardnivån dessa funktioner att lokalt och andra moln. Security Center Standard hjälper dig att hitta och åtgärda säkerhetsproblem, tillämpa kontroller åtkomst och program för att blockera skadliga aktiviteter, identifiera hot med analyser och tillgångsinformation och snabbt svara under ett angrepp. Du kan försöka Security Center Standard för de första 60 dagarna utan kostnad.

I den här artikeln att uppgradera till standardnivån för extra säkerhet och installera Microsoft Monitoring Agent på dina virtuella datorer för att övervaka säkerhetsproblem och hot.

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Om du vill uppgradera en prenumeration till standardnivån om måste du tilldelas rollen prenumeration ägare, deltagare i prenumeration eller säkerhet administratör.

## <a name="enable-your-azure-subscription"></a>Aktivera din Azure-prenumeration

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center - översikt** öppnas.

 ![Översikt över Security Center][2]

**Security Center – översikt över** ger en enhetlig överblick över säkerhetsläget i din hybrid cloud arbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för dina arbetsbelastningar och att identifiera och minimera risker. Security Center aktiveras automatiskt någon av dina Azure-prenumerationer inte tidigare har publicerats så av dig eller en annan prenumeration användare för den kostnadsfria nivån.

Du kan visa och filtrera listan över prenumerationer genom att klicka på den **prenumerationer** menyalternativet. Security Center kommer nu att börja utvärdera säkerheten för dessa prenumerationer att identifiera säkerhetsproblem. Om du vill anpassa vilka typer av bedömningar, kan du ändra säkerhetsprincipen. En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav.

Du kan se i minuter för att starta Security Center första gången av:

- **Rekommendationer** för sätten att förbättra säkerheten för dina Azure-prenumerationer. Klicka på den **rekommendationer** panelen startas en prioriterad lista.
- En förteckning över **Compute**, **nätverk**, **lagring & data**, och **program** resurser som nu som bedöms genom Security Center tillsammans med säkerhetstillståndet för var och en.

Om du vill dra full nytta av Security Center, måste du slutföra stegen nedan för att uppgradera till standardnivån och installera Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Uppgradera till standardnivån
Du måste uppgradera till standardnivån för Snabbstart för Security Center och självstudier. Din första 60 dagarna är gratis och du kan återgå till den kostnadsfria nivån helst.

1. Välj under huvudmenyn Security Center **Onboarding till avancerad säkerhet**.

2. Under **Onboarding till avancerad säkerhet**, Security Center visas prenumerationer och arbetsytor berättigad till onboarding. Välj en prenumeration i listan.

  ![Välj en prenumeration][4]

3. **Säkerhetsprincip** innehåller information om resursgrupper finns i prenumerationen. **Priser** öppnas också.
4. Under **priser**väljer **Standard** att uppgradera från lediga till Standard- och klicka på **spara**.

  ![Välj Standard][5]

Nu när du har uppgraderat till standardnivån om du har åtkomst till ytterligare funktioner i Security Center, inklusive **anpassningsbar programkontroller**, **just-in-time-åtkomst för VM**, **säkerhet aviseringar**, **hot intelligence**, **automation playbooks**, med mera. Observera att säkerhetsvarningar visas endast när Security Center identifierar skadlig aktivitet.

  ![Säkerhetsaviseringar][7]

## <a name="automate-data-collection"></a>Automatisera datainsamling
Security Center samlar in data från virtuella datorer i Azure och Azure-datorer för att övervaka för säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent som läser olika säkerhetsrelaterade konfigurationer och händelseloggar på datorn och kopierar data till din arbetsyta för analys. Som standard skapar Security Center en ny arbetsyta för dig.

När automatisk etablering är aktiverat, installerar Security Center Microsoft Monitoring Agent på alla stöds virtuella Azure-datorer och nya filer som skapas. Vi rekommenderar starkt att du Automatisk etablering.

Aktivera automatisk etablering av Microsoft Monitoring Agent:

1. Välj under huvudmenyn Security Center **säkerhetsprincip**.
2. Välj prenumerationen.
3. Under **säkerhetsprincip**väljer **datainsamling**.
4. Under **datainsamling**väljer **på** att aktivera automatisk etablering.
5. Välj **Spara**.

  ![Aktivera automatisk etablering][6]

Med den här nya inblick i dina virtuella datorer i Azure kan Security Center ge ytterligare rekommendationer som rör system uppdatera status, OS säkerhetskonfigurationer, endpoint protection, samt ytterligare säkerhetsaviseringar genereras.

  ![Rekommendationer][8]

## <a name="clean-up-resources"></a>Rensa resurser
Andra Snabbstart och självstudiekurser i den här samlingen bygger på denna Snabbstart. Om du vill fortsätta på att arbeta med efterföljande Snabbstart och självstudier fortsätta köras standardnivån och automatisk etablering aktiverad. Om du inte planerar att fortsätta eller om du vill gå tillbaka till den kostnadsfria nivån:

1. Återgå till huvudmenyn Security Center och välja **säkerhetsprincip**.
2. Välj den prenumeration eller princip som du vill gå tillbaka till ledig. **Säkerhetsprincip** öppnas.
3. Under **princip komponenter**väljer **prisnivå**.
4. Välj **lediga** ändra prenumerationen från standarden nivå till den kostnadsfria nivån.
5. Välj **Spara**.

Om du vill inaktivera automatisk etablering:

1. Återgå till huvudmenyn Security Center och välja **säkerhetsprincip**.
2. Välj den prenumeration som du vill inaktivera automatisk etablering.
3. Under **säkerhetsprincip – datainsamling**väljer **av** under **Onboarding** att inaktivera automatisk etablering.
4. Välj **Spara**.

>[!NOTE]
> Inaktivera automatisk etablering tar inte bort Microsoft Monitoring Agent från virtuella Azure-datorer där agenten har etablerats. Inaktivera automatisk etablering gränser säkerhetsövervakning för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten uppgraderas till standardnivån och etableras Microsoft Monitoring Agent för enhetlig hantering och skydd över hybrid cloud arbetsbelastningar. Om du vill veta mer om hur du använder Security Center kan du fortsätta att Snabbstart för onboarding Windows-datorer som finns lokalt och i andra moln.

> [!div class="nextstepaction"]
> [Snabbstartsguide: Publicera Windows-datorer till Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/onboarding.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
