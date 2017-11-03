---
title: "Använda Azure Security Center rekommendationer för att förbättra säkerheten | Microsoft Docs"
description: " Lär dig använda säkerhetsprinciper och säkerhetsrekommendationer i Azure Security Center för att minska en säkerhetsattack med. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 0616f5e501324bfd821c1455ce234602f1fcf1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Använda Azure Security Center rekommendationer för att förbättra säkerheten
Du kan minska risken för en betydande säkerhetshändelse genom att konfigurera en säkerhetsprincip och implementera rekommendationerna i Azure Security Center. Den här artikeln visar hur du använder säkerhetsprinciper och säkerhetsrekommendationer i Security Center för att minska en säkerhetsattack med.

> [!NOTE]
> Den här artikeln bygger på de roller och begrepp som introduceras i Security Center [planerings-och operations](security-center-planning-and-operations-guide.md). Det är en bra idé att granska Planeringsguiden innan du fortsätter.
>
>

## <a name="managing-security-recommendations"></a>Hantera säkerhetsrekommendationer
En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom en viss prenumeration eller resursgrupp. I Security Center kan du definiera principer utifrån företagets säkerhetskrav. Läs mer i [ställa in säkerhetsprinciper i Security Center](security-center-policies.md).

Säkerhetsprinciper för resursgrupper ärvs från prenumerationsnivån.

![Arv av säkerhet][1]

Om du behöver anpassade principer i särskilda resursgrupper kan inaktivera du arv i resursgruppen. Om du vill inaktivera, Ställ in arv på unik för bladet med säkerhetsprinciper och anpassa de kontroller som Security Center visar rekommendationer för.

Om du har arbetsbelastningar som inte kräver SQL Database Transparent Data kryptering (TDE) principen, inaktiverar du principen på prenumerationsnivå och aktiverar den bara i de resursgrupper där SQL TDE krävs.

> [!NOTE]
> Om en princip på prenumerationsnivå motsäger en princip på resursgruppsnivå är det principen på resursgruppsnivå som gäller i första hand.
>
>

Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsrisker, skapar rekommendationer baserat på de kontroller som anges i säkerhetsprincipen. Rekommendationerna som leder dig genom processen att konfigurera nödvändiga säkerhetsåtgärder.

Aktuella princip rekommendationerna i Security Center fokus på systemuppdateringar, Operativsystemets konfiguration nätverkssäkerhetsgrupper på undernät och virtuella datorer (VM), SQL Database Auditing TDE för SQL-databasen, och webbprogrammet brandväggar. Den senaste täckning av Security Center-rekommendationerna finns [hantera säkerhetsrekommendationer i Security Center](security-center-recommendations.md).

## <a name="scenario"></a>Scenario
Det här scenariot visar hur du använder Security Center för att minska risken för en betydande säkerhetsincident genom att övervaka Security Center-rekommendationerna och vidtar åtgärder. Scenariot använder det fiktiva företaget, Contoso och roller som visas i Security Center [planerings-och operations](security-center-planning-and-operations-guide.md#security-roles-and-access-controls). Rollerna representerar personer och grupper som kan använda Security Center för att utföra olika säkerhetsrelaterade uppgifter. Rollerna är:

![Scenario-roller][2]

Contoso nyligen har migreras några av sina lokala resurser till Azure. Contoso vill implementera och underhålla skydd som skydda sina mot en säkerhetsattack med på sina resurser i molnet.

## <a name="recommended-solution"></a>Rekommenderad lösning
En lösning är att använda Security Center för att förhindra och identifiera säkerhetsproblem. Contoso har åtkomst till Security Center via sina Azure-prenumeration. Den [kostnadsfria nivån](security-center-pricing.md) av Security Center aktiveras automatiskt på alla Azure-prenumerationer och insamling av data är aktiverat på alla virtuella datorer i prenumerationen.

David i Contosos IT-säkerhet, konfigurerar en **säkerhetsprincip** med hjälp av Security Center. Security Center analyserar säkerhetstillståndet hos Contosos Azure-resurser. När Security Center identifierar potentiella säkerhetsrisker, skapas **rekommendationer** baserat på de kontroller som anges i säkerhetsprincipen.

Jeff molnansvarig, ansvarar för att implementera och underhålla skydd i enlighet med Contosos säkerhetsprinciper. Jeff kan övervaka de rekommendationer som skapats av Security Center att tillämpa skydd. Rekommendationer om hur Jeff konfigureringen av de nödvändiga säkerhetskontrollerna.

För Jeff att implementera och underhålla skydd och eliminera säkerhetsproblem behöver han:

- Övervaka säkerhetsrekommendationer som tillhandahålls av Security Center
- Utvärdera säkerhetsrekommendationer och bestämma om han ska använda eller ignorera
- Tillämpa säkerhetsrekommendationer

Vi gör Jeffs om du vill se hur han använder Security Center-rekommendationerna för att hjälpa honom genom att konfigurera kontroller för att minimera säkerhetsproblem.

## <a name="how-to-implement-this-solution"></a>Hur man implementerar denna lösning
Jeff loggar in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/) och öppnar konsolen Security Center. Som en del av sin dagliga övervakningsaktiviteter kontrollerar han om det finns säkerhetsrekommendationer genom att utföra följande steg:

1. Jeff väljer den **rekommendationer** öppna **rekommendationer**.
   ![Välj panelen rekommendationer][3]
2. Jeff granskar lista över rekommendationer. Han ser att Security Center tillhandahåller lista över rekommendationerna i prioritetsordning, från högsta prioritet till lägsta prioritet. Han bestämmer sig att adressera en hög prioritet rekommendation på listan. Han väljer **installera Endpoint Protection** under **rekommendationer**.
3. **Installera Endpoint Protection** öppnas med en lista över virtuella datorer utan program mot skadlig kod aktiverat. Jeff går igenom listan över virtuella datorer, markerar alla virtuella datorer och väljer sedan **installera på 3 virtuella datorer**.
   ![Installera slutpunktsskydd][4]
4. **Välj Endpoint Protection** öppnar ger Jeff två program mot skadlig kod. Jeff väljer den **Microsoft Antimalware** lösning.
5. Mer information om program mot skadlig kod visas. Jeff väljer **skapa**.
   ![Microsoft program mot skadlig kod][5]
6. Jeff anger de nödvändiga inställningarna under **installera** och väljer **OK**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) är nu aktiv på de valda virtuella datorerna.

Jeff fortsätter att gå igenom hög prioritet och Medelhög prioritet rekommendationer att fatta beslut om genomförandet. Jeff refererar till den [hantera säkerhetsrekommendationer](security-center-recommendations.md) artikel för att förstå rekommendationerna och det var och en gör om han gäller den.

Jeff lär sig som [Microsoft Security Response Center (MSRC)](../security/azure-security-response-center.md) utför väljer säkerhetsövervakning av Azure-nätverk och infrastruktur och tar emot hot intelligence och missbruk klagomål från tredje part. Om Jeff erbjuder säkerhet kontaktinformation för Contosos Azure-prenumeration, Microsoft kontakter Contoso om MSRC upptäcker att Contoso kundinformation används redan av en olaglig eller obehörig part. Vi följer Jeff han gäller den **ange säkerhet kontaktinformation** rekommendation (en rekommendation allvarlighetsgraden medium i listan över rekommendationerna ovan).

1. Jeff väljer **ange säkerhet kontaktinformation** under **rekommendationer**, vilket öppnar **ange säkerhet kontaktinformation**.
2. Jeff väljer att tillhandahålla kontaktinformation i Azure-prenumerationen. En andra **ange säkerhet kontaktinformation** blad öppnas.
   ![Kontakta säkerhetsinformation][6]
3. Under **ange säkerhet kontaktinformation**, Jeff anger:

  - säkerhet kontakta e-postadresser avgränsade med kommatecken (det inte är en gräns för antalet e-postadresser som han kan ange)
  - en säkerhet kontakta telefonnummer

4. Jeff aktiverar också alternativet **Skicka mig e-postmeddelanden om aviseringar** att ta emot e-post om varningar med hög angelägenhetsgrad.
5. Jeff väljer **OK** tillämpa säkerhet kontaktinformation till Contosos prenumeration.

Slutligen Jeff granskar med låg prioritet rekommendationen **reparera OS säkerhetsrisker** och anger den här rekommendationen inte är tillämplig. Han vill stänga rekommendationen. Jeff väljer de tre punkterna till höger och väljer sedan **Ignorera**.
   ![Ignorera rekommendation][7]

## <a name="conclusion"></a>Slutsats
Övervakning av rekommendationerna i Security Center kan hjälpa dig att undvika säkerhetsproblem innan en attack inträffar. Du kan förhindra att en säkerhetsincident genom att implementera och underhålla skydd med säkerhetsprinciper i Security Center.

## <a name="next-steps"></a>Nästa steg
Det här scenariot visades hur du använder säkerhetsprinciper och säkerhetsrekommendationer i Security Center för att minska en säkerhetsattack med. Finns det [incidenter scenariot](security-center-incident-response.md) information om hur du har en incident åtgärdsplan på plats innan en attack äger rum.

Mer information om Security Center finns:

* [Övervakning av säkerhetshälsa](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervakning och bearbetning av säkerhetshändelser](security-center-events-dashboard.md) – Lär dig hur du övervakar och processen säkerhetshändelser samlas in med tiden.
* [Övervaka partnerlösningar](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Azure security nyheter och information.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
