---
title: Lägg till offentliga IP-adresser i Azure Stack | Microsoft Docs
description: Lär dig hur du lägger till flera offentliga IP-adresser till Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: ed2205455c4d3c82554f3d552bacc059fffa68e7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174664"
---
# <a name="add-public-ip-addresses"></a>Lägg till offentliga IP-adresser
*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*  

Lär dig hur du lägger till flera offentliga IP-adresser till Azure Stack.  Vi refererar till externa adresser som offentliga IP-adresser i den här artikeln, men i Azure Stack som detta är avsett att referera till att lägga till IP-Adressblock i det externa nätverket.  Om det externa nätverket är offentliga Internet-dirigerbara eller är i ett intranät och använder privata adressutrymmet spelar egentligen ingen enligt den här artikeln.  Stegen är desamma. 

## <a name="add-a-public-ip-address-pool"></a>Lägg till en offentlig IP-adresspool
Du kan lägga till offentliga IP-adresser till Azure Stack-system när som helst efter den första distributionen av Azure Stack-system. Kolla hur man [förbrukning visa offentliga IP-adress](azure-stack-viewing-public-ip-address-consumption.md) för att se vilka aktuell användning och offentlig IP-adress är tillgänglighet i Azure Stack.

På hög nivå, hur du lägger till ett nytt block för offentliga IP-adressen till Azure Stack som ser ut så här:

 ![Lägg till IP-flöde](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Hämta adressblocket från din provider
Det första du behöver göra är att hämta adressblocket som du vill lägga till Azure Stack.  Beroende på var du har fått din Adressblock från, behöver du tänka på vad ledtiden är och hantera detta mot den hastighet med vilken du förbrukar offentliga IP-adresser i Azure Stack.  

> [!IMPORTANT]
> Azure Stack kommer att acceptera alla Adressblock som du anger, så länge som det är ett giltigt-Adressblock och inte överlappar ett befintligt adressintervall i Azure Stack.  Kontrollera att du har fått ett giltigt-Adressblock som är dirigerbart och inte överlappar med det externa nätverket som Azure Stack är ansluten.  När du har lagt till intervallet till Azure Stack kan du inte ta bort den.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Lägga till IP-adressintervallet i Azure Stack

1. Navigera till instrumentpanelen i portalen administratör i en webbläsare.  I det här exemplet använder vi https://adminportal.local.azurestack.external.  
2.  Logga in på administrationsportalen för Azure Stack som en cloud-operator.
3.  På standardinstrumentpanel – hitta hanteringslistan regionen och väljer du den region som du vill hantera, i det här exemplet lokalt.
4.  Hitta resursprovidrar panelen och klicka på den Provider för nätverksresurser.
5.  Klicka på den offentliga IP-pooler med användningsikonen.
6.  Klicka på knappen Lägg till IP-pool.
7.  Ange ett namn för IP-poolen.  Det namn du väljer är bara att du enkelt kan identifiera IP-adresspoolen så att du kan skriva vad du gillar.  Det är en bra idé att göra namnet identiskt adressintervallet, men som inte är nödvändiga.
8.   Ange adressblocket som du vill lägga till i CIDR-notation.  Exempel: 192.168.203.0/24
9.  När du anger ett giltigt CIDR-intervall i adressfältet adressintervall (CIDR-block) första IP-adress, sista IP-adress och tillgängliga IP-adresser fält automatiskt fylls i.  De är skrivskyddad och genereras automatiskt så att du inte kan ändra dessa utan att ändra värdet i fältet adressintervall.
10. När du har granskat informationen på bladet och bekräftar allt ser ut korrigera, klicka på Ok om du vill genomföra ändringen och lägga till adressintervallet i Azure Stack.

## <a name="update-the-acls-on-your-top-of-rack-switches"></a>Uppdatera ACL: er på din Tor växlar
Det sista du behöver göra för att aktivera det nyligen tillagda IP-adressintervallet som ska fungera är att uppdatera den åtkomstkontrollistor (ACL) för Top-of-Rack (ToR) växlarna.  ACL: er på ToR-växlar är låsta så att anslutningen mellan utanför Azure Stack och det nyligen tillagda IP-adressintervallet inte fungerar förrän det nya området har lagts till ACL: er på växeln.  

Du måste kontakta Komponenttillverkaren och arbeta med dem för att uppdatera ACL: er på ToR-växlar.  De har de verktyg som behövs för att göra detta på ett sätt som stöds.


## <a name="next-steps"></a>Nästa steg 
[Granska skalningsåtgärder enhet nod](azure-stack-node-actions.md) 
