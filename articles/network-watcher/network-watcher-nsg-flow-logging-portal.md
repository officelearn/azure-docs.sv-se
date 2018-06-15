---
title: Logga nätverkstrafik till och från en virtuell dator – Azure Portal | Microsoft Docs
description: Lär dig hur du loggar nätverkstrafik till och från en virtuell dator med funktionen NSG-flödesloggar i Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: f010bebcf1130b3061c60987ffbd4e706a030773
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776558"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Självstudier: Logga nätverkstrafik till och från en virtuell dator med hjälp av Azure Portal

Med en nätverkssäkerhetsgrupp (NSG) kan du filtrera inkommande trafik till och utgående trafik från en virtuell dator (VM). Du kan logga nätverkstrafiken som skickas via en NSG med funktionen NSG-flödesloggar i Network Watcher. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en virtuell dator med en nätverkssäkerhetsgrupp
> * Aktivera Network Watcher och registrera providern Microsoft.Insights
> * Aktivera en trafikflödeslogg för en nätverkssäkerhetsgrupp med hjälp av funktionen NSG-flödesloggar i Network Watcher
> * Ladda ned loggdata
> * Visa loggdata

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute** och välj sedan **Windows Server 2016 Datacenter** eller **Ubuntu Server 17.10 VM**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVm|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Skapa ny** och ange **myResourceGroup**.|
    |Plats| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Acceptera standardinställningarna under **Inställningar** och välj **OK**.
6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen. Det tar några minuter att distribuera den virtuella datorn. Vänta tills distributionen av den virtuella datorn är klar innan du fortsätter med nästa steg.

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med stegen förrän den virtuella datorn har skapats. När den virtuella datorn skapas på portalen skapas även en nätverkssäkerhetsgrupp med namnet **myVm-nsg**, som kopplas till nätverksgränssnittet för den virtuella datorn.

## <a name="enable-network-watcher"></a>Aktivera Network Watcher

Om du redan har aktiverat en nätverksbevakare i östra USA går du vidare till [Registrera Insights-providern](#register-insights-provider).

1. Välj **Alla tjänster** på portalen. Skriv *Network Watcher*i **filterrutan**. Välj **Network Watcher** i sökresultatet.
2. Välj **Regioner** för att expandera avsnittet och välj sedan **...** till höger om **USA, östra**, som du ser i följande bild:

    ![Aktivera Network Watcher](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Välj **Aktivera nätverksbevakaren**.

## <a name="register-insights-provider"></a>Registrera Insights-providern

Providern **Microsoft.Insights** krävs för NSG-flödesloggning. Registrera providern genom att utföra följande steg:

1. Välj **Alla tjänster** längst upp till vänster på portalen. Skriv *Prenumerationer* i filterrutan. När **Prenumerationer** visas i sökresultatet, väljer du posten.
2. Välj den prenumeration som du vill aktivera providern för i listan över prenumerationer.
3. Välj **Resursprovidrar** under **INSTÄLLNINGAR**.
4. Kontrollera att **STATUS** för providern **microsoft.insights** är **Registrerad**, som du ser i bilden nedan. Om statusen är **Oregistrerad** väljer du **Registrera** till höger om providern.

    ![Registrera providern](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>Aktivera NSG-flödesloggar

1. Loggdata från NSG-flödet skrivs till ett Azure Storage-konto. Skapa ett Azure Storage-konto genom att välja **+ Skapa en resurs** längst upp till vänster på portalen.
2. Välj **Lagring** och sedan **Koppla undernät – blob, fil, tabell, kö**.
3. Ange eller välj följande information, acceptera standardvärdena för resten av inställningarna och välj sedan **Skapa**.

    | Inställning        | Värde                                                        |
    | ---            | ---   |
    | Namn           | 3 till 24 tecken långt, får endast innehålla gemener och siffror och måste vara unikt bland alla Azure Storage-konton.                                                               |
    | Plats       | Välj **USA, östra**                                           |
    | Resursgrupp | Välj **Använd befintlig** och sedan **myResourceGroup**. |

    Det tar ungefär en minut att skapa lagringskontot. Fortsätt inte med de återstående stegen förrän lagringskontot har skapats. Om du använder ett befintligt lagringskonto i stället för att skapa ett nytt väljer du ett lagringskonto där **Alla nätverk** (standard) har valts för **Brandväggar och virtuella nätverk** under **Inställningar** för lagringskontot.
4. Välj **Alla tjänster** längst upp till vänster på portalen. Skriv *Network Watcher* i **filterrutan**. När **Network Watcher** visas i sökresultatet väljer du posten.
5. Välj **NSG-flödesloggar** under **LOGGAR**, som du ser i följande bild:

    ![NSG:er](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. Välj nätverkssäkerhetsgruppen **myVm-nsg** i listan med nätverkssäkerhetsgrupper.
7. Välj **På** under **Flödesloggsinställningar**.
8. Välj lagringskontot som du skapade i steg 3.
9. Ange **Bevarande (dagar)** till 5 och välj sedan **Spara**.

## <a name="download-flow-log"></a>Ladda ned flödeslogg

1. Välj **NSG-flödesloggar** under **LOGGAR** från Network Watcher på portalen.
2. Välj **You can download flow logs from configured storage accounts** (Du kan ladda ned flödesloggar från konfigurerade lagringskonton), som du ser i följande bild:

  ![Ladda ned flödesloggar](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Välj lagringskontot som du konfigurerade i steg 2 i [Aktivera NSG-flödesloggar](#enable-nsg-flow-log).
4. Välj **Behållare** under **BLOB SERVICE** och välj sedan behållaren **insights-logs-networksecuritygroupflowevent**, som du ser i följande bilden:

    ![Välj behållare](./media/network-watcher-nsg-flow-logging-portal/select-container.png)
5. Navigera i mapphierarkin tills du kommer till en PT1H.json-fil, som du ser i följande bild:

    ![Loggfil](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

    Loggfiler skrivs till en mapphierarki som använder följande namnkonvention:  https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

6. Välj **...** till höger om PT1H.json-filen och välj **Ladda ned**.

## <a name="view-flow-log"></a>Visa flödeslogg

Följande JSON-kod är ett exempel på vad du ser i PT1H.json-filen för varje flöde som data loggas för:

```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [{
            "rule": "UserRule_default-allow-rdp",
            "flows": [{
                "mac": "000D3A170C69",
                "flowTuples": ["1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"]
            }]
        }]
    }
}
```

Värdet för **mac** i föregående utdata är nätverksgränssnittets MAC-adress som skapades när den virtuella datorn skapades. Den kommateckenavgränsade informationen för **flowTuples** är som följer:

| Exempeldata | Vad data representerar   | Förklaring                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1525186745   | Tidsstämpel             | Tidsstämpeln för när flödet uppstod, i UNIX EPOK-format. I föregående exempel konverterades datumet till 1 maj 2018 kl. 14:59:05 GMT.                                                                                    |
| 192.168.1.4  | Källans IP-adress      | Käll-IP-adressen som flödet kom från.
| 10.0.0.4     | Mål-IP-adress | Mål-IP-adressen som flödet skickades till. 10.0.0.4 är den privata IP-adressen för den virtuella datorn som du skapade i [Skapa en virtuell dator](#create-a-vm).                                                                                 |
| 55960        | Källport            | Källporten som flödet kom från.                                           |
| 3389         | Målport       | Målporten som flödet skickades till. Eftersom trafiken skulle till port 3389 bearbetades flödet av regeln med namnet **UserRule_default-allow-rdp** i loggfilen.                                                |
| T            | Protokoll               | Anger om protokollet för flödet var TCP (T) eller UDP (U).                                  |
| I            | Riktning              | Anger om trafiken var inkommande (I) eller utgående (O).                                     |
| A            | Åtgärd                 | Anger om trafiken tilläts (A) eller nekades (D).                                           |

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig hur du aktiverar NSG-flödesloggning för en nätverkssäkerhetsgrupp. Du lärde dig också hur du laddar ned och visar data som loggats i en fil. Rådata i JSON-filen kan vara svåra att tolka. Du kan visualisera data med hjälp av [trafikanalysverktyg](traffic-analytics.md) i Network Watcher, Microsoft [PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) och andra verktyg.