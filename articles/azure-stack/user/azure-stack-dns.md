---
title: DNS i Azure-stacken | Microsoft Docs
description: DNS i Azure Stack
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: ac93b5eb4228cef373428b7b69932d5993d54fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="dns-in-azure-stack"></a>DNS i Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure-stacken innehåller följande funktioner i DNS:
* Stöd för DNS-värdnamn
* Skapa och hantera DNS-zoner och poster med hjälp av API

## <a name="support-for-dns-hostname-resolution"></a>Stöd för DNS-värdnamn
Du kan ange ett DNS-domännamnet för en offentlig IP-resurs, vilket skapar en mappning för *domainnamelabel.location*. cloudapp.azurestack.external till den offentliga IP-adressen i Azure-stacken hanterade DNS-servrar.  

Om du skapar en offentlig IP-resurs med till exempel **contoso** som en domännamnet på den lokala Azure Stack-plats, det fullständigt kvalificerade domännamnet (FQDN) **contoso.local.cloudapp.azurestack.external**matchar den offentliga IP-adressen för resursen. Du kan använda den här FQDN för att skapa en anpassad domän CNAME-post som pekar på den offentliga IP-adressen i Azure-stacken.

> [!IMPORTANT]
> Varje domännamnet som har skapats måste vara unika inom sin Azure Stack-plats.

Om du skapar den offentliga IP-adressen med hjälp av portalen ser ut så här:

![Skapa offentlig IP-adress](media/azure-stack-whats-new-dns/image01.png)

Den här konfigurationen är användbar om du vill koppla en offentlig IP-adress till en resurs för Utjämning av nätverksbelastning. Du kan till exempel ha en belastningsutjämnare som bearbetar begäranden från ett webbprogram. Bakom belastningen är balancer en webbplats på en eller flera virtuella datorer. Du kan nu komma åt webbplatsen för Utjämning av nätverksbelastning med ett DNS-namn i stället för en IP-adress.

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>Skapa och hantera DNS-zoner och poster med hjälp av API
Du kan skapa och hantera DNS-zoner och poster i Azure-stacken.  

Azure-stacken är en DNS-tjänst som Azure, med API: er som är konsekventa med Azures DNS-API: er.  Du kan hantera DNS-poster med samma autentiseringsuppgifter, API: er, verktyg, fakturering och support som andra Azure-tjänster som värd för dina domäner i Azure-stacken DNS. 

Azure-stacken DNS-infrastruktur är mer kompakt än Azures för uppenbara skäl. Därför omfång, skalning och prestanda som beror på skalan av Azure Stack-distributionen och miljön där den distribueras.  Så, till exempel prestanda, tillgänglighet, global distributionsplatsen och hög tillgänglighet (HA) kan variera från distribution till distribution.

## <a name="comparison-with-azure-dns"></a>Jämförelse med Azure DNS
DNS i Azure-stacken liknar DNS i Azure, med två viktiga undantag:
* **Stöder inte AAAA-poster**

    Azure-stacken stöder inte AAAA-poster eftersom Azure stacken inte har stöd för IPv6-adresser.  Det här är en nyckel skillnaden mellan DNS i Azure och Azure-stacken.
* **Det är inte flera innehavare**

    Till skillnad från Azure är DNS-tjänsten i Azure-stacken inte flera innehavare. Så att varje klient kan inte skapa samma DNS-zon. Endast den första prenumeration som försöker skapa zonen lyckas och misslyckas efterföljande förfrågningar.  Det är ett känt problem och viktigaste skillnaden mellan Azure och Azure-stacken DNS. Det här problemet löses i en framtida version.

Dessutom finns några mindre skillnader i hur Azure-stacken DNS implementerar taggar, Metadata, Etags och begränsningar.

Följande information gäller specifikt för Azure-stacken DNS och skiljer sig något från Azure DNS. Läs mer om Azure DNS i [DNS-zoner och registrerar](../../dns/dns-zones-records.md) på webbplatsen Microsoft Azure-dokumentationen.

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

| Huvudet | Beteende|
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
