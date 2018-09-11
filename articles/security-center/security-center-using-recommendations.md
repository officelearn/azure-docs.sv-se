---
title: Använd Azure Security Center-rekommendationer för att förbättra säkerheten | Microsoft Docs
description: " Lär dig hur du använder säkerhetsprinciper och säkerhetsrekommendationer i Azure Security Center för att minska en säkerhetsangrepp. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: d5689e6b9b4d808c24bc4952f65904654b5e99f8
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297374"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Använd Azure Security Center-rekommendationer för att förbättra säkerheten
Du kan minska risken för en betydande säkerhetshändelse genom att konfigurera en säkerhetsprincip för och implementera rekommendationer som tillhandahålls av Azure Security Center. Den här artikeln visar hur du använder säkerhetsprinciper och säkerhetsrekommendationer i Security Center för att minska en säkerhetsangrepp.

> [!NOTE]
> Den här artikeln bygger på de roller och begreppen som förklaras i Security Center [planerings- och bruksanvisning](security-center-planning-and-operations-guide.md). Det är en bra idé att granska Planeringsguiden innan du fortsätter.
>
>

## <a name="managing-security-recommendations"></a>Hantera säkerhetsrekommendationer
En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom en viss prenumeration eller resursgrupp. I Security Center ställer in du principer enligt ditt företags säkerhetskrav. Mer information finns i [ange säkerhetsprinciper i Security Center](security-center-policies.md).

Säkerhetsprinciper för resursgrupper ärvs från prenumerationsnivån.

![Arv av principer för säkerhet][1]

Om du behöver särskilda principer i en viss resursgrupp kan inaktivera du arv i resursgruppen. Om du vill inaktivera, Ställ in arv till unik på bladet med säkerhetsprinciper och anpassa kontrollerna som Security Center visar rekommendationer för.

Om du har arbetsbelastningar som inte kräver SQL Database Transparent datakryptering (TDE) principen inaktiverar du principen på prenumerationsnivå och aktiverar den bara i de resursgrupper där SQL TDE krävs.

> [!NOTE]
> Om en princip på prenumerationsnivå motsäger en princip på resursgruppsnivå är det principen på resursgruppsnivå som gäller i första hand.
>
>

Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer baserat på de kontroller som angetts i säkerhetsprincipen. Via rekommendationerna får du genom processen med att konfigurera säkerhetskontrollerna som behövs.

Aktuella rekommendationer i Security Center fokuserar på systemuppdateringar, Operativsystemets konfiguration nätverkssäkerhetsgrupper på undernät och virtuella datorer (VM), SQL Database Auditing SQL Database TDE och brandväggar för webbaserade program. Den senaste täckningen av rekommendationerna i Security Center finns [hantera säkerhetsrekommendationer i Security Center](security-center-recommendations.md).

## <a name="scenario"></a>Scenario
Det här scenariot visar hur du använder Security Center för att minska risken för en betydande säkerhetsincident genom att övervaka Security Center-rekommendationer och vidta åtgärder. Scenariot använder fiktiva företag, Contoso och roller som visas i Security Center [planerings- och bruksanvisning](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). Rollerna som representerar personer och grupper som kan använda Security Center för att utföra olika säkerhetsrelaterade uppgifter. Rollerna är:

![Scenario-roller][2]

Contoso migrerade nyligen några av sina lokala resurser till Azure. Contoso vill implementera och underhålla skydd som skydda sina mot en säkerhetsangrepp av sina resurser i molnet.

## <a name="recommended-solution"></a>Rekommenderad lösning
En lösning är att använda Security Center för att förhindra och identifiera säkerhetsproblem. Contoso har åtkomst till Security Center via sina Azure-prenumeration. Den [kostnadsfria nivån](security-center-pricing.md) av Security Center aktiveras automatiskt på alla Azure-prenumerationer och datainsamling är aktiverat på alla virtuella datorer i prenumerationen.

David i Contosos IT-säkerhet, konfigurerar en **säkerhetsprincip** med hjälp av Security Center. Security Center analyserar säkerhetstillståndet för Contosos Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas **rekommendationer** baserat på de kontroller som angetts i säkerhetsprincipen.

Jeff är en molnansvarig ansvarar för att implementera och underhålla säkerheten i enlighet med Contosos säkerhetsprinciper. Jeff kan övervaka de rekommendationer som skapats av Security Center för att tillämpa skydd. Via rekommendationerna får Jeff genom processen med att konfigurera säkerhetskontrollerna som behövs.

För Jeff att implementera och underhålla skydd och eliminera säkerhetsproblem behöver han för att:

- Övervaka säkerhetsrekommendationer från Security Center
- Utvärdera säkerhetsrekommendationer och bestämma om han ska tillämpa eller avvisa
- Tillämpa säkerhetsrekommendationer

Vi gör Jeffs för att se hur han använder rekommendationerna i Security Center för att guida honom genom processen med att konfigurera kontroller för att minimera säkerhetsrisker.

## <a name="how-to-implement-this-solution"></a>Hur man implementerar denna lösning
Jeff loggar in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/) och öppnar Security Center-konsolen. Som en del av sin dagliga övervakning aktiviteter kan kontrollerar han om det finns säkerhetsrekommendationer genom att utföra följande steg:

1. Jeff väljer den **rekommendationer** panelen för att öppna **rekommendationer**.
   ![Välj panelen rekommendationer][3]
2. Jeff granskar lista över rekommendationer. Han ser att Security Center tillhandahåller lista över rekommendationer i prioritetsordning från lägst prioritet prioriteras högst. Han bestämmer sig att följa en rekommendation för hög prioritet i listan. Han väljer **installera Endpoint Protection** under **rekommendationer**.
3. **Installera Endpoint Protection** öppnas en lista över virtuella datorer utan program mot skadlig kod aktiverat. Jeff går igenom listan över virtuella datorer, väljer alla virtuella datorer och väljer sedan **installera på 3 virtuella datorer**.
   ![Installera slutpunktsskydd][4]
4. **Välj Endpoint Protection** öppnas ger Jeff två program mot skadlig kod. Jeff väljer den **Microsoft Antimalware** lösning.
5. Mer information om program mot skadlig kod visas. Jeff väljer **skapa**.
   ![Microsoft program mot skadlig kod][5]
6. Jeff anger nödvändiga konfigurationsinställningar under **installera** och väljer **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) nu är aktiv på de valda virtuella datorerna.

Jeff fortsätter att flytta via hög prioritet och Medelhög prioritet rekommendationer att fatta beslut om implementering. Jeff refererar till den [hantera säkerhetsrekommendationer](security-center-recommendations.md) artikeln för att förstå rekommendationer och vad var och en gör om han tillämpar den.

Jeff lär sig som [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) utför väljer säkerhetsövervakning för Azure-nätverk och infrastruktur och tar emot threat intelligence och missbruk klagomål från tredje part. Om Jeff tillhandahåller säkerhetskontaktuppgifter för Contosos Azure-prenumeration, Microsoft kontakter Contoso om MSRC identifierar den Contoso kunddata har använts av en obehörig part. Vi följer Jeff som han gäller den **ange säkerhetskontaktinformation** rekommendation (en rekommendation med allvarlighetsgrad medel i listan över rekommendationerna ovan).

1. Jeff väljer **ange säkerhetskontaktinformation** under **rekommendationer**, vilket öppnar **ange säkerhetskontaktinformation**.
2. Jeff väljer Azure-prenumeration som tillhandahåller kontaktinformation för. En andra **ange säkerhetskontaktinformation** blad öppnas.
   ![Kontaktuppgifter][6]
3. Under **ange säkerhetskontaktinformation**, Jeff anger:

  - security kontakta e-postadresser, avgränsade med kommatecken (det inte är en gräns för hur många e-postadresser som han kan ange)
  - en Kontakttelefonnummer

4. Jeff aktiverar också alternativet **Skicka mig e-postmeddelanden om aviseringar** att ta emot e-post om varningar med hög angelägenhetsgrad.
5. Jeff väljer **OK** att tillämpa security kontaktinformation till Contosos prenumeration.

Slutligen Jeff granskar med låg prioritet rekommendationen **åtgärda OS-säkerhetsproblem** och anger den här rekommendationen inte är tillämplig. Han vill ignorera rekommendationen. Jeff väljer du de tre punkterna till höger och väljer sedan **Stäng**.
   ![Ignorera rekommendationen][7]

## <a name="conclusion"></a>Sammanfattning
Övervakning av rekommendationer i Security Center kan hjälpa dig att eliminera säkerhetsrisker innan en attack inträffar. Du kan förhindra att en säkerhetsincident genom att implementera och underhålla säkerheten med säkerhetsprinciper i Security Center.

## <a name="next-steps"></a>Nästa steg
Det här scenariot visades hur du använder säkerhetsprinciper och säkerhetsrekommendationer i Security Center för att minska en säkerhetsangrepp. Se den [scenario med incidenthantering](security-center-incident-response.md) och lär dig att ha en incidenthanteringsplan på plats innan en attack äger rum.

Mer information om Security Center finns:

* [Övervakning av säkerhetshälsa](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervakning och bearbetning av säkerhetshändelser](security-center-events-dashboard.md) – Lär dig hur du övervakar och processen säkerhetshändelser som samlas in med tiden.
* [Övervaka partnerlösningar](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – senaste nytt i Azure-säkerhet och information.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
