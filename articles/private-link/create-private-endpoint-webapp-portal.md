---
title: Ansluta privat till en webbapp med Azure Private Endpoint
description: Ansluta privat till en webbapp med Azure Private Endpoint
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287823"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Ansluta privat till en webbapp med Azure Private Endpoint (förhandsversion)

Azure Private Endpoint är den grundläggande byggstenen för Privat länk i Azure. Det gör att du kan ansluta privat till din web app.
I den här snabbstarten får du lära dig hur du distribuerar en webbapp med privat slutpunkt och ansluter till den här webbappen från en virtuell dator.

Mer information finns i [Använda privata slutpunkter för Azure Web App][privatenedpointwebapp].

> [!Note]
>Förhandsversionen är tillgänglig i regioner i Östra USA och Västra USA 2 för alla PremiumV2 Windows- och Linux Web Apps. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Virtuellt nätverk och virtuell dator

I det här avsnittet skapar du det virtuella nätverket och undernätet som värd för den virtuella datorn som används för att komma åt din webbapp via den privata slutpunkten.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och undernät.

1. På skärmens övre vänstra sida väljer du **Skapa ett** > **virtuellt** > **nätverk** för nätverk eller sök efter **virtuellt nätverk** i sökrutan.

1. Ange eller välj den här informationen på fliken Grunderna i **Skapa virtuellt nätverk:**

   > [!div class="mx-imgBorder"]
   > ![Skapa virtuellt nätverk][1]

1. Klicka på **"Nästa: IP-adresser >"** och ange eller välj den här informationen:

   > [!div class="mx-imgBorder"]
   >![Konfigurera IP-adresser][2]

1. Klicka på **"+ Lägg till undernät"** i avsnittet Undernät och ange följande information och klicka på **"Lägg till"**

   > [!div class="mx-imgBorder"]
   >![Lägg till undernät][3]

1. Klicka på **"Granska + skapa"**

1. När valideringen har skickats klickar du på **"Skapa"**

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du Skapa en**virtuell dator** **för resursberäkn** >  **Create a resource** > 

1. I Skapa en virtuell dator – grunder anger eller väljer du följande information:

   > [!div class="mx-imgBorder"]
   >![Grundläggande virtuell dator][4]

1. Välj **"Nästa: Diskar"**

   Behåll standardinställningarna.

1. Välj **"Nästa: Nätverk"** väljer du den här informationen:

   > [!div class="mx-imgBorder"]
   >![Nätverk][5]

1. Klicka på **"Granska + Skapa"**

1. När valideringen skickade meddelandet klickar du på **"Skapa"**

## <a name="create-your-web-app-and-private-endpoint"></a>Skapa din webbapp och privata slutpunkt

I det här avsnittet ska du skapa en privat webbapp med hjälp av en privat slutpunkt till den.

> [!Note]
>Funktionen Privat slutpunkt är endast tillgänglig för Premium V2 SKU.

### <a name="web-app"></a>Webbapp

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du Skapa en > **resurswebbapp** > **Web App** **Create a resource**

1. Ange eller välj den här informationen i Skapa webbapp - Grunderna:

   > [!div class="mx-imgBorder"]
   >![Grundläggande webbapp][6]

1. Välj **"Granska + skapa"**

1. När valideringen skickade meddelandet klickar du på **"Skapa"**

### <a name="create-the-private-endpoint"></a>Skapa den privata slutpunkten

1. I egenskaperna för Webbapp väljer du **Inställningar** > **nätverk** och klickar på **"Konfigurera dina privata slutpunktsanslutningar"**

   > [!div class="mx-imgBorder"]
   >![Nätverk för webbapp][7]

1. Klicka på **"+ lägg till" i** guiden

   > [!div class="mx-imgBorder"]
   >![Privat slutpunkt för webbapp][8]

1. Fyll i information om prenumeration, virtuella nätverk och undernät och klicka på **"OK"**

   > [!div class="mx-imgBorder"]
   >![Nätverk för webbappar][9]

1. Granska skapandet av den privata slutpunkten

   > [!div class="mx-imgBorder"]
   >![Granska][10]
   >![slutvyn för slutpunkten för privat][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

1. I portalens sökfält anger du **myVm**
1. Välj **knappen Anslut**. När du har valt knappen Anslut öppnas knappen Anslut till virtuell dator, väljer **RDP**

   > [!div class="mx-imgBorder"]
   >![Rdp-knapp][12]

1. Azure skapar en fil för Fjärrskrivbordsprotokoll (.rdp) och hämtar den till datorn när du klickar på **Hämta RDP-fil**

   > [!div class="mx-imgBorder"]
   >![Ladda ned RDP-fil][13]

1. Öppna filen downloaded.rdp.

- Välj Anslut om du uppmanas att göra det.
- Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

> [!Note]
> Du kan behöva välja Fler alternativ > Använda ett annat konto för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

- Välj OK.

1. Du kan få en certifikatvarning under inloggningen. Välj Ja eller Fortsätt om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

## <a name="access-web-app-privately-from-the-vm"></a>Komma åt Web App privat från den virtuella datorn

I det här avsnittet ansluter du privat till webbappen med hjälp av den privata slutpunkten.

1. Hämta den privata IP-adressen för din privata slutpunkt, i sökfältet skriver **Privat länk**och väljer Privat länk

   > [!div class="mx-imgBorder"]
   >![Private Link][14]

1. I Private Link Center väljer du **Privata slutpunkter** för att lista alla dina privata slutpunkter

   > [!div class="mx-imgBorder"]
   >![Privat länkcenter][15]

1. Välj länken Privat slutpunkt till webbappen och undernätet

   > [!div class="mx-imgBorder"]
   >![Egenskaper för privat slutpunkt][16]

1. Kopiera den privata IP-adressen för din privata slutpunkt och FQDN i din webbapp, i vårt fall webappdemope.azurewebsites.net 10.10.2.4

1. Kontrollera att webbappen inte är tillgänglig via den offentliga IP-adressen i myVM. Öppna en webbläsare och klistra in Web App namn, måste du ha en 403 förbjuden felsida

   > [!div class="mx-imgBorder"]
   >![Förbjudet][17]

> [!Important]
> Eftersom den här funktionen är i förhandsgranskningen måste du manuellt hantera DNS-posten.

1. Skapa värdposten, öppna utforskaren och leta reda på hosts-filen

   > [!div class="mx-imgBorder"]
   >![Värdfil][18]

1. Lägga till en post med den privata IP-adressen och det offentliga namnet på din Web App genom att redigera hosts-filen med anteckningar

   > [!div class="mx-imgBorder"]
   >![Värdar innehåll][19]

1. Spara filen

1. Öppna en webbläsare och skriv webbadressen till webbappen

   > [!div class="mx-imgBorder"]
   >![Webbplats med PE][20]

1. Du ansluter till din webbapp via den privata slutpunkten

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den privata slutpunkten, Webbappen och den virtuella datorn tar du bort resursgruppen och alla resurser som den innehåller:

1. Ange färdiga rg i sökrutan högst upp på portalen och välj färdig-rg från sökresultaten.
1. Välj Ta bort resursgrupp.
1. Ange färdigt för TYP RESURSGRUPPNAMN och välj Ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en virtuell dator i ett virtuellt nätverk, en webbapp och en privat slutpunkt. Du är ansluten till en virtuell dator från Internet och kommunicerade säkert till webbappen med hjälp av Private Link. Mer information om privat slutpunkt finns i [Vad är Azure Private Endpoint][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
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

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
