---
title: Visa och konfigurera telemetri för DDoS-skydd
description: Lär dig hur du visar och konfigurerar DDoS Protection-telemetri.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 834339a20e369b3835faf05d069f8d4f77385e18
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744867"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>Visa och konfigurera telemetri för DDoS-skydd

Azure DDoS Protection standard ger detaljerad information om angrepp och visualisering med DDoS-attack analys. Kunder som skyddar sina virtuella nätverk mot DDoS-attacker har detaljerad insyn i attack trafik och åtgärder som vidtas för att minska risken för angrepp med hjälp av angrepps minsknings rapporter & skydds flödes loggar. Avancerad telemetri exponeras via Azure Monitor inklusive detaljerade mått under ett DDoS-angrepp. Aviseringar kan konfigureras för alla Azure Monitor mått som visas av DDoS Protection. Loggning kan integreras ytterligare med [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics och Azure Storage för avancerad analys via Azure Monitor Diagnostics-gränssnittet.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Konfigurera aviseringar för DDoS skydds mått
> * Använda DDoS Protection-telemetri
> * Visa DDoS-principer för minskning
> * Visa DDoS-skydds varningar i Azure Security Center

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Innan du kan slutföra stegen i den här självstudien måste du först skapa en [Azure DDoS standard-skydds plan](manage-ddos-protection.md).

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Konfigurera aviseringar för DDoS skydds mått

Du kan välja något av de tillgängliga DDoS-skydds måtten för att varna dig när det finns en aktiv minskning under ett angrepp med hjälp av Azure Monitor varnings konfiguration. När villkoren är uppfyllda får den angivna adressen ett e-postmeddelande om avisering:

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *Monitor* i **filter** rutan. När **övervakaren** visas i resultaten väljer du den.
3. Välj **mått** under **delade tjänster**.
4. Ange eller Välj dina egna värden eller ange följande exempel värden, Godkänn återstående standardvärden och välj sedan **OK**:

    |Inställning                  |Värde                                                                                               |
    |---------                |---------                                                                                           |
    |Namn                     | Ange _MyDdosAlert_.                                                                                |
    |Prenumeration             | Välj den prenumeration som innehåller den offentliga IP-adress som du vill ta emot aviseringar för.        |
    |Resursgrupp           | Välj den resurs grupp som innehåller den offentliga IP-adress som du vill ta emot aviseringar för.      |
    |Resurs                 | Välj den offentliga IP-adress som innehåller den offentliga IP-adress som du vill ta emot aviseringar för. DDoS övervakar offentliga IP-adresser tilldelade till resurser i ett virtuellt nätverk. Om du inte har några resurser med offentliga IP-adresser i det virtuella nätverket måste du först skapa en resurs med en offentlig IP-adress. Du kan övervaka den offentliga IP-adressen för alla resurser som distribueras via Resource Manager (inte klassisk) som listas i [virtuellt nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inklusive Azure Load Balancer där de virtuella datorerna finns i det virtuella nätverket), förutom för Azure App Service miljöer och Azure VPN gateway. Om du vill fortsätta med den här självstudien kan du snabbt skapa en virtuell [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -eller [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -dator.                   |
    |Mått                   | Välj **under DDoS-attack eller inte**.                                                                |
    |Tröskelvärde                | 1- **1** innebär att du är utsatt för angrepp. **0** innebär att du inte är utsatt för angrepp.                         |
    |Period                   | Välj vilket värde du vill.                                                                   |
    |Meddela via e-post         | Markera kryss rutan.                                                                                 |
    |Ytterligare administratör | Ange din e-postadress om du inte är e-postägare, deltagare eller läsare för prenumerationen. |

    Inom ett par minuter får du ett e-postmeddelande från Azure Monitor mått som ser ut ungefär som på följande bild:

    ![Attack avisering](./media/manage-ddos-protection/ddos-alert.png)


Information om hur du simulerar en DDoS-attack för att verifiera din avisering finns i [validera DDoS-identifiering](test-through-simulations.md).

Du kan också lära dig mer om hur du [konfigurerar Webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) och [Logic Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för att skapa aviseringar.

## <a name="use-ddos-protection-telemetry"></a>Använda DDoS Protection-telemetri

Telemetri för ett angrepp tillhandahålls via Azure Monitor i real tid. Telemetri är bara tillgängligt för den varaktighet som en offentlig IP-adress är under minskning. Du ser inte telemetri före eller efter att en attack har begränsats.

1. Välj **alla tjänster** längst upp till vänster i portalen.
2. Ange *Monitor* i **filter** rutan. När **övervakaren** visas i resultaten väljer du den.
3. Välj **mått** under **delade tjänster**.
4. Välj den **prenumeration** och **resurs grupp** som innehåller den offentliga IP-adress som du vill använda telemetri för.
5. Välj **offentlig IP-adress** för **resurs typ** och välj sedan den angivna offentliga IP-adress som du vill använda telemetri för.
6. En serie med **tillgängliga mått** visas till vänster på skärmen. De här måtten, när de är markerade, visas i diagrammet **Azure Monitor mått diagram** på översikts skärmen.
7. Välj **agg regerings** typen **Max**

Mått namnen visar olika paket typer och byte jämfört med paket, med en grundläggande konstruktion av taggnamn på varje mått enligt följande:

- **Taggat taggnamn** (till exempel **inkommande paket som släppts DDoS**): antalet paket som tappas/rensas av DDoS Protection System.
- **Namn på vidarebefordrad tagg** (t. ex. **inkommande paket vidarebefordrade DDoS**): antalet paket som vidarebefordras av DDoS-systemet till den virtuella mål-VIP – trafik som inte har filtrerats.
- **Inget taggnamn** (till exempel **inkommande paket DDoS**): det totala antalet paket som ingår i skrubbnings systemet – som representerar summan av de paket som har släppts och vidarebefordrats.

Den här [Azure Monitor varnings regeln](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) kör en enkel fråga för att identifiera när en aktiv DDoS-minskning inträffar. Information om hur du simulerar en DDoS-attack för att validera telemetri finns i [validera DDoS-identifiering](test-through-simulations.md). 

## <a name="view-ddos-mitigation-policies"></a>Visa DDoS-principer för minskning

DDoS Protection standard tillämpar tre automatiskt justerade skydds principer (TCP-SYN, TCP & UDP) för varje offentlig IP-adress för den skyddade resursen, i det virtuella nätverk där DDoS har Aktiver ATS. Du kan visa princip tröskelvärdena genom att välja de  **inkommande TCP-paketen för att utlösa DDoS-minskning** och **inkommande UDP-paket för att utlösa DDoS** -hälsomått med **agg regerings** typ som "Max", som du ser i följande bild:

![Visa begränsnings principer](./media/manage-ddos-protection/view-mitigation-policies.png)

Princip tröskelvärden konfigureras automatiskt via Azure Machine Learning-baserad nätverks trafik profilering. Endast när princip tröskeln har brutits inträffar DDoS-lösningen för IP-adressen under angrepp.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Visa DDoS-skydds varningar i Azure Security Center

Azure Security Center innehåller en lista över [säkerhets aviseringar](../security-center/security-center-managing-and-responding-alerts.md)med information som hjälper dig att undersöka och åtgärda problem. Med den här funktionen får du en enhetlig vy över aviseringar, inklusive DDoS-relaterade aviseringar och åtgärder som vidtagits för att minimera angreppet i nära tiden.
Det finns två olika aviseringar som du kan se för identifiering och minskning av DDoS-attacker:

- **DDoS-attack identifierad för offentlig IP**: den här aviseringen skapas när DDoS-skydds tjänsten identifierar att en av dina offentliga IP-adresser är målet för en DDoS-attack.
- **DDoS-attack minimerad för offentlig IP**: den här aviseringen genereras när en attack på den offentliga IP-adressen har minimerats.
Om du vill visa aviseringarna öppnar du **Security Center** i Azure Portal. Under **skydd av hot** väljer du **säkerhets aviseringar**. Följande skärm bild visar ett exempel på DDoS-attackens aviseringar.

![DDoS-avisering i Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Aviseringarna inkluderar allmän information om den offentliga IP-adressen som är under attack, geo-och hot information och åtgärder.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

- Konfigurera aviseringar för DDoS skydds mått
- Använda DDoS Protection-telemetri
- Visa DDoS-principer för minskning
- Visa DDoS-skydds varningar i Azure Security Center

Fortsätt till nästa självstudie om du vill lära dig hur du konfigurerar rapporter om attack minskning och flödes loggar.

> [!div class="nextstepaction"]
> [Konfigurera riskreduceringsrapporter och flödesloggar för DDOS-attacker](reports-and-flow-logs.md)