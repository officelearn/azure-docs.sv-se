---
title: Arbeta med säkerhetsprinciper | Microsoft-dokument
description: I den här artikeln beskrivs hur du arbetar med säkerhetsprinciper i Azure Security Center.
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
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024888"
---
# <a name="working-with-security-policies"></a>Arbeta med säkerhetsprinciper

I den här artikeln beskrivs hur säkerhetsprinciper konfigureras och hur du visar dem i Security Center. 

## <a name="introduction-to-security-policies"></a>Introduktion till säkerhetsprinciper

En säkerhetsprincip definierar den önskade konfigurationen av dina arbetsbelastningar och hjälper till att säkerställa att du uppfyller säkerhetskraven för ditt företag eller tillsynsmyndigheter.

Azure Security Center gör sina säkerhetsrekommendationer baserat på dina valda principer. Security Center-principer baseras på principinitiativ som skapats i Azure Policy. Du kan använda [Azure Policy](../governance/policy/overview.md) för att hantera dina principer och ange principer för hanteringsgrupper och flera prenumerationer.

Security Center erbjuder följande alternativ för att arbeta med säkerhetsprinciper:

* **Visa och redigera den inbyggda standardprincipen** - När du aktiverar Security Center tilldelas ett inbyggt initiativ med namnet ASC-standard automatiskt till alla Security Center-registrerade prenumerationer (kostnadsfria eller standardnivåer). Om du vill anpassa det här initiativet kan du aktivera eller inaktivera enskilda principer i det. Se listan över [inbyggda säkerhetsprinciper](security-center-policy-definitions.md) för att förstå vilka alternativ som är tillgängliga direkt.

* **Lägg till egna anpassade principer** – Om du vill anpassa de säkerhetsinitiativ som tillämpas på din prenumeration kan du göra det i Security Center. Du får sedan rekommendationer om dina datorer inte följer de principer du skapar. Instruktioner om hur du skapar och tilldelar anpassade principer finns i [Använda anpassade säkerhetsprinciper](custom-security-policies.md).

* **Lägg till principer för regelefterlevnad** – Security Centers instrumentpanel för regelefterlevnad visar status för alla utvärderingar i din miljö i samband med en viss standard eller förordning (till exempel Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Mer information finns i [Förbättra regelefterlevnad.](security-center-compliance-dashboard.md)


## <a name="managing-your-security-policies"></a>Hantera dina säkerhetsprinciper

Visa dina säkerhetsprinciper i Security Center:

1. Välj **Säkerhetsprincipen**på instrumentpanelen **i Säkerhetscenter** .

    ![Fönstret för principhantering](./media/security-center-policies/security-center-policy-mgt.png)

   På skärmen **Principhantering** kan du se antalet hanteringsgrupper, prenumerationer och arbetsytor samt din hanteringsgruppstruktur.

1. Välj den prenumeration eller hanteringsgrupp vars principer du vill visa.

1. Säkerhetsprincipsidan för den prenumerations- eller hanteringsgruppen visas. Den visar tillgängliga och tilldelade principer.

   ![skärmen princip](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Om det finns en etikett "MG Ärvd" tillsammans med standardprincipen betyder det att principen har tilldelats en hanteringsgrupp och ärvts av prenumerationen du visar.


1. Välj bland de tillgängliga alternativen på den här sidan:

    1. Om du vill arbeta med branschpolicyer väljer du **Lägg till fler standarder**. Mer information finns i [Uppdatera till dynamiska efterlevnadspaket](update-regulatory-compliance-packages.md).

    1. Om du vill tilldela och hantera anpassade initiativ väljer du **Lägg till anpassade initiativ**. Mer information finns i [Använda anpassade säkerhetsprinciper](custom-security-policies.md).

    1. Om du vill visa och redigera standardprincipen väljer du **Visa effektiv princip** och fortsätter enligt beskrivningen nedan. 

       ![skärmen princip](./media/security-center-policies/policy-screen.png)
       
       Den här **säkerhetsprincipskärmen** visar de åtgärder som vidtas av de principer som tilldelats den prenumeration eller hanteringsgrupp som du har valt.
       
       * Använd länkarna högst upp för att öppna en **principtilldelning** som gäller för prenumerationen eller hanteringsgruppen. Med de här länkarna kan du komma åt tilldelningen och redigera eller inaktivera principen. Om du till exempel ser att en viss principtilldelning effektivt nekar slutpunktsskydd använder du länken för att redigera eller inaktivera principen.
       
       * I listan över principer kan du se ett effektivt program för principen för din prenumeration eller hanteringsgrupp. Inställningarna för varje princip som gäller för omfånget beaktas och det kumulativa resultatet av åtgärder som vidtas av principen visas. Om till exempel i en tilldelning av principen är inaktiverad, men i en annan är inställd på AuditIfNotExist, tillämpar den kumulativa effekten AuditIfNotExist. Den mer aktiva effekten har alltid företräde.
       
       * Policyns effekt kan vara: Lägg till, Granska, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Mer information om hur effekter tillämpas finns i [Förstå principeffekter](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > När du visar tilldelade principer kan du se flera tilldelningar och du kan se hur varje tilldelning konfigureras på egen hand.


## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhetsprinciper?

Du kan redigera säkerhetsprinciper via Azure Policy-portalen, via REST API eller med Windows PowerShell.

I Security Center används rollbaserad åtkomstkontroll, vilket innebär att det finns förinställda roller som kan tilldelas användare, grupper och tjänster i Azure. När användare öppnar Security Center ser de bara information som är relaterad till resurser som de har åtkomst till. Vilket innebär att användarna tilldelas rollen som *ägare,* *deltagare*eller *läsare* till resursens prenumeration. Förutom dessa roller finns det två specifika Security Center-roller:

- **Säkerhetsläsare**: Har vyrättigheter till Security Center, som innehåller rekommendationer, aviseringar, princip och hälsotillstånd, men de kan inte göra ändringar.
- **Säkerhetsadministratör**: Har samma vyrättigheter som *säkerhetsläsare*, och de kan också uppdatera säkerhetsprincipen och avvisa rekommendationer och aviseringar.


## <a name="disable-security-policies"></a>Inaktivera säkerhetsprinciper
Om standardsäkerhetsprincipen genererar en rekommendation som inte är relevant för din miljö kan du stoppa den genom att inaktivera principdefinitionen som skickar rekommendationen.
Mer information om rekommendationer finns i [Hantera säkerhetsrekommendationer](security-center-recommendations.md).

1. Välj **Säkerhetsprincip**i avsnittet **Princip & i** Security Center .

   ![policyhantering](./media/tutorial-security-policy/policy-management.png)

2. Välj den prenumeration eller hanteringsgrupp som du vill inaktivera rekommendationen för.

   > [!NOTE]
   > Kom ihåg att en hanteringsgrupp tillämpar sina policyer på prenumerationerna i den. Om du inaktiverar en policy för en prenumeration och prenumerationen tillhör en hanteringsgrupp som fortfarande använder samma policy så fortsätter du att få tillhörande rekommendationer. Policyn tillämpas fortfarande från hanteringsnivån och rekommendationerna fortsätter att genereras.

1. Välj **Visa effektiv princip**.

   ![inaktivera princip](./media/tutorial-security-policy/view-effective-policy.png)

1. Välj den tilldelade principen.

   ![inaktivera princip](./media/tutorial-security-policy/security-policy.png)

1. I avsnittet **PARAMETRAR** söker du efter den princip som anropar den rekommendation som du vill inaktivera och väljer Inaktiverad i listrutan och väljer **Inaktiverad** i listrutan

   ![inaktivera princip](./media/tutorial-security-policy/disable-policy.png)

1. Välj **Spara**.

   > [!NOTE]
   > Ändringarna av inaktivera princip kan ta upp till 12 timmar att börja gälla.



## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig mer om säkerhetsprinciper. Relaterad information finns i följande artiklar:

* Instruktioner om hur du anger principer med PowerShell finns i [Snabbstart: Skapa en principtilldelning för att identifiera icke-kompatibla resurser med Azure PowerShell-modulen](../governance/policy/assign-policy-powershell.md)

* Instruktioner om hur du redigerar en säkerhetsprincip i Azure Policy finns i [Skapa och hantera principer för att tillämpa efterlevnad](../governance/policy/tutorials/create-and-manage.md).

* Instruktioner om hur du anger en princip för prenumerationer eller hanteringsgrupper som använder Azure Policy finns i [Vad är Azure Policy?](../governance/policy/overview.md)