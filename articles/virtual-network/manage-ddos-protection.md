---
title: Hantera Azure DDoS Protection Standard med Azure-portalen
titlesuffix: Azure Virtual Network
description: Lär dig hur du använder Azure DDoS Protection Standard telemetri i Azure Monitor för att minska en attack.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 786b21e7571ed173d2da90f587a5b76d8c92a13d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279733"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Hantera Azure DDoS Protection Standard med Azure-portalen

Lär dig hur du aktiverar och inaktiverar DDoS-skydd (Distributed Denial of Service) och använder telemetri för att minska en DDoS-attack med Azure DDoS Protection Standard. DDoS Protection Standard skyddar Azure-resurser som virtuella datorer, belastningsutjämnare och programgateways som har en Offentlig [Azure-IP-adress](virtual-network-public-ip-address.md) tilldelad. Mer information om DDoS Protection Standard och dess funktioner finns i [DDoS Protection Standard översikt](ddos-protection-overview.md).

Innan du slutför några steg i den här https://portal.azure.com självstudien loggar du in på Azure-portalen med ett konto tilldelat till [nätverksdeltagarens](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som tilldelas lämpliga åtgärder som anges i [Behörigheter](#permissions).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-a-ddos-protection-plan"></a>Skapa en DDoS-skyddsplan

En DDoS-skyddsplan definierar en uppsättning virtuella nätverk som har DDoS-skyddsstandard aktiverad, över prenumerationer. Du kan konfigurera en DDoS-skyddsplan för din organisation och länka virtuella nätverk från flera prenumerationer till samma plan. Själva DDoS-skyddsplanen är också associerad med en prenumeration som du väljer när planen skapades. DDoS-skyddsplanen fungerar i regioner och prenumerationer. Exempel -du kan skapa planen i Region Öst-USA och länka till prenumeration #1 i din klientorganisation. Samma plan kan länkas till virtuella nätverk från andra prenumerationer i olika regioner, i hela din klientorganisation. Prenumerationen som planen är kopplad till ådrar sig den månatliga återkommande fakturan för planen, samt överutnyttjandeavgifter, om antalet skyddade offentliga IP-adresser överstiger 100. Mer information om DDoS-priser finns i [prisinformation](https://azure.microsoft.com/pricing/details/ddos-protection/).

Det krävs inte att fler än en plan skapas för de flesta organisationer. Det går inte att flytta en plan mellan prenumerationer. Om du vill ändra prenumerationen som en plan har finns i måste du [ta bort den befintliga planen](#work-with-ddos-protection-plans) och skapa en ny.

1. Välj **Skapa en resurs** i det övre vänstra hörnet av Azure-portalen.
2. Sök efter *DDoS*. När **DDos-skyddsplanen** visas i sökresultaten markerar du den.
3. Välj **Skapa**.
4. Ange eller välj egna värden, eller ange, eller markera följande exempelvärden och välj sedan **Skapa:**

    |Inställning        |Värde                                              |
    |---------      |---------                                          |
    |Namn           | myDdosProtectionPlan                              |
    |Prenumeration   | Välj din prenumeration.                         |
    |Resursgrupp | Välj **Skapa nytt** och ange *myResourceGroup* |
    |Location       | USA, östra                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Aktivera DDoS för ett nytt virtuellt nätverk

1. Välj **Skapa en resurs** i det övre vänstra hörnet av Azure-portalen.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Ange eller välj egna värden, ange eller välj följande exempelvärden, acceptera de återstående standardvärdena och välj sedan **Skapa:**

    | Inställning         | Värde                                                        |
    | ---------       | ---------                                                    |
    | Namn            | myVirtualNetwork                                             |
    | Prenumeration    | Välj din prenumeration.                                    |
    | Resursgrupp  | Välj **Använd befintlig**och välj sedan **myResourceGroup** |
    | Location        | USA, östra                                                      |
    | DDos-skydd | Välj **Standard** och välj sedan **myDdosProtectionPlan**under **DDoS-skydd**. Abonnemanget du väljer kan vara i samma eller annorlunda prenumeration än det virtuella nätverket, men båda prenumerationerna måste associeras till samma Azure Active Directory-klientorganisation.|

Du kan inte flytta ett virtuellt nätverk till en annan resursgrupp eller prenumeration när DDoS Standard är aktiverat för det virtuella nätverket. Om du behöver flytta ett virtuellt nätverk med DDoS Standard aktiverat inaktiverar du DDoS Standard först, flyttar det virtuella nätverket och aktiverar sedan DDoS-standarden. Efter flytten återställs de automatiskt inställda principtrösklarna för alla skyddade offentliga IP-adresser i det virtuella nätverket.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Aktivera DDoS för ett befintligt virtuellt nätverk

1. Skapa en DDoS-skyddsplan genom att slutföra stegen i [Skapa en DDoS-skyddsplan](#create-a-ddos-protection-plan)om du inte har en befintlig DDoS-skyddsplan.
2. Välj **Skapa en resurs** i det övre vänstra hörnet av Azure-portalen.
3. Ange namnet på det virtuella nätverk som du vill aktivera DDoS Protection Standard för i **rutan Sökresurser, tjänster och dokument** högst upp i portalen. När namnet på det virtuella nätverket visas i sökresultaten markerar du det.
4. Välj **DDoS-skydd**under **INSTÄLLNINGAR**.
5. Välj **Standard**. Under **DDoS-skyddsplan**väljer du en befintlig DDoS-skyddsplan eller planen som du skapade i steg 1 och väljer sedan **Spara**. Abonnemanget du väljer kan vara i samma eller annorlunda prenumeration än det virtuella nätverket, men båda prenumerationerna måste associeras till samma Azure Active Directory-klientorganisation.

**Kommandon** 
- Azure CLI: az network ddos-protection create Azure CLI: az network ddos-protection create Azure CLI: [az network ddos-protection create](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-create) Azure CLI
- Powershell: [New-AzDdosProtectionPlan](https://docs.microsoft.com/powershell/module/Az.Network/New-AzDdosProtectionPlan?view=azps-2.8.0)
 

## <a name="disable-ddos-for-a-virtual-network"></a>Inaktivera DDoS för ett virtuellt nätverk

1. Ange namnet på det virtuella nätverk som du vill inaktivera DDoS-skyddsstandard för i **rutan Sökresurser, tjänster och dokument** högst upp i portalen. När namnet på det virtuella nätverket visas i sökresultaten markerar du det.
2. Välj **DDoS-skydd**under **INSTÄLLNINGAR**.
3. Välj **Grundläggande** under **DDoS-skyddsplan** och välj sedan **Spara**.

**Kommandon** 
- Azure CLI: az network ddos-protection delete Azure CLI: az network ddos-protection delete Azure CLI: [az network ddos-protection delete](https://docs.microsoft.com/cli/azure/network/ddos-protection?view=azure-cli-latest#az-network-ddos-protection-delete) Azure CLI
- Powershell: [Ta bort AzddosProtectionPlan](https://docs.microsoft.com/powershell/module/az.network/remove-azddosprotectionplan?view=azps-3.2.0)

## <a name="work-with-ddos-protection-plans"></a>Arbeta med DDoS-skyddsplaner

1. Välj **Alla tjänster** högst upp till vänster om portalen.
2. Ange *DDoS* i **rutan Filter.** När **DDoS-skyddsplaner** visas i resultaten markerar du den.
3. Välj den skyddsplan som du vill visa i listan.
4. Alla virtuella nätverk som är associerade med planen visas.
5. Om du vill ta bort en plan måste du först ta bort alla virtuella nätverk från den. Om du vill ta bort en plan från ett virtuellt nätverk finns i [Inaktivera DDoS för ett virtuellt nätverk](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurera aviseringar för DDoS-skyddsmått

Du kan välja något av de tillgängliga DDoS-skyddsmåtten för att varna dig när det finns en aktiv begränsning under en attack med hjälp av Azure Monitor-aviseringskonfigurationen. När villkoren är uppfyllda får den angivna adressen ett e-postmeddelande:

1. Välj **Alla tjänster** högst upp till vänster om portalen.
2. Ange *bildskärm* i rutan **Filter.** När **Monitor** visas i resultatet markerar du den.
3. Välj **Mått** under **DELADE TJÄNSTER**.
4. Ange eller välj egna värden, eller ange följande exempelvärden, acceptera de återstående standardvärdena och välj sedan **OK:**

    |Inställning                  |Värde                                                                                               |
    |---------                |---------                                                                                           |
    |Namn                     | myDdosAlert                                                                                        |
    |Prenumeration             | Välj den prenumeration som innehåller den offentliga IP-adress som du vill ta emot aviseringar för.        |
    |Resursgrupp           | Välj den resursgrupp som innehåller den offentliga IP-adress som du vill ta emot aviseringar för.      |
    |Resurs                 | Välj den offentliga IP-adress som innehåller den offentliga IP-adress som du vill ta emot aviseringar för. DDoS övervakar offentliga IP-adresser som tilldelats resurser inom ett virtuellt nätverk. Om du inte har några resurser med offentliga IP-adresser i det virtuella nätverket måste du först skapa en resurs med en offentlig IP-adress. Du kan övervaka den offentliga IP-adressen för alla resurser som distribueras via Resource Manager (inte klassisk) som anges i [virtuellt nätverk för Azure-tjänster](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), med undantag för Azure App Service-miljöer och Azure VPN Gateway. Om du vill fortsätta med den här självstudien kan du snabbt skapa en virtuell [Windows-](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux-dator.](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)                   |
    |Mått                   | Under DDoS-attack eller inte                                                                            |
    |Tröskelvärde                | 1 - **1** betyder att du är under attack. **0** betyder att du inte är under attack.                         |
    |Period                   | Välj vilket värde du väljer.                                                                   |
    |Meddela via e-post         | Markera kryssrutan                                                                                  |
    |Ytterligare administratör | Ange din e-postadress om du inte är e-postägare, deltagare eller läsare för prenumerationen. |

    Inom några minuter efter attackidentifiering får du ett e-postmeddelande från Azure Monitor-mått som liknar följande bild:

    ![Varning för attack](./media/manage-ddos-protection/ddos-alert.png)


Information om hur du simulerar en DDoS-attack för att validera din avisering finns i [Verifiera DDoS-identifiering](#validate-ddos-detection).

Du kan också läsa mer om [hur du konfigurerar webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [logikappar](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa aviseringar.

## <a name="use-ddos-protection-telemetry"></a>Använd DDoS-skydd telemetri

Telemetri för en attack tillhandahålls via Azure Monitor i realtid. Telemetrin är endast tillgänglig under den tid som en offentlig IP-adress är under begränsning. Du ser inte telemetri före eller efter en attack mildras.

1. Välj **Alla tjänster** högst upp till vänster om portalen.
2. Ange *bildskärm* i rutan **Filter.** När **Monitor** visas i resultatet markerar du den.
3. Välj **Mått**under **DELADE TJÄNSTER**.
4. Välj den **prenumerations-** och **resursgrupp** som innehåller den offentliga IP-adress som du vill ha telemetri för.
5. Välj **Offentlig IP-adress** för **resurstyp**och välj sedan den specifika offentliga IP-adress som du vill ha telemetri för.
6. En serie **tillgängliga mått** visas till vänster på skärmen. Dessa mått, när de väljs, är graferade i **Azure Monitor Metrics Chart** på översiktsskärmen.
7. Välj **aggregeringstypen** som **Max**

Måttnamnen presenterar olika pakettyper och byte jämfört med paket, med en grundläggande konstruktion av taggnamn på varje mått enligt följande:

- **Borttagna taggnamn** (till exempel **Inkommande paket tappade DDoS**): Antalet ignorerade/skrubbas paket av DDoS-skyddssystemet.
- **Vidarebefordrat taggnamn** (till exempel **Inkommande paket Vidarebefordrade DDoS**): Antalet paket som vidarebefordras av DDoS-systemet till mål-VIP – trafik som inte filtrerades.
- **Inget taggnamn** (till exempel **Inkommande paket DDoS**): Det totala antalet paket som kom in i skrubbningssystemet – som representerar summan av de paket som har tappats och vidarebefordrats.

Information om hur du simulerar en DDoS-attack för att validera telemetri finns i [Verifiera DDoS-identifiering](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Visa DDoS-begränsningsprinciper

DDoS Protection Standard tillämpar tre automatiskt inställda begränsningsprinciper (TCP SYN, TCP & UDP) för varje offentlig IP-adress för den skyddade resursen, i det virtuella nätverket som har DDoS aktiverat. Du kan visa principtrösklarna genom att välja **inkommande TCP-paket för att utlösa DDoS-begränsning** och **inkommande UDP-paket för att utlösa DDoS-begränsningsmått** med **aggregeringstyp** som "Max", som visas i följande bild:

![Visa begränsningsprinciper](./media/manage-ddos-protection/view-mitigation-policies.png)

Principtröskelvärden konfigureras automatiskt via Azure machine learning-based network traffic profileing. Endast när principtröskeln överskrids uppstår DDoS-begränsning för IP-adressen under attack.

## <a name="configure-ddos-attack-analytics"></a>Konfigurera analyser av DDoS-attacker
Azure DDoS Protection-standarden ger detaljerade attackinsikter och visualisering med DDoS Attack Analytics. Kunder som skyddar sina virtuella nätverk mot DDoS-attacker har detaljerad insyn i attacktrafik och åtgärder som vidtas för att minska attacken via attackreducerande rapporter & begränsningsflödesloggar. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurera DDoS-attackreduceringsrapporter
Attack mitigation rapporter använder Netflow-protokolldata som aggregeras för att ge detaljerad information om attacken på din resurs. När som helst en offentlig IP-resurs är under attack, kommer rapportgenereringen att starta så snart begränsningen startar. Det kommer att finnas en inkrementell rapport som genereras var 5 minuter och en rapport efter begränsning för hela begränsningsperioden. Detta för att säkerställa att DDoS-attacken fortsätter under en längre tid, kommer du att kunna visa den senaste ögonblicksbilden av begränsningsrapporten var 5:e minut och en fullständig sammanfattning när attackreduceringen är över. 

1. Välj **Alla tjänster** högst upp till vänster om portalen.
2. Ange *bildskärm* i rutan **Filter.** När **Monitor** visas i resultatet markerar du den.
3. Under **INSTÄLLNINGAR**väljer du **Diagnostikinställningar**.
4. Välj den **prenumerations-** och **resursgrupp** som innehåller den offentliga IP-adress som du vill logga.
5. Välj **Offentlig IP-adress** för **resurstyp**och välj sedan den specifika offentliga IP-adress som du vill logga mått för.
6. Välj **Aktivera diagnostik för att samla in loggen DDoSMitigationReports** och välj sedan så många av följande alternativ som du behöver:

    - **Arkiv till ett lagringskonto**: Data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns i [Arkivera diagnostikloggar](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Strömma till en händelsehubb**: Tillåter en loggmottagare att plocka upp loggar med hjälp av en Azure Event Hub. Händelsehubbar möjliggör integrering med Splunk- eller andra SIEM-system. Mer information om det här alternativet finns i [Strömma diagnostikloggar till en händelsehubb](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Skicka till Logganalys:** Skriver loggar till Azure Monitor-tjänsten. Mer information om det här alternativet finns [i Samla in loggar för användning i Azure Monitor-loggar](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Både de inkrementella & rapporter efter attacken innehåller följande fält
- Attack vektorer
- Trafikstatistik
- Orsak till avbrutna paket
- Berörda protokoll
- De 10 främsta källländerna eller källregionerna
- Topp 10 källa ASN

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurera DDoS-attackreducerande flödesloggar
Attack Mitigation Flow Logs kan du granska den tappade trafiken, vidarebefordrad trafik och andra intressanta datapunkter under en aktiv DDoS-attack i nära realtid. Du kan inta den konstanta strömmen av dessa data till dina SIEM-system via händelsehubben för övervakning i nära realtid, vidta potentiella åtgärder och åtgärda behovet av dina försvarsåtgärder. 

1. Välj **Alla tjänster** högst upp till vänster om portalen.
2. Ange *bildskärm* i rutan **Filter.** När **Monitor** visas i resultatet markerar du den.
3. Under **INSTÄLLNINGAR**väljer du **Diagnostikinställningar**.
4. Välj den **prenumerations-** och **resursgrupp** som innehåller den offentliga IP-adress som du vill logga.
5. Välj **Offentlig IP-adress** för **resurstyp**och välj sedan den specifika offentliga IP-adress som du vill logga mått för.
6. Välj **Aktivera diagnostik för att samla in Loggen DDoSMitigationFlowLogs** och välj sedan så många av följande alternativ som du behöver:

    - **Arkiv till ett lagringskonto**: Data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns i [Arkivera diagnostikloggar](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Strömma till en händelsehubb**: Tillåter en loggmottagare att plocka upp loggar med hjälp av en Azure Event Hub. Händelsehubbar möjliggör integrering med Splunk- eller andra SIEM-system. Mer information om det här alternativet finns i [Strömma diagnostikloggar till en händelsehubb](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Skicka till Logganalys:** Skriver loggar till Azure Monitor-tjänsten. Mer information om det här alternativet finns [i Samla in loggar för användning i Azure Monitor-loggar](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Om du vill visa flödesloggar data i Azure Analytics instrumentpanelen kan du importera exempelinstrumentpanelen frånhttps://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Flödesloggar har följande fält: 
- Käll-IP-adress
- Mål-IP-adress
- Källport 
- Målport 
- Protokolltyp 
- Åtgärder som vidtas under begränsning



## <a name="validate-ddos-detection"></a>Verifiera DDoS-identifiering

Microsoft samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) för att skapa ett gränssnitt där du kan generera trafik mot DDoS Protection-aktiverade offentliga IP-adresser för simuleringar. Med BreakPoint Cloud-simuleringen kan du:

- Verifiera hur Microsoft Azure DDoS Protection skyddar dina Azure-resurser från DDoS-attacker
- Optimera din incidentsvarsprocess under DDoS-attack
- Dokument DDoS-efterlevnad
- Träna dina nätverkssäkerhetsteam

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Visa DDoS-skyddsaviseringar i Azure Security Center

Azure Security Center innehåller en lista över [säkerhetsaviseringar](/azure/security-center/security-center-managing-and-responding-alerts), med information som hjälper till att undersöka och åtgärda problem. Med den här funktionen får du en enhetlig vy av aviseringar, inklusive DDoS-attackrelaterade aviseringar och de åtgärder som vidtas för att minska attacken i nära tid.
Det finns två specifika aviseringar som du kommer att se för alla DDoS-angreppsidentifiering och begränsning:

- **DDoS-attack som identifierats för offentlig IP:** Den här aviseringen genereras när DDoS-skyddstjänsten upptäcker att en av dina offentliga IP-adresser är målet för en DDoS-attack.
- **DDoS-attack mildras för offentlig IP:** Den här aviseringen genereras när en attack på den offentliga IP-adressen har mildrats.
Öppna **Security Center** i Azure-portalen om du vill visa aviseringarna. Under **Skydd mot hot**väljer du **Säkerhetsvarningar**. Följande skärmbild visar ett exempel på DDoS-attackvarningar.

![DDoS-avisering i Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Aviseringarna innehåller allmän information om den offentliga IP-adressen som är under attack, geo- och hotinformation och åtgärder för reparation.

## <a name="permissions"></a>Behörigheter

Om du vill arbeta med DDoS-skyddsplaner måste ditt konto tilldelas [rollen nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) eller till en [anpassad](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas lämpliga åtgärder i följande tabell:

| Åtgärd                                            | Namn                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Läs en DDoS-skyddsplan              |
| Microsoft.Network/ddosProtectionPlans/write       | Skapa eller uppdatera en DDoS-skyddsplan  |
| Microsoft.Network/ddosProtectionPlans/delete      | Ta bort en DDoS-skyddsplan            |
| Microsoft.Network/ddosProtectionPlans/join/action | Gå med i en DDoS-skyddsplan              |

Om du vill aktivera DDoS-skydd för ett virtuellt nätverk måste ditt konto också tilldelas lämpliga [åtgärder för virtuella nätverk](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Nästa steg

- Skapa och tillämpa [Azure-principen](policy-samples.md) för virtuella nätverk