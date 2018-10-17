---
title: Azure Security Center-självstudie – Definiera och utvärdera säkerhetsprinciper | Microsoft Docs
description: Azure Security Center-självstudie – Definiera och utvärdera säkerhetsprinciper
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: fcd3c2a95cea0a838fc16149a0a74fad95ea3300
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027069"
---
# <a name="tutorial-define-and-assess-security-policies"></a>Självstudie: Definiera och utvärdera säkerhetsprinciper
Security Center hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav med säkerhetsprinciper för att definiera den önskade konfigurationen för arbetsbelastningarna. När du definierar principer för dina Azure-prenumerationer och anpassar dem till typen av arbetsbelastning eller dina datas känslighet kan Security Center tillhandahålla säkerhetsrekommendationer för dina resurser för beräkning, program, nätverk, data och lagring samt identitet och åtkomst. I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera säkerhetsprincip
> * Utvärdera säkerheten för dina resurser

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/pricing/free-trial/) konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att gå igenom funktionerna i den här självstudien måste du ha standardnivån i Security Center. Du kan prova Security Center Standard utan kostnad under de första 60 dagarna. Snabbstarten för att [registrera Azure-prenumerationen till Security Center Standard](security-center-get-started.md) vägleder dig genom uppgraderingen till Standard.

## <a name="configure-security-policy"></a>Konfigurera säkerhetsprincip
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Säkerhetsprinciper består av rekommendationer som du kan slå på eller stänga av utifrån prenumerationens säkerhetskrav. För att göra ändringar i en standardsäkerhetsprincip måste du vara ägare, deltagare eller säkerhetsadministratör för prenumerationen.

1. På huvudmenyn i Security Center väljer du **Säkerhetsprincip**.
2. Ange den Azure-prenumeration som du vill använda.

  ![Säkerhetsprincip](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

3. Under **Compute och appar**, **Nätverk** och **Data** sätter du alla säkerhetskonfigurationer som ska övervakas till **På**. Security Center utvärderar kontinuerligt miljöns konfiguration och när det finns säkerhetsrisker genererar Security Center en säkerhetsrekommendation. Välj **Av** om säkerhetskonfigurationen inte är rekommenderad eller relevant. I till exempel en dev/test-miljö kanske du inte kräver samma säkerhetsnivå som för en produktionsmiljö. När du har valt de principer som gäller för din miljö klickar du på **Spara**.

  ![Säkerhetskonfiguration](./media/tutorial-security-policy/tutorial-security-policy-fig6.png)  

Vänta tills Security Center behandlar principerna och genererar rekommendationer. Vissa konfigurationer, som systemuppdateringar och OS-konfigurationer kan ta upp till 12 timmar, medan nätverkssäkerhetsgrupper och krypteringskonfigurationer kan utvärderas nästan omedelbart. När du ser rekommendationerna på Security Center-instrumentpanelen kan du fortsätta till nästa steg.

## <a name="assess-security-of-resources"></a>Utvärdera säkerheten för resurser
1. Enligt säkerhetsprinciperna vi har aktiverat tillhandahåller Security Center en uppsättning säkerhetsrekommendationer efter behov. Börja med att granska den virtuella datorn och rekommendationer för datorer. På Security Center-instrumentpanelen väljer du **Översikt** och sedan **Compute och appar**.

  ![Compute](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  Granska varje rekommendation genom att prioritera rekommendationer i rött (hög prioritet). Vissa av de här rekommendationerna har åtgärder som kan implementeras direkt från Security Center, som [problem med endpoint protection](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection). Andra rekommendationer har endast riktlinjer för att tillämpa åtgärden, till exempel om det saknas rekommendation för kryptering av disk.

2. När du har gått igenom alla relevanta rekommendationer ska du fortsätta till nästa arbetsbelastning: nätverk. På instrumentpanelen för Security Center klickar du på **Översikt** och sedan på **Nätverk**.

  ![Nätverk](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  På sidan med nätverksrekommendationer finns en lista över säkerhetsproblem för nätverkskonfiguration, internetuppkopplade slutpunkter och nätverkstopologi. Precis som för **Compute och appar** tillhandahåller vissa nätverksrekommendationer integrerade åtgärder och vissa inte.

3. När du har gått igenom alla relevanta nätverksrekommendationer ska du gå vidare till nästa arbetsbelastning: lagring och data. På instrumentpanelen för Security Center klickar du på **Översikt** och sedan på **Data och lagring**.

  ![Dataresurser](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  På sidan **Dataresurser** finns det rekommendationer för att aktivera granskning för Azure SQL-servrar och -databaser, aktivering av kryptering för SQL-databaser och aktivering av kryptering för ditt Azure-lagringskonto. Om du inte har dessa arbetsbelastningar visas inga rekommendationer. Precis som för **Compute och appar** tillhandahåller vissa rekommendationer för data och lagring integrerade åtgärder och vissa inte.

4. När du har gått igenom alla relevanta rekommendationer för data och lagring ska du gå vidare till nästa arbetsbelastning: Identitet och åtkomst. På instrumentpanelen för Security Center klickar du på **Översikt** och sedan på **Identitet och åtkomst**.

  ![Identitet och åtkomst](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  Sidan **Identitet och åtkomst** innehåller rekommendationer som:

   - Aktivera MFA för privilegierade konton för prenumerationen
   - Ta bort externa konton med skrivbehörigheter från prenumerationen
   - Ta bort externa privilegierade konton från prenumerationen

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
I den här självstudien har du lärt dig om grundläggande principdefinition och säkerhetsbedömning för arbetsbelastningar med Security Center, som:

> [!div class="checklist"]
> * Konfiguration av säkerhetsprincip för att garantera efterlevnad med ditt företag eller lagstadgade säkerhetskrav
> * Säkerhetsbedömningar för dina resurser för beräkning, nätverk, SQL och lagring och program

Fortsätt till nästa självstudie för att lära dig att använda Security Center för att skydda dina resurser.

> [!div class="nextstepaction"]
> [Skydda dina resurser](tutorial-protect-resources.md)
