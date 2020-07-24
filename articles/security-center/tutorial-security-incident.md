---
title: Själv studie kurs om incident svar – Azure Security Center
description: I den här självstudien får du lära dig hur du prioritering säkerhets aviseringar, fastställer rotor saken & omfattningen av en incident och söker efter säkerhets data.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: dc19d74953db48ccf51d4731e2b9feccb2294041
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076394"
---
# <a name="tutorial-respond-to-security-incidents"></a>Självstudie: Reagera på säkerhetsincidenter
Security Center analyserar kontinuerligt dina hybridmolnarbetsbelastningar med avancerad analys och hotinformation för att varna dig om skadlig aktivitet. Dessutom kan du integrera varningar från andra säkerhetsprodukter och -tjänster i Security Center och skapa anpassade varningar utifrån dina egna indikatorer eller intelligenskällor. När en varning har genererats krävs snabba åtgärder för att undersöka och åtgärda. I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Prioritera säkerhetsvarningar
> * Undersök ytterligare för att fastställa rotorsaken och omfånget för en säkerhetsincident
> * Sök säkerhetsdata för enklare undersökning

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom de funktioner som beskrivs i den här självstudien måste du vara på Security Center standard pris nivån. Du kan prova Security Center Standard utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). Snabbstarten för att [registrera Azure-prenumerationen till Security Center Standard](security-center-get-started.md) vägleder dig genom uppgraderingen till Standard.

## <a name="scenario"></a>Scenario
Contoso migrerade nyligen några av sina lokala resurser till Azure, inklusive vissa virtuella datorbaserade branschspecifika arbetsbelastningar och SQL-databaser. För närvarande har Contosos team som ansvarar för datorsäkerheten, CSIRT (Computer Security Incident Response Team) svårt att undersöka säkerhetsproblem på grund av bristande integrering av säkerhetsfunktionerna i de aktuella incidenthanteringsverktygen. Bristen på integrering utgör ett problem vid identifiering (det blir för många falska positiva resultat), samt vid utvärderings- och diagnostiseringsstegen. Som en del av den här migreringen beslutar de att välja Security Center för att lösa problemet.

Den första fasen i migreringen slutförs när alla resurser har integreras och alla säkerhetsrekommendationer från Security Center har hanterats. Contoso CSIRT är huvudresursen för att hantera säkerhetsincidenter. Gruppen består av en grupp personer med ansvar för att hantera en alla säkerhetsincidenter. Gruppmedlemmarna har tydligt definierade uppgifter som säkerställer att inget område lämnas utan åtgärd.

I det här scenariot ska vi fokusera på rollerna för följande personer som ingår i Contoso CSIRT:

![Livscykel för incidenthantering](./media/tutorial-security-incident/security-center-incident-response.png)

Johanna, som arbetar med säkerheten. Deras ansvar omfattar:

* Övervakning och hantering av säkerhetshot dygnet runt.
* Eskalering till molnets arbetsbelastningsägare eller säkerhetsanalytiker vid behov.

Sam är en säkerhetsanalytiker och deras ansvar omfattar:

* Undersöka attacker.
* Åtgärda aviseringar.
* Arbeta med arbetsbelastningsägare för att fastställa och tillämpa åtgärder.

Som du kan se har Johanna och Sam olika ansvarsområden och de måste samarbeta för att dela informationen från Security Center.

## <a name="triage-security-alerts"></a>Prioritera säkerhetsvarningar
Security Center tillhandahåller en enhetlig vy över alla säkerhetsvarningar. Säkerhetsvarningar rankas beroende på allvarlighetsgrad och när det är möjligt kombineras relaterade varningar till en säkerhetsincident. När du sorterar varningar och hot ska du:

- Avvisa aviseringar för vilka ingen ytterligare åtgärd krävs, till exempel om aviseringen är ett falsklarm
- Vidta åtgärder för kända attacker, till exempel blockering av nätverkstrafik från en skadlig IP-adress
- Fastställa aviseringar som kräver ytterligare undersökning


1. På Security Center-huvudmenyn under **IDENTIFIERING** väljer du **Säkerhetsvarningar**:

   ![Säkerhetsaviseringar](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. I listan över varningar klickar du på en säkerhetsincident för att läsa mer om incidenten. En säkerhetsincident är en samling varningar. **Säkerhetsincident har identifierats** öppnas.

   ![Säkerhetsincident](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. På den här skärmen ser du en beskrivning av säkerhetsincidenten överst och listan med varningar som ingår i incidenten. Klicka på den varning du vill titta närmare på för att visa mer information.

   ![Säkerhetsincident](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   Typen av varning kan variera. Läs [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) för mer information om typen av varning och potentiella åtgärder. För varningar som kan avfärdas säkert kan du högerklicka på varningen och välja alternativet **Stäng**:

   ![Varning](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Om rotorsaken och omfånget för den skadliga aktiviteten är okänd fortsätter du till nästa steg för att undersöka närmare.

## <a name="investigate-an-alert-or-incident"></a>Undersöka en varning eller ett hot
1. På sidan **Säkerhetsvarning** klickar du på knappen **Starta undersökning** (om du redan har börjat ändras namnet till **Fortsätt undersökningen**).

   ![Undersökning](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   Undersökningskartan är en grafisk representation av de entiteter som är anslutna till säkerhetsvarningen eller incidenten. Om du klickar på en entitet på kartan visas information om den entiteten, som nya entiteter, och kartan expanderar. Egenskaperna för entiteten som är vald på kartan är markerade i rutan på höger sida. Informationen som är tillgänglig på varje flik varierar beroende på vald entitet. Under undersöknings processen granskar du all relevant information för att bättre förstå angriparens rörelse.

2. Om du behöver mer bevis eller om du ytterligare måste undersöka entiteter som hittades vid undersökningen fortsätter du till nästa steg.

## <a name="search-data-for-investigation"></a>Söka efter data för undersökning

Du kan använda sökfunktioner i Security Center för att hitta mer bevis för upptäckta system och mer information om entiteterna som är en del av undersökningen.

Om du vill göra en sökning öppnar du instrumentpanelen för **Security Center**, klickar på **Sök** i det vänstra navigeringsfönstret, väljer arbetsytan som innehåller entiteterna du vill söka, skriver sökfrågan och klickar på sökknappen.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabb starter och självstudier kan du fortsätta att köra standard-nivån och behålla automatisk etablering aktive rad. Om du inte tänker fortsätta eller vill återgå till den kostnadsfria nivån:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. Välj den prenumeration eller princip du vill ska återgå till den kostnadsfria nivån. **Säkerhetsprincip** öppnas.
3. Under **PRINCIPKOMPONENTER** väljer du **Prisnivå**.
4. Välj **kostnads fri** om du vill ändra prenumerationen från standard nivån till den kostnads fria nivån.
5. Välj **Spara**.

Om du vill avaktivera automatisk etablering:

1. Gå tillbaka till Security Center huvud menyn och välj **säkerhets princip**.
2. Välj den prenumeration du vill avaktivera automatisk etablering för.
3. Under **Säkerhetsprincip – Datainsamling** väljer du **Av** under **Registrering** för att inaktivera automatisk etablering.
4. Välj **Spara**.

>[!NOTE]
> Om du inaktiverar automatisk etablering tas inte Log Analytics agenten bort från virtuella Azure-datorer där agenten har etablerats. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig om vilka funktioner i Security Center som ska användas när du åtgärdar en säkerhetsincident, som:

> [!div class="checklist"]
> * Säkerhetsincident som är en sammansättning av relaterade varningar för en resurs
> * Undersökningskartan är en grafisk representation av de entiteter som är anslutna till en säkerhetsvarning eller incidenten
> * Sökfunktioner för att hitta fler bevis på upptäckta system