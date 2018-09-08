---
title: Azure Security Center-säkerhetsprinciper kan ställas in individuellt eller som en del av Azure-principer | Microsoft Docs
description: Det här dokumentet hjälper dig att ange principer i Azure Security Center eller i Azure Policy.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/5/2018
ms.author: terrylan
ms.openlocfilehash: bc6226d462bac7e9c50ce3f348007023bf861ec3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162299"
---
# <a name="setting-security-policies-in-security-center-or-in-azure-policy"></a>Ange säkerhetsprinciper i Security Center eller i Azure Policy

Den här artikeln hjälper dig att konfigurera säkerhetsprinciper i Azure Security Center. Azure Scurity Center principer som integreras med Azure-principer så att du kan ställa in dem antingen i Security Center på en viss prenumeration eller i [Azure Policy](../azure-policy/azure-policy-introduction.md), vilket gör att du kan ange principer över hanteringsgrupper och över flera prenumerationer...

## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. Du kan definiera principer för dina Azure-prenumerationer och anpassa dem till din typ av arbetsbelastning eller känslighet för dina data i Azure Security Center. Program som använder reglerade data, till exempel personligt identifierbar information kan till exempel kräva en högre säkerhetsnivå än andra arbetsbelastningar. Om du vill ange en princip för prenumerationer eller hanteringsgrupper, ställa in dem [Azure Policy](../azure-policy/azure-policy-introduction.md).

> [!NOTE]
> Om du tidigare har konfigurerat säkerhetsprinciper för en prenumeration som en del av en hanteringsgrupp eller har flera princip rolltilldelningar visas dessa principer nedtonade i Security Center så att du kan hantera principerna på gruppnivå hantering via Azure Sidan för principen. 

## <a name="how-security-policies-work"></a>Hur fungerar säkerhetsprinciper?
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principer i Security Center eller använda Azure Policy för att göra följande:
- Skapa nya principdefinitioner.
- Tilldela principer över hanteringsgrupper och prenumerationer som motsvarar en hel organisation eller affärsenhet inom organisationen.
- Övervaka principefterlevnad.

Om du vill ha mer information om Azure Policy kan du läsa [Create and manage policies to enforce compliance](../azure-policy/create-manage-policy.md) (Skapa och hantera principer för att genomdriva efterlevnad).

En Azure-princip består av följande komponenter:

- En **princip** är en regel
- Ett **initiativ** är en samling principer
- En **tilldelning** är en tillämpning av ett initiativ eller en princip på ett specifikt omfång (hanteringsgrupp, prenumeration eller resursgrupp)

En resurs utvärderas mot de principer den tilldelats och får en efterlevnadsgrad utifrån antalet principer som gäller för resursen.

## <a name="who-can-edit-security-policies"></a>Vem kan redigera säkerhetsprinciper?
Security Center använder rollbaserad åtkomstkontroll (RBAC), som erbjuder inbyggda roller som kan tilldelas till användare, grupper och tjänster i Azure. När användarna öppnar Security Center, visas endast information som är relaterad till resurser som de har åtkomst till. Vilket innebär att användare har tilldelats rollen ägare, deltagare eller läsare i prenumeration eller resursgrupp gruppen som en resurs hör till. Förutom dessa roller finns två specifika roller i Security Center:

- Säkerhetsläsare: har visa rättigheter till Security Center, som innehåller rekommendationer, aviseringar, principer och hälsa, men de kan inte göra ändringar.
- Säkerhetsadministratör: har samma visa rättigheter som security-läsare och de kan också uppdatera säkerhetsprinciper och stänga rekommendationer och aviseringar.

## <a name="edit-security-policies"></a>Redigera säkerhetsprinciper
Du kan redigera standardsäkerhetsprincipen för var och en av dina Azure-prenumerationer och hanteringsgrupper i Security Center. Om du vill ändra en säkerhetsprincip måste du vara ägare, deltagare eller säkerhetsadministratör för prenumerationen eller hanteringsgruppen som innehåller den. Visa dina säkerhetsprinciper i Security Center:

> [!NOTE]
> Alla principer som angetts för en prenumeration som en del av en hanteringsgrupp eller har flera princip-rolltilldelningar visas nedtonade i Security Center. Du kan redigera dessa principer i [Azure Policy](../azure-policy/azure-policy-introduction.md). 

1. Klicka på **Säkerhetsprincip** under **Princip och efterlevnad** på instrumentpanelen i **Security Center**. **Principhanteringen** öppnas.

    ![Fönstret för principhantering](./media/security-center-azure-policy/security-center-policies-fig10.png)

  I principhanteringen visas antalet hanteringsgrupper, prenumerationer och arbetsytor, samt strukturen för hanteringsgruppen.

  > [!NOTE]
  > Instrumentpanelen i Security Center kan visa ett högre antal prenumerationer under **Prenumerationstäckning** än antalet prenumerationer som visas under **Principhantering**. Prenumerationstäckningen visar antalet prenumerationer av typen Standard, Kostnadsfri och Omfattas inte. Security Center har inte aktiverats för Omfattas inte-prenumerationer och de visas inte under **Principhantering**.
  >
  >

  Tabellens kolumner:

 - Policy Initiative Assignment (Principinitiativstilldelning) – Inbyggda principer och initiativ i Security Center som tilldelas till en prenumeration eller hanteringsgrupp.
 - Compliance (Efterlevnad) – Total efterlevnadsgrad för en hanteringsgrupp, prenumeration eller arbetsyta. Efterlevnadsgraden är det viktade medelvärdet av tilldelningarna. Viktade medelvärdesfaktorer är antalet principer i en enda tilldelning och antalet resurser som omfattas av tilldelningen.

 Om din prenumeration till exempel innehåller två virtuella datorer som är tilldelade ett initiativ med fem principer har prenumerationen 10 utvärderingar. Om en av de virtuella datorerna inte uppfyller två av principerna är den totala efterlevnadsgraden 80 % för din prenumerations tilldelning.

 - Coverage (Täckning) – Visar prisnivån (Kostnadsfri eller Standard) för hanteringsgruppen, prenumerationen eller arbetsytan.  Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
 - Settings (Inställningar) – För prenumerationer finns länken **Edit settings** (Redigera inställningar). Välj **Edit settings** (Redigera inställningar) om du vill uppdatera prenumerationsinställningar som datainsamling, prisnivå och e-postaviseringar.

2. Välj den prenumeration eller hanteringsgrupp som du vill aktivera en säkerhetsprincip för. **Säkerhetsprincip** öppnas.

3.  Under **Säkerhetsprincip** väljer du **På** för de kontroller du vill att Security Center ska övervaka och ge rekommendationer om.  Välj **Av** om du inte vill att en kontroll ska övervakas av Security Center.

    ![Principkomponenter](./media/security-center-azure-policy/security-policy.png)

4. Välj **Spara**.

## <a name="available-security-policy-definitions"></a>Tillgängliga säkerhetsprincipdefinitioner

Använd följande tabell som referens för att förstå de principdefinitioner som finns tillgängliga i standard-säkerhetsprincipen:

| Princip | Vad den aktiverade principen gör |
| --- | --- |
| Systemuppdateringar |Hämtar en daglig lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services. Den hämtade listan beror på vilken tjänst som har konfigurerats för de virtuella datorerna och den rekommenderar att saknade uppdateringar tillämpas. I Linux-system använder principen pakethanteringssystemet med distribution för att se vilka paket som har tillgängliga uppdateringar. Den söker också efter säkerhetsuppdateringar och viktiga uppdateringar från virtuella datorer i [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Säkerhetskonfigurationer |Analyserar dagligen konfigurationer av operativsystemet för att fastställa problem som kan göra den virtuella datorn sårbar för angrepp. Principen rekommenderar också konfigurationsändringar för att åtgärda dessa sårbarheter. Se [listan med rekommenderade baslinjer](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för mer information om vilka konfigurationer som övervakas. (Just nu stöds inte Windows Server 2016 fullt ut.) |
| Slutpunktsskydd |Rekommendationer om att slutpunktsskydd ska installeras på alla virtuella datorer (VM) med Windows så att virus, spionprogram och annan skadlig programvara kan identifieras och tas bort. |
| Diskkryptering |Rekommenderar att aktivera diskkryptering på alla virtuella datorer för att förbättra skydd av data i vila. |
| Nätverkssäkerhetsgrupper |Rekommenderar att [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) ska konfigureras för kontroll av inkommande och utgående trafik till virtuella datorer med offentliga slutpunkter. Nätverkssäkerhetsgrupper som konfigureras för ett undernät ärvs av alla VM-nätverksgränssnitt om inget annat anges. Förutom att kontrollera om en nätverkssäkerhetsgrupp har konfigurerats kontrollerar även den här principen att inkommande säkerhetsregler för att se om det finns några regler som tillåter inkommande trafik. |
| Brandvägg för webbaserade program |Rekommenderar att en brandvägg för webbaserade program konfigureras på virtuella datorer när något av följande stämmer: <ul><li>En [Offentlig IP på instansnivå](../virtual-network/virtual-networks-instance-level-public-ip.md) används och de inkommande säkerhetsreglerna för den associerade nätverkssäkerhetsgruppen är konfigurerad för att tillåta åtkomst till port 80/443.</li><li>En belastningsutjämnad IP-adress används och associerad nätverksbelastning och inkommande NAT-regler (Network Address Translation) är konfigurerade för att tillåta åtkomst till port 80/443. Mer information finns i [Azure Resource Manager-stöd för lastbalanserare](../load-balancer/load-balancer-arm.md).</li> |
| Nästa generations brandvägg |Nätverksskyddet utökas utöver nätverkssäkerhetsgrupperna, som är inbyggda i Azure. Security Center identifierar distributioner som nästa generations brandvägg rekommenderas för och du kan sedan göra en virtuell installation. |
| SQL-granskning och hotidentifiering |Rekommenderar att granskning av åtkomst till Azure-databasen aktiveras för lag- och regelefterlevnad, avancerad hotidentifiering och undersökning. |
| SQL-kryptering |Rekommenderar att kryptering i vila ska aktiveras för Azure SQL Database, tillhörande säkerhetskopior och transaktionsloggfiler. Även om intrång sker i dessa data så går dem inte att läsa. |
| Sårbarhetsbedömning |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| Lagringskryptering |Den här funktionen är för närvarande tillgänglig för Azure Blob Storage och Azure Files. När du har aktiverat Kryptering av lagringstjänst kommer endast nya data att krypteras. Befintliga filer i lagringskontot förblir okrypterade. |
| JIT-nätverksåtkomst |När JIT-nätverksåtkomst (Just-In-Time) är aktiverat begränsar Security Center inkommande trafik till dina virtuella Azure-datorer genom att skapa en gruppregel för nätverkssäkerhet. Du väljer vilka portar på den virtuella datorn som inkommande trafik ska begränsas till. Mer information finns i [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Hantera åtkomsten till virtuella datorer med Just-In-Time). |

## <a name="management-groups"></a>Hanteringsgrupper
Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsprinciper på hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de principer som tillämpas på hanteringsgruppen. Varje katalog tilldelas en hanteringsgrupp på översta nivån som kallas rothanteringsgruppen. Rothanteringsgruppen är inbyggd i hierarkin så att alla hanteringsgrupper och prenumerationer är dess underordnade element. Med hjälp av rothanteringsgruppen kan globala principer och RBAC-tilldelningar tillämpas på katalognivå. Hanteringsgrupper konfigureras för användning med Azure Security Center genom att följa anvisningarna i artikeln om att få [synlighet i hela klientorganisationen för Azure Security Center](security-center-management-groups.md). 

> [!NOTE]
> Det är viktigt att du är införstådd med hierarkin för hanteringsgrupper och prenumerationer. Du kan läsa mer om hanteringsgrupper, rothantering och hanteringsgruppåtkomst i artikeln om att [organisera dina resurser med Azures hanteringsgrupper](../azure-resource-manager/management-groups-overview.md#root-management-group-for-each-directory).
>
>


## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md): Här får du lära dig att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Få synlighet i hela klientorganisationen för Azure Security Center](security-center-management-groups.md): Lär dig hur du konfigurerar hanteringsgrupper för Azure Security Center. 
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

Mer information om Azure Policy finns i [Vad är Azure Policy?](../azure-policy/azure-policy-introduction.md)
