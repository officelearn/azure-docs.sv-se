---
title: Data insamling i Azure Security Center | Microsoft Docs
description: Den här artikeln beskriver hur du installerar en Log Analytics agent och anger en Log Analytics arbets yta där insamlade data ska lagras.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: memildin
ms.openlocfilehash: be212de7a24b416ad4e5dc08998ba1147c6f3753
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855944"
---
# <a name="data-collection-in-azure-security-center"></a>Datainsamling i Azure Security Center
Security Center samlar in data från dina virtuella Azure-datorer, skalnings uppsättningar för virtuella datorer, IaaS behållare och icke-Azure (inklusive lokala) datorer för att övervaka säkerhets problem och hot. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från datorn och kopierar data till din arbets yta för analys. Exempel på sådana data är: operativ systemets typ och version, operativ system loggar (Windows-händelseloggar), processer som körs, dator namn, IP-adresser och inloggad användare.

Data insamling krävs för att ge insyn i saknade uppdateringar, felkonfigurerade OS-säkerhetsinställningar, Endpoint Protection-status och hälso-och hot skydd. 

Den här artikeln beskriver hur du installerar en Log Analytics agent och anger en Log Analytics arbets yta där insamlade data ska lagras. Båda åtgärderna krävs för att aktivera data insamling. 

> [!NOTE]
> - Data insamling behövs bara för beräknings resurser (virtuella datorer, skalnings uppsättningar för virtuella datorer, IaaS behållare och datorer som inte är Azure-datorer). Du kan dra nytta av Azure Security Center även om du inte etablerar agenter. du kommer dock att ha begränsad säkerhet och de funktioner som anges ovan stöds inte.  
> - En lista över plattformar som stöds finns [i plattformar som stöds i Azure Security Center](security-center-os-coverage.md).
> - Om du lagrar data i Log Analytics, oavsett om du använder en ny eller befintlig arbets yta, kan ytterligare kostnader för data lagring uppstå. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Aktivera automatisk etablering av Log Analytics agent<a name="auto-provision-mma"></a>

Om du vill samla in data från datorerna bör du ha Log Analytics-agenten installerad. Installationen av agenten kan göras automatiskt (rekommenderas) eller så kan du installera agenten manuellt. Automatisk etablering är inaktive rad som standard.

När automatisk etablering är aktiverat distribuerar Security Center Log Analytics agent på alla virtuella Azure-datorer som stöds och eventuella nya som skapas. Automatisk etablering rekommenderas, men du kan installera agenten manuellt om det behövs (se [manuell installation av Log Analytics agent](#manual-agent)).


Så här aktiverar du automatisk etablering av Log Analytics agent:
1. Från Security Center menyn i portalen väljer du **pris & inställningar**.
2. Välj relevant prenumeration.

   ![Välj en prenumeration][7]

3. Välj **data insamling**.
4. Under **Automatisk etablering**väljer du **på** för att aktivera automatisk etablering.
5. Välj **Spara**. Agenten kommer att distribueras på alla virtuella datorer inom 15 minuter. 

>[!TIP]
> Om en arbets yta behöver tillhandahållas kan Agent installationen ta upp till 25 minuter.

   ![Aktivera automatisk försörjning][1]

>[!NOTE]
> - Instruktioner för hur du etablerar en befintlig installation finns i [Automatisk etablering i händelse av en befintlig agent installation](#preexisting).
> - Anvisningar om manuell etablering finns i [installera Log Analytics agent-tillägget manuellt](#manual-agent).
> - Anvisningar om hur du inaktiverar automatisk etablering finns i [inaktivera automatisk etablering](#offprovisioning).
> - Instruktioner för hur du integrerar Security Center med hjälp av PowerShell finns i [Automatisera onboarding av Azure Security Center med PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Konfiguration av arbets yta
Data som samlas in av Security Center lagras i Log Analytics-arbetsytor. Dina data kan samlas in från virtuella Azure-datorer som lagras i arbets ytor som skapats av Security Center eller i en befintlig arbets yta som du har skapat. 

Arbets ytans konfiguration anges per prenumeration och många prenumerationer kan använda samma arbets yta.

### <a name="using-a-workspace-created-by-security-center"></a>Använda en arbets yta som skapats av Security Center

Security Center kan automatiskt skapa en standard arbets yta där data ska lagras. 

Så här väljer du en arbets yta som skapats av Security Center:

1. Under **standard arbets ytans konfiguration**väljer du Använd arbets ytor som skapats av Security Center.
   ![Välj pris nivå][10] 

1. Klicka på **Spara**.<br>
    Security Center skapar en ny resurs grupp och standard arbets yta på den platsen och ansluter agenten till den arbets ytan. Namngivnings konventionen för arbets ytan och resurs gruppen är:<br>
   **Arbets yta: DefaultWorkspace-[prenumerations-ID]-[geo] <br> resurs grupp: DefaultResourceGroup-[geo]**

   Om en prenumeration innehåller virtuella datorer från flera platser skapar Security Center flera arbets ytor. Flera arbets ytor skapas för att underhålla data sekretess regler.
1. Security Center aktiverar automatiskt en Security Center lösning på arbets ytan enligt den pris nivå som angetts för prenumerationen. 

> [!NOTE]
> Log Analytics pris nivå för arbets ytor som skapats av Security Center påverkar inte Security Center fakturering. Security Center-fakturering baseras alltid på din säkerhetsprincip i Security Center och de lösningar som är installerade på en arbetsyta. För den kostnadsfria nivån aktiverar Security Center lösningen *SecurityCenterFree* på standardarbetsytan. För standardnivån aktiverar Security Center lösningen *Security* på standardarbetsytan.
> Att lagra data i Log Analytics kan debiteras ytterligare avgifter för data lagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

Mer information om befintliga Log Analytics-konton finns i [befintliga Log Analytics-kunder](./faq-azure-monitor-logs.md).

### <a name="using-an-existing-workspace"></a>Använda en befintlig arbets yta

Om du redan har en befintlig Log Analytics arbets yta kan du vilja använda samma arbets yta.

Om du vill använda din befintliga Log Analytics-arbetsyta måste du ha läs-och Skriv behörighet på arbets ytan.

> [!NOTE]
> Lösningar som är aktiverade på den befintliga arbets ytan kommer att tillämpas på virtuella Azure-datorer som är anslutna till den. För betalda lösningar kan detta leda till ytterligare kostnader. Se till att den valda arbets ytan är i rätt geografiskt region för data sekretess överväganden.
> Att lagra data i Log Analytics kan debiteras ytterligare avgifter för data lagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).

Så här väljer du en befintlig Log Analytics arbets yta:

1. Under **standard arbets ytans konfiguration**väljer du **Använd en annan arbets yta**.

   ![Välj en befintlig arbets yta][2]

2. I den nedrullningsbara menyn väljer du en arbets yta där insamlade data ska lagras.

   > [!NOTE]
   > På den nedrullningsbara menyn är alla arbets ytor i alla prenumerationer tillgängliga. Mer information finns i avsnittet om [arbets ytor mellan prenumerationer](security-center-enable-data-collection.md#cross-subscription-workspace-selection) . Du måste ha behörighet att komma åt arbets ytan.
   >
   >

3. Välj **Spara**.
4. När du har valt **Spara**blir du tillfrågad om du vill konfigurera om övervakade virtuella datorer som tidigare var anslutna till en standard arbets yta.

   - Välj **Nej** om du vill att de nya inställningarna för arbets ytan bara ska gälla för nya virtuella datorer. De nya inställningarna för arbets ytan gäller endast nya agent installationer. nyligen identifierade virtuella datorer som inte har Log Analytics-agenten installerad.
   - Välj **Ja** om du vill att de nya inställningarna för arbets ytan ska tillämpas på alla virtuella datorer. Dessutom återansluts varje virtuell dator som är ansluten till en Security Center skapad arbets yta till den nya mål arbets ytan.

   > [!NOTE]
   > Om du väljer Ja får du inte ta bort arbets ytorna som skapats av Security Center tills alla virtuella datorer har återanslutits till den nya mål arbets ytan. Den här åtgärden Miss lyckas om en arbets yta tas bort för tidigt.
   >
   >

   - Välj **Avbryt** om du vill avbryta åtgärden.

     ![Välj en befintlig arbets yta][3]

5. Välj pris nivå för den önskade arbets ytan som du vill ange Log Analytics agenten. <br>Ange pris nivån för arbets ytan om du vill använda en befintlig arbets yta. Då installeras en Security Center-lösning på arbets ytan om det inte redan finns en sådan.

    a.  På Security Center huvud menyn väljer du **pris & inställningar**.
     
    b.  Välj önskad arbets yta där du vill ansluta agenten.
        ![Välj arbets yta ][7] c. Ange pris nivå.
        ![Välj pris nivå][9]
   
   >[!NOTE]
   >Om arbets ytan redan har en **säkerhets** -eller **SecurityCenterFree** -lösning aktive rad anges prissättningen automatiskt. 

## <a name="cross-subscription-workspace-selection"></a>Val av arbets yta mellan prenumerationer
När du väljer en arbets yta där data ska lagras är alla arbets ytorna i alla prenumerationer tillgängliga. Denna möjlighet att välja arbetsyta mellan prenumerationer gör det möjligt att samla in data från virtuella datorer som körs i olika prenumerationer och lagra dessa data på önskad arbetsyta. Det här alternativet är användbart om du använder en centraliserad arbetsyta i din organisation och vill använda den för insamling av säkerhetsdata. Mer information om hur du hanterar arbets ytor finns i [Hantera åtkomst till arbets yta](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Data insamlings nivå
När du väljer en nivå för datainsamling i Azure Security Center påverkar det endast lagringen av säkerhetshändelser i Log Analytics-arbetsytan. Log Analytics-agenten samlar fortfarande in och analyserar de säkerhets händelser som krävs för Azure Security Center s hot skydd, oavsett vilken nivå av säkerhets händelser som du väljer att lagra i din Log Analytics arbets yta (om det finns några). När du väljer att lagra säkerhetshändelser i din arbetsyta, aktiveras undersökning, sökning och granskning av dessa händelser arbetsytan. 
> [!NOTE]
> Att lagra data i Log Analytics kan debiteras ytterligare avgifter för data lagring. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Du kan välja rätt filtrerings princip för dina prenumerationer och arbets ytor från fyra uppsättningar händelser som ska lagras i din arbets yta: 

- **Ingen** – inaktivera lagring av säkerhets händelser. Det här är standardinställningen.
- **Minimal** – en mindre uppsättning händelser för kunder som vill minimera händelse volymen.
- **Common** – det här är en uppsättning händelser som uppfyller de flesta kunder och som gör det möjligt för dem att göra en fullständig Gransknings logg.
- **Alla händelser** – för kunder som vill se till att alla händelser lagras.


> [!NOTE]
> De här säkerhets händelse uppsättningarna är bara tillgängliga på Security Center standard nivån. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md).
Dessa uppsättningar har utformats för att hantera typiska scenarier. Se till att utvärdera vilken som passar dina behov innan du implementerar den.
>
>

För att avgöra vilka händelser som ska tillhöra **vanliga** och **minimala** händelse uppsättningar arbetade vi med kunder och bransch standarder för att lära dig om den ofiltrerada frekvensen för varje händelse och deras användning. Vi använde följande rikt linjer i den här processen:

- **Minimal** – se till att den här uppsättningen bara omfattar händelser som kan tyda på en lyckad överträdelse och viktiga händelser som har en mycket låg volym. Den här uppsättningen innehåller till exempel användaren lyckade och misslyckade inloggningar (händelse-ID 4624, 4625), men det innehåller inte någon utloggning som är viktigt för granskning men inte meningsfullt för identifiering och har en relativt hög volym. Merparten av data volymen för den här uppsättningen är inloggnings händelser och händelse för att skapa process (händelse-ID 4688).
- **Common** – ange en fullständig Gransknings logg för användare i den här uppsättningen. Den här uppsättningen innehåller till exempel både användar inloggningar och användar inloggningar (händelse-ID 4634). Vi inkluderar gransknings åtgärder som säkerhets grupps ändringar, viktiga Kerberos-åtgärder för domänkontrollanter och andra händelser som rekommenderas av bransch organisationer.

Händelser som har mycket låg volym ingår i den gemensamma uppsättningen som huvud motivation att välja den över alla händelser är att minska volymen och inte filtrera bort vissa händelser.

Här är en fullständig analys av händelse-ID: n för säkerhet och app Locker för varje uppsättning:

| Datanivå | Insamlade händelse indikatorer |
| --- | --- |
| Minimal | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755,,,,,,, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Common | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697,,,,,, |
| | 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771,,,,,, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902,,,,,, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272,,,,,, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> - Om du använder grupprincip objekt (GPO) rekommenderar vi att du aktiverar gransknings princip processen för att skapa händelse 4688 och fältet *kommandorad* i händelse 4688. Mer information om hur du skapar en process 4688 finns i [vanliga frågor om](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)Security Center. Mer information om de här gransknings principerna finns i [rekommendationer för gransknings principer](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Om du vill aktivera insamling av data för [anpassningsbara program kontroller](security-center-adaptive-application.md)konfigurerar Security Center en lokal AppLocker-princip i gransknings läge så att alla program tillåts. Detta gör att AppLocker genererar händelser som sedan samlas in och utnyttjas av Security Center. Det är viktigt att Observera att den här principen inte kommer att konfigureras på datorer där det redan finns en konfigurerad AppLocker-princip. 
> - Om du vill samla in [händelse-ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)för Windows Filtering Platform måste du aktivera [Granska filter plattforms anslutning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/set/Subcategory: "Filtering Platform Connection"/Success: Enable)
>

Så här väljer du filtrerings princip:
1. På sidan **data insamling** väljer du din filtrerings princip under **säkerhets händelser**.
2. Välj **Spara**.

   ![Välj filtrerings princip][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatisk etablering i fall av en redan befintlig agent installation<a name="preexisting"></a> 

Följande användnings fall anger hur automatisk etablering fungerar i fall när det redan finns en agent eller ett tillägg installerat. 

- Log Analytics agent är installerad på datorn, men inte som ett tillägg (direkt agent)<br>
Om Log Analytics-agenten installeras direkt på den virtuella datorn (inte som ett Azure-tillägg), kommer Security Center att installera Log Analytics agent tillägget, och kan uppgradera Log Analytics agent till den senaste versionen.
Den installerade agenten fortsätter att rapportera till sina redan konfigurerade arbets ytor och rapporterar även till arbets ytan som kon figurer ATS i Security Center (Multi-värdar stöds på Windows-datorer).
Om den konfigurerade arbets ytan är en användar arbets yta (inte Security Center standard arbets ytan) måste du installera lösningen "Security/" securityFree på den för Security Center för att börja bearbeta händelser från virtuella datorer och datorer som rapporterar till arbets ytan.<br>
<br>
För Linux-datorer stöds inte agent multi-värdar ännu, vilket innebär att om en befintlig agent installation identifieras sker ingen automatisk etablering och datorns konfiguration ändras inte.
<br>
För befintliga datorer på prenumerationer som har registrerats på Security Center före 17 mars 2019, kommer Log Analytics agent-tillägget inte att installeras och datorn påverkas inte om du kommer att identifiera den. De här datorerna finns i rekommendationen "lösa övervaknings agent hälso problem på dina datorer" för att lösa problem med Agent installationen på de här datorerna.

  
- System Center Operations Manager agenten är installerad på datorn<br>
Security Center kommer att installera Log Analytics agent-tillägget sida vid sida till den befintliga Operations Manager. Den befintliga Operations Manager-agenten fortsätter att rapportera till Operations Manager servern på normalt sätt. Den Operations Manager agenten och Log Analytics agent delar vanliga kör tids bibliotek, som kommer att uppdateras till den senaste versionen under den här processen. Om Operations Manager agent version 2012 är installerad ska **du inte** aktivera automatisk etablering.<br>

- Det finns redan ett befintligt VM-tillägg<br>
    - När övervaknings agenten installeras som ett tillägg tillåter tilläggs konfigurationen rapportering till endast en enda arbets yta. Security Center åsidosätter inte befintliga anslutningar till användar arbets ytor. Security Center kommer att lagra säkerhets data från den virtuella datorn i arbets ytan som redan är ansluten, förutsatt att lösningen "säkerhet" eller "securityFree" har installerats på den. Security Center kan uppgradera tilläggs versionen till den senaste versionen i den här processen.  
    - Om du vill se vilken arbets yta som det befintliga tillägget skickar data till kör du testet för att [Verifiera anslutningen med Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Du kan också öppna Log Analytics arbets ytor, välja en arbets yta, välja den virtuella datorn och titta på Log Analytics agent-anslutningen. 
    - Om du har en miljö där Log Analytics-agenten är installerad på klient arbets stationer och rapporterar till en befintlig Log Analytics arbets yta, granskar du listan över [operativ system som stöds av Azure Security Center](security-center-os-coverage.md) för att kontrol lera att operativ systemet stöds. Mer information finns i [befintliga Log Analytics-kunder](./faq-azure-monitor-logs.md).
 
### <a name="turn-off-automatic-provisioning"></a>Inaktivera automatisk etablering<a name="offprovisioning"></a>
Du kan inaktivera automatisk etablering från resurser när du vill genom att inaktivera den här inställningen i säkerhets principen. 


1. Gå tillbaka till Security Center huvud menyn och välj säkerhets princip.
2. Klicka på **Redigera inställningar** på den rad i prenumerationen som du vill inaktivera automatisk etablering för.
3. På sidan **säkerhets princip – data insamling** under **Automatisk etablering** väljer du **av**.
4. Välj **Spara**.

   ![Inaktivera automatisk etablering][6]

När automatisk etablering är inaktive rad (inaktive rad) visas inte avsnittet standard konfiguration av arbets yta.

Om du stänger av automatisk etablering efter det tidigare har agenterna inte etableras på nya virtuella datorer.

 
> [!NOTE]
>  Om du inaktiverar automatisk etablering tas inte Log Analytics agenten bort från virtuella Azure-datorer där agenten etablerades. Information om hur du tar bort OMS-tillägget finns i [Hur gör jag för att ta bort OMS-tillägg som installerats av Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Manuell agent etablering<a name="manual-agent"></a>
 
Det finns flera sätt att installera Log Analytics-agenten manuellt. När du installerar manuellt kontrollerar du att du inaktiverar automatisk etablering.

### <a name="operations-management-suite-vm-extension-deployment"></a>Distribution av Operations Management Suite VM-tillägg 

Du kan installera Log Analytics-agenten manuellt, så Security Center kan samla in säkerhets data från dina virtuella datorer och tillhandahålla rekommendationer och aviseringar.

1. Inaktivera automatisk etablering.

1. Du kan också skapa en arbets yta.

1. Ange arbets ytan där du installerar Log Analytics agenten på standard pris nivån:

    1. Från Security Center menyn väljer du **pris & inställningar**.

    1. Ange den arbets yta som du vill installera agenten på. Kontrol lera att arbets ytan finns i samma prenumeration som du använder i Security Center och att du har Läs-/Skriv behörighet på arbets ytan.

    1. Ange standard pris nivån och välj **Spara**.

        ![Ange en arbets yta som standard pris nivå](.\media\security-center-enable-data-collection\workspace-to-standard-tier.gif)

       >[!NOTE]
       >Om arbets ytan redan har en **säkerhets** -eller **SecurityCenterFree** -lösning aktive rad anges prissättningen automatiskt. 
   > 

1. Om du vill distribuera agenterna på nya virtuella datorer med en Resource Manager-mall installerar du Log Analytics-agenten:

   a.  [Installera Log Analytics agent för Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Installera Log Analytics agent för Linux](../virtual-machines/extensions/oms-linux.md)

1. Om du vill distribuera tilläggen på befintliga virtuella datorer följer du anvisningarna i [samla in data om Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > Avsnittet **samla in händelse-och prestanda data** är valfritt.
   >

1. Om du vill använda PowerShell för att distribuera tillägget använder du anvisningarna från Virtual Machines-dokumentationen:

    - [För Windows-datorer](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#powershell-deployment)

    - [För Linux-datorer](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-linux?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#azure-cli-deployment)



> [!NOTE]
> Instruktioner för hur du integrerar Security Center med hjälp av PowerShell finns i [Automatisera onboarding av Azure Security Center med PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Felsökning

-   Information om hur du identifierar installations problem med automatisk etablering finns i [övervaka agentens hälso problem](security-center-troubleshooting-guide.md#mon-agent).

-  Information om hur du identifierar nätverks krav för övervaknings agenten finns i [Felsöka nätverks krav för övervaknings agenten](security-center-troubleshooting-guide.md#mon-network-req).
-   Information om hur du identifierar manuella onboarding-problem finns i [så här felsöker du problem med Operations Management Suite onboarding](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Identifiera oövervakade virtuella datorer och problem med datorer:

    En virtuell dator eller dator är inte övervakad av Security Center om datorn inte kör Log Analytics agent-tillägget. En dator kan ha en lokal agent som redan är installerad, till exempel OMS Direct-agenten eller System Center Operations Manager agenten. Datorer med dessa agenter identifieras som oövervakade eftersom de här agenterna inte stöds fullt ut i Security Center. För att helt kunna dra nytta av alla Security Centers funktioner krävs Log Analytics agent-tillägget.

    Mer information om varför Security Center inte kan övervaka virtuella datorer och datorer som har initierats för automatisk etablering finns i [övervaknings agentens hälso problem](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Nästa steg
Den här artikeln visar hur data samlas in och automatisk etablering i Security Center fungerar. Mer information om Security Center finns på följande sidor:

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
