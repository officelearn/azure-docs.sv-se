---
title: DNS i Azure-stacken | Microsoft Docs
description: Med hjälp av DNS i Azure-stacken
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 4e854a2751ce366e3ca3a353487f2c972401c248
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196533"
---
# <a name="using-dns-in-azure-stack"></a>Med hjälp av DNS i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure-stacken stöder följande Domain Name System (DNS)-funktioner:

* Matchning av DNS-värdnamn
* Skapa och hantera DNS-zoner och poster med hjälp av API

## <a name="support-for-dns-hostname-resolution"></a>Stöd för DNS-värdnamn

Du kan ange ett DNS-domännamnet för den offentliga IP-resurser. Azure-stacken använder *domainnamelabel.location*. cloudapp.azurestack.external för namn och mappar den till den offentliga IP-adressen i Azure-stacken hanterade DNS-servrar.

Om du skapar en offentlig IP-resurs med till exempel **contoso** som en domännamnet på den lokala Azure Stack-plats i [fullständigt kvalificerade domännamnet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **Contoso.Local.cloudapp.azurestack.external** matchar den offentliga IP-adressen för resursen. Du kan använda den här FQDN för att skapa en anpassad domän CNAME-post som pekar på den offentliga IP-adressen i Azure-stacken.

Mer information om namnmatchning avser den [DNS-matchning](https://docs.microsoft.com/en-us/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) artikel.

> [!IMPORTANT]
> Varje domännamnet som du skapar måste vara unika inom sin Azure Stack-plats.

I nästa skärmdump visar den **skapa offentlig IP-adress** dialogrutan för att skapa en offentlig IP-adress med hjälp av portalen.

![Skapa offentlig IP-adress](media/azure-stack-whats-new-dns/image01.png)

**Exempelscenario**

Du har en belastningsutjämnare som bearbetar begäranden från ett webbprogram. Bakom belastningen är balancer en webbplats som körs på en eller flera virtuella datorer. Du kan komma åt webbplatsen Utjämning av nätverksbelastning med ett DNS-namn i stället för en IP-adress.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Skapa och hantera DNS-zoner och poster med hjälp av API

Du kan skapa och hantera DNS-zoner och poster i Azure-stacken.

Azure-stacken är en DNS-tjänst som Azure, med API: er som är konsekventa med Azures DNS-API: er.  Du kan hantera dina DNS-poster med samma autentiseringsuppgifter, API: er och verktyg som värd för dina domäner i Azure-stacken DNS. Du kan också använda samma fakturering och stöd som andra Azure-tjänster.

Azure-stacken DNS-infrastrukturen är mer kompakt än Azures. Storlek och placering av en Azure-stacken distribution påverkar DNS-scope, skalning och prestanda. Det innebär också att prestanda, tillgänglighet, global distributionsplatsen och hög tillgänglighet kan variera från distribution till distribution.

## <a name="comparison-with-azure-dns"></a>Jämförelse med Azure DNS

DNS i Azure-stacken liknar DNS i Azure, men det finns viktiga undantag måste du förstå.

* **Stöder inte AAAA-poster**

    Azure-stacken stöder inte AAAA-poster eftersom Azure stacken inte har stöd för IPv6-adresser.  Det här är en nyckel skillnaden mellan DNS i Azure och Azure-stacken.
* **Det är inte flera innehavare**

    DNS-tjänsten i Azure-stacken är inte flera innehavare. Varje klient kan inte skapa samma DNS-zon. Endast den första prenumeration som försöker skapa zonen lyckas och misslyckas efterföljande förfrågningar.  Det är ett känt problem och viktigaste skillnaden mellan Azure och Azure-stacken DNS. Det här problemet löses i en framtida version.
* **Taggar, metadata och Etags**

    Det finns mindre skillnader i hur Azure-stacken hanterar taggar, metadata, Etags och begränsningar.

Läs mer om Azure DNS i [DNS-zoner och registrerar](../../dns/dns-zones-records.md).

### <a name="tags-metadata-and-etags"></a>Taggar, metadata och Etags

**Taggar**

Azure-stacken DNS stöder med hjälp av Azure Resource Manager taggar på DNS-zonen resurser. Det stöder inte taggar på DNS-postuppsättningar, även om som ett alternativ 'metadata' stöds på DNS-post anger som beskrivs i nästa.

**Metadata**

Som ett alternativ till postuppsättning taggar stöder Azure Stack DNS kommentera postuppsättningar med 'metadata'. Liknar taggar, metadata kan du associera namn / värde-par med varje uppsättning av poster. Detta kan exempelvis vara användbar för att registrera syftet med varje uppsättning av poster. Till skillnad från taggar, metadata kan inte användas för att tillhandahålla en filtrerad vy av fakturan Azure och kan inte anges i en Azure Resource Manager-princip.

**Etags**

Anta att två personer eller två processer försök att ändra en DNS-post på samma gång. Vilken wins? Och vinnaren känner att de har över ändringar som skapats av någon annan?

Azure DNS i stacken använder Etags för att hantera samtidiga ändringar till samma resurs på ett säkert sätt. Etags skiljer sig från Azure Resource Manager ”-taggar'. Varje DNS-resursposter (zonen eller postuppsättning) har en Etag som är kopplade till den. När en resurs hämtas, hämtas även dess Etag. När du uppdaterar en resurs, kan du välja att skicka tillbaka Etag så att Azure-stacken DNS kan kontrollera som Etag på servern matchar. Eftersom varje uppdatering till en resurs resulterar i Etag återskapas, anger ett Etag-Typfel samtidiga har ändrats. Etags kan också användas när du skapar en ny resurs att se till att resursen inte finns redan.

Som standard använder Azure Stack DNS-PowerShell Etags blockera samtidiga ändringar till zoner och postuppsättningar. Det valfria *-skriva över* växel kan användas för att undertrycka Etag-kontroller, i vilket fall alla samtidiga ändringarna som skett skrivs över.

På nivån av Azure-stacken DNS-REST API anges Etags med HTTP-huvuden. Deras beteende ges i följande tabell:

| Sidhuvud | Beteende|
|--------|---------|
| Ingen   | PLACERA lyckas alltid (inga Etag-kontroller)|
| IF-match| PLACERA lyckas bara om resursen finns och Etag matchar|
| IF-match *| PLACERA lyckas bara om resursen finns|
| IF-none-match *| PLACERA lyckas bara om resursen inte finns|

### <a name="limits"></a>Begränsningar

Följande standard begränsningar gäller när du använder Azure Stack DNS:

| Resurs| Standardgräns|
|---------|--------------|
| Zoner per prenumeration| 100|
| Postuppsättningar per zon| 5000|
| Poster per uppsättningen av poster| 20|

## <a name="next-steps"></a>Nästa steg

[Introduktion till IDN: er för Azure-Stack](azure-stack-understanding-dns.md)
