---
title: Arbeta med säkerhets principer | Microsoft Docs
description: Den här artikeln beskriver hur du arbetar med säkerhets principer i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906885"
---
# <a name="working-with-security-policies"></a>Arbeta med säkerhetsprinciper

Den här artikeln förklarar hur du konfigurerar säkerhets principer och hur du visar dem i Security Center. 

## <a name="introduction-to-security-policies"></a>Introduktion till säkerhets principer

En säkerhets princip definierar den önskade konfigurationen av dina arbets belastningar och hjälper dig att se till att du följer säkerhets kraven för ditt företag eller din regulator.

Azure Security Center gör säkerhets rekommendationerna baserade på dina valda principer. Security Center principer baseras på princip initiativ som skapats i Azure Policy. Du kan använda [Azure policy](../governance/policy/overview.md) för att hantera principer och ange principer över hanterings grupper och över flera prenumerationer.

Security Center erbjuder följande alternativ för att arbeta med säkerhets principer:

* **Visa och redigera den inbyggda standard principen** – när du aktiverar Security Center tilldelas en inbyggd initiativ med namnet "ASC default" automatiskt till alla Security Center registrerade prenumerationer (kostnads fria eller standard nivåer). Du kan anpassa det här initiativet genom att aktivera eller inaktivera enskilda principer i den. Se listan över [inbyggda säkerhets principer](security-center-policy-definitions.md) för att förstå alternativen som är tillgängliga direkt.

* **Lägg till egna anpassade principer** – om du vill anpassa de säkerhets initiativ som tillämpas på din prenumeration kan du göra det i Security Center. Du får sedan rekommendationer om datorerna inte följer de principer som du skapar. Anvisningar om hur du skapar och tilldelar anpassade principer finns i [använda anpassade säkerhets principer](custom-security-policies.md).

* **Lägg till regler för efterlevnadsprinciper** -Security Centers instrument panelen för kontroll av efterlevnad visar status för alla utvärderingar i din miljö i samband med en viss standard eller regel (till exempel Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Mer information finns i [Förbättra regelefterlevnaden](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Hantera dina säkerhets principer

Visa dina säkerhetsprinciper i Security Center:

1. I instrument panelen **Security Center** väljer du **säkerhets princip**.

    ![Fönstret för principhantering](./media/security-center-policies/security-center-policy-mgt.png)

   På skärmen **princip hantering** kan du se antalet hanterings grupper, prenumerationer och arbets ytor samt hanterings gruppens struktur.

1. Välj den prenumeration eller hanterings grupp vars principer du vill visa.

1. Sidan säkerhets princip för den prenumerationen eller hanterings gruppen visas. Den visar de tillgängliga och tilldelade principerna.

   ![princip skärm](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Om det finns en etikett "MG ärvd" tillsammans med standard principen, innebär det att principen har tilldelats till en hanterings grupp och ärvts av den prenumeration som du visar.


1. Välj bland de tillgängliga alternativen på den här sidan:

    1. Om du vill arbeta med bransch principer klickar du på **Lägg till fler standarder**. Mer information finns i [Uppdatera till dynamiska Compliance-paket](update-regulatory-compliance-packages.md).

    1. Om du vill tilldela och hantera anpassade initiativ klickar du på **Lägg till anpassade initiativ**. Mer information finns i [använda anpassade säkerhets principer](custom-security-policies.md).

    1. Om du vill visa och redigera standard principen klickar du på **Visa gällande princip** och fortsätter enligt beskrivningen nedan. 

       ![princip skärm](./media/security-center-policies/policy-screen.png)
       
       Den här **säkerhets princip** skärmen visar den åtgärd som vidtas av de principer som har tilldelats den prenumeration eller hanterings grupp du valt.
       
       * Använd länkarna överst för att öppna en princip **tilldelning** som gäller för prenumerationen eller hanterings gruppen. Med dessa länkar kan du komma åt tilldelningen och redigera eller inaktivera principen. Om du till exempel ser att en viss princip tilldelning på ett effektivt sätt nekar Endpoint Protection använder du länken för att redigera eller inaktivera principen.
       
       * I listan över principer kan du se det effektiva tillämpnings programmet för principen i din prenumeration eller hanterings grupp. Inställningarna för varje princip som gäller för omfånget beaktas och det ackumulerade resultatet av de åtgärder som vidtagits av principen visas. Om till exempel en tilldelning av principen är inaktive rad, men i en annan som är inställd på AuditIfNotExist, gäller den kumulativa påverkan AuditIfNotExist. Den mer aktiva effekter har alltid företräde.
       
       * Principens inverkan kan vara: tillägg, granskning, AuditIfNotExists, Deny, DeployIfNotExists, disabled. Mer information om hur effekter tillämpas finns i [Förstå princip effekter](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > När du visar tilldelade principer kan du se flera tilldelningar och du kan se hur varje tilldelning har kon figurer ATS på egen hand.


## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhets principer?

Du kan redigera säkerhets principer via Azure Policy-portalen via REST API eller med hjälp av Windows PowerShell.

Security Center använder rollbaserad Access Control (RBAC), som innehåller inbyggda roller som kan tilldelas användare, grupper och tjänster i Azure. När användarna öppnar Security Center ser de bara information som är relaterad till de resurser som de har åtkomst till. Det innebär att användarna tilldelas rollen som *ägare*, *deltagare*eller *läsare* till resursens prenumeration. Det finns två olika Security Center roller och dessa roller:

- **Säkerhets läsare**: har behörighet att Security Center, som innehåller rekommendationer, aviseringar, principer och hälsa, men som inte kan göra ändringar.
- **Säkerhets administratör**: har samma visnings rättigheter som *säkerhets läsaren*, och de kan också uppdatera säkerhets principen och stänga rekommendationer och aviseringar.


## <a name="disable-security-policies"></a>Inaktivera säkerhets principer
Om standard säkerhets principen genererar en rekommendation som inte är relevant för din miljö kan du stoppa den genom att inaktivera den princip definition som skickar rekommendationen.
Mer information om rekommendationer finns i [hantera säkerhets rekommendationer](security-center-recommendations.md).

1. I Security Center går du till avsnittet **princip & efterlevnad** och klickar på **säkerhets princip**.

   ![princip hantering](./media/tutorial-security-policy/policy-management.png)

2. Klicka på den prenumeration eller hanterings grupp som du vill inaktivera rekommendationen för.

   > [!NOTE]
   > Kom ihåg att en hanterings grupp tillämpar sina principer på sina prenumerationer. Om du inaktiverar en prenumerations princip och prenumerationen tillhör en hanterings grupp som fortfarande använder samma princip, kommer du att fortsätta att ta emot princip rekommendationerna. Principen kommer fortfarande att tillämpas från hanterings nivån och rekommendationerna kommer fortfarande att genereras.

1. Klicka på **Visa gällande princip**.

   ![Inaktivera princip](./media/tutorial-security-policy/view-effective-policy.png)

1. Klicka på den tilldelade principen.

   ![Inaktivera princip](./media/tutorial-security-policy/security-policy.png)

1. I avsnittet **parametrar** söker du efter principen som anropar rekommendationen som du vill inaktivera. Välj **inaktive rad** i list rutan.

   ![Inaktivera princip](./media/tutorial-security-policy/disable-policy.png)

1. Klicka på **Save** (Spara).

   > [!NOTE]
   > Det kan ta upp till 12 timmar innan ändringar av principen inaktive ras.



## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om säkerhets principer. Relaterad information finns i följande artiklar:

* Instruktioner för hur du anger principer med hjälp av PowerShell finns i [snabb start: skapa en princip tilldelning för att identifiera icke-kompatibla resurser med hjälp av modulen Azure PowerShell](../governance/policy/assign-policy-powershell.md)

* Instruktioner för hur du redigerar en säkerhets princip i Azure Policy finns i [skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md).

* Instruktioner för hur du ställer in en princip över prenumerationer eller på hanterings grupper med hjälp av Azure Policy finns [Azure policy?](../governance/policy/overview.md)