---
title: Avancerad automatisk skalning med Azure Virtual Machines
description: 'Använder Resource Manager och VM Scale Sets med flera regler och profiler som skickar e-postmeddelande och anropa webhook-URL: er med skalningsåtgärder.'
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: af20d84cbead84e3a9fa5b6ea93661814e42a851
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326379"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Avancerad automatisk skalning med Resource Manager-mallar för VM Scale Sets
Du kan skala in och ut på Virtual Machine Scale Sets baserat på prestanda tröskelmått, genom ett återkommande schema eller genom att ett visst datum. Du kan också konfigurera e-post och webhook meddelanden om skalningsåtgärder. Den här genomgången visar ett exempel på hur du konfigurerar dessa objekt med en Resource Manager-mall för VM-Skalningsuppsättningen.

> [!NOTE]
> Även om den här genomgången beskriver stegen för VM Scale Sets, samma information som gäller för automatisk skalning [molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) För en enkel skala in/ut inställningen på VM Scale Sets baserat på en enkel prestandamått som CPU, referera till den [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) och [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) dokument
>
>

## <a name="walkthrough"></a>Genomgång
I den här genomgången använder vi [Azure Resource Explorer](https://resources.azure.com/) att konfigurera och uppdatera autoskalningsinställningarna för en skalningsuppsättning. Azure Resource Explorer är ett enkelt sätt att hantera Azure-resurser via Resource Manager-mallar. Om du är nybörjare på Azure Resource Explorer-verktyget kan du läsa [den här introduktionen](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Distribuera en ny skalningsuppsättning med en grundläggande autoskalningsinställningen. Den här artikeln använder en från Azure QuickStart-galleriet, som har en Windows-skalningsuppsättning med en mall för grundläggande automatisk skalning. Skalningsuppsättningar för Linux fungerar på samma sätt.
2. När skalningsuppsättningen skapas, går du till scale set resursen från Azure Resource Explorer. Du ser följande under Microsoft.Insights noden.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Mall-körning har skapat en standardinställning för automatisk skalning med namnet **'autoscalewad'**. Till höger, kan du visa den fullständiga definitionen av den här autoskalningsinställningen. I det här fallet kommer standardinställningen för automatisk skalning med en processor % baserat skalning och skala in regel.  

3. Du kan nu lägga till fler profiler och regler baserat på det schema eller specifika krav. Vi skapa en autoskalningsinställning med tre profiler. För att förstå profiler och regler i automatisk skalning, granska [Metodtips för autoskalning](autoscale-best-practices.md).  

    | Profiler och regler | Beskrivning |
    |--- | --- |
    | **Profil** |**Prestanda/mått baserade** |
    | Regel |Service Bus antal Kömeddelanden > x |
    | Regel |Service Bus antal Kömeddelanden < y |
    | Regel |Processor % > n |
    | Regel |Processor % < p |
    | **Profil** |**Veckodag morgon timmar (inga regler)** |
    | **Profil** |**Produkten starta dag (inga regler)** |

4. Här är ett hypotetiskt skalning scenario som vi använder för den här genomgången.

    * **Belastningen baserat** -jag vill skala in eller ut baserat på belastning på min App på min skala set.*
    * **Meddelandestorlek kö** – jag använder en Service Bus-kön för inkommande meddelanden till mitt program. Jag använda kön antal meddelanden och processor och konfigurera en standardprofil för att utlösa en skalningsåtgärd om något av meddelandeantalet eller processor når threshold.*
    * **Tid för vecka och dag** -jag vill ha en veckovis återkommande ”tid på dagen' baserat profil med namnet” veckodag morgon timmar ”. Baserat på historiska data kan vet jag är det bättre att ha vissa antalet Virtuella datorinstanser att hantera belastningen för mitt program under den här tid.*
    * **Datum** – jag har lagt till en produkt starta dag-profil. Jag Planera framåt för specifika datum så att programmet är redo att hantera belastningen på grund av marknadsföring meddelanden och när vi publicerar en ny produkt i programmet.*
    * *De två sista profilerna kan också ha andra mått baserat Prestandaregler i dem. I det här fallet jag valt att inte har någon och i stället för att förlita dig på standard-prestandamått baseras regler. Regler är valfria för återkommande och datum-baserade profiler.*

    Automatisk skalning motorn prioritering av profiler och regler hämtas även i den [Metodtips för automatisk skalning](autoscale-best-practices.md) artikeln.
    En lista över vanliga mått för autoskalning [vanliga mått för autoskalning](autoscale-common-metrics.md)

5. Kontrollera att du är på den **Läs/Skriv** läge i Resursläsaren

    ![Autoscalewad standard autoskalningsinställning](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klicka på Redigera. **Ersätt** ”profiler”-element i autoskalningsinställning med följande konfiguration:

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
    Fält som stöds och deras värden finns i [Autoskala REST API-dokumentation](https://msdn.microsoft.com/library/azure/dn931928.aspx). Din inställning för automatisk skalning innehåller nu tre profiler som beskrivs tidigare.

7. Ta en titt på automatisk skalning **meddelande** avsnittet. Meddelanden om autoskalning kan du göra tre saker när en skalbar eller i praktiken aktiverats har.
   - Meddela administratören och medadministratörer för din prenumeration
   - Skicka en uppsättning användare
   - Utlös ett webhook-anrop. När utlöstes, denna webhook skickar metadata om villkor för automatisk skalning och skalningsuppsättning resurs. Läs mer om nyttolasten för automatisk skalning webhook i [konfigurera Webhook & e-postmeddelanden för automatisk skalning](autoscale-webhook-email.md).

   Lägg till följande Autoskala inställningen ersätta din **meddelande** element vars värde är null

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

   Tryck på **placera** knappen i Resursläsaren för att uppdatera autoskalningsinställningarna.

Du har uppdaterat en autoskalningsinställning på en VM-Skalningsuppsättning kan innehålla flera skala profiler och skala meddelanden.

## <a name="next-steps"></a>Nästa steg
Använd dessa länkar om du vill veta mer om automatisk skalning.

[Felsök automatisk skalning med VM-Skalningsuppsättningar](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Vanliga mått för autoskalning](autoscale-common-metrics.md)

[Metodtips för automatisk skalning i Azure](autoscale-best-practices.md)

[Hantera automatisk skalning med hjälp av PowerShell](../../monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Hantera automatisk skalning med hjälp av CLI](cli-samples.md#autoscale)

[Konfigurera Webhook & e-postmeddelanden för automatisk skalning](autoscale-webhook-email.md)
