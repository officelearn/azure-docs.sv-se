---
title: Hantera Azure DDoS Protection Standard med hjälp av Azure portal | Microsoft Docs
description: Lär dig hur du använder Azure DDoS Protection standardmoduler i Azure Monitor för att åtgärda ett angrepp.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: jdial
ms.openlocfilehash: 66b2930e06cef1a31602df3d358c78f42c8cd2cf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406372"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Hantera Azure DDoS Protection Standard med hjälp av Azure portal

Lär dig hur du aktiverar och inaktiverar datorn protection service (DDoS) och Använd telemetri för att minska en DDoS-attack med Azure DDoS Protection Standard. DDoS Protection Standard skyddar Azure-resurser som virtuella datorer, belastningsutjämnare och programgatewayer som har en Azure [offentliga IP-adressen](virtual-network-public-ip-address.md) tilldelade till den. Läs mer om DDoS Protection Standard och dess funktioner i [DDoS Protection Standard översikt](ddos-protection-overview.md).

Innan du har slutfört alla steg i den här självstudien, logga in på Azure Portal på https://portal.azure.com med ett konto som har tilldelats den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som är tilldelade den aktuella åtgärder som anges i [behörigheter](#permissions).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-ddos-protection-plan"></a>Skapa en DDoS-skyddsplan

En DDoS-skyddsplan definierar en uppsättning virtuella nätverk där DDoS protection standard aktiverat för prenumerationer. Du kan konfigurera en DDoS-skyddsplanen för din organisation och länka virtuella nätverk från flera prenumerationer till samma plan. DDoS-skyddsplanen själva är även associerat med en prenumeration som du väljer under genereringen av planen. Prenumerationen planen är kopplad till medför månatliga återkommande fakturan för plan, samt avgifter för överförbrukning, om antalet skyddade offentliga IP-adresser överskrider 100. Mer information om DDoS priser finns i [prisinformation](https://azure.microsoft.com/pricing/details/ddos-protection/).

Skapandet av mer än en plan krävs inte för de flesta organisationer. En plan kan inte flyttas mellan prenumerationer. Om du vill ändra en plan som är i prenumerationen kan du behöva [ta bort befintliga planen](#work-with-ddos-protection-plans) och skapa en ny.

1. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
2. Sök efter *DDoS*. När **DDos-skyddsplanen** visas i sökresultatet väljer du det.
3. Välj **Skapa**.
4. Ange eller välj dina egna värden, eller ange, eller Välj följande exempelvärden och välj sedan **skapa**:

    |Inställning        |Värde                                              |
    |---------      |---------                                          |
    |Namn           | myDdosProtectionPlan                              |
    |Prenumeration   | Välj din prenumeration.                         |
    |Resursgrupp | Välj **Skapa nytt** och ange *myResourceGroup* |
    |Plats       | Östra USA                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Aktivera DDoS för ett nytt virtuellt nätverk

1. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Ange eller välj dina egna värden för RETUR eller Välj följande exempelvärden, acceptera standardinställningarna för återstående inställningar och därefter **skapa**:

    | Inställning         | Värde                                                        |
    | ---------       | ---------                                                    |
    | Namn            | myVirtualNetwork                                             |
    | Prenumeration    | Välj din prenumeration.                                    |
    | Resursgrupp  | Välj **Använd befintlig** och sedan **myResourceGroup**. |
    | Plats        | Östra USA                                                      |
    | DDos-skydd | Välj **Standard** och sedan under **DDoS protection**väljer **myDdosProtectionPlan**. Den plan som du väljer kan finnas i samma, eller en annan prenumeration än det virtuella nätverket, men båda prenumerationerna måste vara kopplade till samma Azure Active Directory-klient.|

Du kan inte flytta ett virtuellt nätverk till en annan resursgrupp eller prenumeration när DDoS-standarden aktiveras för det virtuella nätverket. Om du behöver flytta ett virtuellt nätverk med DDoS Standard aktiverad, inaktivera DDoS Standard först, flytta det virtuella nätverket och sedan aktivera DDoS-standard. Efter överflyttningen återställs automatiskt justerade princip tröskelvärdena för alla skyddade offentliga IP-adresser i det virtuella nätverket.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Aktivera DDoS för ett befintligt virtuellt nätverk

1. Skapa en DDoS-skyddsplan genom att följa stegen i [skapa en DDoS-skyddsplan](#create-a-ddos-protection-plan), om du inte har en befintlig DDoS-skyddsplan.
2. Välj **skapa en resurs** i det övre vänstra hörnet i Azure Portal.
3. Ange namnet på det virtuella nätverk som du vill aktivera DDoS Protection Standard för i den **Sök efter resurser, tjänster och docs box** överst i portalen. När namnet på det virtuella nätverket visas i sökresultatet väljer du den.
4. Välj **DDoS protection**under **inställningar**.
5. Välj **Standard**. Under **DDoS-skyddsplanen**, Välj en befintlig DDoS-skyddsplanen eller den plan som du skapade i steg 1 och därefter **spara**. Den plan som du väljer kan finnas i samma, eller en annan prenumeration än det virtuella nätverket, men båda prenumerationerna måste vara kopplade till samma Azure Active Directory-klient.

## <a name="disable-ddos-for-a-virtual-network"></a>Inaktivera DDoS för ett virtuellt nätverk

1. Ange namnet på det virtuella nätverket som du vill inaktivera DDoS protection standarden för i den **Sök efter resurser, tjänster och docs box** överst i portalen. När namnet på det virtuella nätverket visas i sökresultatet väljer du den.
2. Välj **DDoS protection**under **inställningar**.
3. Välj **grundläggande** under **DDoS-skyddsplanen** och välj sedan **spara**.

## <a name="work-with-ddos-protection-plans"></a>Arbeta med DDoS-skyddsplaner

1. Välj **alla tjänster** längst upp till vänster på portalen.
2. Ange *DDoS* i den **Filter** box. När **DDoS-skyddsplaner** visas i resultaten, markerar du den.
3. Välj skyddsplan som du vill visa i listan.
4. Alla virtuella nätverk som är associerade med planen som visas.
5. Om du vill ta bort en plan måste du först koppla bort alla virtuella nätverk från den. Om du vill koppla bort en plan från ett virtuellt nätverk, se [inaktivera DDoS för ett virtuellt nätverk](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurera aviseringar för DDoS protection-mått

Du kan välja någon av tillgängliga DDoS protection mått så att du varnas när det finns en aktiv minskning under en attack med hjälp av Azure Monitor alert konfigurationen. När villkoren uppfylls, får den angivna adressen en e-postavisering:

1. Välj **alla tjänster** längst upp till vänster på portalen.
2. Ange *övervakaren* i den **Filter** box. När **övervakaren** visas i resultaten och välj den.
3. Välj **mått** under **delade tjänster**.
4. Ange eller välj dina egna värden, eller ange följande exempelvärden, acceptera standardinställningarna för återstående inställningar och därefter **OK**:

    |Inställning                  |Värde                                                                                               |
    |---------                |---------                                                                                           |
    |Namn                     | myDdosAlert                                                                                        |
    |Prenumeration             | Välj den prenumeration som innehåller offentliga IP-adress som du vill få aviseringar för.        |
    |Resursgrupp           | Välj den resursgrupp som innehåller den offentliga IP-adress som du vill få aviseringar för.      |
    |Resurs                 | Välj offentlig IP-adress som innehåller offentliga IP-adress som du vill få aviseringar för. DDoS övervakar offentliga IP-adresser som tilldelas resurser inom ett virtuellt nätverk. Om du inte har några resurser med offentliga IP-adresser i det virtuella nätverket, måste du först skapa en resurs med en offentlig IP-adress. Du kan övervaka offentliga IP-adressen för alla resurser som har distribuerats via Resource Manager (inte klassisk) visas i [virtuellt nätverk för Azure-tjänster](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), förutom Azure App Service-miljöer och Azure VPN Gateway. Om du vill fortsätta med den här självstudiekursen, kan du snabbt skapa en [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuell dator.                   |
    |Mått                   | Under DDoS angrepp eller inte                                                                            |
    |Tröskelvärde                | 1 – **1** innebär att du är utsatt för en attack. **0** innebär att du inte är utsatt för en attack.                         |
    |Period                   | Välj det värde som du väljer.                                                                   |
    |Meddela via e-post         | Markera kryssrutan                                                                                  |
    |Ytterligare administratör | Ange din e-postadress om du inte e-ägare, deltagare eller läsare för prenumerationen. |

    Inom några minuter för identifiering av attack får du ett e-postmeddelande från Azure Monitor-mått som liknar följande bild:

    ![Attack avisering](./media/manage-ddos-protection/ddos-alert.png)


För att simulera en DDoS-attack för att verifiera aviseringen, se [Validera DDoS-identifiering](#validate-ddos-detection).

Du kan också läsa mer om [konfigurerar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [logikappar](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa aviseringar.

## <a name="use-ddos-protection-telemetry"></a>Använd DDoS protection telemetri

Telemetri för en attack tillhandahålls via Azure Monitor i realtid. Telemetri är tillgänglig endast för tiden då en offentlig IP-adress är under problemlösning. Du ser inte telemetri innan eller efter ett angrepp har minimerats.

1. Välj **alla tjänster** längst upp till vänster på portalen.
2. Ange *övervakaren* i den **Filter** box. När **övervakaren** visas i resultaten och välj den.
3. Välj **mått**under **delade tjänster**.
4. Välj den **prenumeration** och **resursgrupp** som innehåller den offentliga IP-adress som du vill att telemetri för.
5. Välj **offentliga IP-adressen** för **resurstyp**, välj sedan specifika offentliga IP-adress du vill att telemetri för.
6. En serie **tillgängliga mått** visas till vänster på skärmen. De här måtten, när du väljer är visas i diagram registreringen i den **Måttdiagram i Azure Monitor** på skärmen.

Tjänstmåttets namn finns olika typer av paket och byte och paket, med en grundläggande konstruktion av taggnamn på varje mått på följande sätt:

- **Ignorerad taggnamnet** (till exempel **inkommande paket som tas bort DDoS**): antalet paket som tas bort/gömd DDoS protection-systemet.
- **Vidarebefordrade taggnamnet** (till exempel **inkommande paket vidarebefordras DDoS**): antalet paket som vidarebefordras av DDoS-system till målet VIP – trafik som inte har filtrerats.
- **Inga taggnamnet** (till exempel **inkommande paket DDoS**): det totala antalet paket som kommer in i rensningsjobbet systemet – som representerar summan av paket som ignoreras och vidarebefordras.

För att simulera en DDoS-attack för att verifiera telemetri, se [Validera DDoS-identifiering](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Visa principer för DDoS-minskning

DDoS Protection Standard gäller tre automatiskt justerade minskning principer (TCP SYN, TCP och UDP) för varje offentliga IP-adressen för den skyddade resursen i det virtuella nätverket som har aktiverat DDoS. Du kan visa tröskelvärdena som principen genom att välja den **inkommande TCP-paket för att utlösa DDoS-minskning** och **inkommande UDP-paket för att utlösa DDoS-minskning** mått, enligt följande bild:

![Visa minskning principer](./media/manage-ddos-protection/view-mitigation-policies.png)

Tröskelvärden för principen är automatiskt konfigurerade via Azure machine learning-baserat nätverk trafik profilering. Endast när principen tröskelvärdet överskrids uppstår DDoS-minskning för IP-adressen under attack.

## <a name="configure-ddos-attack-analytics"></a>Konfigurera DDoS-attack analytics
Azure DDoS Protection standard ger detaljerad attack insikter och visualisering med DDoS-Attack Analytics. Kunder som skyddar sina virtuella nätverk mot DDoS-attacker finns detaljerad överblick attack trafik och åtgärder som vidtas för att undvika angreppet via attack minskning rapporter & minskning flödesloggar. 

## <a name="configure-ddos-attack-mitigation-reports"></a>Konfigurera rapporter för DDoS-attack minskning
Attack minskning rapporter använder Netflow protocol data som sammanställs som ger detaljerad information om attacken på resursen. När som helst en offentlig IP-resurs är utsatt för en attack, startar rapportgenereringen så fort minskningen startar. Det blir en inkrementell rapport genereras var 5 minuter och en rapport efter minskning under hela problemlösning. Detta är att säkerställa att i en händelse som DDoS-attack fortsätter under en längre period tid, kommer du att kunna se den senaste ögonblicksbilden av minskning rapporten var femte minut och en fullständig sammanfattning när nätverksattacker är över. 

1. Välj **alla tjänster** längst upp till vänster på portalen.
2. Ange *övervakaren* i den **Filter** box. När **övervakaren** visas i resultaten och välj den.
3. Under **inställningar**väljer **diagnostikinställningar**.
4. Välj den **prenumeration** och **resursgrupp** som innehåller offentliga IP-adress som du vill logga.
5. Välj **offentliga IP-adressen** för **resurstyp**, välj sedan specifika offentliga IP-adress du vill logga mått för.
6. Välj **slå på diagnostik för att samla in loggen DDoSMitigationReports** och välj sedan så många av de följande alternativen som du behöver:

    - **Arkivera till ett lagringskonto**: Data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns [Arkivera diagnostikloggar](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream till en händelsehubb**: gör en logg mottagare att hämta loggar med en Azure-Händelsehubb. Event hubs möjliggör integrering med Splunk eller andra SIEM-system. Mer information om det här alternativet finns [Stream diagnostikloggar till en händelsehubb](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Skicka till Log Analytics**: skriver loggar till Azure Log Analytics-tjänsten. Mer information om det här alternativet finns [samla in loggar för användning i Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Både inkrementella och efter attack minskning rapporterna innehåller följande fält
- Attackvektorer
- Trafik-statistik
- Orsak för avbrutna paket
- Protokoll omfattas
- De främsta 10 källa länder eller regioner
- Översta 10 källa ASN: er

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>Konfigurera flödesloggar för DDoS-attack minskning
Attack minskning Flow loggar kan du granska förlorad trafik vidarebefordras trafiken och andra intressanta datapoints under ett pågående DDoS-angrepp i nära realtid. Du kan mata in konstant ström av dessa data i dina SIEM system via händelsehubben för övervakning nästan i realtid, åtgärda potentiella och tillgodose behovet av defense-åtgärder. 

1. Välj **alla tjänster** längst upp till vänster på portalen.
2. Ange *övervakaren* i den **Filter** box. När **övervakaren** visas i resultaten och välj den.
3. Under **inställningar**väljer **diagnostikinställningar**.
4. Välj den **prenumeration** och **resursgrupp** som innehåller offentliga IP-adress som du vill logga.
5. Välj **offentliga IP-adressen** för **resurstyp**, välj sedan specifika offentliga IP-adress du vill logga mått för.
6. Välj **slå på diagnostik för att samla in loggen DDoSMitigationFlowLogs** och välj sedan så många av de följande alternativen som du behöver:

    - **Arkivera till ett lagringskonto**: Data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns [Arkivera diagnostikloggar](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Stream till en händelsehubb**: gör en logg mottagare att hämta loggar med en Azure-Händelsehubb. Event hubs möjliggör integrering med Splunk eller andra SIEM-system. Mer information om det här alternativet finns [Stream diagnostikloggar till en händelsehubb](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Skicka till Log Analytics**: skriver loggar till Azure Log Analytics-tjänsten. Mer information om det här alternativet finns [samla in loggar för användning i Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
1. Om du vill visa loggarna flödesdata i Azure analytics-instrumentpanelen, kan du importera exempelinstrumentpanel från https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip

Flödesloggar har följande fält: 
- Käll-IP-adress
- Mål-IP-adress
- Källport 
- Målport 
- Protokolltypen 
- Åtgärder som vidtagits under minskning



## <a name="validate-ddos-detection"></a>Verifiera DDoS-identifiering

Microsoft samarbetar med [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) att skapa ett gränssnitt där du kan generera trafik mot DDoS Protection-aktiverade offentliga IP-adresser för simulering. Simuleringen brytpunkt molnet kan du:

- Kontrollera hur Microsoft Azure DDoS Protection skyddar dina Azure-resurser mot DDoS-attacker
- Optimera din incidenthanteringsprocess medan under DDoS-attack
- Dokumentera DDoS-efterlevnad
- Träna dina network security team

## <a name="permissions"></a>Behörigheter

Om du vill arbeta med DDoS-skyddsplaner måste ditt konto måste tilldelas den [nätverksdeltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller till en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som visas i följande tabell:

| Åtgärd                                            | Namn                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Läsa en DDoS-skyddsplanen              |
| Microsoft.Network/ddosProtectionPlans/write       | Skapa eller uppdatera en DDoS-skyddsplanen  |
| Microsoft.Network/ddosProtectionPlans/delete      | Ta bort en DDoS-skyddsplanen            |
| Microsoft.Network/ddosProtectionPlans/join/action | Ansluta till en DDoS-skyddsplanen              |

Om du vill aktivera DDoS-skydd för ett virtuellt nätverk, ditt konto också tilldelas rätt [åtgärder för virtuella nätverk](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Nästa steg

- Skapa och tillämpa [Azure policy](policy-samples.md) för virtuella nätverk