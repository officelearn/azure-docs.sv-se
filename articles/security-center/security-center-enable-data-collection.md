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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/2/2018
ms.author: rkarlin
ms.openlocfilehash: 5150f782d13e198c59e8d79f09ba0ffead5426d3
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100554"
---
# <a name="data-collection-in-azure-security-center"></a>Insamling av data i Azure Security Center
Security Center samlar in data från dina virtuella Azure-datorer (VM) och icke-Azure-datorer för att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys. Exempel på sådana data är: driva systemtyp och version, operativsystemloggar (Windows-händelseloggar), kör processer, datornamn, IP-adresser och inloggad användare. Microsoft Monitoring Agent kopierar också kraschdumpfiler till din arbetsyta.

Insamling av data krävs för att ge dig information om saknade uppdateringar, felkonfigurerad OS-säkerhetsinställningar, aktivering av endpoint protection och hälsa och threat identifieringar. 

Den här artikeln innehåller råd om hur du installerar Microsoft Monitoring Agent och ange en Log Analytics-arbetsyta där du vill lagra insamlade data. Både krävs för att aktivera insamling av data. 

> [!NOTE]
> - Datainsamling krävs endast för beräkningsresurser (virtuella datorer och Azure-datorer). Du kan dra nytta av Azure Security Center även om du inte etablera agenter; men du har begränsad säkerhet och de funktioner som anges ovan stöds inte.  
> - Lista över plattformar som stöds finns i [plattformar som stöds i Azure Security Center](security-center-os-coverage.md).
> - Insamling av data för Virtual machine scale Sets stöds inte för närvarande.


## Aktivera automatisk etablering av Microsoft Monitoring Agent <a name="auto-provision-mma"></a>

Om du vill samla in data från datorerna bör du ha Microsoft Monitoring Agent installerad.  Installationen av agenten kan vara automatiskt (rekommenderas) eller så kan du välja att installera agenten manuellt.  

>[!NOTE]
> Automatisk etablering är inaktiverat som standard. Om du vill ange Security Center för att installera Automatisk etablering som standard, ange den till **på**.
>

När automatisk etablering är aktiverat, stöds etablerar Security Center Microsoft Monitoring Agent på alla virtuella Azure-datorer och alla nya som skapas. Automatisk försörjning rekommenderas starkt men manuell agentinstallation är också tillgängliga. [Lär dig hur du installerar tillägget Microsoft Monitoring Agent](#manualagent).



Så här aktiverar du automatisk försörjning för Microsoft Monitoring Agent:
1. Huvudmenyn i Security Center, Välj **säkerhetsprincip**.
2. Klicka på **redigera inställningar för** i kolumnen inställningar i den önskade prenumerationen i listan.

  ![Välj en prenumeration][7]

3. Under **Säkerhetsprincip** väljer du **Datainsamling**.
4. Under **Automatisk etablering**väljer **på** att aktivera automatisk etablering.
5. Välj **Spara**.

  ![Aktivera automatisk försörjning][1]

>[!NOTE]
> - Anvisningar för hur du etablerar en befintlig installation finns i [Automatisk etablering i händelse av en befintlig agentinstallation](#preexisting).
> - Information om manuell etablering finns [installerar tillägget Microsoft Monitoring Agent manuellt](#manualagent).
> - Anvisningar för att stänga av Automatisk etablering, se [inaktivera automatisk etablering](#offprovisioning).
> - Mer information om hur du integrera Security Center med hjälp av PowerShell, se [automatisera onboarding av Azure Security Center med hjälp av PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Arbetsytekonfiguration
Data som samlas in av Security Center lagras i Log Analytics-arbetsytor.  Du kan välja för att ha data som samlas in från virtuella Azure-datorer lagras i arbetsytor som skapats av Security Center eller i en befintlig arbetsyta som du skapade. 

Arbetsytekonfiguration anges per prenumeration och många prenumerationer använder samma arbetsyta.

### <a name="using-a-workspace-created-by-security-center"></a>Med en arbetsyta som skapats av Security Center

Security center kan automatiskt skapa en standardarbetsyta där du kan lagra data. 

Välja den arbetsyta som skapats av Security Center:

1.  Under **standardkonfigurationen för arbetstytan**, väljer du Använd arbetsyta/arbetsytor som skapats av Security center.
   ![Välj prisnivå][10] 

2. Klicka på **Spara**.<br>
    Security Center skapar en ny resurs och standardenhet arbetsyta i den geoplats och ansluter agenten till arbetsytan. Namngivningskonventionen för gruppen arbetsyta och resursen är:<br>
**Arbetsyta: DefaultWorkspace-[subscription-ID]-[geo]<br> Resource Group: DefaultResourceGroup-[geo]**

   Om en prenumeration innehåller virtuella datorer från flera geolocations, skapar flera arbetsytor med Security Center. Flera arbetsytor skapas för att upprätthålla strängare data.
-   Security Center aktiverar automatiskt en Security Center-lösning på arbetsytan per prisnivån för prenumerationen. 

> [!NOTE]
> Log Analytics prisnivån för arbetsytor som skapats av Security Center påverkar inte Security Center fakturering. Security Center fakturering baseras alltid på din säkerhetsprincip i Security Center och de lösningar som är installerad på en arbetsyta. Den kostnadsfria nivån med Security Center kan de *SecurityCenterFree* lösning på standardarbetsytan. Standard-nivån med Security Center kan de *Security* lösning på standardarbetsytan.

Mer information om priser finns i [Security Center-prissättning](https://azure.microsoft.com/pricing/details/security-center/).

Mer information om befintliga Log Analytics-konton finns i [befintliga Log Analytics-kunder](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Med hjälp av en befintlig arbetsyta

Om du redan har en befintlig Log Analytics-arbetsyta som du kanske vill använda samma arbetsyta.

Om du vill använda din befintliga Log Analytics-arbetsyta som du måste ha läs- och skrivbehörighet på arbetsytan.

> [!NOTE]
> Lösningar som aktiverats på den befintliga arbetsytan tillämpas på Azure virtuella datorer som är anslutna till den. Detta kan resultera i ytterligare avgifter för betalda lösningar. För data överväganden av datasekretess, kontrollera att din valda arbetsyta är i rätt geografisk region.
>

Att välja en befintlig Log Analytics-arbetsyta:

1. Under **standardkonfigurationen för arbetstytan**väljer **använder en annan arbetsyta**.

   ![Välj en befintlig arbetsyta][2]

2. Välj en arbetsyta för att lagra insamlade data från den nedrullningsbara menyn.

  > [!NOTE]
  > Alla arbetsytor för alla dina prenumerationer är tillgängliga i nedrullningsbara menyn. Se [mellan val av arbetsyta prenumeration](security-center-enable-data-collection.md#cross-subscription-workspace-selection) för mer information. Du måste ha behörighet att få åtkomst till arbetsytan.
  >
  >

3. Välj **Spara**.
4. När du har valt **spara**, tillfrågas du om du vill konfigurera om övervakade virtuella datorer som anslöts till en standardarbetsyta.

   - Välj **nr** om du vill att de nya arbetsyteinställningarna tillämpas på nya datorer. De nya arbetsyteinställningarna gäller endast för nya agentinstallationer; Nyligen identifierade virtuella datorer som inte har Microsoft Monitoring Agent installerad.
   - Välj **Ja** om du vill att de nya arbetsyteinställningarna tillämpas på alla virtuella datorer. Dessutom kan återansluta varje virtuell dator som är anslutna till en Security Center som skapat arbetsyta till den nya målarbetsytan.

   > [!NOTE]
   > Om du väljer Ja måste du inte ta bort arbetsytor som skapats av Security Center tills alla virtuella datorer har återanslutit till den nya målarbetsytan. Den här åtgärden misslyckas om en arbetsyta har tagits bort för tidigt.
   >
   >

   - Välj **Avbryt** att avbryta åtgärden.

     ![Välj en befintlig arbetsyta][3]

5. Välj prisnivå för den önskade arbetsytan som du vill ange Microsoft Monitoring agent. <br>Ställer in prisnivån för arbetsytan om du vill använda en befintlig arbetsyta. Detta installerar security Center-lösning på arbetsytan om något inte redan finns.

    a.  I huvudmenyn i Security Center väljer **säkerhetsprincip**.
     
    b.  Välj önskad arbetsyta där du tänker ansluta agenten genom att klicka på **redigera inställningar för** i kolumnen inställningar i den önskade prenumerationen i listan.
        ![Välj arbetsyta][8] c. Ställer in prisnivån.
        ![Välj prisnivå][9] 
   
   >[!NOTE]
   >Om arbetsytan finns redan en **Security** eller **SecurityCenterFree** lösning som har aktiverats, priserna anges automatiskt. 

## <a name="cross-subscription-workspace-selection"></a>Val av arbetsyta mellan prenumerationer
När du väljer en arbetsyta där du vill lagra dina data finns alla arbetsytor för alla dina prenumerationer. Val av arbetsyta mellan prenumerationer kan du samla in data från virtuella datorer som körs i olika prenumerationer och lagra den på arbetsytan i ditt val. Det här alternativet är användbart om du använder en centraliserade arbetsytor i din organisation och vill använda den för insamling av säkerhetsdata. Läs mer om att hantera arbetsytor, [hantera arbetsyteåtkomst](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Samling datanivå
Att välja en nivå för insamling av data i Azure Security Center påverkar endast lagring av säkerhetshändelser i Log Analytics-arbetsytan. Microsoft Monitoring Agent kommer fortfarande att samla in och analysera säkerhetshändelser som krävs för Azure Security Center threat identifieringar, oavsett vilken nivå av säkerhetshändelser som du väljer att lagra i Log Analytics-arbetsytan (om sådan finns). Välja att lagra säkerhetshändelser i din arbetsyta aktiverar undersökning, sökning och granskning av dessa händelser i din arbetsyta. 
> [!NOTE]
> Lagra data i Log Analytics kan debiteras ytterligare avgifter för lagring av data, se prissättningssidan för mer information.
>
Du kan välja rätt filtrera principer för dina prenumerationer och arbetsytor från fyra uppsättningar av händelser som ska lagras i din arbetsyta: 

- **Ingen** – inaktivera händelselagring för säkerhet. Det här är standardinställningen.
- **Minimal** – en mindre uppsättning händelser för kunder som vill minimera händelse volymen.
- **Vanliga** – det här är en uppsättning händelser som uppfyller de flesta kunder och låter dem till fullständiga spårningen.
- **Alla händelser** – för kunder som vill se till att alla händelser lagras.


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
> - Samla in Windows Filtering Platform [händelse-ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), måste du aktivera [anslutning för granskning filtrering](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/subcategory: ”filtrering plattform anslutning” du)
>

Att välja din filtreringsprincip:
1. På den **säkerhetsprincip datainsamling** bladet Välj din filtreringsprincip under **säkerhetshändelser**.
2. Välj **Spara**.

   ![Välj filtrera principer][5]

### Automatisk etablering i händelse av en befintlig installation av agent <a name="preexisting"></a> 

Följande användningsfall ange hur automatisk etablering i fall när det finns redan en agent eller tillägget har installerats. 

- Microsoft Monitoring Agent är installerad på datorn, men inte som ett tillägg<br>
Om Microsoft Monitoring Agent är installerad direkt på den virtuella datorn (inte som en utökning av Azure), installera inte Microsoft Monitoring Agent i Security Center. Du kan aktivera automatisk etablering och välj användararbetsytan relevanta i Security Center automatiskt etableringskonfiguration. Om du väljer samma arbetsyta som den virtuella datorn är redan ansluten till den befintliga agenten hanteras med tillägget Microsoft Monitoring Agent. 

> [!NOTE]
> Om SCOM agent-version 2012 installeras, **inte** aktivera automatisk etablering på. 

Mer information finns i [vad händer om en SCOM eller OMS dirigera agenten är redan installerad på den virtuella datorn?](security-center-faq.md#scomomsinstalled)

-   Det finns en befintlig VM-tillägg<br>
    - Security center har stöd för befintliga installationer av tillägget och åsidosätts inte befintliga anslutningar. Security Center lagrar data från den virtuella datorn i arbetsytan redan är ansluten och ger skydd baserat på de lösningar som har aktiverats på arbetsytan.   
    - Se på vilka arbetsyta befintliga tillägget skickar data för att köra testet till [Kontrollera anslutning med Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Du kan också öppna Log analytics, Välj en arbetsyta, väljer den virtuella datorn och titta på Microsoft Monitoring Agent-anslutningen. 
    - Om du har en miljö där Microsoft Monitoring Agent är installerad på klientdatorer och rapporterar till en befintlig Log Analytics-arbetsyta, granska listan över [operativsystem som stöds av Azure Security Center](security-center-os-coverage.md) till Kontrollera att det finns stöd för operativsystemet och se [befintliga Log Analytics-kunder](security-center-faq.md#existingloganalyticscust) för mer information.
 
### Inaktivera automatisk etablering <a name="offprovisioning"></a>
Du kan inaktivera automatisk etablering från resurser när som helst genom att stänga av den här inställningen i säkerhetsprincipen. 


1. Återgå till huvudmenyn i Security Center och välj säkerhetsprincipen.
2. Välj den prenumeration du vill avaktivera automatisk etablering för.
3. På den **säkerhetsprincip – datainsamling** bladet under **Automatisk etablering** Välj **av**.
4. Välj **Spara**.

  ![Inaktivera automatisk etablering][6]

När automatisk etablering är inaktiverat (inaktiverad), visas inte i konfigurationsavsnittet för standard-arbetsytan.

Om du byter ut automatiskt etablera efter tidigare på:
-   Agenter ska inte etableras på nya virtuella datorer.
-   Security Center slutar att samla in data från standardarbetsytan.
 
> [!NOTE]
>  Inaktivering av Automatisk etablering tar inte bort Microsoft Monitoring Agent från virtuella Azure-datorer där agenten har etablerats. Mer information om hur du tar bort OMS-tillägget finns i [hur tar jag bort OMS-tillägg installeras av Security Center](security-center-faq.md#remove-oms).
>
    
## Manuell agentetablering <a name="manualagent"></a>
 
Det finns flera sätt att installera Microsoft Monitoring Agent manuellt. När du installerar manuellt, kontrollera att du inaktiverar automatisk etablering.

### <a name="operations-management-suite-vm-extension-deployment"></a>Distribution av Operations Management Suite-VM 

Du kan installera Microsoft Monitoring Agent manuellt så att Security Center kan samla in säkerhetsdata från dina virtuella datorer och tillhandahålla rekommendationer och aviseringar.
1.  Välj Automatisk etablering – ut.
2.  Skapa en arbetsyta och ställer in prisnivån för arbetsytan som du vill ange Microsoft Monitoring agent:

    a.  I huvudmenyn i Security Center väljer **säkerhetsprincip**.
     
    b.  Välj den arbetsyta som du tänker ansluta agenten. Kontrollera att arbetsytan som är i samma prenumeration du använder i Security Center och att du har läs-/ skrivbehörighet på arbetsytan.
        ![Välj en arbetsyta][8]
3. Ställer in prisnivån.
   ![Välj prisnivå][9] 
   >[!NOTE]
   >Om arbetsytan finns redan en **Security** eller **SecurityCenterFree** lösning som har aktiverats, priserna anges automatiskt. 
   > 

4.  Om du vill distribuera agenter på nya virtuella datorer med en Resource Manager-mall kan du installera OMS-tillägget för virtuell dator:

    a.  [Installera OMS-tillägget för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md)
    
    b.  [Installera OMS-tillägget för virtuell dator för Linux](../virtual-machines/extensions/oms-linux.md)
5.  Om du vill distribuera tillägg på befintliga virtuella datorer, följer du anvisningarna i [samla in data om Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md).

  > [!NOTE]
  > Avsnittet **samlar in händelse- och prestandadata** är valfritt.
  >
6. Om du vill använda PowerShell för att distribuera tillägget, använder du följande PowerShell-exempel:
    1.  Gå till **Log Analytics** och klicka på **avancerade inställningar**.
    
        ![Ställ in log analytics][11]

    2. Kopiera värdena från **WorkspaceID** och **primärnyckel**.
  
       ![Kopiera värden][12]

    3. Fyll i offentliga konfigurations- och privat-konfigurationen med följande värden:
     
            $PublicConf = '{
                "workspaceId": "WorkspaceID value",
                "MultipleConnections": true
            }' 
 
            $PrivateConf = '{
                "workspaceKey": "<Primary key value>”
            }' 

      - När du installerar på en virtuell Windows-dator:
        
             Set-AzureRmVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
       - När du installerar på en Linux-VM:
        
             Set-AzureRmVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Mer information om hur du integrera Security Center med hjälp av PowerShell, se [automatisera onboarding av Azure Security Center med hjälp av PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Felsökning

-   För att identifiera problem med installation av Automatisk etablering, se [hälsoproblem för Övervakningsagenten](security-center-troubleshooting-guide.md#mon-agent).

-  För att identifiera nätverkskrav för övervakningsagenten Se [felsökning övervakning nätverkskrav för övervakningsagenten](security-center-troubleshooting-guide.md#mon-network-req).
-   För att identifiera problem med manuell integrationsprocessen Se [felsökning av problem i Operations Management Suite onboarding](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- För att identifiera oövervakade virtuella datorer och datorer problem, se [oövervakade virtuella datorer och datorer](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur datainsamling och automatisk etablering i Security Center fungerar. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
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
