---
title: Azure Security Center-säkerhetsprinciper kan ställa in som en del av Azure-principer och visas i Security Center | Microsoft Docs
description: Det här dokumentet hjälper dig att ange principer i Azure Policy eller visa dem i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2018
ms.author: rkarlin
ms.openlocfilehash: 330b66e64484417e50f39c35cf90a6fd62b1e888
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334677"
---
# <a name="view-security-policies"></a>Visa säkerhetsprinciper

Den här artikeln förklarar hur säkerhetsprinciper har konfigurerats och hur du visar dem i Security Center. Azure Security Center tilldelar automatiskt dess [inbyggda säkerhetsprinciper](security-center-policy-definitions.md) på varje prenumeration som har publicerats. Du kan konfigurera dem i [Azure Policy](../azure-policy/azure-policy-introduction.md), som också kan du ange principer över hanteringsgrupper och över flera prenumerationer.

Anvisningar om hur du anger principer med hjälp av PowerShell finns i [Snabbstart: skapa en principtilldelning som identifierar icke-kompatibla resurser med hjälp av Azure RM PowerShell-modulen](../azure-policy/assign-policy-definition-ps.md).



## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. Du kan definiera principer för dina Azure-prenumerationer och anpassa dem till din typ av arbetsbelastning eller känslighet för dina data i Azure Policy. Program som använder reglerade data, till exempel personligt identifierbar information kan till exempel kräva en högre säkerhetsnivå än andra arbetsbelastningar. Om du vill ange en princip för prenumerationer eller hanteringsgrupper, ställa in dem [Azure Policy](../azure-policy/azure-policy-introduction.md).



Dina säkerhetsprinciper innehåller säkerhetsrekommendationer som du får i Azure Security Center. Du kan övervaka efterlevnad med dem för att hjälpa dig att upptäcka potentiella säkerhetsrisker och avhjälpa hot. Mer information om hur du avgör vilket alternativ som passar dig finns i listan över [inbyggda säkerhetsprinciper](security-center-policy-definitions.md).

### <a name="management-groups"></a>Hanteringsgrupper
Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsprinciper på hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de principer som tillämpas på hanteringsgruppen. Varje katalog tilldelas en hanteringsgrupp på översta nivån som kallas rothanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med hjälp av rothanteringsgruppen kan globala principer och RBAC-tilldelningar tillämpas på katalognivå. Om du vill konfigurera hanteringsgrupper för användning med Azure Security Center, följer du anvisningarna i [klienttäckande insyn för Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Det är viktigt att du är införstådd med hierarkin för hanteringsgrupper och prenumerationer. Du kan läsa mer om hanteringsgrupper, rothantering och hanteringsgruppåtkomst i artikeln om att [organisera dina resurser med Azures hanteringsgrupper](../governance/management-groups/index.md#root-management-group-for-each-directory).
>

## <a name="how-security-policies-work"></a>Hur fungerar säkerhetsprinciper?
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principer i Azure-principen för att göra följande:
- Skapa nya principdefinitioner.
- Tilldela principer över hanteringsgrupper och prenumerationer som motsvarar en hel organisation eller affärsenhet inom organisationen.
- Övervaka principefterlevnad.

Om du vill ha mer information om Azure Policy kan du läsa [Create and manage policies to enforce compliance](../azure-policy/create-manage-policy.md) (Skapa och hantera principer för att genomdriva efterlevnad).

En Azure-princip består av följande komponenter:

- En **princip** är en regel.
- En **initiativ** är en samling av principer.
- En **tilldelning** är program till ett initiativ eller en princip för ett visst område (hanteringsgruppen, prenumeration eller resursgrupp).

En resurs utvärderas mot de principer den tilldelats och får en efterlevnadsgrad utifrån antalet principer som gäller för resursen.

## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhetsprinciper?
Security Center använder rollbaserad åtkomstkontroll (RBAC), som erbjuder inbyggda roller som kan tilldelas till användare, grupper och tjänster i Azure. När användarna öppnar Security Center, visas endast information som är relaterad till resurser som de har åtkomst till. Vilket innebär att användare har tilldelats rollen ägare, deltagare eller läsare i prenumeration eller resursgrupp gruppen som en resurs hör till. Förutom dessa roller finns två specifika roller i Security Center:

- Säkerhetsläsare: har visa rättigheter till Security Center, som innehåller rekommendationer, aviseringar, principer och hälsa, men de kan inte göra ändringar.
- Säkerhetsadministratör: har samma visa rättigheter som security-läsare och de kan också uppdatera säkerhetsprinciper och stänga rekommendationer och aviseringar.

## <a name="edit-security-policies"></a>Redigera säkerhetsprinciper
Du kan redigera standardsäkerhetsprincipen för var och en av dina Azure-prenumerationer och hanteringsgrupper i [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Om du vill ändra en säkerhetsprincip måste du vara ägare, deltagare eller säkerhetsadministratör för prenumerationen eller hanteringsgruppen som innehåller den.

Instruktioner om hur du redigerar en säkerhetsprincip i Azure Policy finns och [skapa och hantera principer för att tvinga kompatibilitet](../governance/policy/tutorials/create-and-manage.md).

## <a name="view-security-policies"></a>Visa säkerhetsprinciper

Visa dina säkerhetsprinciper i Security Center:

1. I den **Security Center** instrumentpanelen, väljer **säkerhetsprincip**.

    ![Fönstret för principhantering](./media/security-center-policies/security-center-policy-mgt.png)

  I den **principhantering** skärmen, du kan se antalet hanteringsgrupper, prenumerationer och arbetsytor, samt dina hanteringsgruppsstruktur.

  > [!NOTE]
  > - Instrumentpanelen i Security Center kan indikera ett högre antal prenumerationer under **prenumerationstäckning** än antalet prenumerationer som visas under **principhantering**. Prenumerationstäckningen visar antalet prenumerationer av typen Standard, Kostnadsfri och Omfattas inte. ”Inte motsvarar”-prenumerationer har inte Security Center aktiverat och visas inte **principhantering**.
  >

  Tabellens kolumner:

 - **Principinitiativsuppgift** – Security Center [inbyggda principer](security-center-policy-definitions.md) och initiativ som har tilldelats en prenumerations- eller grupp.
 - **Efterlevnad** – övergripande kompatibilitetspoäng för en hanteringsgrupp, en prenumeration eller en arbetsyta. Efterlevnadsgraden är det viktade medelvärdet av tilldelningarna. Viktade medelvärdesfaktorer är antalet principer i en enda tilldelning och antalet resurser som omfattas av tilldelningen.

 Om din prenumeration till exempel innehåller två virtuella datorer som är tilldelade ett initiativ med fem principer har prenumerationen 10 utvärderingar. Om en av de virtuella datorerna inte uppfyller två av principerna är den totala efterlevnadsgraden 80 % för din prenumerations tilldelning.

 - **Täckning** – identifierar prisnivån kostnadsfri eller Standard som hanteringsgruppen, prenumeration eller arbetsyta körs på.  Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
 - **Inställningar för** – prenumerationer har länken **redigera inställningar för**. Att välja **redigera inställningar för** låter dig uppdatera ditt [Security Center-inställningar](security-center-policies-overview.md) för varje prenumerations- eller grupp.

2. Välj den prenumeration eller hanteringsgrupp grupp vars principer som du vill visa.

  - Den **säkerhetsprincip** skärmen återspeglar den åtgärd som de principer som tilldelats för prenumerations- eller gruppen som du har valt.
  - Överst på sidan använder du länkarna för att öppna varje princip **tilldelning** som gäller för prenumerations- eller gruppen. Du kan använda länkarna för att komma åt tilldelningen och redigera eller inaktivera principen. Om du ser att en viss principtilldelning effektivt nekas slutpunktsskydd kan använda du länken till åtkomst till principen och redigera eller inaktivera den.
  - I listan med principer ser du en effektiv tillämpning av principen på din prenumeration eller hanteringsgrupp. Det innebär att inställningarna för varje princip som gäller för omfånget beaktas och du får det kumulativa resultatet av vilka åtgärder som vidtas av principen. Om principen är inaktiverad i en tilldelning, men i en annan som den är inställd på AuditIfNotExist, gäller den ackumulerade effekten AuditIfNotExist. Mer aktiva effekten har alltid företräde.
  - Effekten av de principer som kan vara: lägga till, granskning, AuditIfNotExists, neka, DeployIfNotExists, inaktiverad. Mer information om hur effekterna tillämpas finns [Förstå princip effekterna](../governance/policy/concepts/effects.md).

   ![princip för skärmen](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - När du visa tilldelade principer, du kan se flera tilldelningar och du kan se hur varje uppgift är konfigurerad på egen hand.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md): Här får du lära dig att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Få synlighet i hela klientorganisationen för Azure Security Center](security-center-management-groups.md): Lär dig hur du konfigurerar hanteringsgrupper för Azure Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns i [Vad är Azure Policy?](../azure-policy/azure-policy-introduction.md)
