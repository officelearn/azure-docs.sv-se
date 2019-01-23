---
title: Delegera en underdomän i Azure DNS
description: Lär dig mer om att delegera en underdomän i Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/22/2019
ms.author: victorh
ms.openlocfilehash: 87a80703c473245660a850645ca3fef21bbd80f6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452725"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegera en underdomän i Azure DNS

Du kan använda Azure-portalen för att delegera DNS-underdomänen. Till exempel om du äger domänen contoso.com, kan du delegera en underdomän som kallas *engineering* till en annan, separat zon kan administreras separat från zonen contoso.com.

## <a name="prerequisites"></a>Förutsättningar

Om du vill delegera en underdomän i Azure DNS måste du först Delegera din offentliga domän till Azure DNS. Se [delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) anvisningar om hur du konfigurerar dina namnservrar för delegering. När din domän har delegerats till Azure DNS-zon kan delegera du din underdomän.

I exemplen i den här artikeln används domänen contoso.com. Du bör ersätta din egen domän när du använder de här procedurerna.

## <a name="create-a-zone-for-your-subdomain"></a>Skapa en zon för din underdomän

Börja med att skapa zonen för den **engineering** underdomänen.

1. Azure-portalen väljer du **skapa en resurs**.
2. I sökrutan skriver **DNS**, och välj **DNS-zon**.
3. Välj **Skapa**.
4. I den **skapa DNS-zon** rutan, skriver du in **engineering.contoso.com** i den **namn** textrutan.
5. Välj resursgrupp för din zon. Du kanske vill använda samma resursgrupp som den överordnade zonen för att bevara liknande resurser tillsammans.
6. Klicka på **Skapa**.
7. När distributionen är klar går du till den nya zonen.

## <a name="note-the-name-servers"></a>Observera namnservrarna

Next, copy the four name servers for your subdomain.

1. På den **engineering** zon fönstret Observera fyra namnservrarna för zonen. Du använder dessa namnservrar senare.
2. Skapa en **A** post för testning. Till exempel skapa en **www** A spela in och konfigurera den med en **10.10.10.10** IP-adress.

## <a name="create-an-ns-record"></a>Skapa en NS-post

Skapa sedan en namnet namnserver-post för den **engineering** zon.

1. Gå till zonen för den överordnade domänen.
2. Välj **+ Postuppsättning**.
3. På den **lägga till en postuppsättning** rutan, skriver du in **engineering** i den **namn** textrutan.
4. För **typ**väljer **NS**.
5. Under **namnservern**, ange fyra namnservrarna som du antecknade tidigare från den **engineering** zon.
6. Klicka på **OK**.

## <a name="test-the-delegation"></a>Testa delegeringen

Använda nslookup för att testa delegeringen.

1. Öppna ett PowerShell-fönster.
2. Kommandotolken skriver du: `nslookup www.engineering.<your domain name>.`
3. Du bör få ett icke-auktoritativa svar som visar adressen **10.10.10.10**.



## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurera omvänd DNS för tjänster som hanteras i Azure](dns-reverse-dns-for-azure-services.md).