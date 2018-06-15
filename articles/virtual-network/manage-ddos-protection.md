---
title: Hantera Azure DDoS-skydd Standard med hjälp av Azure portal | Microsoft Docs
description: Lär dig hur du använder Azure DDoS-skydd Standard telemetri i Azure-Monitor minimera angreppet.
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
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: dd094f2b9cdb9b5eb164dda2925d094cafa7cd89
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895620"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Hantera Azure DDoS-skydd Standard med hjälp av Azure portal

Lär dig mer om att aktivera och inaktivera för tjänsten (DDoS)-skydd och använda telemetri för att minska en DDoS-attack med Azure DDoS-skydd Standard. DDoS-skydd Standard skyddar Azure-resurser som virtuella datorer, belastningsutjämnare och programgatewayer som har en Azure [offentliga IP-adressen](virtual-network-public-ip-address.md) kopplade till den. Läs mer om DDoS-skydd Standard och dess funktioner i [DDoS-skydd Standard översikt](ddos-protection-overview.md).

Innan du slutfört alla steg i den här självstudiekursen, logga in på Azure-portalen på https://portal.azure.com med ett konto som har tilldelats den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som har tilldelats en lämplig åtgärder som anges i [behörigheter](#permissions).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-ddos-protection-plan"></a>Skapa en plan för DDoS-skydd

En DDoS skyddsplan definierar en uppsättning virtuella nätverk som har DDoS-skydd standard aktiverats över prenumerationer. Du kan konfigurera en DDoS-skyddsplan för din organisation och länken virtuella nätverk från flera prenumerationer till samma plan. DDoS-skyddsplan själva är även associerat med en prenumeration som du väljer under genereringen av planen. Prenumerationen planen är kopplad till ådrar sig återkommande månadsfaktura för plan samt överförbrukning avgifter om skyddade offentliga IP-adresser mer än 100. Mer information om priser DDoS finns [prisinformationen](https://azure.microsoft.com/pricing/details/ddos-protection/).

Skapa mer än en plan krävs inte för de flesta organisationer. En plan kan inte flyttas mellan prenumerationer. Om du vill ändra prenumerationen har en plan måste du [ta bort den befintliga planen](#work-with-ddos-protection-plans) och skapa en ny.

1. Välj **skapar du en resurs** i det övre vänstra hörnet på Azure-portalen.
2. Sök efter *DDoS*. När **DDos skyddsplan** visas i sökresultaten väljer den.
3. Välj **Skapa**.
4. Ange eller välj egna värden eller ange, eller väljer du följande exempelvärden och välj sedan **skapa**:

    |Inställning        |Värde                                              |
    |---------      |---------                                          |
    |Namn           | myDdosProtectionPlan                              |
    |Prenumeration   | Välj din prenumeration.                         |
    |Resursgrupp | Välj **Skapa nytt** och ange *myResourceGroup* |
    |Plats       | Östra USA                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>Aktivera DDoS för ett nytt virtuellt nätverk

1. Välj **skapar du en resurs** i det övre vänstra hörnet på Azure-portalen.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Ange eller välj egna värden för RETUR eller väljer du följande exempelvärden, återstående standardvärdena och välj sedan **skapa**:

    | Inställning         | Värde                                                        |
    | ---------       | ---------                                                    |
    | Namn            | myVirtualNetwork                                             |
    | Prenumeration    | Välj din prenumeration.                                    |
    | Resursgrupp  | Välj **använda befintliga**, och välj sedan **myResourceGroup** |
    | Plats        | Östra USA                                                      |
    | DDos-skydd | Välj **Standard** och sedan under **DDoS-skydd**väljer **myDdosProtectionPlan**. Den plan som du väljer kan vara i samma eller olika prenumerationen än det virtuella nätverket, men båda prenumerationer måste vara kopplad till samma Azure Active Directory-klienten.|

Du kan inte flytta ett virtuellt nätverk till en annan resursgrupp eller prenumeration när DDoS Standard är aktiverat för det virtuella nätverket. Om du behöver flytta ett virtuellt nätverk med DDoS Standard aktiverad, inaktivera DDoS Standard först, flyttar det virtuella nätverket och sedan aktivera DDoS-standard. Efter överflyttningen återställs automatiskt justerade princip tröskelvärden för alla skyddade offentliga IP-adresser i det virtuella nätverket.

## <a name="enable-ddos-for-an-existing-virtual-network"></a>Aktivera DDoS för ett befintligt virtuellt nätverk

1. Skapa en plan för DDoS-skydd genom att slutföra stegen i [skapar en skyddsplan DDoS](#create-a-ddos-protection-plan), om du inte har en befintlig plan för DDoS-skydd.
2. Välj **skapar du en resurs** i det övre vänstra hörnet på Azure-portalen.
3. Ange namnet på det virtuella nätverk som du vill aktivera DDoS-skydd Standard för i den **söka efter resurser, tjänster och dokumenten rutan** överst i portalen. När namnet på det virtuella nätverket visas i sökresultaten väljer du den.
4. Välj **DDoS-skydd**under **inställningar**.
5. Välj **Standard**. Under **DDoS skyddsplan**, Välj en befintlig plan för DDoS-skydd eller det schema som du skapade i steg 1 och välj sedan **spara**. Den plan som du väljer kan vara i samma eller olika prenumerationen än det virtuella nätverket, men båda prenumerationer måste vara kopplad till samma Azure Active Directory-klienten.

## <a name="disable-ddos-for-a-virtual-network"></a>Inaktivera DDoS för ett virtuellt nätverk

1. Ange namnet på det virtuella nätverket som du vill inaktivera DDoS-skydd standard för i den **söka efter resurser, tjänster och dokumenten rutan** överst i portalen. När namnet på det virtuella nätverket visas i sökresultaten väljer du den.
2. Välj **DDoS-skydd**under **inställningar**.
3. Välj **grundläggande** under **DDoS skyddsplan** och välj sedan **spara**.

## <a name="work-with-ddos-protection-plans"></a>Arbeta med planer för DDoS-skydd

1. Välj **alla tjänster** högst upp till vänster i portalen.
2. Ange *DDoS* i den **Filter** rutan. När **planer för DDoS-skydd** visas i resultaten, markerar du den.
3. Välj skyddsplan som du vill visa i listan.
4. Alla virtuella nätverk som är associerade med planen visas.
5. Om du vill ta bort ett schema, måste du först koppla bort alla virtuella nätverk från den. Om du vill koppla bort en plan från ett virtuellt nätverk, se [inaktivera DDoS för ett virtuellt nätverk](#disable-ddos-for-a-virtual-network).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurera aviseringar för mått för DDoS-skydd

Du kan välja någon av de tillgängliga DDoS-skydd mått för att meddela dig när det finns en aktiv migrering under en attack med hjälp av Azure-Monitor varningskonfigurationen. När villkoren är uppfyllda, får den angivna adressen en avisering e-postmeddelande:

1. Välj **alla tjänster** högst upp till vänster i portalen.
2. Ange *övervakaren* i den **Filter** rutan. När **övervakaren** visas i sökresultaten väljer den.
3. Välj **mått** under **delade tjänster**.
4. Anger, eller välj egna värden eller ange följande exempelvärden återstående standardvärdena och välj sedan **OK**:

    |Inställning                  |Värde                                                                                               |
    |---------                |---------                                                                                           |
    |Namn                     | myDdosAlert                                                                                        |
    |Prenumeration             | Välj den prenumeration som innehåller den offentliga IP-adressen som du vill få aviseringar för.        |
    |Resursgrupp           | Välj den resursgrupp som innehåller den offentliga IP-adressen som du vill få aviseringar för.      |
    |Resurs                 | Välj den offentliga IP-adressen som innehåller den offentliga IP-adressen som du vill få aviseringar för. DDoS övervakar den offentliga IP-adresser tilldelas resurser inom ett virtuellt nätverk. Om du inte har några resurser med offentliga IP-adresser i det virtuella nätverket, måste du först skapa en resurs med en offentlig IP-adress. Du kan övervaka offentliga IP-adressen för alla resurser som har distribuerats via Resource Manager (inte klassiskt) i [för Azure-tjänster för virtuella nätverk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), förutom Azure App Service-miljöer och Azure VPN-Gateway. Om du vill fortsätta med den här kursen kan du snabbt skapa en [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuella datorn.                   |
    |Mått                   | Under DDoS attacker eller inte                                                                            |
    |Tröskelvärde                | 1 - **1** innebär du angripet. **0** innebär att du inte är angripet.                         |
    |Period                   | Välj det värde som du väljer.                                                                   |
    |Meddela via e-post         | Markera kryssrutan                                                                                  |
    |Ytterligare administratör | Ange din e-postadress om du inte är en e-ägare, deltagare eller läsare för prenumerationen. |

    Inom några minuter för angreppsidentifiering får du ett e-postmeddelande från Azure-Monitor mått som liknar bilden nedan:

    ![Attack avisering](./media/manage-ddos-protection/ddos-alert.png)


För att simulera en DDoS-attack för att validera aviseringen finns [Validera DDoS identifiering](#validate-ddos-detection).

Du kan också lära dig mer om [hur du konfigurerar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [logikappar](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa aviseringar.

## <a name="configure-logging-for-ddos-protection-metrics"></a>Konfigurera loggning för mått för DDoS-skydd

1. Välj **alla tjänster** högst upp till vänster i portalen.
2. Ange *övervakaren* i den **Filter** rutan. När **övervakaren** visas i sökresultaten väljer den.
3. Under **inställningar**väljer **diagnostikinställningar**.
4. Välj den **prenumeration** och **resursgruppen** som innehåller den offentliga IP-adressen som du vill logga.
5. Välj **offentliga IP-adressen** för **resurstypen**, markera specifika offentliga IP-adressen du vill logga mätvärden för.
6. Välj **aktiverar diagnostik för att samla in följande data** och välj sedan som många av de följande alternativen som du behöver:

    - **Arkivet till ett lagringskonto**: Data skrivs till ett Azure Storage-konto. Mer information om det här alternativet finns [Arkivera diagnostikloggar](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Dataströmmen till en händelsehubb**: gör en logg mottagare att hämta loggar med en Azure-Händelsehubb. Händelsehubbar aktivera integrering med Splunk eller andra SIEM-system. Mer information om det här alternativet finns [strömma diagnostiska loggar till en händelsehubb](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - **Skicka till logganalys**: skriver loggar till Azure OMS Log Analytics-tjänsten. Mer information om det här alternativet finns [samla in loggar för användning i logganalys](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

För att simulera en DDoS-attack för att validera loggning finns [Validera DDoS identifiering](#validate-ddos-detection).

## <a name="use-ddos-protection-telemetry"></a>Använd telemetri för DDoS-skydd

Telemetri för en attack tillhandahålls via Azure-Monitor i realtid. Telemetrin är tillgänglig enbart för den varaktighet som en offentlig IP-adress är under lösning. Du ser inte telemetri före eller efter en attack minskas.

1. Välj **alla tjänster** högst upp till vänster i portalen.
2. Ange *övervakaren* i den **Filter** rutan. När **övervakaren** visas i sökresultaten väljer den.
3. Välj **mått**under **delade tjänster**.
4. Välj den **prenumeration** och **resursgruppen** som innehåller den offentliga IP-adressen som du vill telemetri för.
5. Välj **offentliga IP-adressen** för **resurstypen**, välj sedan önskad specifika offentliga IP-adress telemetri för.
6. En serie **tillgängliga mått** visas till vänster på skärmen. De här måtten, när du väljer är visas i diagram registreringen i den **Azure övervakaren mått diagram** på översiktsskärmen.

Mått namnen finns olika typer av paket och byte kontra paket, med en grundläggande konstruktion av taggnamn på varje mått på följande sätt:

- **Ignorerade taggnamnet** (till exempel **inkommande paket bort DDoS**): antalet paket som tagits bort/befordras av systemet för DDoS-skydd.
- **Vidarebefordrade taggnamnet** (till exempel **inkommande paket som vidarebefordras DDoS**): antalet paket som vidarebefordras av DDoS-systemet till målet VIP-trafik som inte var filtrerade.
- **Inga taggnamnet** (till exempel **inkommande paket DDoS**): det totala antalet paket som hör till bakgrundsrensning systemet – som representerar summan av paket som ignoreras och vidarebefordras.

För att simulera en DDoS-attack för att validera telemetri finns [Validera DDoS identifiering](#validate-ddos-detection).

## <a name="view-ddos-mitigation-policies"></a>Visa DDoS minskning principer

DDoS-skydd Standard gäller tre automatiskt justerade minskning principer (TCP SYN, TCP och UDP) för varje offentlig IP-adressen för den skyddade resursen i det virtuella nätverket som har DDoS aktiverat. Du kan visa tröskelvärdena som principen genom att välja den **inkommande TCP-paket för att utlösa DDoS minskning** och **inkommande UDP-paket för att utlösa DDoS minskning** mått, enligt följande bild:

![Visa minskning principer](./media/manage-ddos-protection/view-mitigation-policies.png)

Tröskelvärden för principen är automatiskt konfigurerade via Azure machine learning-baserat nätverk trafik profilering. Endast när principen tröskelvärdet överskrids uppstår DDoS minskning för IP-adress angripet.

## <a name="validate-ddos-detection"></a>Validera DDoS-identifiering

Microsoft samarbetar med [BreakingPoint moln](https://www.ixiacom.com/products/breakingpoint-cloud) att skapa ett gränssnitt där du kan skapa trafik mot DDoS-skydd-aktiverade offentliga IP-adresser för simulering. Simuleringen brytpunkt molnet kan du:

- Kontrollera hur Microsoft Azure DDoS-skydd skyddar dina Azure-resurser från DDoS-attacker
- Optimera incidenter processen under tiden för DDoS-attack
- Dokumentet DDoS-kompatibilitet
- Träna din network security team

## <a name="permissions"></a>Behörigheter

Om du vill arbeta med planer för DDoS-skydd, ditt konto måste ha tilldelats den [network-deltagare](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roll eller en [anpassade](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roll som tilldelas åtgärderna som anges i följande tabell:

| Åtgärd                                            | Namn                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Läs en plan för DDoS-skydd              |
| Microsoft.Network/ddosProtectionPlans/write       | Skapa eller uppdatera en plan för DDoS-skydd  |
| Microsoft.Network/ddosProtectionPlans/delete      | Ta bort en plan för DDoS-skydd            |
| Microsoft.Network/ddosProtectionPlans/join/action | Ansluta till en plan för DDoS-skydd              |

Om du vill aktivera DDoS-skydd för ett virtuellt nätverk, ditt konto måste också tilldelas lämpliga [åtgärder för virtuella nätverk](manage-virtual-network.md#permissions).

## <a name="next-steps"></a>Nästa steg

- Skapa och använda [Azure princip](policy-samples.md) för virtuella nätverk