---
title: Hantera Azure DDoS Protection standard med hjälp av Azure Portal
titlesuffix: Azure Virtual Network
description: Lär dig hur du använder Azure DDoS Protection standard telemetri i Azure Monitor för att minimera en attack.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 706379649b47846b5c020dc76493a98e346c4a8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317692"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Hantera Azure DDoS Protection standard med hjälp av Azure Portal

Lär dig hur du aktiverar och inaktiverar DDoS-skydd (distributed denial of Service) och använder telemetri för att minimera en DDoS-attack med Azure DDoS Protection standard. DDoS Protection standard skyddar Azure-resurser, till exempel virtuella datorer, belastningsutjämnare och programgatewayer som har tilldelats en [offentlig Azure-IP-adress](virtual-network-public-ip-address.md) . Mer information om DDoS Protection standard och dess funktioner finns i [DDoS Protection standard översikt](ddos-protection-overview.md).

Innan du slutför några steg i den här självstudien loggar du in på Azure Portal på https://portal.azure.com med ett konto som tilldelats rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats lämpliga åtgärder som anges i [behörigheter](#permissions-and-restrictions).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-ddos-protection-plan"></a>Skapa en DDoS skydds plan

En DDoS skydds plan definierar en uppsättning virtuella nätverk som har DDoS Protection standard aktiverat, mellan prenumerationer. Du kan konfigurera en DDoS skydds plan för din organisation och länka virtuella nätverk från flera prenumerationer till samma plan. Själva DDoS Protections planen är också kopplad till en prenumeration, som du väljer när du skapar planen. DDoS Protections planen fungerar mellan regioner och prenumerationer. Exempel – du kan skapa planen i regionen öst-USA och länka till prenumerations #1 i din klient organisation. Samma plan kan länkas till virtuella nätverk från andra prenumerationer i olika regioner över din klient organisation. Prenumerationen är associerad med den månatliga återkommande fakturan för planen, samt överförbruknings avgifter, om antalet skyddade offentliga IP-adresser överstiger 100. Mer information om DDoS-priser finns i [pris information](https://azure.microsoft.com/pricing/details/ddos-protection/).

Det krävs inte att skapa mer än en plan för de flesta organisationer. Det går inte att flytta en plan mellan prenumerationer. Om du vill ändra prenumerationen som en plan finns i måste du [ta bort den befintliga planen](#work-with-ddos-protection-plans) och skapa en ny.

1. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal.
2. Sök efter *DDoS*. När **DDoS Protection Plan** visas i Sök resultaten väljer du den.
3. Välj **Skapa**.
4. Ange eller Välj dina egna värden eller ange eller Välj följande exempel värden och välj sedan **skapa**:

    |Inställning        |Värde                                              |
    |---------      |---------                                          |
    |Namn           | myDdosProtectionPlan                              |
    |Prenumeration   | Välj din prenumeration.                         |
    |Resursgrupp | Välj **Skapa nytt** och ange *myResourceGroup* |
    |Plats       | USA, östra                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Aktivera DDoS för ett nytt virtuellt nätverk

1. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Ange eller Välj dina egna värden, av ange eller Välj följande exempel värden, acceptera återstående standardvärden och välj sedan **skapa**:

    | Inställning         | Värde                                                        |
    | ---------       | ---------                                                    |
    | Namn            | myVirtualNetwork                                             |
    | Prenumeration    | Välj din prenumeration.                                    |
    | Resursgrupp  | Välj **Använd befintlig**och välj sedan **myResourceGroup** |
    | Plats        | USA, östra                                                      |
    | DDoS Protection standard | Välj **Aktivera**. Den plan du väljer kan vara i samma eller en annan prenumeration än det virtuella nätverket, men båda prenumerationerna måste vara kopplade till samma Azure Active Directory-klient.|

Du kan inte flytta ett virtuellt nätverk till en annan resurs grupp eller prenumeration när DDoS standard är aktive rad för det virtuella nätverket. Om du behöver flytta ett virtuellt nätverk med DDoS standard aktiverat inaktiverar du DDoS standard först, flyttar det virtuella nätverket och aktiverar sedan DDoS standard. Efter flyttningen återställs automatiskt justerade princip tröskelvärden för alla skyddade offentliga IP-adresser i det virtuella nätverket.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Aktivera DDoS för ett befintligt virtuellt nätverk

1. Skapa en DDoS skydds plan genom att slutföra stegen i [skapa en DDoS-skydds plan](#create-a-ddos-protection-plan), om du inte har en befintlig DDoS Protection-plan.
2. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal.
3. Ange namnet på det virtuella nätverk som du vill aktivera DDoS Protection standard för i **rutan Sök efter resurser, tjänster och dokument** högst upp i portalen. När namnet på det virtuella nätverket visas i Sök resultatet väljer du det.
4. Välj **DDoS-skydd**under **Inställningar**.
5. Välj **standard**. Under **DDoS Protection Plan**väljer du en befintlig DDoS-skydds plan eller den plan som du skapade i steg 1 och väljer sedan **Spara**. Den plan du väljer kan vara i samma eller en annan prenumeration än det virtuella nätverket, men båda prenumerationerna måste vara kopplade till samma Azure Active Directory-klient.

**Kommandon** 
- Azure CLI: [AZ Network DDoS-Protection Create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create)
- PowerShell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Inaktivera DDoS för ett virtuellt nätverk

1. Ange namnet på det virtuella nätverk som du vill inaktivera DDoS-skydds standard för i **rutan Sök efter resurser, tjänster och dokument** överst i portalen. När namnet på det virtuella nätverket visas i Sök resultatet väljer du det.
2. Välj **Under DDoS Protection standard**, Välj **inaktivera**.

**Kommandon** 
- Azure CLI: [AZ Network DDoS-Protection Delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete)
- PowerShell: [Remove-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Arbeta med DDoS-skydds planer

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *DDoS* i **filter** rutan. När **DDoS skydds planer** visas i resultaten väljer du det.
3. Välj den skydds plan som du vill visa i listan.
4. Alla virtuella nätverk som är kopplade till planen visas i listan.
5. Om du vill ta bort en plan måste du först koppla bort alla virtuella nätverk från det. Om du vill koppla bort en plan från ett virtuellt nätverk kan du läsa [inaktivera DDoS för ett virtuellt nätverk](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurera aviseringar för DDoS skydds mått

Du kan välja något av de tillgängliga DDoS-skydds måtten för att varna dig när det finns en aktiv minskning under ett angrepp med hjälp av Azure Monitor varnings konfiguration. När villkoren är uppfyllda får den angivna adressen ett e-postmeddelande om avisering:

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *Monitor* i **filter** rutan. När **övervakaren** visas i resultaten väljer du den.
3. Välj **mått** under **delade tjänster**.
4. Ange eller Välj dina egna värden eller ange följande exempel värden, Godkänn återstående standardvärden och välj sedan **OK**:

    |Inställning                  |Värde                                                                                               |
    |---------                |---------                                                                                           |
    |Namn                     | myDdosAlert                                                                                        |
    |Prenumeration             | Välj den prenumeration som innehåller den offentliga IP-adress som du vill ta emot aviseringar för.        |
    |Resursgrupp           | Välj den resurs grupp som innehåller den offentliga IP-adress som du vill ta emot aviseringar för.      |
    |Resurs                 | Välj den offentliga IP-adress som innehåller den offentliga IP-adress som du vill ta emot aviseringar för. DDoS övervakar offentliga IP-adresser tilldelade till resurser i ett virtuellt nätverk. Om du inte har några resurser med offentliga IP-adresser i det virtuella nätverket måste du först skapa en resurs med en offentlig IP-adress. Du kan övervaka den offentliga IP-adressen för alla resurser som distribueras via Resource Manager (inte klassisk) i det [virtuella nätverket för Azure-tjänster](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), förutom för Azure App Service miljöer och Azure VPN gateway. Om du vill fortsätta med den här självstudien kan du snabbt skapa en virtuell [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator.                   |
    |Mått                   | Under DDoS-attack eller inte                                                                            |
    |Tröskelvärde                | 1- **1** innebär att du är utsatt för angrepp. **0** innebär att du inte är utsatt för angrepp.                         |
    |Period                   | Välj vilket värde du vill.                                                                   |
    |Meddela via e-post         | Markera kryssrutan                                                                                  |
    |Ytterligare administratör | Ange din e-postadress om du inte är e-postägare, deltagare eller läsare för prenumerationen. |

    Inom ett par minuter får du ett e-postmeddelande från Azure Monitor mått som ser ut ungefär som på följande bild:

    ![Attack avisering](./media/manage-ddos-protection/ddos-alert.png)


Information om hur du simulerar en DDoS-attack för att verifiera din avisering finns i [validera DDoS-identifiering](#validate-ddos-detection).

Du kan också lära dig mer om hur du [konfigurerar Webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa aviseringar.

## <a name="use-ddos-protection-telemetry"></a>Använda DDoS Protection-telemetri

Telemetri för ett angrepp tillhandahålls via Azure Monitor i real tid. Telemetri är bara tillgängligt för den varaktighet som en offentlig IP-adress är under minskning. Du ser inte telemetri före eller efter att en attack har begränsats.

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *Monitor* i **filter** rutan. När **övervakaren** visas i resultaten väljer du den.
3. Välj **mått**under **delade tjänster**.
4. Välj den **prenumeration** och **resurs grupp** som innehåller den offentliga IP-adress som du vill använda telemetri för.
5. Välj **offentlig IP-adress** för **resurs typ**och välj sedan den angivna offentliga IP-adress som du vill använda telemetri för.
6. En serie med **tillgängliga mått** visas till vänster på skärmen. De här måtten, när de är markerade, visas i diagrammet **Azure Monitor mått diagram** på översikts skärmen.
7. Välj **agg regerings** typen **Max**

Mått namnen visar olika paket typer och byte jämfört med paket, med en grundläggande konstruktion av taggnamn på varje mått enligt följande:

- **Taggat taggnamn** (till exempel **inkommande paket som släppts DDoS**): antalet paket som tappas/rensas av DDoS Protection System.
- **Namn på vidarebefordrad tagg** (t. ex. **inkommande paket vidarebefordrade DDoS**): antalet paket som vidarebefordras av DDoS-systemet till den virtuella mål-VIP – trafik som inte har filtrerats.
- **Inget taggnamn** (till exempel **inkommande paket DDoS**): det totala antalet paket som ingår i skrubbnings systemet – som representerar summan av de paket som har släppts och vidarebefordrats.

Information om hur du simulerar en DDoS-attack för att validera telemetri finns i [validera DDoS-identifiering](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Visa DDoS-principer för minskning

DDoS Protection standard tillämpar tre automatiskt justerade skydds principer (TCP-SYN, TCP & UDP) för varje offentlig IP-adress för den skyddade resursen, i det virtuella nätverk där DDoS har Aktiver ATS. Du kan visa princip tröskelvärdena genom att välja de  **inkommande TCP-paketen för att utlösa DDoS-minskning** och **inkommande UDP-paket för att utlösa DDoS** -hälsomått med **agg regerings** typ som "Max", som du ser i följande bild:

![Visa begränsnings principer](./media/manage-ddos-protection/view-mitigation-policies.png)

Princip tröskelvärden konfigureras automatiskt via Azure Machine Learning-baserad nätverks trafik profilering. Endast när princip tröskeln har brutits inträffar DDoS-lösningen för IP-adressen under angrepp.

## <a name="configure-ddos-attack-analytics"></a>Konfigurera analyser av DDoS-attacker
Azure DDoS Protection standard ger detaljerad information om angrepp och visualisering med DDoS-attack analys. Kunder som skyddar sina virtuella nätverk mot DDoS-attacker har detaljerad insyn i attack trafik och åtgärder som vidtas för att minska risken för angrepp med hjälp av angrepps minsknings rapporter & skydds flödes loggar. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurera DDoS attack minsknings rapporter
Rapporter om attack minskning använder de Netflow Protocol-data som sammanställs för att ge detaljerad information om angreppet på din resurs. När en offentlig IP-resurs är utsatt för angrepp startar rapportgenerering så snart som lösningen startar. Det kommer att finnas en stegvis rapport som genereras var 5: e minut och en efter minsknings rapport för hela minsknings perioden. För att säkerställa att DDoS-attacken fortsätter under en längre tid kan du Visa den senaste ögonblicks bilden av minsknings rapporten var 5: e minut och en fullständig sammanfattning när risken för attacker är över. 

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *Monitor* i **filter** rutan. När **övervakaren** visas i resultaten väljer du den.
3. Under **Inställningar**väljer du **diagnostikinställningar**.
4. Välj den **prenumeration** och **resurs grupp** som innehåller den offentliga IP-adress som du vill logga.
5. Välj **offentlig IP-adress** för **resurs typ**och välj sedan den angivna offentliga IP-adress som du vill använda för att logga mått för.
6. Välj **Aktivera diagnostik för att samla in DDoSMitigationReports-loggen** och välj sedan så många av följande alternativ som du behöver:

    - **Arkivera till ett lagrings konto**: data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns i [arkivera resurs loggar](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Strömma till en Event Hub**: tillåter att en logg mottagare hämtar loggar med hjälp av en Azure Event Hub. Event Hub möjliggör integrering med Splunk eller andra SIEM-system. Mer information om det här alternativet finns i [strömma resurs loggar till en Event Hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Skicka till Log Analytics**: skriver loggar till tjänsten Azure Monitor. Mer information om det här alternativet finns [i samla in loggar för användning i Azure Monitor loggar](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Både den stegvisa & minsknings rapporter efter attacken innehåller följande fält
- Angrepps vektorer
- Trafik statistik
- Orsak till ignorerade paket
- Protokoll som ingår
- 10 främsta käll länder eller regioner
- ASN: er 10 främsta källa

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurera flödes loggar för DDoS-attackens minskning
Med anslutnings åtgärder för attackering kan du granska förlorad trafik, vidarebefordrad trafik och andra intressanta Datapoints under en aktiv DDoS-attack i nära real tid. Du kan mata in en konstant ström från dessa data till dina SIEM-system via händelsehubben för övervakning i nära real tid, vidta potentiella åtgärder och åtgärda behovet av ditt försvar. 

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *Monitor* i **filter** rutan. När **övervakaren** visas i resultaten väljer du den.
3. Under **Inställningar**väljer du **diagnostikinställningar**.
4. Välj den **prenumeration** och **resurs grupp** som innehåller den offentliga IP-adress som du vill logga.
5. Välj **offentlig IP-adress** för **resurs typ**och välj sedan den angivna offentliga IP-adress som du vill använda för att logga mått för.
6. Välj **Aktivera diagnostik för att samla in DDoSMitigationFlowLogs-loggen** och välj sedan så många av följande alternativ som du behöver:

    - **Arkivera till ett lagrings konto**: data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns i [arkivera resurs loggar](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Strömma till en Event Hub**: tillåter att en logg mottagare hämtar loggar med hjälp av en Azure Event Hub. Event Hub möjliggör integrering med Splunk eller andra SIEM-system. Mer information om det här alternativet finns i [strömma resurs loggar till en Event Hub](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Skicka till Log Analytics**: skriver loggar till tjänsten Azure Monitor. Mer information om det här alternativet finns [i samla in loggar för användning i Azure Monitor loggar](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Om du vill visa flödes logg data i Azure Analytics-arbetsboken kan du importera exempel instrument panelen från https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook

Flödes loggar har följande fält: 
- Käll-IP-adress
- Mål-IP-adress
- Källport 
- Målport 
- Protokoll typ 
- Åtgärd som vidtas under minskning

Attack analys fungerar bara om DDoS Protection standard är aktiverat på det virtuella nätverket för den offentliga IP-adressen. 

## <a name="validate-ddos-detection"></a>Verifiera DDoS-identifiering

Microsoft samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) för att skapa ett gränssnitt där du kan generera trafik mot DDoS Protection-aktiverade offentliga IP-adresser för simuleringar. Med moln simulerings Bryt punkt kan du:

- Verifiera hur Microsoft Azure DDoS Protection skyddar dina Azure-resurser från DDoS-attacker
- Optimera incident svars processen under DDoS-attack
- Dokumentera DDoS-kompatibilitet
- Träna dina nätverks säkerhets team

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Visa DDoS-skydds varningar i Azure Security Center

Azure Security Center innehåller en lista över [säkerhets aviseringar](/azure/security-center/security-center-managing-and-responding-alerts)med information som hjälper dig att undersöka och åtgärda problem. Med den här funktionen får du en enhetlig vy över aviseringar, inklusive DDoS-relaterade aviseringar och åtgärder som vidtagits för att minimera angreppet i nära tiden.
Det finns två olika aviseringar som du kan se för identifiering och minskning av DDoS-attacker:

- **DDoS-attack identifierad för offentlig IP**: den här aviseringen skapas när DDoS-skydds tjänsten identifierar att en av dina offentliga IP-adresser är målet för en DDoS-attack.
- **DDoS-attack minimerad för offentlig IP**: den här aviseringen genereras när en attack på den offentliga IP-adressen har minimerats.
Om du vill visa aviseringarna öppnar du **Security Center** i Azure Portal. Under **skydd av hot**väljer du **säkerhets aviseringar**. Följande skärm bild visar ett exempel på DDoS-attackens aviseringar.

![DDoS-avisering i Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Aviseringarna inkluderar allmän information om den offentliga IP-adressen som är under attack, geo-och hot information och åtgärder.

## <a name="permissions-and-restrictions"></a>Behörigheter och begränsningar

För att kunna arbeta med DDoS-skydds planer måste ditt konto tilldelas rollen [nätverks deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som har tilldelats lämpliga åtgärder i följande tabell:

| Action                                            | Namn                                     |
| ---------                                         | -------------                            |
| Microsoft. Network/ddosProtectionPlans/Read        | Läs en DDoS skydds plan              |
| Microsoft. Network/ddosProtectionPlans/Write       | Skapa eller uppdatera en DDoS-skydds plan  |
| Microsoft. Network/ddosProtectionPlans/Delete      | Ta bort en DDoS skydds plan            |
| Microsoft. Network/ddosProtectionPlans/JOIN/åtgärd | Delta i en DDoS skydds plan              |

För att aktivera DDoS-skydd för ett virtuellt nätverk måste ditt konto också tilldelas lämpliga [åtgärder för virtuella nätverk](manage-virtual-network.md#permissions).

### <a name="azure-policy"></a>Azure Policy
För kunder som har olika prenumerationer och som vill se till att en enda plan för Azure DDoS Protection standard distribueras över sin klient för kostnads kontroll, kan du använda Azure Policy för att [begränsa skapandet av Azure DDoS Protection standard-planer](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). Den här principen blockerar skapandet av eventuella DDoS planer, om inte prenumerationen tidigare har marker ATS som ett undantag. Den här principen visar också en lista över alla prenumerationer som har en DDoS-plan distribuerad men bör inte markeras som inkompatibel. 

## <a name="next-steps"></a>Nästa steg

- Skapa och tilldela [Azure policy definitioner](policy-samples.md) för virtuella nätverk
