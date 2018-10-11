---
title: Självstudie – skapa en Azure DNS-aliaspost för att stödja apex domännamn med Traffic Manager
description: Den här självstudien visar hur du konfigurerar en Azure DNS-aliaspost för att stödja användning av ditt apex-domännamn med Traffic Manager.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 4a5d8968861f6f2938e605d2f7106529ca401df4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967446"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Självstudie: Konfigurera en aliaspost för att hantera apex-domännamn med Traffic Manager 

Du kan skapa en aliaspost för ditt apex-domännamn (till exempel contoso.com) för att referera till en Traffic Manager-profil. Istället för att använda en omdirigeringstjänst för detta, kan du konfigurera Azure DNS för att referera till en Traffic Manager-profil direkt från din zon. 


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en virtuell värddator och nätverksinfrastruktur
> * Skapa en Traffic Manager-profil
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
2. I det övre vänstra hörnet i portalen klickar du på **Skapa en resurs**, skriver in *resursgrupp* i sökfältet och skapar en resursgrupp med namnet **RG-DNS-Alias-TM**.
3. Klicka på **Skapa en resurs**, klicka på **Nätverk** och klicka sedan på **Virtuellt nätverk**.
4. Skapa ett virtuellt nätverk med namnet **VNet-Servers**, placera det i resursgruppen **RG-DNS-Alias-TM** och namnge undernätet **SN-Web**.

## <a name="create-two-web-server-virtual-machines"></a>Skapa två virtuella webbserverdatorer
1. Klicka på **Skapa en resurs**, klicka på **Windows Server 2016 VM**.
2. Skriv in **Web-01** som namn och placera den virtuella datorn i resursgruppen **RG-DNS-Alias-TM**. Skriv in ett användarnamn, lösenord och klicka på **OK**.
3. Som **Storlek**, väljer du en SKU med 8 GB RAM-minne.
4. Som **Inställningar**, väljer du det virtuella nätverket **VNet-Servers** och undernätet **SN-Web**.
5. Klicka på **offentlig IP-adress** och under **Tilldelning** klickar du på **Statiska** och klickar sedan på **OK**.
6. För offentliga inkommande portar, väljer du **HTTP**, **HTTPS** och **RDP (3389)** och klickar sedan på **OK**.
7. På sammanfattningssidan klickar du på **Skapa**.

   Det tar några minuter att slutföra.
6. Upprepa proceduren för att en annan virtuell dator med namnet **Web-02**.

### <a name="add-a-dns-label"></a>Ange en DNS-etikett
De offentliga IP-adresserna behöver en DNS-etikett för att fungera med Traffic Manager.
1. I resursgruppen **RG-DNS-Alias-TM** klickar du på den offentliga IP-adressen **Web-01-ip**.
2. Under **Inställningar** klickar du på **Konfiguration**.
3. I textrutan DNS-namnetikett anger du **web01pip**.
4. Klicka på **Spara**.

Upprepa den här proceduren för den offentliga IP-adressen **Web-02-ip** med **web02pip** som DNS-namnetikett.

### <a name="install-iis"></a>Installera IIS

Installera IIS på både **Web-01** och **Web-02**.

1. Anslut till **Web-01** och logga in.
2. Från Serverhanterarens instrumentpanel klickar du på **Lägg till roller och funktioner**.
3. Klicka på **Nästa** tre gånger och på sidan **Serverroller** väljer du **Webbserver (IIS)**.
4. Klicka på **Lägg till funktioner** och klicka på **Nästa**.
5. Klicka på **Nästa** fyra gånger och klicka sedan på **Installera**.

   Det här kan ta några minuter.
6. När installationen är klar klickar du på **Stäng**.
7. Öppna en webbläsare och gå till **localhost** för att kontrollera att IIS-standardwebbsidan visas.

Upprepa den här proceduren för att installera IIS på **Web-02**.


## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Förfaller 

1. Öppna resursgruppen **RG-DNS-Alias-TM** och klicka på den offentliga IP-adressen **Web-01-ip**. Anteckna IP-addessen för senare användning. Upprepa detta för den offentliga IP-adressen för **Web-02-ip**.
1. Klicka på **Skapa en resurs**, klicka på **Nätverk** och klicka sedan på **Traffic Manager-profil**.
2. Skriv in **Web-01** som namn och placera det i resursgruppen **RG-DNS-Alias-TM**.
3. Klicka på **Skapa**.
4. När distributionen är klar klickar du på **Gå till resurs**.
5. På Traffic Manager-profilsidan under **Inställningar**, klickar du på **Slutpunkter**.
6. Klicka på **Lägg till**.
7. Som **Typ** väljer du **Extern slutpunkt**, som **Namn** skriver du in **EP-Web01**.
8. I textrutan **Fullständigt kvalificerat domännamn (FQDN) eller IP** skriver du in IP-adressen för **Web-01-IP** som du antecknade tidigare.
9. Välj samma **Plats** som dina andra resurser och klicka sedan på **OK**.

Upprepa den här proceduren för att lägga till slutpunkten **Web-02** med hjälp av IP-adressen du antecknade tidigare för **Web-02-ip**.

## <a name="create-an-alias-record"></a>Skapa en aliaspost

Skapa en aliaspost som pekar på Traffic Manager-profilen.

1. Klicka på din Azure DNS-zon för att öppna zonen.
2. Klicka på **Postuppsättning**.
3. Lämna textrutan **Namn** tom för att representera domännamnsapex (till exempel contoso.com).
4. Lämna **Typ** som en **A**-post.
5. Klicka på kryssrutan **Aliaspostuppsättning**.
6. Klicka på **Välj Azure-tjänst** och välj Traffic Manager-profilen **TM-alias-test**.

## <a name="test-the-alias-record"></a>Testa aliasposten

1. Från en webbläsare går du till ditt domännamnsapex (till exempel contoso.com). Du borde se IIS-standardwebbsidan. Stäng webbläsaren.
2. Stäng av den virtuella datorn **Web-01** och vänta några minuter tills den är helt avstängd.
3. Öppna ett nytt webbläsarfönster och gå till ditt domännamnsapex igen.
4. Du bör återigen se IIS-standardsidan, eftersom Traffic Manager har hanterat situationen och dirigerat trafiken till **Web-02**.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs så kan du ta bort resursgruppen **RG-DNS-Alias-TM** för att ta bort alla resurser som skapades för den här självstudien.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en aliaspost för att använda ditt domännamnsapex för att referera till en Traffic Manager-profil. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
