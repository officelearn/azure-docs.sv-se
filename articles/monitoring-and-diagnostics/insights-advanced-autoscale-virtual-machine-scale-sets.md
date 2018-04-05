---
title: Avancerade Autoskala med Azure Virtual Machines | Microsoft Docs
description: 'Använder Resource Manager och Skalningsuppsättningar med flera regler och profiler som kan skicka e-post och anropa webhook URL: er med skalningsåtgärder.'
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 7e3576e2-4a2b-4736-b5ae-98c4689cdd2b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2016
ms.author: ancav
ms.openlocfilehash: 80955535c8d863cd3d8d1b77e2ab8bc016b6d9f3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Avancerade Autoskala konfigurationen med hjälp av Resource Manager-mallar för Skalningsuppsättningar
Du kan skala i och skalbar i Skalningsuppsättningar i virtuella datorer baserat på mått prestandatrösklarna genom ett återkommande schema eller genom att ett visst datum. Du kan också konfigurera e-post och webhook-meddelanden för skala åtgärder. Den här genomgången visar ett exempel på hur du konfigurerar dessa objekt med en Resource Manager-mall i en Skaluppsättning.

> [!NOTE]
> När den här genomgången beskriver stegen för Skalningsuppsättningar, samma information gäller autoskalning [molntjänster](https://azure.microsoft.com/services/cloud-services/), och [App Service - Webbappar](https://azure.microsoft.com/services/app-service/web/).
> För en enkel skala in/ut inställning på en VM Scale Set baserat på ett enkelt prestanda mått t.ex CPU, referera till den [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) och [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) dokument
>
>

## <a name="walkthrough"></a>Genomgång
I den här genomgången ska vi använda [resursutforskaren Azure](https://resources.azure.com/) att konfigurera och uppdatera autoskalningsinställning för en skaluppsättning. Azure Resource Explorer är ett enkelt sätt att hantera Azure-resurser via Resource Manager-mallar. Om du är nybörjare på för Azure Resource Explorer kan du läsa [här introduktionen](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Distribuera en ny skala med en grundläggande autoskalningsinställning. Den här artikeln används från Snabbstartsgalleriet Azure som har en Windows skaluppsättning med en grundläggande Autoskala mall. Skaluppsättningar för Linux fungerar på samma sätt.
2. När skaluppsättning har skapats kan du gå till resursen scale set från resursutforskaren i Azure. Du ser följande under Microsoft.Insights nod.

    ![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

    Mall-körningen har skapat en Autoskala standardinställning med namnet **'autoscalewad'**. Du kan visa fullständig definitionen av den här autoskalningsinställning till höger. I det här fallet levereras Autoskala standardinställningen med en processor % baserat skalbar och skala i regeln.  

3. Du kan nu lägga till fler profiler och regler baserat på det schema eller den specifika krav. Vi skapa en autoskalningsinställning med tre profiler. För att förstå profiler och regler i Autoskala, granska [Autoskala metodtips](insights-autoscale-best-practices.md).  

    | Profiler och regler | Beskrivning |
    |--- | --- |
    | **Profil** |**Prestanda/mått baserade** |
    | Regel |Service Bus-kön Meddelandemängd > x |
    | Regel |Service Bus-kön Meddelandemängd < y |
    | Regel |CPU i % > n |
    | Regel |CPU i % < p |
    | **Profil** |**Veckodag morgon timmar (inga regler)** |
    | **Profil** |**Produkten starta dag (inga regler)** |

4. Här är ett hypotetiskt skalning scenario som vi använder för den här genomgången.

    * **Belastningen baserat** -jag vill skala in eller ut utifrån belastningen på mitt program som finns på min skala set.*
    * **Meddelande köstorlek** -jag använder en Service Bus-kö för inkommande meddelanden till Mina program. Jag använder köns antalet meddelanden och processor och konfigurera en standardprofil för att utlösa en skalningsåtgärd om antalet meddelanden eller CPU träffar i threshold.*
    * **Tid för vecka och dag** -jag vill ha en vecka återkommande 'tid på dagen-baserade profilen med namnet veckodag morgon-timmar. Baserat på historisk data, vet jag är det bättre att ha vissa antal VM-instanser för att hantera mina programinläsning under den här time.*
    * **Datum** -jag har lagt till en profil för produkten starta dag. Jag planera för specifika datum så mitt program är redo att hantera belastningen på grund av marknadsföring meddelanden och när vi publicerar en ny produkt i application.*
    * *De sista två profilerna kan också ha andra prestanda mått baserat på regler i dem. I det här fallet jag valt att inte har någon och i stället för att förlita sig på prestanda standardmått-baserade regler. Regler är valfria för återkommande och datum-baserade profiler.*

    Autoskala motorn prioritering av profiler och regler fångas även i den [autoskalning metodtips](insights-autoscale-best-practices.md) artikel.
    En lista över vanliga mätvärden för Autoskala [vanliga mätvärden för Autoskala](insights-autoscale-common-metrics.md)

5. Kontrollera att du är på den **läsning och skrivning** läge i Resursläsaren

    ![Autoscalewad autoskalningsinställning som standard](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Klicka på Redigera. **Ersätt** 'profiler'-element i autoskalningsinställningen med följande konfiguration:

    ![Profiler](./media/insights-advanced-autoscale-vmss/profiles.png)

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
    Fält som stöds och deras värden finns i [Autoskala REST API-dokumentation](https://msdn.microsoft.com/en-us/library/azure/dn931928.aspx). Autoskalningsinställningen innehåller nu tre profiler som tidigare förklarats.

7. Slutligen titta på Autoskalningsformeln **meddelande** avsnitt. Meddelanden om autoskalning kan du göra tre saker när en skalbar eller i praktiken utlösts har.
   - Meddela administratören och medadministratörer för din prenumeration
   - Skicka e-post till en uppsättning användare
   - Utlös ett webhook-anrop. När det utlöses, skickar denna webhook metadata om villkoret autoskalning och skaluppsättning resurs. Läs mer om nyttolasten för Autoskala webhook i [konfigurera Webhook & e-postaviseringar för Autoskala](insights-autoscale-to-webhook-email.md).

   Lägg till följande inställning Autoskala ersätta din **meddelande** element vars värde är null

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

   Träffa **placera** knapp i Resursläsaren för att uppdatera autoskalningsinställningen.

Du har uppdaterat en autoskalningsinställning på en VM-skala innefattar flera skala profiler och skala meddelanden.

## <a name="next-steps"></a>Nästa steg
Använd dessa länkar om du vill veta mer om autoskalning.

[Felsöka Autoskala med virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Vanliga mätvärden för Autoskala](insights-autoscale-common-metrics.md)

[Metodtips för Azure Autoskala](insights-autoscale-best-practices.md)

[Hantera Autoskala med hjälp av PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Hantera Autoskala med hjälp av CLI](insights-cli-samples.md#autoscale)

[Konfigurera Webhook & e-postaviseringar för Autoskala](insights-autoscale-to-webhook-email.md)
