---
title: "Visa Azure aktivitetsloggar att övervaka resurser | Microsoft Docs"
description: "Använd aktivitetsloggar till användaråtgärder för granskning och fel. Visar PowerShell för Azure Portal, Azure CLI och REST."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: tomfitz
ms.openlocfilehash: 9f90bc80c146c6c2da04aacbc110f7d389c0baa2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>Visa aktivitetsloggar granska åtgärder på resurser
Du kan bestämma via aktivitetsloggar:

* vilka åtgärder som utförts på resurserna i prenumerationen
* vem som initierat åtgärden (även om en serverdelstjänst åtgärder inte returnerar en användare som anroparen)
* När åtgärden utfördes
* Status för åtgärden
* Värdena för andra egenskaper som kan hjälpa dig undersöka igen

[!INCLUDE [resource-manager-audit-limitations](../../includes/resource-manager-audit-limitations.md)]

Du kan hämta information från aktivitetsloggar via portalen, PowerShell, Azure CLI, insikter REST API eller [insikter .NET-bibliotek](https://www.nuget.org/packages/Microsoft.Azure.Insights/).

## <a name="portal"></a>Portalen
1. Om du vill visa aktivitetsloggar via portalen **övervakaren**.
   
    ![Välj aktivitetsloggar](./media/resource-group-audit/select-monitor.png)

   Om du vill filtrera automatiskt aktivitetsloggen för en viss resurs eller en resursgrupp, väljer du **aktivitetsloggen** från den resursbladet. Observera att aktivitetsloggen filtreras automatiskt av den valda resursen.
   
    ![Filtrera efter resurs](./media/resource-group-audit/filtered-by-resource.png)
2. I den **aktivitetsloggen** bladet visas en sammanfattning av nya åtgärder.
   
    ![Visa åtgärder](./media/resource-group-audit/audit-summary.png)
3. Välj olika villkor för att begränsa antalet åtgärder visas. Till exempel följande bild visar den **Timespan** och **händelse som initieras av** fält ändras för att visa de åtgärder som vidtas av en viss användare eller ett program för den senaste månaden. Välj **tillämpa** att visa resultat av frågan.
   
    ![Ange alternativ för filter](./media/resource-group-audit/set-filter.png)

4. Om du behöver köra frågan igen senare väljer **spara** och ge ett namn för frågan.
   
    ![Spara frågan](./media/resource-group-audit/save-query.png)
5. Du kan välja något av de inbyggda frågorna, till exempel inte kunde distribueras för att köra en fråga snabbt.

    ![Välj fråga](./media/resource-group-audit/select-quick-query.png)

   Den valda frågan anger automatiskt filtervärdena som krävs.

    ![Visa distributionsfel](./media/resource-group-audit/view-failed-deployment.png)   

6. Välj en av åtgärderna för att visa en sammanfattning av händelsen.

    ![Visa åtgärden](./media/resource-group-audit/view-operation.png)  

## <a name="powershell"></a>PowerShell
1. Om du vill hämta loggposter kör den **Get-AzureRmLog** kommando. Du kan ange ytterligare parametrar om du vill filtrera listan med poster. Om du inte anger en start- och Slutdatum tid returneras poster för den senaste timmen. Till exempel för att hämta kör åtgärder för en resursgrupp under den senaste timmen:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup
  ```
   
    I följande exempel visas hur du använder aktivitetsloggen till research åtgärder som vidtagits under en angiven tid. Start- och slutdatum har angetts i ett datumformat.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00
  ```

    Eller så kan du använda datumfunktioner för att ange datumintervallet, till exempel de senaste 14 dagarna.
   
  ```powershell 
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

2. Beroende på den starttid som du anger kan de föregående kommandona returnera en lång lista med åtgärder för resursgruppen. Du kan filtrera resultaten för vad du letar efter genom att ange sökvillkor. Om du vill undersöka hur en webbapp har stoppats, kan du köra följande kommando:

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) | Where-Object OperationName -eq Microsoft.Web/sites/stop/action
  ```

    Vilket det här exemplet visar att en stoppåtgärden utfördes av someone@contoso.com. 

  ```powershell 
  Authorization     :
  Scope     : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Action    : Microsoft.Web/sites/stop/action
  Role      : Subscription Admin
  Condition :
  Caller            : someone@contoso.com
  CorrelationId     : 84beae59-92aa-4662-a6fc-b6fecc0ff8da
  EventSource       : Administrative
  EventTimestamp    : 8/28/2015 4:08:18 PM
  OperationName     : Microsoft.Web/sites/stop/action
  ResourceGroupName : ExampleGroup
  ResourceId        : /subscriptions/xxxxx/resourcegroups/ExampleGroup/providers/Microsoft.Web/sites/ExampleSite
  Status            : Succeeded
  SubscriptionId    : xxxxx
  SubStatus         : OK
  ```

3. Du kan söka efter de åtgärder som vidtas för en viss användare, även för en resursgrupp som inte längre finns.

  ```powershell 
  Get-AzureRmLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

4. Du kan filtrera efter misslyckade åtgärder.

  ```powershell
  Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed
  ```

5. Du kan fokusera på ett fel genom att titta på statusmeddelanden för posten.
   
        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
   
    Som returnerar:
   
        code           message                                                                        
        ----           -------                                                                        
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. 


## <a name="azure-cli"></a>Azure CLI
* Om du vill hämta loggposter måste du köra den **azure-grupp loggen visar** kommando.

  ```azurecli
  azure group log show ExampleGroup --json
  ```


## <a name="rest-api"></a>REST API
REST-åtgärder för att arbeta med aktivitetsloggen är en del av den [insikter REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx). Om du vill hämta aktivitet logghändelser finns [management-händelser i en prenumeration](https://msdn.microsoft.com/library/azure/dn931934.aspx).

## <a name="next-steps"></a>Nästa steg
* Azure aktivitetsloggar kan användas med Power BI och få större insikter om åtgärderna i din prenumeration. Se [visa och analysera Azure aktivitetsloggar i Power BI och mycket mer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).
* Mer information om att ställa in säkerhetsprinciper, se [Azure rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-configure.md).
* Läs om kommandona för att visa distributionsåtgärder i [visa distributionsåtgärder](resource-manager-deployment-operations.md).
* Information om hur du hindrar borttagningar på en resurs för alla användare finns [låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

