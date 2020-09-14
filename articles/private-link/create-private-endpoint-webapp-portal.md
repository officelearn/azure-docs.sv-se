---
title: Anslut privat till en webbapp med hjälp av Azures privata slut punkt (för hands version)
description: Den här artikeln förklarar hur du ansluter privat till en webbapp med hjälp av Azures privata slut punkt (för hands version).
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 09/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ccbcdbe9204120e1cf181136f566556ec30be871
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054542"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint-preview"></a>Anslut privat till en webbapp med hjälp av Azures privata slut punkt (för hands version)

Azures privata slut punkt (för hands version) är det grundläggande Bygg blocket för Azures privata länk. Genom att använda privat slut punkt kan du ansluta privat till din webbapp. I den här artikeln får du lära dig hur du distribuerar en webbapp med hjälp av en privat slut punkt och sedan ansluter till webbappen från en virtuell dator (VM).

Mer information finns i [använda privata slut punkter för en Azure-webbapp][privateendpointwebapp].

> [!Note]
> Privat slut punkt (för hands version) är tillgänglig i offentliga regioner för PremiumV2-webbappar i Windows, Linux-webbappar och Azure Functions Premium-planen (kallas ibland för elastisk Premium-plan). 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com)innan du börjar.

## <a name="create-a-virtual-network-and-virtual-machine"></a>Skapa ett virtuellt nätverk och en virtuell dator

I det här avsnittet skapar du ett virtuellt nätverk och ett undernät som är värd för en virtuell dator som du kommer att använda för att få åtkomst till en webbapp via en privat slut punkt.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

Gör så här för att skapa det virtuella nätverket och under nätet:

1. I den vänstra rutan väljer du **skapa en resurs**  >  **nätverk**  >  **virtuellt nätverk**.

1. I fönstret **Skapa virtuellt nätverk** väljer du fliken **grundläggande** och anger sedan den information som visas här:

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fönstret "skapa Virtual Network" i Azure Portal.][1]

1. Välj fliken **IP-adresser** och ange sedan den information som visas här:

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fliken "IP-adresser" i fönstret Skapa virtuellt nätverk.][2]

1. I avsnittet **undernät** väljer du **Lägg till undernät**, anger den information som visas här och väljer sedan **Lägg till**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fönstret "Lägg till undernät".][3]

1. Välj **Granska + skapa**.

1. När verifieringen är klar väljer du **skapa**.

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Gör så här om du vill skapa den virtuella datorn:

1. I Azure Portal i det vänstra fönstret väljer du **skapa en resurs**  >  **beräkning**  >  **virtuell dator**.

1. I fönstret **skapa en virtuell dator – grundläggande** anger du den information som visas här:

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fönstret "skapa en virtuell dator".][4]

1. Välj **Nästa: diskar**.

1. Behåll standardinställningarna i fönstret **diskar** och välj sedan **Nästa: nätverk**.

1. I fönstret **nätverk** anger du den information som visas här:

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fliken "nätverk" i fönstret "skapa en virtuell dator".][5]

1. Välj **Granska + skapa**.

1. När verifieringen är klar väljer du **skapa**.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Skapa en webbapp och en privat slut punkt

I det här avsnittet skapar du en privat webbapp som använder en privat slut punkt.

> [!Note]
> Den privata slut punkts funktionen är bara tillgänglig för PremiumV2-nivån.

### <a name="create-the-web-app"></a>Skapa webbappen

1. I Azure Portal i det vänstra fönstret väljer du **skapa en resurs**  >  **webb**  >  **webbapp**.

1. Välj fliken **grundläggande** i fönstret **webbapp** och ange sedan den information som visas här:

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fliken "grunder" i fönstret "webbapp".][6]

1. Välj **Granska + skapa**.

1. När verifieringen är klar väljer du **skapa**.

### <a name="create-the-private-endpoint"></a>Skapa den privata slut punkten

1. I egenskaper för webbapp under Inställningar väljer du **nätverk**och under **Inställningar**för **privata slut punkter (för hands version)** väljer **du konfigurera dina privata slut punkts anslutningar**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av länken "Konfigurera dina anslutningar för privata slut punkter" i fönstret nätverk för webbapp.][7]

1. I guiden **anslutningar för privata slut punkter (för hands version)** väljer du **Lägg till**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av knappen Lägg till i guiden "anslutningar för privata slut punkter (för hands version)".][8]

1. Välj rätt information i list rutorna **prenumeration**, **virtuellt nätverk**och **undernät** och välj sedan **OK**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fönstret Lägg till privat slut punkt (förhands granskning).][9]

1. Övervaka förloppet för skapandet av den privata slut punkten.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av förloppet för att lägga till den privata slut punkten. ][10]
   >  ![ Skärm bild av den nyligen skapade privata slut punkten.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Ansluta till den virtuella datorn från Internet

1. Skriv **myVm**i **sökrutan Azure Portal** .
1. Välj **Anslut**och välj sedan **RDP**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av knappen "RDP" i fönstret "myVM".][12]

1. I fönstret **Anslut med RDP** väljer du **Ladda ned RDP-fil**.  

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av knappen "Ladda ned RDP-fil" i fönstret "Anslut med RDP".][13]

   Azure skapar en Remote Desktop Protocol-fil (RDP) och laddar ned den till datorn.   

1. Öppna den nedladdade RDP-filen.

   a. Välj **Anslut**i prompten.  
   b. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

     > [!Note]
     > Om du vill använda dessa autentiseringsuppgifter kan du behöva välja **fler alternativ**för att  >  **använda ett annat konto**.

1. Välj **OK**.

   > [!Note]
   > Om du får en certifikat varning under inloggnings processen väljer du **Ja** eller **Fortsätt**.

1. När fönstret VM Desktop visas, minimerar du det så att det går tillbaka till det lokala Skriv bordet.

## <a name="access-the-web-app-privately-from-the-vm"></a>Få åtkomst till webb programmet privat från den virtuella datorn

I det här avsnittet ansluter du privat till webbappen med hjälp av den privata slut punkten.

1. Om du vill hämta den privata IP-adressen för din privata slut punkt **går du till sökrutan,** skriver **privat länk** och väljer sedan **privat länk**i resultat listan.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av länken "privat länk" i listan med Sök resultat.][14]

1. I Private Link Center i det vänstra fönstret väljer du **privata slut punkter** för att visa dina privata slut punkter.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av listan privata slut punkter i Private Link Center.][15]

1. Välj den privata slut punkt som länkar till din webbapp och ditt undernät.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fönstret Egenskaper för en privat slut punkt.][16]

1. Kopiera den privata IP-adressen för din privata slut punkt och det fullständigt kvalificerade domän namnet (FQDN) för din webbapp. I föregående exempel är det privata ID: t *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. I fönstret **myVM** kontrollerar du att webbappen inte är tillgänglig via den offentliga IP-adressen. Det gör du genom att öppna en webbläsare och klistra in namnet på webb programmet. På sidan ska meddelandet "fel 403-förbjuden" visas.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fel sidan "fel 403-förbud".][17]

   > [!Important]
   > Eftersom den här funktionen finns i för hands version måste du hantera posten Domain Name Service (DNS) manuellt.

   Gör något av följande för DNS:
 
   - Använd tjänsten Azure DNS Private Zone.  

     a. Skapa en privat DNS-zon med namnet *`privatelink.azurewebsites.net`* och länka den sedan till det virtuella nätverket.  
     b. Skapa två A-poster (det vill säga appens namn och namnet på tjänst kontroll hanteraren [SCM]) med IP-adressen för din privata slut punkt.  
     > [!div class="mx-imgBorder"]
     > ![Skärm bild av poster för privata DNS-zoner.][21]  

   - Använd *hosts* -filen för den virtuella datorn.  

     a. Skapa värd posten, öppna Utforskaren och leta efter *hosts* -filen.  
     > [!div class="mx-imgBorder"]
     > ![Skärm bild som visar hosts-filen i Utforskaren.][18]  
     b. Lägg till en post som innehåller den privata IP-adressen och det offentliga namnet på din webbapp genom att redigera *hosts* -filen i en text redigerare.  
     > [!div class="mx-imgBorder"]
     > ![Skärm bild av texten för hosts-filen.][19]  
     c. Spara filen.

1. Skriv URL: en för din webbapp i en webbläsare.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av en webbläsare som visar en webbapp.][20]

Du har nu åtkomst till din webbapp via den privata slut punkten.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den privata slut punkten, webbappen och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller.

1. I rutan Azure Portal, i **sökrutan,** anger du **Ready-RG**och väljer sedan **Ready-RG** i resultat listan.

1. Välj **Ta bort resursgrupp**.

1. Under **Skriv resurs gruppens namn**, anger du **Ready-RG**och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator i ett virtuellt nätverk, en webbapp och en privat slut punkt. Du har anslutit till en virtuell dator från Internet och kommunicerat på ett säkert sätt till webbappen med hjälp av en privat länk. 

Mer information om privat slut punkt (för hands version) finns i [Vad är Azures privata slut punkt?][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
