---
title: Datainsamling i Azure Security Center | Microsoft-dokument
description: I den här artikeln beskrivs hur du installerar en Log Analytics-agent och anger en Log Analytics-arbetsyta där de insamlade data kan lagras.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 61d0a57c541837ab3aebf65e47d757f7ecbe7e40
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435983"
---
# <a name="data-collection-in-azure-security-center"></a>Datainsamling i Azure Security Center
Security Center samlar in data från dina virtuella Azure-datorer , skalningsuppsättningar för virtuella datorer, IaaS-behållare och icke-Azure-datorer (inklusive lokala) för att övervaka säkerhetsproblem och hot. Data samlas in med log analytics-agenten, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till arbetsytan för analys. Exempel på sådana data är: operativsystemtyp och version, operativsystemloggar (Windows-händelseloggar), processer som körs, datornamn, IP-adresser och inloggade användare. Log Analytics-agenten kopierar också kraschdumpfiler till din arbetsyta.

Datainsamling krävs för att ge insyn i saknade uppdateringar, felkonfigurerade os-säkerhetsinställningar, slutpunktsskyddsstatus och hälso- och hotskydd. 

I den här artikeln beskrivs hur du installerar en Log Analytics-agent och anger en Log Analytics-arbetsyta där de insamlade data kan lagras. Båda åtgärderna krävs för att möjliggöra datainsamling. 

> [!NOTE]
> - Datainsamling behövs bara för beräkningsresurser (virtuella datorer, skalningsuppsättningar för virtuella datorer, IaaS-behållare och datorer som inte är Azure). Du kan dra nytta av Azure Security Center även om du inte etablerar agenter. Du kommer dock att ha begränsad säkerhet och funktionerna ovan stöds inte.  
> - Information om listan över plattformar som stöds finns [i Plattformar som stöds i Azure Security Center](security-center-os-coverage.md).
> - Lagring av data i Logganalys, oavsett om du använder en ny eller befintlig arbetsyta, kan medföra ytterligare avgifter för datalagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Aktivera automatisk etablering av Log Analytics Agent<a name="auto-provision-mma"></a>

Om du vill samla in data från datorerna bör du ha Log Analytics Agent installerad. Installation av agenten kan göras automatiskt (rekommenderas) eller så kan du installera agenten manuellt.  

>[!NOTE]
> Automatisk etablering är inaktiverad som standard. Om du vill att Security Center ska installeras som standard installerar du den **på På**.
>

När automatisk etablering är På, Security Center bestämmelser log Analytics Agent på alla stöds Azure virtuella datorer och alla nya som skapas. Automatisk etablering rekommenderas starkt, men manuell agentinstallation är också tillgänglig. [Läs om hur du installerar tillägget Log Analytics Agent](#manual-agent).



Så här aktiverar du automatisk etablering av Log Analytics Agent:
1. Under huvudmenyn för Säkerhetscenter väljer du **Inställningar för &.**
2. Klicka på gällande prenumeration

   ![Välj en prenumeration][7]

3. Välj **datainsamling**.
4. Under **Automatisk etablering**väljer du **På** för automatisk etablering.
5. Välj **Spara**.

   ![Aktivera automatisk försörjning][1]

>[!NOTE]
> - Instruktioner om hur du etablerar en befintlig installation finns [i Automatisk etablering vid en befintlig agentinstallation](#preexisting).
> - Instruktioner om manuell etablering finns i [Installera tillägget Log Analytics Agent manuellt](#manual-agent).
> - Instruktioner om hur du inaktiverar automatisk etablering finns i [Inaktivera automatisk etablering](#offprovisioning).
> - Instruktioner om hur du läser in Security Center med PowerShell finns i [Automatisera introduktion av Azure Security Center med PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfiguration av arbetsyta
Data som samlas in av Security Center lagras i Log Analytics-arbetsytor. Du kan välja att data ska samlas in från virtuella Azure-datorer i arbetsytor som skapats av Security Center eller på en befintlig arbetsyta som du har skapat. 

Arbetsytekonfigurationen anges per prenumeration och många prenumerationer kan använda samma arbetsyta.

### <a name="using-a-workspace-created-by-security-center"></a>Använda en arbetsyta som skapats av Security Center

Säkerhetscenter kan automatiskt skapa en standardarbetsyta där data kan lagras. 

Så här väljer du en arbetsyta som skapats av Security Center:

1. Under **Standardarbetsytakonfiguration**väljer du Använd arbetsyta som skapats av Säkerhetscenter.
   ![Välj prisnivå][10] 

1. Klicka på **Spara**.<br>
    Security Center skapar en ny resursgrupp och standardarbetsyta i den geolokaliseringen och ansluter agenten till den arbetsytan. Namngivningskonventionen för arbetsytan och resursgruppen är:<br>
   **Arbetsyta: DefaultWorkspace-[subscription-ID]-[geo]<br> Resursgrupp: DefaultResourceGroup-[geo]**

   Om en prenumeration innehåller virtuella datorer från flera geolokaliseringar skapar Security Center flera arbetsytor. Flera arbetsytor skapas för att upprätthålla datasekretessregler.
1. Security Center aktiverar automatiskt en Security Center-lösning på arbetsytan per den prisnivå som angetts för prenumerationen. 

> [!NOTE]
> Prisnivån Log Analytics för arbetsytor som skapats av Security Center påverkar inte Security Center-faktureringen. Security Center-fakturering baseras alltid på din säkerhetsprincip i Security Center och de lösningar som är installerade på en arbetsyta. För den kostnadsfria nivån aktiverar Security Center lösningen *SecurityCenterFree* på standardarbetsytan. För standardnivån aktiverar Security Center lösningen *Security* på standardarbetsytan.
> Om du lagrar data i Log Analytics kan det medföra ytterligare avgifter för datalagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

Mer information om befintliga logganalyskonton finns i [Befintliga logganalyskunder](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Använda en befintlig arbetsyta

Om du redan har en befintlig Log Analytics-arbetsyta kanske du vill använda samma arbetsyta.

Om du vill använda din befintliga Log Analytics-arbetsyta måste du ha läs- och skrivbehörighet på arbetsytan.

> [!NOTE]
> Lösningar som är aktiverade på den befintliga arbetsytan tillämpas på virtuella Azure-datorer som är anslutna till den. För betalda lösningar kan detta leda till extra avgifter. Av hänsyn till datasekretess kontrollerar du att den valda arbetsytan finns i rätt geografisk region.
> Om du lagrar data i logganalys kan det medföra ytterligare avgifter för datalagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

Så här väljer du en befintlig Log Analytics-arbetsyta:

1. Under **Standardarbetsytakonfiguration**väljer du **Använd en annan arbetsyta**.

   ![Välj befintlig arbetsyta][2]

2. På rullytan väljer du en arbetsyta för att lagra insamlade data.

   > [!NOTE]
   > I rullytan är alla arbetsytor i alla dina prenumerationer tillgängliga. Mer information finns i [val av arbetsyta för flera prenumerationer.](security-center-enable-data-collection.md#cross-subscription-workspace-selection) Du måste ha behörighet att komma åt arbetsytan.
   >
   >

3. Välj **Spara**.
4. När du har valt **Spara**tillfrågas du om du vill konfigurera om övervakade virtuella datorer som tidigare var anslutna till en standardarbetsyta.

   - Välj **Nej** om du vill att de nya arbetsyteinställningarna ska gälla endast för nya virtuella datorer. De nya arbetsyteinställningarna gäller endast för nya agentinstallationer. nyupptäckta virtuella datorer som inte har Log Analytics Agent installerad.
   - Välj **Ja** om du vill att de nya arbetsyteinställningarna ska gälla på alla virtuella datorer. Dessutom återansluts varje virtuell dator som är ansluten till en säkerhetscenter som skapas till den nya målarbetsytan.

   > [!NOTE]
   > Om du väljer Ja får du inte ta bort de arbetsytor som skapats av Security Center förrän alla virtuella datorer har återanslutits till den nya målarbetsytan. Den här åtgärden misslyckas om en arbetsyta tas bort för tidigt.
   >
   >

   - Välj **Avbryt** om du vill avbryta åtgärden.

     ![Välj befintlig arbetsyta][3]

5. Välj prisnivå för önskad arbetsyta som du tänker ange Log Analytics Agent. <br>Om du vill använda en befintlig arbetsyta anger du prisnivån för arbetsytan. Detta kommer att installera en security Center-lösning på arbetsytan om en sådan inte redan finns.

    a.  Välj Pris & inställningar **i**Huvudmenyn för Säkerhetscenter .
     
    b.  Välj önskad arbetsyta där du tänker ansluta agenten.
        ![Välj arbetsyta][7] c. Ange prisnivå.
        ![Välj prisnivå][9]
   
   >[!NOTE]
   >Om arbetsytan redan har aktiverat **en Security-** eller **SecurityCenterFree-lösning** ställs prissättningen in automatiskt. 

## <a name="cross-subscription-workspace-selection"></a>Val av arbetsyta för flera prenumerationer
När du väljer en arbetsyta där du vill lagra dina data är alla arbetsytor för alla dina prenumerationer tillgängliga. Denna möjlighet att välja arbetsyta mellan prenumerationer gör det möjligt att samla in data från virtuella datorer som körs i olika prenumerationer och lagra dessa data på önskad arbetsyta. Det här alternativet är användbart om du använder en centraliserad arbetsyta i din organisation och vill använda den för insamling av säkerhetsdata. Mer information om hur du hanterar arbetsytor finns i [Hantera åtkomst till arbetsytan](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Nivå för datainsamling
När du väljer en nivå för datainsamling i Azure Security Center påverkar det endast lagringen av säkerhetshändelser i Log Analytics-arbetsytan. Log Analytics-agenten samlar fortfarande in och analyserar de säkerhetshändelser som krävs för Azure Security Centers hotskydd, oavsett vilken nivå av säkerhetshändelser du väljer att lagra i din Log Analytics-arbetsyta (om sådana finns). När du väljer att lagra säkerhetshändelser i din arbetsyta, aktiveras undersökning, sökning och granskning av dessa händelser arbetsytan. 
> [!NOTE]
> Om du lagrar data i logganalys kan det medföra ytterligare avgifter för datalagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Du kan välja rätt filtreringsprincip för dina prenumerationer och arbetsytor från fyra uppsättningar händelser som ska lagras på arbetsytan: 

- **Ingen** – Inaktivera lagring av säkerhetshändelseer. Det här är standardinställningen.
- **Minimal** – En mindre uppsättning händelser för kunder som vill minimera händelsevolymen.
- **Vanliga** - Detta är en uppsättning händelser som uppfyller de flesta kunder och ger dem en fullständig verifieringskedja.
- **Alla händelser** – För kunder som vill se till att alla händelser lagras.


> [!NOTE]
> Dessa säkerhetshändelser är endast tillgängliga på Security Centers standardnivå. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
Dessa uppsättningar har utformats för att hantera typiska scenarier. Se till att utvärdera vilka som passar dina behov innan du implementerar den.
>
>

För att fastställa vilka händelser som ska **tillhöra** de gemensamma och **minimala** händelseuppsättningarna arbetade vi med kunder och branschstandarder för att lära oss mer om den ofiltrerade frekvensen för varje händelse och deras användning. Vi använde följande riktlinjer i den här processen:

- **Minimal** - Se till att den här uppsättningen endast täcker händelser som kan tyda på en lyckad överträdelse och viktiga händelser som har en mycket låg volym. Den här uppsättningen innehåller till exempel en lyckad och misslyckad inloggning (händelse-ID 4624, 4625), men den innehåller inte utloggning som är viktig för granskning men inte meningsfull för identifiering och har relativt hög volym. De flesta av datavolymen för den här uppsättningen är inloggningshändelser och processskapande händelse (händelse-ID 4688).
- **Vanliga** - Ange en fullständig verifieringskedja för användare i den här uppsättningen. Den här uppsättningen innehåller till exempel både användarinloggningar och användarloggningar (händelse-ID 4634). Vi inkluderar granskningsåtgärder som säkerhetsgruppändringar, viktiga domänkontrollant Kerberos-åtgärder och andra händelser som rekommenderas av branschorganisationer.

Händelser som har mycket låg volym ingick i den gemensamma uppsättningen som den främsta motivationen att välja den över alla händelser är att minska volymen och inte filtrera bort specifika händelser.

Här är en fullständig uppdelning av säkerhets- och appskåps-händelse-ID:erna för varje uppsättning:

| Datanivå | Insamlade händelseindikatorer |
| --- | --- |
| Minimal | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Om du använder grupprincipobjekt (GPO) rekommenderar vi att du aktiverar granskningsprinciper Process Creation Event 4688 och *CommandLine-fältet* i händelse 4688. Mer information om Process Creation Event 4688 finns i Vanliga [frågor och svar om](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)Säkerhetscenter . Mer information om dessa granskningsprinciper finns i [Rekommendationer för revisionsprinciper](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Security Center konfigurerar en lokal AppLocker-princip i granskningsläge för att tillåta alla program om du vill aktivera datainsamling för [adaptiva programkontroller.](security-center-adaptive-application.md) Detta kommer att orsaka AppLocker att generera händelser som sedan samlas in och utnyttjas av Security Center. Det är viktigt att notera att den här principen inte kommer att konfigureras på alla datorer där det redan finns en konfigurerad AppLocker-princip. 
> - Om du vill samla in [händelse-ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)för Windows-filtreringsplattform måste du aktivera anslutning till [granskningsfiltreringsplattform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set/underkategori:"Filtreringsplattformsanslutning" /Framgång:Aktivera)
>

Så här väljer du filtreringsprincip:
1. På sidan **Datainsamling** väljer du filtreringsprincipen under **Säkerhetshändelser**.
2. Välj **Spara**.

   ![Välj filtreringsprincip][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatisk etablering vid befintlig agentinstallation<a name="preexisting"></a> 

Följande användningsfall anger hur automatisk etablering fungerar i fall där det redan finns en agent eller ett tillägg installerat. 

- Log Analytics Agent är installerad på datorn, men inte som ett tillägg (Direct agent)<br>
Om Log Analytics Agent installeras direkt på den virtuella datorn (inte som ett Azure-tillägg) installerar Security Center tillägget Log Analytics Agent och kan uppgradera Log Analytics Agent till den senaste versionen.
Agenten som installeras fortsätter att rapportera till de redan konfigurerade arbetsytan och rapporterar dessutom till arbetsytan som konfigurerats i Security Center (Multi-homing stöds på Windows-datorer).
Om den konfigurerade arbetsytan är en användararbetsyta (inte Säkerhetscenters standardarbetsyta) måste du installera lösningen "security/"securityFree" på den för att Security Center ska kunna börja bearbeta händelser från virtuella datorer och datorer som rapporterar till den arbetsytan.<br>
<br>
För Linux-datorer stöds agent multi-homing ännu inte - om en befintlig agentinstallation upptäcks kommer automatisk etablering inte att ske och maskinens konfiguration inte kommer att ändras.
<br>
För befintliga datorer på prenumerationer som finns ombord på Security Center före 2019-03-17, när en befintlig agent kommer att identifieras, kommer log Analytics Agent-tillägget inte att installeras och datorn påverkas inte. För dessa datorer, se rekommendationen "Lös hälsoproblem för övervakningsagenten på dina datorer" för att lösa agentinstallationsproblemen på dessa datorer.

  
- System Center Operations Manager-agenten är installerad på datorn<br>
Säkerhetscenter installerar log Analytics Agent-tillägget sida vid sida till den befintliga Operations Manager.Security center will install the Log Analytics Agent extension side-by-side to the existing Operations Manager. Den befintliga Operations Manager-agenten fortsätter att rapportera till Operations Manager-servern normalt. Observera att Operations Manager-agenten och Log Analytics Agent delar vanliga körningsbibliotek, som uppdateras till den senaste versionen under den här processen.
Om Operations Manager-agentversionen 2012 är installerad **ska du inte** aktivera automatisk etablering.<br>

- Det finns ett befintligt VM-tillägg<br>
    - När övervakningsagenten installeras som ett tillägg tillåter tilläggskonfigurationen rapportering till endast en enda arbetsyta. Security Center åsidosätter inte befintliga anslutningar till användararbetsytor. Security Center lagrar säkerhetsdata från den virtuella datorn på den arbetsyta som redan är ansluten, förutsatt att "security" eller "securityFree"-lösningen har installerats på den. Security Center kan uppgradera tilläggsversionen till den senaste versionen i den här processen.  
    - Om du vill se till vilken arbetsyta det befintliga tillägget skickar data till kör du testet för att [validera anslutningen till Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Du kan också öppna Log Analytics-arbetsytor, välja en arbetsyta, välja den virtuella datorn och titta på Log Analytics-agentanslutningen. 
    - Om du har en miljö där Log Analytics-agenten är installerad på klientarbetsstationer och rapporterar till en befintlig Log Analytics-arbetsyta läser du listan över [operativsystem som stöds av Azure Security Center](security-center-os-coverage.md) för att se till att operativsystemet stöds. Mer information finns i [Befintliga logganalyskunder](./faq-azure-monitor-logs.md).
 
### <a name="turn-off-automatic-provisioning"></a>Inaktivera automatisk etablering<a name="offprovisioning"></a>
Du kan när som helst inaktivera automatisk etablering från resurser genom att inaktivera den här inställningen i säkerhetsprincipen. 


1. Gå tillbaka till huvudmenyn för Säkerhetscenter och välj säkerhetsprincipen.
2. Klicka på **Redigera inställningar** på raden i den prenumeration som du vill inaktivera automatisk etablering för.
3. I **säkerhetsprincipen – Datainsamlingsblad** väljer du **Av**under **Automatisk etablering.**
4. Välj **Spara**.

   ![Inaktivera automatisk etablering][6]

När automatisk etablering är inaktiverad (inaktiverad) visas inte standardkonfigurationsavsnittet för arbetsytan.

Om du stänger av automatisk etablering efter att den tidigare var på:
-   Agenter kommer inte att etableras på nya virtuella datorer.
-   Security Center slutar samla in data från standardarbetsytan.
 
> [!NOTE]
>  Om du inaktiverar automatisk etablering tas inte Log Analytics-agenten bort från virtuella Azure-datorer där agenten har etablerats. Information om hur du tar bort OMS-tillägget finns i [Hur tar jag bort OMS-tillägg som installerats av Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Etablering av manuella ombud<a name="manual-agent"></a>
 
Det finns flera sätt att installera Log Analytics Agent manuellt. När du installerar manuellt måste du inaktivera automatisk etablering.

### <a name="operations-management-suite-vm-extension-deployment"></a>Distribution av VM-tillägg för Operations Management Suite 

Du kan installera Log Analytics-agenten manuellt så att Security Center kan samla in säkerhetsdata från dina virtuella datorer och ge rekommendationer och aviseringar.
1. Välj Automatisk etablering – AV.
2. Skapa en arbetsyta och ange prisnivån för den arbetsyta som du tänker ange Log Analytics Agent:

   a.  Välj **Säkerhetsprincip**i huvudmenyn för Säkerhetscenter .
     
   b.  Välj arbetsytan där du tänker ansluta agenten. Kontrollera att arbetsytan finns i samma prenumeration som du använder i Security Center och att du har läs-/skrivbehörighet på arbetsytan.
       ![Välj arbetsyta][8]
3. Ange prisnivå.
   ![Välj prisnivå][9] 
   >[!NOTE]
   >Om arbetsytan redan har aktiverat **en Security-** eller **SecurityCenterFree-lösning** ställs prissättningen in automatiskt. 
   > 

4. Om du vill distribuera agenterna på nya virtuella datorer med hjälp av en Resource Manager-mall installerar du oms-tillägget för virtuella datorer:

   a.  [Installera oms-tillägget för virtuella datorer för Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Installera oms-tillägget för virtuella datorer för Linux](../virtual-machines/extensions/oms-linux.md)
5. Om du vill distribuera tilläggen på befintliga virtuella datorer följer du instruktionerna i [Samla in data om virtuella Azure-datorer](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > Avsnittet **Samla in händelse- och prestandadata** är valfritt.
   >
6. Om du vill använda PowerShell för att distribuera tillägget använder du följande PowerShell-exempel:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Gå till **Log Analytics** och klicka på **Avancerade inställningar**.
    
      ![Ange logganalys][11]

   2. Kopiera värdena utanför **WorkspaceID** och **primärnyckel**.
  
      ![Kopiera värden][12]

   3. Fyll på den offentliga konfigen och den privata konfigen med dessa värden:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - När du installerar på en virtuell dator med Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - När du installerar på en Virtuell Linux-dator:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Instruktioner om hur du läser in Security Center med PowerShell finns i [Automatisera introduktion av Azure Security Center med PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Felsökning

-   Information om hur du identifierar problem med installation av automatisk etablering finns i [Hälsoproblem för övervakningsagenten](security-center-troubleshooting-guide.md#mon-agent).

-  Identifiera nätverkskrav för övervakningsagenten finns i [Felsöka nätverkskrav för övervakningsagenten](security-center-troubleshooting-guide.md#mon-network-req).
-   Mer om du vill identifiera manuella introduktionsproblem finns i [Så här felsöker du problem med Operations Management Suite onboarding](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Så här identifierar du problem med oövervakade virtuella datorer och datorer:

    En virtuell dator eller dator övervakas inte av Security Center om datorn inte kör agenttillägget Log Analytics. En dator kan ha en lokal agent redan installerad, till exempel OMS direct agent eller System Center Operations Manager-agenten. Datorer med dessa agenter identifieras som oövervakade eftersom dessa agenter inte stöds fullt ut i Security Center. För att fullt ut kunna dra nytta av alla Security Centers funktioner krävs tillägget Log Analytics-agent.

    Mer information om orsakerna till att Security Center inte kan övervaka virtuella datorer och datorer som initierats för automatisk etablering finns i [Hälsoproblem för övervakningsagenten](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Nästa steg
I den här artikeln visas hur datainsamling och automatisk etablering i Security Center fungerar. I följande avsnitt kan du lära dig mer om Security Center:

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
