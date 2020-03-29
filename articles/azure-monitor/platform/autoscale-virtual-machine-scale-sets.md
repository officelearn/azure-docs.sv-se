---
title: Avancerad automatisk skalning med virtuella Azure-datorer
description: Använder Resurshanterarens och VM-skalningsuppsättningar med flera regler och profiler som skickar url:er för e-post och anropar webhook med skalningsåtgärder.
ms.topic: conceptual
ms.date: 02/22/2016
ms.subservice: autoscale
ms.openlocfilehash: e22806ff94ce2eb830bb6918bfc7f80e5ad3ba0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364228"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Avancerad konfiguration för automatisk skalning med Resource Manager-mallar för vm-skalningsuppsättningar
Du kan skala in och skala ut i skalningsuppsättningar för virtuella datorer baserat på tröskelvärden för prestandamått, ett återkommande schema eller vid ett visst datum. Du kan också konfigurera e-post- och webhook-meddelanden för skalningsåtgärder. Den här genomgången visar ett exempel på att konfigurera alla dessa objekt med hjälp av en Resource Manager-mall i en vm-skaluppsättning.

> [!NOTE]
> Medan den här genomgången förklarar stegen för VM-skalningsuppsättningar, gäller samma information för automatisk skalning av [molntjänster](https://azure.microsoft.com/services/cloud-services/), [Apptjänst - Webbappar](https://azure.microsoft.com/services/app-service/web/)och [API-hanteringstjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) För en enkel skalningsinställning för en VM-skalningsuppsättning baserat på ett enkelt prestandamått som CPU, se [Linux-](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) och [Windows-dokument](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Genomgång
I den här genomgången använder vi [Azure Resource Explorer](https://resources.azure.com/) för att konfigurera och uppdatera inställningen för automatisk skalning för en skalningsuppsättning. Azure Resource Explorer är ett enkelt sätt att hantera Azure-resurser via Resource Manager-mallar. Om du inte har något nytt för Azure Resource Explorer-verktyget läser du [den här introduktionen](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Distribuera en ny skalningsuppsättning med en grundläggande inställning för automatisk skalning. Den här artikeln använder den från Azure QuickStart Gallery, som har en Windows-skalningsuppsättning med en grundläggande mall för automatisk skalning. Linux skalningsuppsättningar fungerar på samma sätt.
2. När skalningsuppsättningen har skapats navigerar du till skalningsuppsättningsresursen från Azure Resource Explorer. Följande visas under Noden Microsoft.Insights.

    ![Utforskaren i Azure](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Mallkörningen har skapat en standardinställning för automatisk skalning med namnet **"automatisk skalstartwad".** Till höger kan du visa den fullständiga definitionen av den här inställningen för automatisk skalning. I det här fallet levereras standardinställningen för automatisk skalning med en CPU%-baserad skalnings- och skalningsregel.  

3. Nu kan du lägga till fler profiler och regler baserat på schemat eller specifika krav. Vi skapar en automatisk skalningsinställning med tre profiler. Om du vill förstå profiler och regler i automatisk skalning läser du [Metodtips för automatisk skalning](autoscale-best-practices.md).  

    | Profiler & regler | Beskrivning |
    |--- | --- |
    | **Profil** |**Prestanda/mått baserat** |
    | Regel |Antal meddelanden om servicebusskö > x |
    | Regel |Antal meddelanden om servicebusskö < y |
    | Regel |CPU% > n |
    | Regel |CPU% < p |
    | **Profil** |**Veckodag morgontimmar (inga regler)** |
    | **Profil** |**Produktlanseringsdag (inga regler)** |

4. Här är ett hypotetiskt skalningsscenario som vi använder för den här genomsprföringen.

   * **Belastningsbaserad** - Jag vill skala ut eller in baserat på belastningen på mitt program som finns på min skalningsuppsättning.*
   * **Meddelandeköstorlek** - Jag använder en servicebusskö för inkommande meddelanden till mitt program. Jag använder köns meddelandeantal och CPU% och konfigurerar en standardprofil för att utlösa en skalningsåtgärd om något av meddelandena eller processorn når tröskelvärdet.\*
   * **Tid på vecka och dag** - Jag vill ha en veckovis återkommande "tid på dagen" baserad profil som heter "Veckotimmar". Baserat på historiska data, jag vet att det är bättre att ha ett visst antal VM-instanser för att hantera mitt programs belastning under denna tid.\*
   * **Särskilda datum** - Jag har lagt till en profil för produktlanseringsdagen. Jag planerar i förväg för specifika datum så min ansökan är redo att hantera belastningen på grund marknadsföring meddelanden och när vi sätter en ny produkt i ansökan.\*
   * *De två sista profilerna kan också ha andra prestandamåttsbaserade regler inom sig. I det här fallet bestämde jag mig för att inte ha en och istället förlita sig på standardprestandamåttbaserade regler. Regler är valfria för återkommande och datumbaserade profiler.*

     Automatisk skalning motorns prioritering av profiler och regler är också fångas i [autoscaling bästa praxis](autoscale-best-practices.md) artikeln.
     En lista över vanliga mått för automatisk skalning finns [i Vanliga mått för automatisk skalning](autoscale-common-metrics.md)

5. Kontrollera att du är i **läs-/skrivläge** i Resource Explorer

    ![Automatisk skalning, standardinställning för automatisk skalning](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klicka på Redigera. **Ersätt** elementet "profiler" i inställningen för automatisk skalning med följande konfiguration:

    ![Profiler](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    För fält som stöds och deras värden finns i [DOKUMENTATION för REST API för automatisk skalning](https://msdn.microsoft.com/library/azure/dn931928.aspx). Nu innehåller inställningen för automatisk skalning de tre profiler som förklarats tidigare.

7. Titta slutligen på **meddelandeavsnittet För automatisk** skalning. Med aviseringar för automatisk skalning kan du göra tre saker när en utskalning eller åtgärd utlöses.
   - Meddela administratören och medadministratörerna om din prenumeration
   - Skicka en uppsättning användare via e-post
   - Utlösa ett webhook-samtal. När den här webhooken utlöses skickar den metadata om villkoret för automatisk skalning och skalningsuppsättningsresursen. Mer information om nyttolasten för webhook för automatisk skalning finns i [Konfigurera Webhook & e-postaviseringar för automatisk skalning](autoscale-webhook-email.md).

   Lägg till följande i inställningen Automatisk skalning som ersätter **meddelandeelementet** vars värde är null

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Tryck på **Placera** knappen i Resource Explorer för att uppdatera inställningen för automatisk skalning.

Du har uppdaterat en automatisk skalningsinställning på en vm-skala inställd på att inkludera flera skalprofiler och skalningsmeddelanden.

## <a name="next-steps"></a>Efterföljande moment
Använd de här länkarna om du vill veta mer om automatisk skalning.

[Felsöka automatisk skalning med skaluppsättningar för virtuella datorer](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Vanliga mått för automatisk skalning](autoscale-common-metrics.md)

[Metodtips för automatisk azure-skalning](autoscale-best-practices.md)

[Hantera automatisk skalning med PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Hantera automatisk skalning med CLI](cli-samples.md#autoscale)

[Konfigurera Webhook & e-postmeddelanden för automatisk skalning](autoscale-webhook-email.md)

[Mallreferens för Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)
