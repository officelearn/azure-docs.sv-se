---
title: Självstudie – skapa en Azure DNS-aliaspost för att referera till en resurspost i zonen.
description: Den här självstudien visar hur du konfigurerar en Azure DNS-aliaspost för att referera till en resurspost i zonen.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990849"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Självstudie: Skapa en aliaspost för att referera till en resurspost för zonen

Aliasposter kan referera till andra postuppsättningar av samma typ. Du kan till exempel låta en DNS CNAME-postuppsättning vara ett alias för en annan CNAME-postuppsättning av samma typ. Detta är användbart om du vill ha vissa postuppsättningar som alias och andra som alias när det gäller beteende.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en aliaspost för en resurspost i zonen
> * Testa aliasposten


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen, inklusive möjlighet att ange namnserverposter (NS) för domänen.

Anvisningar för att vara värd för din domän i Azure DNS finns i [Självstudie: var värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Skapa en aliaspost

Skapa en aliaspost för som pekar mot en resurspost i zonen.

### <a name="create-the-target-resource-record"></a>Skapa målresursposten
1. Klicka på din Azure DNS-zon för att öppna zonen.
2. Klicka på **Postuppsättning**.
3. I textrutan **Namn** skriver du **server**.
4. För **Typ**, väljer du **A**.
5. I textrutan **IP-adress** skriver du **10.10.10.10**.
6. Klicka på **OK**.

### <a name="create-the-alias-record"></a>Skapa aliasposten
1. Klicka på din Azure DNS-zon för att öppna zonen.
2. Klicka på **Postuppsättning**.
3. I textrutan **Namn** skriver du **test**.
4. För **Typ**, väljer du **A**.
5. Klicka på **Ja** i kryssrutan **Aliaspostuppsättning** och välj alternativet **zonpostuppsättning**.
6. Som **zonpostuppsättning**väljer du posten **server**.
7. Klicka på **OK**.

## <a name="test-the-alias-record"></a>Testa aliasposten

1. Starta ditt föredragna nslookup-verktyg. Ett alternativ är att bläddra till [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Ange frågetyp för A-poster och sök efter **test.\< ditt domännamn\>**. Du bör få **10.10.10.10** som svar.
3. I Azure-portalen, ändrar du **server** A-posten till **10.11.11.11**.
4. Vänta några minuter och använd därefter nslookup igen för att **testa** posten.
5. Du bör få **10.11.11.11** som svar.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort **servern** och **testa** resursposterna i din zon.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en aliaspost för att referera till en resurspost i zonen. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
