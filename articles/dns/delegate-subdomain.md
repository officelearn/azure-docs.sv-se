---
title: Delegera en underdomän - Azure DNS
description: Med den här utbildningssökvägen kommer du igång med att delegera en Azure DNS-underdomän.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: cd1443a9ca8ccf7172072078734f21d789d36194
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937437"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Delegera en Azure DNS-underdomän

Du kan använda Azure-portalen för att delegera en DNS-underdomän. Om du till exempel äger contoso.com domänen kan du delegera en underdomän som kallas *teknik* till en annan, separat zon som du kan administrera separat från zonen contoso.com.

Om du vill kan du delegera en underdomän med [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Krav

Om du vill delegera en Azure DNS-underdomän måste du först delegera din offentliga domän till Azure DNS. Se [Delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) för instruktioner om hur du konfigurerar dina namnservrar för delegering. När domänen har delegerats till din Azure DNS-zon kan du delegera underdomänen.

> [!NOTE]
> Contoso.com används som exempel i hela den här artikeln. Använd ditt eget domännamn i stället för contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Skapa en zon för underdomänen

Skapa först zonen för **den tekniska** underdomänen.

1. På Azure-portalen väljer du **Skapa en resurs**.
2. Skriv **DNS**i sökrutan och välj **DNS-zon**.
3. Välj **Skapa**.
4. Skriv **engineering.contoso.com** i textrutan **Namn** i fönstret **Skapa DNS-zon.**
5. Välj resursgruppen för zonen. Du kanske vill använda samma resursgrupp som den överordnade zonen för att hålla ihop liknande resurser.
6. Klicka på **Skapa**.
7. När distributionen har slutförts går du till den nya zonen.

## <a name="note-the-name-servers"></a>Observera namnservrarna

Observera sedan de fyra namnservrarna för underdomänen för teknik.

I fönstret **teknikzon** tecknar du zonens fyra namnservrar. Du kommer att använda dessa namnservrar senare.

## <a name="create-a-test-record"></a>Skapa en testpost

Skapa en **A-post** som ska användas för testning. Skapa till exempel en **www** A-post och konfigurera den med en **IP-adress på 10.10.10.10.**

## <a name="create-an-ns-record"></a>Skapa en NS-post

Skapa sedan en namnserverpost (NS) för **teknikzonen.**

1. Navigera till zonen för den överordnade domänen.
2. Välj **+ Postuppsättning**.
3. Skriv **teknik** i textrutan **Namn** i fönstret Lägg **till postuppsättning.**
4. För **Typ**väljer du **NS**.
5. Under **Namnserver**anger du de fyra namnservrar som du tidigare spelat in från **teknikzonen.**
6. Klicka på **OK**.

## <a name="test-the-delegation"></a>Testa delegeringen

Använd nslookup för att testa delegeringen.

1. Öppna ett PowerShell-fönster.
2. Skriv i kommandotolken`nslookup www.engineering.contoso.com.`
3. Du bör få ett icke-auktoritativt svar som visar adressen **10.10.10.10**.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar omvänd DNS för tjänster som finns i Azure](dns-reverse-dns-for-azure-services.md).