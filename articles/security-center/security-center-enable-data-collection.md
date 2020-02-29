---
title: Insamling av data i Azure Security Center | Microsoft Docs
description: Den här artikeln beskriver hur du installerar en Log Analytics agent och anger en Log Analytics arbets yta där insamlade data ska lagras.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 5236fd89ed99ca14bb7fc24e40ef79e12e5177d9
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919523"
---
# <a name="data-collection-in-azure-security-center"></a>Insamling av data i Azure Security Center
Security Center samlar in data från dina virtuella Azure-datorer, skalnings uppsättningar för virtuella datorer, IaaS behållare och icke-Azure (inklusive lokala) datorer för att övervaka säkerhets problem och hot. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din arbets yta för analys. Exempel på sådana data är: driva systemtyp och version, operativsystemloggar (Windows-händelseloggar), kör processer, datornamn, IP-adresser och inloggad användare. Log Analytics agenten kopierar även krasch dum par till din arbets yta.

Data insamling krävs för att ge insyn i saknade uppdateringar, felkonfigurerade OS-säkerhetsinställningar, Endpoint Protection-status och hälso-och hot skydd. 

Den här artikeln beskriver hur du installerar en Log Analytics agent och anger en Log Analytics arbets yta där insamlade data ska lagras. Både krävs för att aktivera insamling av data. 

> [!NOTE]
> - Data insamling behövs bara för beräknings resurser (virtuella datorer, skalnings uppsättningar för virtuella datorer, IaaS behållare och datorer som inte är Azure-datorer). Du kan dra nytta av Azure Security Center även om du inte etablera agenter; men du har begränsad säkerhet och de funktioner som anges ovan stöds inte.  
> - En lista över plattformar som stöds finns [i plattformar som stöds i Azure Security Center](security-center-os-coverage.md).
> - Om du lagrar data i Log Analytics, oavsett om du använder en ny eller befintlig arbets yta, kan ytterligare kostnader för data lagring uppstå. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

## Aktivera automatisk etablering av Log Analytics agent<a name="auto-provision-mma"></a>

Om du vill samla in data från datorerna bör du ha Log Analytics-agenten installerad. Installationen av agenten kan göras automatiskt (rekommenderas) eller så kan du installera agenten manuellt.  

>[!NOTE]
> Automatisk etablering är inaktiverat som standard. Om du vill ställa in Security Center att installera automatisk etablering som standard ställer du in den **på på**.
>

När automatisk etablering är aktiverat etablerar Security Center Log Analytics agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas. Automatisk försörjning rekommenderas starkt men manuell agentinstallation är också tillgängliga. [Lär dig hur du installerar Log Analytics agent-tillägget](#manual-agent).



Så här aktiverar du automatisk etablering av Log Analytics agent:
1. Under Security Center huvud menyn väljer du **pris & inställningar**.
2. Klicka på lämplig prenumeration

   ![Välj en prenumeration][7]

3. Välj **data insamling**.
4. Under **Automatisk etablering**väljer du **på** för att aktivera automatisk etablering.
5. Välj **Spara**.

   ![Aktivera automatisk försörjning][1]

>[!NOTE]
> - Instruktioner för hur du etablerar en befintlig installation finns i [Automatisk etablering i händelse av en befintlig agent installation](#preexisting).
> - Anvisningar om manuell etablering finns i [installera Log Analytics agent-tillägget manuellt](#manual-agent).
> - Anvisningar om hur du inaktiverar automatisk etablering finns i [inaktivera automatisk etablering](#offprovisioning).
> - Instruktioner för hur du integrerar Security Center med hjälp av PowerShell finns i [Automatisera onboarding av Azure Security Center med PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Arbetsytekonfiguration
Data som samlas in av Security Center lagras i Log Analytics-arbetsytor. Du kan välja för att ha data som samlas in från virtuella Azure-datorer lagras i arbetsytor som skapats av Security Center eller i en befintlig arbetsyta som du skapade. 

Arbetsytekonfiguration anges per prenumeration och många prenumerationer använder samma arbetsyta.

### <a name="using-a-workspace-created-by-security-center"></a>Med en arbetsyta som skapats av Security Center

Security center kan automatiskt skapa en standardarbetsyta där du kan lagra data. 

Välja den arbetsyta som skapats av Security Center:

1. Under **standard arbets ytans konfiguration**väljer du Använd arbets ytor som skapats av Security Center.
   ![välja pris nivå][10] 

1. Klicka på **Save** (Spara).<br>
    Security Center skapar en ny resurs och standardenhet arbetsyta i den geoplats och ansluter agenten till arbetsytan. Namngivningskonventionen för gruppen arbetsyta och resursen är:<br>
   **Arbets yta: DefaultWorkspace-[prenumerations-ID]-[geo]<br> resurs grupp: DefaultResourceGroup-[geo]**

   Om en prenumeration innehåller virtuella datorer från flera geolocations, skapar flera arbetsytor med Security Center. Flera arbetsytor skapas för att upprätthålla strängare data.
1. Security Center aktiverar automatiskt en Security Center-lösning på arbetsytan per prisnivån för prenumerationen. 

> [!NOTE]
> Log Analytics prisnivån för arbetsytor som skapats av Security Center påverkar inte Security Center fakturering. Security Center-fakturering baseras alltid på din säkerhetsprincip i Security Center och de lösningar som är installerade på en arbetsyta. För den kostnadsfria nivån aktiverar Security Center lösningen *SecurityCenterFree* på standardarbetsytan. För standardnivån aktiverar Security Center lösningen *Security* på standardarbetsytan.
> Att lagra data i Log Analytics kan debiteras ytterligare avgifter för data lagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

Mer information om befintliga Log Analytics-konton finns i [befintliga Log Analytics-kunder](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Med hjälp av en befintlig arbetsyta

Om du redan har en befintlig Log Analytics arbets yta kan du vilja använda samma arbets yta.

Om du vill använda din befintliga Log Analytics-arbetsyta som du måste ha läs- och skrivbehörighet på arbetsytan.

> [!NOTE]
> Lösningar som aktiverats på den befintliga arbetsytan tillämpas på Azure virtuella datorer som är anslutna till den. Detta kan resultera i ytterligare avgifter för betalda lösningar. För data överväganden av datasekretess, kontrollera att din valda arbetsyta är i rätt geografisk region.
> Att lagra data i Log Analytics kan debiteras ytterligare avgifter för data lagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

Att välja en befintlig Log Analytics-arbetsyta:

1. Under **standard arbets ytans konfiguration**väljer du **Använd en annan arbets yta**.

   ![Välj en befintlig arbetsyta][2]

2. Välj en arbetsyta för att lagra insamlade data från den nedrullningsbara menyn.

   > [!NOTE]
   > Alla arbetsytor för alla dina prenumerationer är tillgängliga i nedrullningsbara menyn. Mer information finns i avsnittet om [arbets ytor mellan prenumerationer](security-center-enable-data-collection.md#cross-subscription-workspace-selection) . Du måste ha behörighet att få åtkomst till arbetsytan.
   >
   >

3. Välj **Spara**.
4. När du har valt **Spara**blir du tillfrågad om du vill konfigurera om övervakade virtuella datorer som tidigare var anslutna till en standard arbets yta.

   - Välj **Nej** om du vill att de nya inställningarna för arbets ytan bara ska gälla för nya virtuella datorer. De nya inställningarna för arbets ytan gäller endast nya agent installationer. nyligen identifierade virtuella datorer som inte har Log Analytics-agenten installerad.
   - Välj **Ja** om du vill att de nya inställningarna för arbets ytan ska tillämpas på alla virtuella datorer. Dessutom kan återansluta varje virtuell dator som är anslutna till en Security Center som skapat arbetsyta till den nya målarbetsytan.

   > [!NOTE]
   > Om du väljer Ja måste du inte ta bort arbetsytor som skapats av Security Center tills alla virtuella datorer har återanslutit till den nya målarbetsytan. Den här åtgärden misslyckas om en arbetsyta har tagits bort för tidigt.
   >
   >

   - Välj **Avbryt** om du vill avbryta åtgärden.

     ![Välj en befintlig arbetsyta][3]

5. Välj pris nivå för den önskade arbets ytan som du vill ange Log Analytics agenten. <br>Ställer in prisnivån för arbetsytan om du vill använda en befintlig arbetsyta. Detta installerar security Center-lösning på arbetsytan om något inte redan finns.

    a.  På Security Center huvud menyn väljer du **pris & inställningar**.
     
    b.  Välj önskad arbets yta där du vill ansluta agenten.
        ![Välj arbets yta][7] c. Ställer in prisnivån.
        ![välja pris nivå][9]
   
   >[!NOTE]
   >Om arbets ytan redan har en **säkerhets** -eller **SecurityCenterFree** -lösning aktive rad anges prissättningen automatiskt. 

## <a name="cross-subscription-workspace-selection"></a>Val av arbetsyta mellan prenumerationer
När du väljer en arbetsyta där du vill lagra dina data finns alla arbetsytor för alla dina prenumerationer. Denna möjlighet att välja arbetsyta mellan prenumerationer gör det möjligt att samla in data från virtuella datorer som körs i olika prenumerationer och lagra dessa data på önskad arbetsyta. Det här alternativet är användbart om du använder en centraliserad arbetsyta i din organisation och vill använda den för insamling av säkerhetsdata. Mer information om hur du hanterar arbets ytor finns i [Hantera åtkomst till arbets yta](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Samling datanivå
När du väljer en nivå för datainsamling i Azure Security Center påverkar det endast lagringen av säkerhetshändelser i Log Analytics-arbetsytan. Log Analytics-agenten samlar fortfarande in och analyserar de säkerhets händelser som krävs för Azure Security Center s hot skydd, oavsett vilken nivå av säkerhets händelser som du väljer att lagra i din Log Analytics arbets yta (om det finns några). När du väljer att lagra säkerhetshändelser i din arbetsyta, aktiveras undersökning, sökning och granskning av dessa händelser arbetsytan. 
> [!NOTE]
> Att lagra data i Log Analytics kan debiteras ytterligare avgifter för data lagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Du kan välja rätt filtrera principer för dina prenumerationer och arbetsytor från fyra uppsättningar av händelser som ska lagras i din arbetsyta: 

- **Ingen** – inaktivera lagring av säkerhets händelser. Det här är standardinställningen.
- **Minimal** – en mindre uppsättning händelser för kunder som vill minimera händelse volymen.
- **Common** – det här är en uppsättning händelser som uppfyller de flesta kunder och som gör det möjligt för dem att göra en fullständig Gransknings logg.
- **Alla händelser** – för kunder som vill se till att alla händelser lagras.


> [!NOTE]
> Uppsättningarna security händelser är endast tillgängliga på standardnivån i Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
Dessa uppsättningar har utformats för att hantera vanliga scenarier. Se till att utvärdera vilket alternativ som passar dina behov innan du implementerar den.
>
>

För att avgöra vilka händelser som ska tillhöra **vanliga** och **minimala** händelse uppsättningar arbetade vi med kunder och bransch standarder för att lära dig om den ofiltrerada frekvensen för varje händelse och deras användning. Vi använde följande riktlinjer i den här processen:

- **Minimal** – se till att den här uppsättningen bara omfattar händelser som kan tyda på en lyckad överträdelse och viktiga händelser som har en mycket låg volym. Den här uppsättningen innehåller till exempel användaren lyckade och misslyckade inloggningar (händelse-ID 4624, 4625), men det innehåller inte någon utloggning som är viktigt för granskning men inte meningsfullt för identifiering och har en relativt hög volym. De flesta av datavolymen för den här uppsättningen är inloggningshändelser och skapa processhändelse (händelse-ID 4688).
- **Common** – ange en fullständig Gransknings logg för användare i den här uppsättningen. Den här uppsättningen innehåller till exempel både användar inloggningar och användar inloggningar (händelse-ID 4634). Vi tar granskning åtgärder som att ändringarna, viktiga domain controller Kerberos åtgärder och andra händelser som rekommenderas av organisationer inom.

Händelser som har mycket små volymer ingick i den gemensamma som huvudsakliga skälet att välja det över alla händelser är att minska mängden och inte för att filtrera bort specifika händelser.

Här är en fullständig uppdelning av säkerhets- och AppLocker händelse-ID för varje uppsättning:

| Datanivå | Insamlade händelser indikatorer |
| --- | --- |
| Minimalt | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Om du använder grupprincip objekt (GPO) rekommenderar vi att du aktiverar gransknings princip processen för att skapa händelse 4688 och fältet *kommandorad* i händelse 4688. Mer information om hur du skapar en process 4688 finns i [vanliga frågor om](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)Security Center. Mer information om de här gransknings principerna finns i [rekommendationer för gransknings principer](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Om du vill aktivera insamling av data för [anpassningsbara program kontroller](security-center-adaptive-application.md)konfigurerar Security Center en lokal AppLocker-princip i gransknings läge så att alla program tillåts. Detta innebär att AppLocker att generera händelser som sedan kan samlas in och används av Security Center. Det är viktigt att Observera att den här principen inte konfigureras på alla datorer där det finns redan en konfigurerade AppLocker-principen. 
> - Om du vill samla in [händelse-ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)för Windows Filtering Platform måste du aktivera [Granska filter plattforms anslutning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/set/Subcategory: "Filtering Platform Connection"/Success: Enable)
>

Att välja din filtreringsprincip:
1. På sidan **data insamling** väljer du din filtrerings princip under **säkerhets händelser**.
2. Välj **Spara**.

   ![Välj filtrera principer][5]

### Automatisk etablering i fall av en redan befintlig agent installation<a name="preexisting"></a> 

Följande användningsfall ange hur automatisk etablering i fall när det finns redan en agent eller tillägget har installerats. 

- Log Analytics agent är installerad på datorn, men inte som ett tillägg (direkt agent)<br>
Om Log Analytics-agenten installeras direkt på den virtuella datorn (inte som ett Azure-tillägg), kommer Security Center att installera Log Analytics agent tillägget, och kan uppgradera Log Analytics agent till den senaste versionen.
Den installerade agenten fortsätter att rapportera till sina redan konfigurerade arbets ytor och rapporterar även till arbets ytan som kon figurer ATS i Security Center (Multi-värdar stöds på Windows-datorer).
Om den konfigurerade arbets ytan är en användar arbets yta (inte Security Center standard arbets ytan) måste du installera lösningen "Security/" securityFree på den för Security Center för att börja bearbeta händelser från virtuella datorer och datorer som rapporterar till arbets ytan.<br>
<br>
För Linux-datorer stöds inte agent multi-värdar ännu, vilket innebär att om en befintlig agent installation identifieras sker ingen automatisk etablering och datorns konfiguration ändras inte.
<br>
För befintliga datorer på prenumerationer som har registrerats på Security Center före 2019-03-17, kommer Log Analytics agent tillägget inte att installeras, och datorn påverkas inte om en befintlig agent identifieras. De här datorerna finns i rekommendationen "lösa övervaknings agent hälso problem på dina datorer" för att lösa problem med Agent installationen på de här datorerna.

  
- System Center Operations Manager agenten är installerad på datorn<br>
Security Center kommer att installera Log Analytics agent-tillägget sida vid sida till den befintliga Operations Manager. Den befintliga Operations Manager-agenten fortsätter att rapportera till Operations Manager servern på normalt sätt. Observera att Operations Manager agent och Log Analytics agent delar gemensamma kör tids bibliotek, som kommer att uppdateras till den senaste versionen under den här processen.
OBS! om Operations Manager agent version 2012 är installerad aktiverar **du inte** automatisk etablering.<br>

- Det finns en befintlig VM-tillägg<br>
    - När övervaknings agenten installeras som ett tillägg tillåter tilläggs konfigurationen rapportering till endast en enda arbets yta. Security Center åsidosätts inte befintliga anslutningar till arbetsytor som användaren. Security Center kommer att lagra säkerhets data från den virtuella datorn i arbets ytan som redan är ansluten, förutsatt att lösningen "säkerhet" eller "securityFree" har installerats på den. Security Center kan uppgradera tilläggs versionen till den senaste versionen i den här processen.  
    - Om du vill se vilken arbets yta som det befintliga tillägget skickar data till kör du testet för att [Verifiera anslutningen med Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Du kan också öppna Log Analytics arbets ytor, välja en arbets yta, välja den virtuella datorn och titta på Log Analytics agent-anslutningen. 
    - Om du har en miljö där Log Analytics-agenten är installerad på klient arbets stationer och rapporterar till en befintlig Log Analytics arbets yta, granskar du listan över [operativ system som stöds av Azure Security Center](security-center-os-coverage.md) för att kontrol lera att operativ systemet stöds. Mer information finns i [befintliga Log Analytics-kunder](./faq-azure-monitor-logs.md).
 
### Inaktivera automatisk etablering<a name="offprovisioning"></a>
Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhetsprincipen. 


1. Återgå till huvudmenyn i Security Center och välj säkerhetsprincipen.
2. Klicka på **Redigera inställningar** på den rad i prenumerationen som du vill inaktivera automatisk etablering för.
3. På bladet **säkerhets princip – data insamling** under **Automatisk etablering** väljer du **av**.
4. Välj **Spara**.

   ![Inaktivera automatisk etablering][6]

När automatisk etablering är inaktiverat (inaktiverad), visas inte i konfigurationsavsnittet för standard-arbetsytan.

Om du byter ut automatiskt etablera efter tidigare på:
-   Agenter ska inte etableras på nya virtuella datorer.
-   Security Center slutar att samla in data från standardarbetsytan.
 
> [!NOTE]
>  Om du inaktiverar automatisk etablering tas inte Log Analytics agenten bort från virtuella Azure-datorer där agenten etablerades. Information om hur du tar bort OMS-tillägget finns i [Hur gör jag för att ta bort OMS-tillägg som installerats av Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## Manuell agent etablering<a name="manual-agent"></a>
 
Det finns flera sätt att installera Log Analytics-agenten manuellt. När du installerar manuellt, kontrollera att du inaktiverar automatisk etablering.

### <a name="operations-management-suite-vm-extension-deployment"></a>Distribution av Operations Management Suite-VM 

Du kan installera Log Analytics-agenten manuellt, så Security Center kan samla in säkerhets data från dina virtuella datorer och tillhandahålla rekommendationer och aviseringar.
1. Välj Automatisk etablering – ut.
2. Skapa en arbets yta och ange pris nivån för arbets ytan som du vill ställa in Log Analytics agent:

   a.  På Security Center huvud menyn väljer du **säkerhets princip**.
     
   b.  Välj den arbetsyta som du tänker ansluta agenten. Kontrollera att arbetsytan som är i samma prenumeration du använder i Security Center och att du har läs-/ skrivbehörighet på arbetsytan.
       ![Välj arbets yta][8]
3. Ställer in prisnivån.
   ![välja pris nivå][9] 
   >[!NOTE]
   >Om arbets ytan redan har en **säkerhets** -eller **SecurityCenterFree** -lösning aktive rad anges prissättningen automatiskt. 
   > 

4. Om du vill distribuera agenter på nya virtuella datorer med en Resource Manager-mall kan du installera OMS-tillägget för virtuell dator:

   a.  [Installera OMS-tillägget för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Installera OMS-tillägget för virtuell dator för Linux](../virtual-machines/extensions/oms-linux.md)
5. Om du vill distribuera tilläggen på befintliga virtuella datorer följer du anvisningarna i [samla in data om Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > Avsnittet **samla in händelse-och prestanda data** är valfritt.
   >
6. Om du vill använda PowerShell för att distribuera tillägget, använder du följande PowerShell-exempel:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Gå till **Log Analytics** och klicka på **Avancerade inställningar**.
    
      ![Ställ in log analytics][11]

   2. Kopiera värdena från **WorkspaceID** och **primär nyckeln**.
  
      ![Kopiera värden][12]

   3. Fyll i offentliga konfigurations- och privat-konfigurationen med följande värden:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - När du installerar på en virtuell Windows-dator:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - När du installerar på en Linux-VM:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Instruktioner för hur du integrerar Security Center med hjälp av PowerShell finns i [Automatisera onboarding av Azure Security Center med PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Felsökning

-   Information om hur du identifierar installations problem med automatisk etablering finns i [övervaka agentens hälso problem](security-center-troubleshooting-guide.md#mon-agent).

-  Information om hur du identifierar nätverks krav för övervaknings agenten finns i [Felsöka nätverks krav för övervaknings agenten](security-center-troubleshooting-guide.md#mon-network-req).
-   Information om hur du identifierar manuella onboarding-problem finns i [så här felsöker du problem med Operations Management Suite onboarding](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Identifiera oövervakade virtuella datorer och problem med datorer:

    En virtuell dator eller en dator övervakas av Security Center om datorn inte kör tillägget Microsoft Monitoring Agent. En dator kan ha en lokal agent som redan är installerad, till exempel OMS Direct-agenten eller System Center Operations Manager agenten. Datorer med dessa agenter identifieras som oövervakade eftersom dessa agenter inte stöds fullt ut i Security Center. För att kunna utnyttja Security Center fullt ut krävs tillägget Microsoft Monitoring Agent.

    Mer information om varför Security Center inte kan övervaka virtuella datorer och datorer som har initierats för automatisk etablering finns i [övervaknings agentens hälso problem](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur datainsamling och automatisk etablering i Security Center fungerar. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](faq-general.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
