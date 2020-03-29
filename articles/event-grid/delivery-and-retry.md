---
title: Azure Event Grid leverans och försök igen
description: Beskriver hur Azure Event Grid levererar händelser och hur det hanterar olevererade meddelanden.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921070"
---
# <a name="event-grid-message-delivery-and-retry"></a>Leverans och försök med meddelande från Event Grid

I den här artikeln beskrivs hur Azure Event Grid hanterar händelser när leverans inte bekräftas.

Event Grid ger hållbar leverans. Det ger varje meddelande minst en gång för varje prenumeration. Händelser skickas till den registrerade slutpunkten för varje prenumeration omedelbart. Om en slutpunkt inte bekräftar mottagandet av en händelse försöker Event Grid leverera händelsen igen.

## <a name="batched-event-delivery"></a>Batchad händelseleverans

Event Grid skickar varje händelse individuellt till prenumeranter. Prenumeranten tar emot en matris med en enda händelse. Du kan konfigurera Event Grid till batchhändelser för leverans för förbättrad HTTP-prestanda i scenarier med hög dataflöde.

Batchad leverans har två inställningar:

* **Max händelser per batch** - Maximalt antal händelser Event Grid levereras per batch. Detta antal kommer aldrig att överskridas, men färre händelser kan levereras om inga andra händelser är tillgängliga vid tidpunkten för publiceringen. Event Grid fördröjer inte händelser för att skapa en batch om färre händelser är tillgängliga. Måste vara mellan 1 och 5000.
* **Önskad batchstorlek i kilobyte** - Måltak för batchstorlek i kilobyte. I likhet med maxhändelser kan batchstorleken vara mindre om fler händelser inte är tillgängliga vid publiceringstillfället. Det är möjligt att en batch är större än önskad batchstorlek *om* en enskild händelse är större än önskad storlek. Om den önskade storleken till exempel är 4 kB och en 10 KB-händelse skickas till Event Grid, levereras händelsen 10 kB fortfarande i en egen batch i stället för att tas bort.

Batchad leverans i konfigurerad per händelse prenumerationsbasis via portalen, CLI, PowerShell eller SDK:er.

### <a name="azure-portal"></a>Azure Portal: 
![Inställningar för batchleverans](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
När du skapar en händelseprenumeration använder du följande parametrar: 

- **max-events-per-batch** - Maximalt antal händelser i en batch. Måste vara ett tal mellan 1 och 5000.
- **önskad-batch-size-in-kilobytes** - Önskad batchstorlek i kilobyte. Måste vara ett tal mellan 1 och 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Mer information om hur du använder Azure CLI med Event Grid finns i [Dirigera lagringshändelser till webbslutpunkt med Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Schema och varaktighet för återförsök

Event Grid väntar 30 sekunder på ett svar efter att ha levererat ett meddelande. Efter 30 sekunder, om slutpunkten inte har svarat, köas meddelandet för ett nytt försök. Event Grid använder en exponentiell backoff-återförsöksprincip för händelseleverans. Event Grid försöker leverera på följande schema på bästa sätt:

- 10 sekunder
- 30 sekunder
- 1 minut
- 5 minuter
- 10 minuter
- 30 minuter
- 1 timme
- Varje timme i upp till 24 timmar

Om slutpunkten svarar inom 3 minuter försöker Event Grid ta bort händelsen från återförsökskön på bästa sätt, men dubbletter kan fortfarande tas emot.

Event Grid lägger till en liten randomisering i alla steg för återförsök och kan opportunistiskt hoppa över vissa försök om en slutpunkt är konsekvent felaktig, ned under en lång period eller verkar vara överväldigad.

För deterministiskt beteende anger du händelsetiden till live- och maxleveransförsök i [principerna för återförsök](manage-event-delivery.md)för prenumerationen .

Som standard upphör Event Grid att alla händelser som inte levereras inom 24 timmar. Du kan [anpassa återförsöksprincipen](manage-event-delivery.md) när du skapar en händelseprenumeration. Du anger det maximala antalet leveransförsök (standard är 30) och händelsen tid att leva (standard är 1440 minuter).

## <a name="delayed-delivery"></a>Fördröjd leverans

När en slutpunkt visar leveransfel börjar Event Grid fördröja leveransen och återförsöka händelser till den slutpunkten. Om till exempel de första 10 händelserna som publiceras till en slutpunkt misslyckas, antar Event Grid att slutpunkten har problem och kommer att fördröja alla efterföljande återförsök *och nya* leveranser under en tid - i vissa fall upp till flera timmar.

Det funktionella syftet med fördröjd leverans är att skydda felaktiga slutpunkter samt Event Grid-systemet. Utan back-off och fördröjning av leverans till felaktiga slutpunkter kan Event Grids återförsöksprincip och volymfunktioner enkelt överbelasta ett system.

## <a name="dead-letter-events"></a>Händelser i obeställbara brev

När Event Grid inte kan leverera en händelse kan den skicka den olevererade händelsen till ett lagringskonto. Denna process kallas dead-lettering. Som standard aktiveras inte obeställbara bokstäver i Händelserutnätet. Om du vill aktivera det måste du ange ett lagringskonto som ska innehålla olevererade händelser när du skapar händelseprenumerationen. Du hämtar händelser från det här lagringskontot för att lösa leveranser.

Event Grid skickar en händelse till platsen för obeställbara meddelanden när den har försökt alla sina försök att försöka igen. Om Event Grid tar emot en svarskod på 400 (felaktig begäran) eller 413 (Begär entitet för stor) skickas händelsen omedelbart till slutpunkten för obeställbara bokstäver. Dessa svarskoder indikerar att leveransen av händelsen aldrig kommer att lyckas.

Det är fem minuters fördröjning mellan det senaste försöket att leverera en händelse och när den levereras till obeställbara platser. Den här fördröjningen är avsedd att minska antalet Blob-lagringsåtgärder. Om platsen för obeställbara objekt inte är tillgänglig i fyra timmar tas händelsen bort.

Innan du anger platsen för obeställbara brev måste du ha ett lagringskonto med en behållare. Du anger slutpunkten för den här behållaren när du skapar händelseprenumerationen. Slutpunkten är i formatet:`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Du kanske vill bli meddelad när en händelse har skickats till platsen för obeställbara meddelanden. Om du vill använda Event Grid för att svara på olevererade händelser [skapar du en händelseprenumeration](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) för blob-lagring av obeställbara bokstäver. Varje gång din blob-lagring med obeställbara brev tar emot en olevererad händelse meddelar Event Grid din hanterare. Hanteraren svarar med åtgärder som du vill vidta för att stämma av olevererade händelser.

Ett exempel på att skapa en plats för obeställbara bokstäver finns i [Principer för obeställbara bokstäver och försök igen](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Leveransstatus för meddelanden

Event Grid använder HTTP-svarskoder för att bekräfta mottagandet av händelser. 

### <a name="success-codes"></a>Lyckades koder

Event Grid betraktar **endast** följande HTTP-svarskoder som lyckade leveranser. Alla andra statuskoder betraktas som misslyckade leveranser och kommer att göras om eller deadlettered på lämpligt sätt. När event grid får en lyckad statuskod anses leveransen vara slutförd.

- 200 OK
- 201 Skapad
- 202 Accepterad
- 203 Icke-auktoritativ information
- 204 Inget innehåll

### <a name="failure-codes"></a>Felkoder

Alla andra koder som inte finns i ovanstående uppsättning (200-204) betraktas som fel och kommer att göras om. Vissa har specifika principer för återförsök som är kopplade till dem som beskrivs nedan, alla andra följer standardexens exponentiell back-off-modell. Det är viktigt att komma ihåg att på grund av den mycket parallelliserade karaktären av Event Grid arkitektur, återförsök beteende är icke-deterministisk. 

| Statuskod | Beteende för återförsök |
| ------------|----------------|
| 400 dålig begäran | Försök igen efter 5 minuter eller mer (Deadletter omedelbart om deadletter setup) |
| 401 Obehörig | Försök igen efter 5 minuter eller mer |
| 403 Förbjudet | Försök igen efter 5 minuter eller mer |
| 404 – Hittades inte | Försök igen efter 5 minuter eller mer |
| 408 Timeout för begäran | Försök igen efter 2 minuter eller mer |
| 413 Begärandeenheten är för stor | Försök igen efter 10 sekunder eller mer (Deadletter omedelbart om deadletter setup) |
| 503 Tjänsten är inte tillgänglig | Försök igen efter 30 sekunder eller mer |
| Alla andra | Försök igen efter 10 sekunder eller mer |


## <a name="next-steps"></a>Nästa steg

* Om du vill visa status för händelseleveranser finns i Övervaka leverans av [händelserutnätsmeddelanden](monitor-event-delivery.md).
* Om du vill anpassa alternativ för händelseleverans finns i [Principer för obeställbara brev och försök igen](manage-event-delivery.md).
