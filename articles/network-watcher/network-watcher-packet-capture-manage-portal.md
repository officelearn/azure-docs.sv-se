---
title: Hantera paketinsamlingar - Azure-portal
titleSuffix: Azure Network Watcher
description: Lär dig hur du hanterar paketinsamlingsfunktionen i Network Watcher med Azure-portalen.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 6fc4a25e39fb8f27151b2e3bec1959d74a619233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840835"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Hantera paketinsamlingar med Azure Network Watcher med hjälp av portalen

Med network watcher-paketfånget kan du skapa insamlingssessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för insamlingssessionen för att säkerställa att du bara samlar in den trafik du vill ha. Paketinsamling hjälper till att diagnostisera nätverksavvikelser, både reaktivt och proaktivt. Andra användningsområden inkluderar att samla in nätverksstatistik, få information om nätverksintrång, att felsöka klient-server kommunikation och mycket mer. Att kunna fjärrutlösa paketfångsten, underlättar bördan av att köra ett paketfångst manuellt på en önskad virtuell dator, vilket sparar värdefull tid.

I den här artikeln lär du dig att starta, stoppa, ladda ned och ta bort en paketfångst. 

## <a name="before-you-begin"></a>Innan du börjar

Paketinsamling kräver följande anslutning:
* Utgående anslutning till ett lagringskonto via port 443.
* Inkommande och utgående anslutning till 169.254.169.254
* Inkommande och utgående anslutning till 168.63.129.16

Om en nätverkssäkerhetsgrupp är associerad med nätverksgränssnittet eller undernätet som nätverksgränssnittet finns i, kontrollerar du att det finns regler som tillåter tidigare portar. På samma sätt kan det förhindra anslutning till ovan nämnda IPs och portar om du lägger till användardefinierade trafikvägar i nätverket. Se till att de kan nås. 

## <a name="start-a-packet-capture"></a>Starta en paketfångst

1. I webbläsaren navigerar du till [Azure-portalen](https://portal.azure.com) och väljer **Alla tjänster**och väljer sedan **Nätverksbevakare** i **avsnittet Nätverk**.
2. Välj **Paketinsamling** under **Nätverksdiagnostikverktyg**. Alla befintliga paketinfång listas, oavsett deras status.
3. Välj **Lägg till** om du vill skapa en paketfångst. Du kan välja värden för följande egenskaper:
   - **Prenumeration:** Prenumerationen som den virtuella datorn som du vill skapa paketfångsten för finns i.
   - **Resursgrupp**: Resursgruppen för den virtuella datorn.
   - **Mål virtuell dator:** Den virtuella datorn som du vill skapa paketfångsten för.
   - **Paketfångstens namn**: Ett namn på paketfångsten.
   - **Lagringskonto eller -fil**: Välj **lagringskonto,** **Arkiv**eller båda. Om du väljer **Arkiv**skrivs insamlingen till en sökväg inom den virtuella datorn.
   - **Sökväg till lokal fil**: Den lokala sökvägen på den virtuella datorn där paketfångsten sparas (gäller endast när *filen* är markerad). Sökvägen måste vara en giltig sökväg. Om du använder en virtuell Linux-dator måste sökvägen börja med */var/captures*.
   - **Lagringskonton:** Välj ett befintligt lagringskonto om du har valt *Lagringskonto*. Det här alternativet är bara tillgängligt om du har valt **Lagring**.
   
     > [!NOTE]
     > Premiumlagringskonton stöds för närvarande inte för lagring av paketinsamlingar.

   - **Maximala byte per paket:** Antalet byte från varje paket som fångas. Om de lämnas tomma fångas alla byte.
   - **Maximala byte per session**: Det totala antalet byte som fångas in. När värdet har uppnåtts stoppas paketinsamlingen.
   - **Tidsgräns (sekunder)**: Tidsgränsen innan paketinsamlingen stoppas. Standardvärdet är 18 000 sekunder.
   - Filtrering (valfritt). Välj **+ Lägg till filter**
     - **Protokoll**: Det protokoll som ska filtreras för paketfångsten. De tillgängliga värdena är TCP, UDP och Any.
     - **Lokal IP-adress**: Filtrerar paketfångsten för paket där den lokala IP-adressen matchar det här värdet.
     - **Lokal port:** Filtrerar paketfångsten för paket där den lokala porten matchar det här värdet.
     - **Fjärr-IP-adress:** Filtrerar paketfångsten för paket där fjärr-IP-adressen matchar det här värdet.
     - **Fjärrport:** Filtrerar paketfångsten för paket där fjärrporten matchar det här värdet.
    
     > [!NOTE]
     > Port- och IP-adressvärden kan vara ett enda värde, värdeintervall eller ett intervall, till exempel 80-1024, för port. Du kan definiera så många filter som du behöver.

4. Välj **OK**.

När den tidsgräns som angetts för paketinsamlingen har upphört stoppas paketinsamlingen och kan granskas. Du kan också stoppa en paketfångstsession manuellt.

> [!NOTE]
> Portalen automatiskt:
>  * Skapar en nätverksbevakare i samma region som den region som den virtuella datorn du valde finns i, om regionen inte redan har en nätverksbevakare.
>  * Lägger till *AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md) eller [Windows](../virtual-machines/windows/extensions-nwa.md) virtual machine-tillägg till den virtuella datorn, om det inte redan är installerat.

## <a name="delete-a-packet-capture"></a>Ta bort en paketfångst

1. I paketfångningsvyn väljer du **...** till höger om paketfångsten eller högerklickar på en befintlig paketfångst och väljer **Ta bort**.
2. Du uppmanas att bekräfta att du vill ta bort paketfångsten. Välj **Ja**.

> [!NOTE]
> Om du tar bort en paketfångst tas inte hämtningsfilen bort i lagringskontot eller på den virtuella datorn.

## <a name="stop-a-packet-capture"></a>Stoppa en paketfångst

I paketfångningsvyn väljer du **...** till höger om paketfångsten eller högerklickar på en befintlig paketfångst och väljer **Stoppa**.

## <a name="download-a-packet-capture"></a>Ladda ned en paketfångst

När pakethämtningssessionen har slutförts överförs hämtningsfilen till blob-lagring eller till en lokal fil på den virtuella datorn. Lagringsplatsen för paketinsamlingen definieras när paketinsamlingen skapas. Ett praktiskt verktyg för att komma åt hämtningsfiler som sparats i ett lagringskonto är Microsoft Azure Storage Explorer, som du kan [hämta](https://storageexplorer.com/).

Om ett lagringskonto anges sparas paketinsamlingsfiler i ett lagringskonto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Om du valde **Arkiv** när du skapade hämtningen kan du visa eller hämta filen från sökvägen som du konfigurerade på den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du automatiserar paketinsamlingar med aviseringar för virtuella datorer finns i [Skapa en avisering som utlöst paketfångsten](network-watcher-alert-triggered-packet-capture.md).
- Information om huruvida specifik trafik tillåts in eller ut från en virtuell dator finns i [Diagnostisera ett problem med nätverksfilter för virtuella datorer](diagnose-vm-network-traffic-filtering-problem.md).
