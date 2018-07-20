---
title: Insamling av data i Azure Security Center | Microsoft Docs
description: " Lär dig hur du aktiverar datainsamling i Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2018
ms.author: rkarlin
ms.openlocfilehash: d70eb1a329b2d1ba560aecbbb4132d2a8e2b7df1
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160135"
---
# <a name="data-collection-in-azure-security-center"></a>Insamling av data i Azure Security Center
Security Center samlar in data från dina virtuella Azure-datorer (VM) och icke-Azure-datorer för att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys. Exempel på sådana data är: operativsystemets typ och version, operativsystemloggar (Windows-händelseloggar), kör processer, datornamn, IP-adresser, inloggad användare, AppLocker-händelser och klient-ID. Microsoft Monitoring Agent kopierar också kraschdumpfiler till din arbetsyta.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Aktivera automatisk etablering av Microsoft Monitoring Agent     
Automatisk etablering är inaktiverat som standard. När automatisk etablering är aktiverat, stöds etablerar Security Center Microsoft Monitoring Agent på alla virtuella Azure-datorer och alla nya som skapas. Automatisk försörjning rekommenderas starkt men manuell agentinstallation är också tillgängliga. [Lär dig hur du installerar tillägget Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension).

> [!NOTE]
> - Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser. Mer information finns i [inaktivera automatisk etablering](security-center-enable-data-collection.md#disable-automatic-provisioning) i den här artikeln. Virtuella datordisker och artefaktinsamling är aktiverade även om Automatisk etablering är inaktiverad.
> - Aktivera datainsamling för [anpassningsbara programkontroller](security-center-adaptive-application.md), konfigurerar en lokal AppLocker-princip i granskningsläge så att alla program i Security Center. Detta innebär att AppLocker att generera händelser som sedan kan samlas in och används av Security Center. Det är viktigt att Observera att den här principen inte konfigureras på alla datorer där det finns redan en konfigurerade AppLocker-principen. 
>

Så här aktiverar du automatisk försörjning för Microsoft Monitoring Agent:
1. Huvudmenyn i Security Center, Välj **säkerhetsprincip**.
2. Välj prenumerationen.

  ![Välj en prenumeration][7]

3. Under **Säkerhetsprincip** väljer du **Datainsamling**.
4. Under **Automatisk etablering**väljer **på** att aktivera automatisk etablering.
5. Välj **Spara**.

  ![Aktivera automatisk försörjning][1]

## <a name="default-workspace-configuration"></a>Standardkonfiguration för arbetsyta
Data som samlas in av Security Center lagras i Log Analytics-arbetsytor.  Du kan välja att låta data som samlas in från virtuella Azure-datorer lagras i arbetsytor som skapats av Security Center eller i en befintlig arbetsyta som du skapade.

Använda din befintliga Log Analytics-arbetsyta:
- Arbetsytan måste vara associerad med din valda Azure-prenumeration.
- Som ett minimum måste du ha läsbehörighet till arbetsytan.

Att välja en befintlig Log Analytics-arbetsyta:

1. Under **standardkonfigurationen för arbetstytan**väljer **använder en annan arbetsyta**.

   ![Välj en befintlig arbetsyta][2]

2. Välj en arbetsyta för att lagra insamlade data från den nedrullningsbara menyn.

  > [!NOTE]
  > Alla arbetsytor för alla dina prenumerationer är tillgängliga i nedrullningsbara menyn. Se [mellan val av arbetsyta prenumeration](security-center-enable-data-collection.md#cross-subscription-workspace-selection) för mer information.
  >
  >

3. Välj **Spara**.
4. När du har valt **spara**, tillfrågas du om du vill konfigurera om övervakade virtuella datorer.

   - Välj **nr** om du vill att de nya arbetsyteinställningarna tillämpas på nya datorer. De nya arbetsyteinställningarna gäller endast för nya agentinstallationer; Nyligen identifierade virtuella datorer som inte har Microsoft Monitoring Agent installerad.
   - Välj **Ja** om du vill att de nya arbetsyteinställningarna tillämpas på alla virtuella datorer. Dessutom kan återansluta varje virtuell dator som är anslutna till en Security Center som skapat arbetsyta till den nya målarbetsytan.

   > [!NOTE]
   > Om du väljer Ja måste du inte ta bort arbetsytor som skapats av Security Center tills alla virtuella datorer har återanslutit till den nya målarbetsytan. Den här åtgärden misslyckas om en arbetsyta har tagits bort för tidigt.
   >
   >

   - Välj **Avbryt** att avbryta åtgärden.

     ![Välj en befintlig arbetsyta][3]

## <a name="cross-subscription-workspace-selection"></a>Mellan prenumeration val av arbetsyta
När du väljer en arbetsyta för att lagra dina data finns alla arbetsytor för alla dina prenumerationer. Mellan prenumerationer kan val av arbetsyta du samla in data från virtuella datorer som körs i olika prenumerationer och lagra den på arbetsytan i ditt val. Den här funktionen fungerar för både virtuella datorer som körs på Linux och Windows.

> [!NOTE]
> Val av arbetsyta är en del av kostnadsfria nivån av Azure Security Center mellan prenumerationer. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
>
>

## <a name="data-collection-tier"></a>Samling datanivå
Security Center kan minska mängden händelser samtidigt tillräckligt med händelser för undersökning, granskning och hotidentifiering. Du kan välja rätt filtrera principer för dina prenumerationer och arbetsytor från fyra uppsättningar av händelser som ska samlas in av agenten.

- **Alla händelser** – för kunder som vill se till att alla händelser har samlats in. Detta är standardinställningen.
- **Vanliga** – det här är en uppsättning händelser som uppfyller de flesta kunder och låter dem till fullständiga spårningen.
- **Minimal** – en mindre uppsättning händelser för kunder som vill minimera händelse volymen.
- **Ingen** – inaktivera security händelseinsamling från säkerhet och AppLocker-loggar. För kunder som väljer det här alternativet kan ha sina security instrumentpaneler endast loggar från Windows-brandväggen och proaktiva utvärderingar som program mot skadlig kod, baslinjen och uppdatering.

> [!NOTE]
> Uppsättningarna security händelser är endast tillgängliga på standardnivån i Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
Dessa uppsättningar har utformats för att hantera vanliga scenarier. Se till att utvärdera vilket alternativ som passar dina behov innan du implementerar den.
>
>

Att fastställa de händelser som hör till den **vanliga** och **Minimal** händelse mängder, vi har samarbetat med kunder och branschstandarder för att lära dig om ofiltrerade frekvensen för varje händelse och deras användning. Vi använde följande riktlinjer i den här processen:

- **Minimal** – se till att den här uppsättningen omfattar endast de händelser som kan indikera en lyckad intrång och viktiga händelser som har en mycket låg volym. Exempelvis kan den här uppsättningen innehåller lyckade och misslyckade användarinloggning (händelse-ID 4624 4625), men den innehåller inte utloggning som är viktiga för granskning, men inte beskrivande för identifiering och har relativt stor volym. De flesta av datavolymen för den här uppsättningen är inloggningshändelser och skapa processhändelse (händelse-ID 4688).
- **Vanliga** -ange en fullständig användarspårning i den här uppsättningen. Den här uppsättningen innehåller till exempel både användarinloggningar och utloggningar (händelse-ID 4634). Vi tar granskning åtgärder som att ändringarna, viktiga domain controller Kerberos åtgärder och andra händelser som rekommenderas av organisationer inom.

Händelser som har mycket små volymer ingick i den gemensamma som huvudsakliga skälet att välja det över alla händelser är att minska mängden och inte för att filtrera bort specifika händelser.

Här är en fullständig uppdelning av säkerhets- och AppLocker händelse-ID för varje uppsättning:

| Datanivå | Insamlade händelser indikatorer |
| --- | --- |
| Minimalt | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Om du använder grupprincipobjekt (GPO), rekommenderar vi att du aktiverar granskningsprinciper processen skapa händelse 4688 och *CommandLine* fältet i händelsen 4688. Mer information om processen att skapa händelse 4688 finns i Security Center [vanliga frågor och svar](security-center-faq.md#what-happens-when-data-collection-is-enabled). Mer information om dessa granskningsprinciper, se [granska rekommendationer](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Aktivera datainsamling för [anpassningsbara programkontroller](security-center-adaptive-application.md), konfigurerar en lokal AppLocker-princip i granskningsläge så att alla program i Security Center. Detta innebär att AppLocker att generera händelser som sedan kan samlas in och används av Security Center. Det är viktigt att Observera att den här principen inte konfigureras på alla datorer där det finns redan en konfigurerade AppLocker-principen. 
>

Att välja din filtreringsprincip:
1. På den **säkerhetsprincip datainsamling** bladet Välj din filtreringsprincip under **säkerhetshändelser**.
2. Välj **Spara**.

   ![Välj filtrera principer][5]

## <a name="disable-automatic-provisioning"></a>Inaktivera automatisk etablering
Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhetsprincipen. Automatisk försörjning rekommenderas starkt för att få säkerhetsaviseringar och rekommendationer om systemuppdateringar, OS-säkerhetsproblem och endpoint protection.

> [!NOTE]
> Inaktivering av automatisk etablering tar inte bort Microsoft Monitoring Agent från virtuella Azure-datorer där agenten har etablerats.
>
>

1. Återgå till huvudmenyn i Security Center och välj säkerhetsprincipen.
2. Välj den prenumeration du vill avaktivera automatisk etablering för.
3. På den **säkerhetsprincip – datainsamling** bladet under **Automatisk etablering** Välj **av**.
4. Välj **Spara**.

  ![Inaktivera automatisk etablering][6]

När automatisk etablering är inaktiverat (inaktiverad), visas inte i konfigurationsavsnittet för standard-arbetsytan.

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur datainsamling och automatisk etablering i Security Center fungerar. I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
- [Datasäkerhet i Azure Security Center](security-center-data-security.md) – Lär dig hur data hanteras och skyddas i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här kan du hitta de senaste nyheterna och aktuell information om säkerheten i Azure .

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
