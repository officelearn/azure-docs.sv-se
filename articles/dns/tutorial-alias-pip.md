---
title: Självstudie – Konfigurera en Azure DNS-aliaspost för att referera till en offentlig IP-adress i Azure.
description: Den här självstudien visar hur du konfigurerar en Azure DNS-aliaspost för att referera till en offentlig IP-adress i Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 842015d853790e3ac78214cca6a70becb7f9fadf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991240"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Självstudie: Konfigurera en aliaspost för att referera till en offentlig IP-adress i Azure 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en nätverksinfrastruktur
> * Skapa en virtuell webbserverdator
> * Skapa en aliaspost
> * Testa aliasposten


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen, inklusive möjlighet att ange namnserverposter (NS) för domänen.

Anvisningar för att vara värd för din domän i Azure DNS finns i [Självstudie: var värd för din domän i Azure DNS](dns-delegate-domain-azure-dns.md).

Exempeldomänen som används i den här självstudien är contoso.com, men du bör använda ditt eget domännamn.

## <a name="create-the-network-infrastructure"></a>Skapa nätverksinfrastrukturen
Skapa först ett virtuellt nätverk och ett undernät för att placera dina webbservrar i.
1. Logga in på Azure Portal på http://portal.azure.com.
2. I det övre vänstra hörnet i portalen klickar du på **Skapa en resurs**, skriver in *resursgrupp* i sökfältet och skapar en resursgrupp med namnet **RG-DNS-Alias-pip**.
3. Klicka på **Skapa en resurs**, klicka på **Nätverk** och klicka sedan på **Virtuellt nätverk**.
4. Skapa ett virtuellt nätverk med namnet **VNet-Server**, placera det i resursgruppen **RG-DNS-Alias-pip** och namnge undernätet **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Skapa en virtuell webbserverdator
1. Klicka på **Skapa en resurs**, klicka på **Windows Server 2016 VM**.
2. Skriv in **Web-01** som namn och placera den virtuella datorn i resursgruppen **RG-DNS-Alias-TM**. Skriv in ett användarnamn, lösenord och klicka på **OK**.
3. Som **Storlek**, väljer du en SKU med 8 GB RAM-minne.
4. Som **Inställningar**, väljer du det virtuella nätverket **VNet-Servers** och undernätet **SN-Web**. För offentliga inkommande portar, väljer du **HTTP**, **HTTPS** och **RDP (3389)** och klickar sedan på **OK**.
5. På sammanfattningssidan klickar du på **Skapa**.

   Det tar några minuter att slutföra.

### <a name="install-iis"></a>Installera IIS

Installera IIS på **Web-01**.

1. Anslut till **Web-01** och logga in.
2. Från Serverhanterarens instrumentpanel klickar du på **Lägg till roller och funktioner**.
3. Klicka på **Nästa** tre gånger och på sidan **Serverroller** väljer du **Webbserver (IIS)**.
4. Klicka på **Lägg till funktioner** och klicka på **Nästa**.
5. Klicka på **Nästa** fyra gånger och klicka sedan på **Installera**.

   Det här kan ta några minuter.
6. När installationen är klar klickar du på **Stäng**.
7. Öppna en webbläsare och gå till **localhost** för att kontrollera att IIS-standardwebbsidan visas.

## <a name="create-an-alias-record"></a>Skapa en aliaspost

Skapa en aliaspost som pekar på den offentliga IP-adressen.

1. Klicka på din Azure DNS-zon för att öppna zonen.
2. Klicka på **Postuppsättning**.
3. I textrutan **Namn** skriver du **web01**.
4. Lämna **Typ** som en **A**-post.
5. Klicka på kryssrutan **Aliaspostuppsättning**.
6. Klicka på **Välj Azure-tjänst** och välj den offentliga IP-adressen **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testa aliasposten

1. I resursgruppen **RG-DNS-Alias-pip**, klickar du på den virtuella datorn **Web-01**. Anteckna den offentliga IP-adressen.
1. Från en webbläsare bläddrar du till det fullständigt kvalificerade domännamnet för den virtuella Web01-01-datorn. Till exempel: **web01.contoso.com**. Du borde se IIS-standardwebbsidan.
2. Stäng webbläsaren.
3. Stoppa den virtuella datorn **Web-01** och starta sedan om den.
4. När den startar om, antecknar du den nya IP-adressen för den virtuella datorn.
5. Öppna en ny webbläsare och bläddra till det fullständigt kvalificerade domännamnet för den virtuella datorn Web01-01. Till exempel: **web01.contoso.com**.
6. Det bör fortfarande fungera, eftersom du har använt en aliaspost för att peka mot den offentliga IP-adressresursen och inte en standard A-post.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs så kan du ta bort resursgruppen **RG-DNS-Alias-pip** för att ta bort alla resurser som skapades för den här självstudien.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat en aliaspost för att referera till en offentlig IP-adress i Azure. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
