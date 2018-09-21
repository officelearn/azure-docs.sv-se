---
title: DNS i Azure Stack | Microsoft Docs
description: Med DNS i Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: sethm
ms.openlocfilehash: df4f6066a4bf03f6b09777f3556c52a237501592
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497662"
---
# <a name="using-dns-in-azure-stack"></a>Med DNS i Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack stöd för följande System DNS (Domain Name)-funktioner:

* Matchning av DNS-värdnamn
* Skapa och hantera DNS-zoner och poster med hjälp av API

## <a name="support-for-dns-hostname-resolution"></a>Stöd för DNS-värdnamnsmatchning

Du kan ange en DNS-domännamnsetikett för offentliga IP-resurser. Azure Stack använder **domainnamelabel.location.cloudapp.azurestack.external** för etikettnamn och maps den offentliga IP-adressen i Azure Stack hanteras DNS-servrar.

Exempel: Om du skapar en offentlig IP-adressresurs med **contoso** som en etikett med domän i den lokala Azure Stack-platsen i [fullständigt kvalificerade domännamnet (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)  **Contoso.Local.cloudapp.azurestack.external** motsvarar den offentliga IP-adressen för resursen.   Du kan använda detta fullständiga domännamn för att skapa en anpassad domän CNAME-post som pekar på den offentliga IP-adressen i Azure Stack.

Mer information om namnmatchning finns i [DNS-matchningen](../../dns/dns-for-azure-services.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) artikeln.

> [!IMPORTANT]
> Varje domännamnsetikett som du skapar måste vara unika inom dess Azure Stack-plats.

Följande skärmbild visar de **skapa offentlig IP-adress** dialogrutan för att skapa en offentlig IP-adress med hjälp av portalen:

![Skapa offentlig IP-adress](media/azure-stack-whats-new-dns/image01.png)

### <a name="example-scenario"></a>Exempel på ett scenario

Du har en belastningsutjämnare som bearbetar begäranden från ett webbprogram. Bakom load är balancer en webbplats som körs på en eller flera virtuella datorer. Du kan komma åt webbplatsen Utjämning av nätverksbelastning med ett DNS-namn i stället för en IP-adress.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Skapa och hantera DNS-zoner och poster med hjälp av API

Du kan skapa och hantera DNS-zoner och poster i Azure Stack.

Azure Stack är en DNS-tjänst som är liknande till Azure, med hjälp av API: er som är konsekventa med Azure DNS API: er.  Du kan som värd för domäner i Azure Stack DNS för att hantera dina DNS-poster med samma autentiseringsuppgifter, API: er och verktyg. Du kan också använda samma fakturering och support som dina andra Azure-tjänster.

Azure Stack DNS-infrastrukturen är mer kompakt än Azure. Storlek och plats för en Azure Stack-distribution påverkar DNS omfång, skala och prestanda. Det innebär också att prestanda, tillgänglighet, global distribution och hög tillgänglighet kan variera från distribution till distribution.

## <a name="comparison-with-azure-dns"></a>Jämförelse med Azure DNS

DNS i Azure Stack liknar DNS i Azure, men det finns viktiga undantag:

* **Stöder inte AAAA-poster**

    Azure Stack stöder inte AAAA-poster eftersom Azure Stack inte har stöd för IPv6-adresser. Det här är en viktig skillnad mellan DNS i Azure och Azure Stack.
* **Är inte flera innehavare**

    DNS-tjänsten i Azure Stack är inte flera innehavare. Varje klient kan inte skapa samma DNS-zonen. Endast den första prenumerationen som försöker skapa zonen lyckas och efterföljande begäranden som misslyckas. Det här är en viktig skillnad mellan Azure och Azure Stack DNS.
* **Taggar, metadata och Etags**

    Det finns mindre skillnader i hur Azure Stack hanterar taggar, metadata, Etags och gränser.

Läs mer om Azure DNS i [DNS-zoner och poster](../../dns/dns-zones-records.md).

### <a name="tags"></a>Taggar

Azure Stack-DNS stöder med hjälp av Azure Resource Manager-taggar på DNS-zon resurser. Det stöder inte taggar på DNS-postuppsättningar, även om som ett alternativ ”metadata” stöds på DNS-postuppsättningar som beskrivs härnäst.

### <a name="metadata"></a>Metadata

Som ett alternativ till uppsättning av poster taggar, Azure Stack DNS stöder kommentera postuppsättningar med *metadata*. Liknar taggar, metadata kan du associera namn / värde-par med varje uppsättning av poster. Detta kan exempelvis vara användbar för att registrera syftet med varje uppsättning av poster. Du kan inte använda metadata för att tillhandahålla en filtrerad vy av Azure-fakturan till skillnad från taggar, och metadata kan inte anges i en Azure Resource Manager-princip.

### <a name="etags"></a>Etags

Anta att två personer eller två processer du försöker ändra en DNS-post på samma gång. Vilken wins? Och vinnaren känner att de har över ändringar som skapats av någon annan?

Azure Stack-DNS använder *Etags* att på ett säkert sätt hantera samtidiga ändringar till samma resurs. Etags skiljer sig från Azure Resource Manager *taggar*. Varje DNS-resurs (zon eller uppsättning av poster) har en Etag som är kopplade till den. När en resurs hämtas, hämtas även dess Etag. När du uppdaterar en resurs måste välja du att skicka tillbaka Etag så att Azure Stack DNS kan kontrollera som Etag på server-matchningar. Eftersom varje uppdatering till en resurs resulterar i Etag återskapas, anger ett Etag-Typfel samtidiga har ändrats. Etags kan också användas när du skapar en ny resurs att se till att resursen inte finns redan.

Använda Etags för att blockera samtidiga ändringar till zoner och uppsättningar av poster som standard Azure Stack DNS PowerShell-cmdlets. Den valfria **-Skriv över** växel kan användas för att undertrycka Etag kontroller, i så fall eventuella samtidiga ändringar som har skett skrivs över.

På Azure Stack DNS REST API-nivå anges Etags med HTTP-huvuden. Deras beteende beskrivs i följande tabell:

| Sidhuvud | Beteende|
|--------|---------|
| Ingen   | PUT lyckas alltid (inga Etag-kontroller)|
| IF-match| PUT lyckas bara om resursen finns och Etag matchar|
| IF-match *| PUT lyckas bara om resursen finns|
| IF-none-match *| PUT lyckas bara om resursen inte finns|

### <a name="limits"></a>Begränsningar

Följande standard begränsningar gäller när du använder Azure Stack DNS:

| Resurs| Standardgräns|
|---------|--------------|
| Zoner per prenumeration| 100|
| Postuppsättningar per zon| 5000|
| Poster per uppsättning av poster| 20|

## <a name="next-steps"></a>Nästa steg

[Introduktion till IDN: er för Azure Stack](azure-stack-understanding-dns.md)
