---
title: Insamling av data i Azure Security Center | Microsoft Docs
description: " Lär dig hur du aktiverar datainsamling i Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: terrylan
ms.openlocfilehash: d5f2c9960b720fc44f37956f9150e89d6425d154
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2018
---
# <a name="data-collection-in-azure-security-center"></a>Insamling av data i Azure Security Center
Security Center samlar in data från dina virtuella Azure-datorer (VM) och Azure-datorer att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent som läser olika säkerhetsrelaterade konfigurationer och händelseloggar på datorn och kopierar data till din arbetsyta för analys. Exempel på sådana data är: operativsystemets typ och version, operativsystemloggar (Windows-händelseloggar), processer som körs, datornamn, IP-adresser, inloggad användare och klient-ID. Microsoft Monitoring Agent kopieras också kraschdumpfiler till arbetsytan.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Aktivera automatisk etablering av Microsoft Monitoring Agent     
När automatisk etablering är aktiverat, stöds Security Center tillhandahåller Microsoft Monitoring Agent på alla virtuella datorer i Azure och nya filer som skapas. Automatisk etablering rekommenderas och krävs för prenumerationer på standardnivån av Security Center.

> [!NOTE]
> Inaktivera automatisk etablering gränser säkerhetsövervakning för dina resurser. Läs mer i [inaktivera automatisk etablering](security-center-enable-data-collection.md#disable-automatic-provisioning) i den här artikeln. Virtuella diskbilder aktiveras artefakt samlingen och även om Automatisk etablering har inaktiverats.
>
>

Aktivera automatisk etablering av Microsoft Monitoring Agent:
1. Välj under huvudmenyn Security Center **säkerhetsprincip**.
2. Välj prenumerationen.
3. Under **säkerhetsprincip**väljer **datainsamling**.
4. Under **Onboarding**väljer **på** att aktivera automatisk etablering.
5. Välj **Spara**.

![Aktivera automatisk etablering][1]

## <a name="default-workspace-configuration"></a>Standardkonfiguration för arbetsyta
Data som samlas in av Security Center lagras i logganalys arbetsytor.  Du kan välja att få data som samlas in från virtuella datorer lagras i arbetsytor som skapats av Security Center eller i en befintlig arbetsyta som du skapade i Azure.

Använda din befintliga logganalys-arbetsyta:
- Arbetsytan måste vara kopplad till din valda Azure-prenumeration.
- Åtminstone, måste du ha läsbehörighet till arbetsytan.

Att välja en befintlig logganalys-arbetsyta:

1. Under **säkerhetsprincip – datainsamling**väljer **använder en annan arbetsyta**.

   ![Välj en befintlig arbetsyta][2]

2. Välj en arbetsyta för att spara insamlade data i den nedrullningsbara menyn.

> [!NOTE]
> I nedrullningsbara menyn visas arbetsytor som du har åtkomst till och finns i din Azure-prenumeration.
>
>

3. Välj **Spara**.
4. När du har valt **spara**, tillfrågas du om du vill konfigurera om övervakas virtuella datorer.

   - Välj **nr** om du vill tillämpa på nya virtuella datorer bara de nya arbetsyteinställningarna för. De nya arbetsyteinställningarna gäller endast för nya agentinstallationer; Nyligen identifierade virtuella datorer som inte har Microsoft Monitoring Agent installerad.
   - Välj **Ja** om du vill att de nya arbetsyteinställningarna för ska tillämpas på alla virtuella datorer. Dessutom återansluta var ansluten till en Security Center skapade arbetsytan VM till den nya målarbetsytan.

   > [!NOTE]
   > Om du väljer Ja kan du inte ta bort arbetsytor som skapats av Security Center tills alla virtuella datorer har återanslutit till målarbetsytan. Den här åtgärden misslyckas om en arbetsyta tas bort för tidigt.
   >
   >

   - Välj **Avbryt** avbryta åtgärden.

   ![Välj en befintlig arbetsyta][3]

## <a name="data-collection-tier"></a>Samlingen datanivå
Security Center kan minska mängden händelser samtidigt tillräckligt med händelser för undersökning, granskning och hotidentifiering. Du kan välja rätt filtrera principer för dina prenumerationer och arbetsytor från fyra uppsättningar av händelser som ska samlas in av agenten.

- **Alla händelser** – för kunder som vill kontrollera att alla händelser har samlats in. Detta är standardinställningen.
- **Vanliga** – detta är en uppsättning händelser som uppfyller de flesta kunder och låter dem en fullständig verifieringskedja.
- **Minimal** – en mindre uppsättning händelser för kunder som vill minimera händelse volymen.
- **Ingen** – inaktivera säkerhet händelseinsamling från säkerhets- och AppLocker-loggarna. För kunder som väljer det här alternativet har sina säkerhet instrumentpaneler endast Windows-brandväggen loggar och proaktiv bedömningar som program mot skadlig kod, baslinjen och uppdatering.

> [!NOTE]
> Dessa uppsättningar har utformats för att adressera vanliga scenarier. Se till att utvärdera vilket som passar dina behov före implementeringen.
>
>

Att fastställa de händelser som hör till den **vanliga** och **Minimal** händelse anger vi arbetat med kunder och standarder för att lära dig om ofiltrerade frekvensen för varje händelse och deras användning. Vi använde följande riktlinjer i den här processen:

- **Minimal** -Kontrollera att den här uppsättningen omfattar händelser som kan indikera en lyckad överträdelse och viktiga händelser som har mycket låg. Till exempel den här uppsättningen innehåller lyckade och misslyckade användarinloggning (händelse-ID: N 4624 4625), men det innehåller inte logga ut som är viktiga för granskning men inte användbar för att identifiera och har relativt hög volym. De flesta av datavolymen i den här uppsättningen är inloggningshändelser och process skapas händelser (händelse-ID 4688).
- **Vanliga** -ange en fullständig användaren verifieringskedja i den här uppsättningen. Den här uppsättningen innehåller till exempel både användarinloggningar och användarutloggning (händelse-ID 4634). Vi inkludera granskning åtgärder som ändringarna, viktiga domain controller Kerberos åtgärder och andra händelser som rekommenderas av organisationer inom.

Händelser som har mycket små volymer ingick i uppsättningen som huvudsakliga syfte att välja det över alla händelser är att minska volymen och inte för att filtrera ut specifika händelser.

Här är en fullständig uppdelning av den säkerhet och AppLocker händelsen-ID för varje uppsättning:

| Datanivå | Insamlade händelser indikatorer |
| --- | --- |
| Minimal | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Gemensamma (standard) | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> Om du använder grupprincipobjekt (GPO), rekommenderas att du aktiverar granskningsprinciper processen att skapa händelsen 4688 och *CommandLine* fältet inuti 4688-händelse. Mer information om processen att skapa händelsen 4688 finns i Security Center [vanliga frågor och svar](security-center-faq.md#what-happens-when-data-collection-is-enabled). Mer information om dessa granskningsprinciper, se [Granska principrekommendationer](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
>
>

Välja din princip för filtrering:
1. På den **inställningar för säkerhetsprincip &** bladet Välj din filtrering princip under **säkerhetshändelser**.
2. Välj **Spara**.

   ![Välj filtrera principer][5]

## <a name="disable-automatic-provisioning"></a>Inaktivera automatisk etablering
Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhetsprincipen. Automatisk etablering rekommenderas för att få säkerhetsaviseringar och rekommendationer om systemuppdateringar, OS säkerhetsrisker och endpoint protection.

> [!NOTE]
> Inaktivera automatisk etablering tar inte bort Microsoft Monitoring Agent från virtuella Azure-datorer där agenten har etablerats.
>
>

1. Tillbaka till huvudmenyn Security Center och väljer säkerhetsprincipen.

   ![Inaktivera automatisk etablering][6]

2. Välj den prenumeration som du vill inaktivera automatisk etablering.
3. På den **säkerhetsprincip – datainsamling** bladet under **Onboarding** Välj **av** att inaktivera automatisk etablering.
4. Välj **Spara**.  

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur datainsamling och automatisk etablering i Security Center fungerar. I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig rekommendationer för att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
- [Datasäkerhet i Azure Security Center](security-center-data-security.md) – Lär dig hur data hanteras och garanteras i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
