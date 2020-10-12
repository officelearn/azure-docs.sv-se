---
title: Självstudie för aviserings svar – Azure Security Center
description: I den här självstudien får du lära dig hur du prioritering säkerhets aviseringar och fastställer rotor saken & omfattningen av en avisering.
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
ms.date: 09/30/2020
ms.author: memildin
ms.openlocfilehash: a04f94f5ebc7c1fdaf7b95e71dc8549e19863b39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614170"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Självstudie: prioritering, undersöka och reagera på säkerhets aviseringar
Security Center analyserar kontinuerligt dina hybridmolnarbetsbelastningar med avancerad analys och hotinformation för att varna dig om skadlig aktivitet. Du kan också integrera aviseringar från andra säkerhets produkter och tjänster i Security Center och skapa anpassade aviseringar baserat på dina egna indikatorer eller informations källor. När en varning har genererats krävs snabba åtgärder för att undersöka och åtgärda. 

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Prioritera säkerhetsvarningar
> * Undersök en säkerhets avisering för att fastställa rotor saken
> * Reagera på en säkerhets avisering och minimera den rotor saken

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav
Om du vill gå igenom de funktioner som beskrivs i den här självstudien måste du ha Azure Defender aktiverat. Du kan testa Azure Defender utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). Snabb start [Kom igång med Security Center](security-center-get-started.md) hjälper dig att uppgradera.


## <a name="triage-security-alerts"></a>Prioritera säkerhetsvarningar
Security Center tillhandahåller en enhetlig vy över alla säkerhetsvarningar. Säkerhets aviseringar rangordnas baserat på den identifierade aktivitetens allvarlighets grad. 

Prioritering aviseringar från sidan **säkerhets aviseringar** :

:::image type="content" source="./media/tutorial-security-incident/alerts-list.png" alt-text="Sidan säkerhets aviseringar" lightbox="./media/tutorial-security-incident/alerts-list.png":::

Använd den här sidan om du vill granska aktiva säkerhets aviseringar i din miljö för att bestämma vilken avisering som ska undersökas först.

När du sorterar säkerhets aviseringar prioriterar du aviseringar baserat på aviseringens allvarlighets grad genom att adressera aviseringar med högre allvarlighets grad först. Vill du veta mer om aviserings allvarlighets grad i [hur är aviseringar klassificerade?](security-center-alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> Du kan ansluta Azure Security Center till de populäraste SIEM-lösningarna, inklusive Azure Sentinel och använda aviseringar från det verktyg du väljer. Läs mer i [Exportera aviseringar till en Siem](continuous-export.md).


## <a name="investigate-a-security-alert"></a>Undersök en säkerhets avisering

När du har bestämt vilken avisering som ska undersökas först:

1. Välj önskad avisering.
1. På sidan aviserings översikt väljer du den resurs som du vill undersöka först.
1. Påbörja undersökningen från den vänstra rutan, som visar information på hög nivå om säkerhets aviseringen.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Sidan säkerhets aviseringar":::

    I den här rutan visas:
    - Allvarlighets grad, status och aktivitets tid för avisering
    - Beskrivning som förklarar exakt aktivitet som upptäcktes
    - Berörda resurser
    - Kill-kedje avsikt för aktiviteten på MITRE to&CK-matrisen

1. Mer detaljerad information som kan hjälpa dig att undersöka den misstänkta aktiviteten finns på fliken **aviserings information** .

1. När du har granskat informationen på den här sidan kan du ha tillräckligt med svar för att kunna fortsätta. Om du behöver ytterligare information:

    - Kontakta resursens ägare för att kontrol lera om den identifierade aktiviteten är ett falskt positivt värde.
    - Undersök de obehandlade loggar som genereras av den angripna resursen

## <a name="respond-to-a-security-alert"></a>Svara på en säkerhets avisering
När du har undersökt en avisering och förstår dess omfattning kan du svara på säkerhets aviseringar från Azure Security Center:

1.  Öppna fliken **vidta åtgärder** för att se de rekommenderade svaren.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Sidan säkerhets aviseringar" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Läs avsnittet **minimera hotet** för de manuella undersöknings stegen som krävs för att åtgärda problemet.
1.  Åtgärda dina resurser och förhindra framtida attacker av den här typen genom att åtgärda säkerhets rekommendationerna i avsnittet **förhindra framtida attacker** .
1.  Använd avsnittet **Utlös automatiserat svar** för att utlösa en Logic app med automatiserade svars steg.
1.  Om den identifierade aktiviteten *inte är* skadlig kan du ignorera framtida aviseringar av den här typen med hjälp av avsnittet **utelämna liknande aviseringar** .

1.  När du har slutfört undersökningen i aviseringen och svarat på lämpligt sätt ändrar du statusen till **avstängt**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Sidan säkerhets aviseringar":::

    Aviseringen tas bort från huvud aviserings listan. Du kan använda filtret från sidan aviserings lista om du vill visa alla aviseringar **med statusen** avvisad.

1.  Alternativt kan du ge feedback om en avisering till Microsoft:
    1. Markera aviseringen som **användbar** eller **inte användbar** och tillhandahålla
    1. Välj en orsak och Lägg till en kommentar.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Sidan säkerhets aviseringar":::

    > [!TIP]
    > Vi går igenom din feedback för att förbättra våra algoritmer och ger bättre säkerhets aviseringar.

## <a name="end-the-tutorial"></a>Avsluta självstudien

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med efterföljande snabb starter och självstudier måste du ha automatisk etablering och Azure Defender aktiverat. 

Om du inte planerar att fortsätta eller vill inaktivera någon av dessa funktioner:

1. Gå tillbaka till Security Center huvud menyn och välj **priser och inställningar**.
1. Välj relevant prenumeration.
1. Om du vill nedgradera väljer du **Azure Defender av**.
1. Om du vill inaktivera automatisk etablering öppnar du sidan **data insamling** och ställer in **Automatisk etablering** till **av**.
4. Välj **Spara**.

>[!NOTE]
> Om du inaktiverar automatisk etablering tas inte Log Analytics agenten bort från virtuella Azure-datorer som redan har agenten. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig om Security Center funktioner som ska användas när du svarar på en säkerhets avisering. Information om relaterade material finns i:

- [Svara på Azure Defender för Key Vault-aviseringar](defender-for-key-vault-usage.md)
- [Säkerhets aviseringar – en referens guide](alerts-reference.md)
- [Introduktion till Azure Defender](azure-defender.md)