---
title: Anslut privat till en webbapp med hjälp av privat Azure-slutpunkt
description: Anslut privat till en webbapp med hjälp av privat Azure-slutpunkt
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: bb78536326885e043279de1ff77e6e8efcd95193
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037155"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Anslut privat till en webbapp med hjälp av Azures privata slut punkt (för hands version)

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Det gör att du kan ansluta privat till din webbapp.
I den här snabb starten får du lära dig hur du distribuerar en webbapp med privat slut punkt och ansluter till den här webbappen från en virtuell dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Virtuellt nätverk och virtuell dator

I det här avsnittet ska du skapa ett virtuellt nätverk och under nätet som är värd för den virtuella datorn som används för att komma åt din webbapp via den privata slut punkten.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs** > **nätverk** > **virtuellt nätverk** eller söker efter **virtuellt nätverk** i sökrutan.

1. I **Skapa virtuellt nätverk**anger eller väljer du den här informationen på fliken grundläggande:

 ![Skapa Virtual Network][1]

1. Klicka på **Nästa: IP-adresser >** och ange eller Välj den här informationen:

![Konfigurera IP-adresser][2]

1. I avsnittet undernät klickar du på **"+ Lägg till undernät"** och anger följande information och klickar på **"Lägg till"**

![Lägg till undernät][3]

1. Klicka på **Granska + skapa**

1. När verifieringen har slutförts klickar du på **"skapa"**

### <a name="create-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **Compute** > **virtuell dator**

1. I skapa en virtuell dator – grunderna anger eller väljer du den här informationen:

![Bas för virtuell dator ][4]

1. Välj **Nästa: diskar**

Behåll standardinställningarna.

1. Välj **"Nästa: nätverk"** och välj den här informationen:

![Nätverk ][5]

1. Klicka på **Granska + skapa**

1. När meddelandet verifieringen skickades klickar du på **"skapa"**

## <a name="create-your-web-app-and-private-endpoint"></a>Skapa din webbapp och privat slut punkt

I det här avsnittet ska du skapa en privat webbapp med en privat slut punkt till den.

> [!Note]
>Den privata slut punkts funktionen är bara tillgänglig för Premium v2 och isolerad med extern ASE SKU

### <a name="web-app"></a>Webbapp

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **Web** >  **-** webbapp

1. I skapa webbapp – grunderna anger eller väljer du den här informationen:

![Web App Basic ][6]

1. Välj **"granska + skapa"**

1. När meddelandet verifieringen skickades klickar du på **"skapa"**

### <a name="create-the-private-endpoint"></a>Skapa den privata slut punkten

1. I egenskaperna för webbappen väljer du **inställningar** > **nätverk** och klickar på **"Konfigurera dina privata slut punkts anslutningar"**

![Webb program nätverk][7]

1. I guiden klickar du på **"+ Lägg till"**

![Privat slut punkt för webbapp][8]

1. Fyll i informationen om prenumeration, VNet och undernät och klicka på **OK**

![Webb program nätverk][9]

1. Granska skapandet av den privata slut punkten

![granska][10]
![slutlig vy av den privata slut punkten][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från internet

1. I portalens Sök fält anger du **myVm**
1. Välj **knappen Anslut**. När du har valt knappen Anslut öppnas Anslut till virtuell dator, Välj **RDP**

![RDP-knapp][12]

1. Azure skapar en Remote Desktop Protocol-fil (. RDP) och laddar ned den till datorn när du har klickat på **Hämta RDP-fil**

![Ladda ned RDP-fil][13]

1. Öppna den nedladdade RDP-filen.

- Om du uppmanas väljer du Anslut.
- Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

> [!Note]
> Du kan behöva välja fler alternativ > använda ett annat konto för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

- Välj OK.

1. Du kan få en certifikatvarning under inloggningen. Om du får en certifikat varning väljer du Ja eller Fortsätt.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

## <a name="access-web-app-privately-from-the-vm"></a>Få till gång till webb program privat från den virtuella datorn

I det här avsnittet ska du ansluta privat till webbappen med hjälp av den privata slut punkten.

1. Hämta den privata IP-adressen för din privata slut punkt, i Sök fältet typ, **privat länk**och välj privat länk

![Private Link][14]

1. I det privata länk centret väljer du **privata slut punkter** för att visa en lista över alla dina privata slut punkter

![Privat länk Center][15]

1. Välj den privata slut punkts länken till din webbapp och under nätet

![Egenskaper för privat slut punkt][16]

1. Kopiera den privata IP-adressen för din privata slut punkt och det fullständiga domän namnet för din webbapp, i vårt fall webappdemope.azurewebsites.net 10.10.2.4

1. I myVM kontrollerar du att webbappen inte är tillgänglig via den offentliga IP-adressen. Öppna en webbläsare och kopiera webbappens namn. du måste ha en 403-sida med otillåtna fel

![Förbjudet][17]

> [!Note]
> Eftersom den här funktionen finns i för hands version måste du hantera DNS-posten manuellt.

1. Skapa värd posten, öppna Utforskaren och leta upp värd filen

![Värd fil][18]

1. Lägg till en post med den privata IP-adressen och det offentliga namnet på din webbapp genom att redigera hosts-filen med anteckningar

![Värd innehåll][19]

1. Spara filen

1. Öppna en webbläsare och ange URL: en för din webbapp

![Webbplats med PE][20]

1. Du ansluter till din webbapp via den privata slut punkten

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den privata slut punkten, webbappen och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller:

1. Ange Ready-rg i rutan Sök högst upp i portalen och välj redo-RG från Sök resultaten.
1. Välj Ta bort resurs grupp.
1. Ange ett klart-RG för Skriv resurs GRUPPens namn och välj Ta bort.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en virtuell dator i ett virtuellt nätverk, en webbapp och en privat slut punkt. Du är ansluten till en virtuell dator från Internet och kommunicerat på ett säkert sätt till webbappen med hjälp av en privat länk. Mer information om privat slut punkt finns i [Vad är en privat Azure-slutpunkt][privateendpoint].

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
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
