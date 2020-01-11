---
title: Skapa en privat slut punkt för säkra anslutningar
titleSuffix: Azure Cognitive Search
description: För närvarande i för hands version kan du begränsa åtkomsten till en Sök tjänst slut punkt med hjälp av privat slut punkt och en säker VNet-anslutning.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865017"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>Begränsa åtkomsten till Azure Kognitiv sökning med hjälp av privat slut punkt och en virtuell nätverks anslutning.

> [!IMPORTANT] 
> Stöd för privat slut punkt är för närvarande tillgängligt som en för hands version av begränsad åtkomst. Den här för hands versionen är bara tillgänglig för search Services på **Basic-nivån**.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2019-10-01 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande ingen portal eller .NET SDK-support.

I den här artikeln lär du dig hur du skapar en ny Sök tjänst som är tillgänglig via säkra anslutningar, utan åtkomst från offentliga IP-adresser. Klient anslutningar är tillåtna från virtuella Azure-datorer som distribueras i samma virtuella nätverk som tjänsten.

## <a name="about-private-endpoint-support"></a>Om stöd för privata slut punkter

[Privata slut punkter](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåta en klient i ett virtuellt nätverk att på ett säkert sätt få åtkomst till data via en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från det [virtuella nätverkets adress utrymme](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) för Sök tjänsten. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponeringen för det offentliga Internet. För en lista över PaaS-tjänster som stöder privat länk, se [avsnittet tillgänglighet](../private-link/private-link-overview.md#availability) i produkt dokumentationen.

Med en privat slut punkt för Sök tjänsten kan du:

+ Blockera alla anslutningar på den offentliga slut punkten för Sök tjänsten.
+ Öka säkerheten för det virtuella nätverket genom att göra det möjligt att blockera exfiltrering av data från det virtuella nätverket.
+ Anslut säkert till din Sök tjänst från lokala nätverk som ansluter till det virtuella nätverket med hjälp av [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-locations.md) med privat peering.

> [!NOTE]
> När tjänstens slut punkt är privat är vissa Portal funktioner inaktiverade. Du kan visa och hantera information om service nivå, men Portal åtkomst till index data och de olika komponenterna i tjänsten, till exempel index, indexerare och färdigheter definitioner, är begränsad av säkerhets skäl.

## <a name="request-access"></a>Begär åtkomst 

Klicka på [begär åtkomst](https://aka.ms/SearchPrivateLinkRequestAccess) för att registrera dig för den här förhands gransknings funktionen. Formuläret efterfrågar information om dig, ditt företag och den allmänna nätverk sto pol Ogin. När vi har granskat din begäran får du ett bekräftelse meddelande med ytterligare instruktioner.

## <a name="create-a-vm"></a>Skapa en virtuell dator
I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät som är värd för den virtuella datorn som ska användas för att få åtkomst till Sök tjänstens privata slut punkt.

### <a name="set-up-the-virtual-network"></a>Konfigurera det virtuella nätverket
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Längst upp till vänster väljer du **skapa en resurs** > **nätverk** > **virtuella nätverk**.
1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork* |
    | Adressutrymme | Ange *10.1.0.0/16* |
    | Prenumeration | Välj din prenumeration|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup*och välj sedan **OK** |
    | Location | Välj **västra USA** eller vilken region du använder|
    | Undernät – Namn | Ange *undernät* |
    | Undernät – adressintervall | Ange *10.1.0.0/24* |
    |||

1. Lämna resten som standard och välj **skapa**.


### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **skapa en resurs** > **Compute** > **virtuell dator**överst till vänster på huvud Portal sidan.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.  |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **USA, västra** eller vilken region du använder. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Bild | Välj **Windows Server 2019 Data Center**. |
    | Storlek | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användar namn som du väljer. |
    | lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |
    |||

1. Välj **Nästa: diskar**.

1. Lämna standardvärdena i **skapa en virtuell dator – diskar**och välj **Nästa: nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna standard **MyVirtualNetwork**.  |
    | Adressutrymme | Lämna standard **10.1.0.0/24**.|
    | Undernät | Lämna standard **under nätet (10.1.0.0/24)** .|
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    ||

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.

1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.


## <a name="create-your-search-service-with-a-private-endpoint"></a>Skapa din Sök tjänst med en privat slut punkt

I det här avsnittet ska du skapa en ny Azure Kognitiv sökning-tjänst med en privat slut punkt. 

1. På den övre vänstra delen av huvud Portal sidan väljer du **skapa en resurs** > webb > Azure **-** **kognitiv sökning**.

1. I **nya search service – grunderna**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | URL | Ange ett unikt namn. |
    | Location | Välj den region som du angav när du begär åtkomst till den här för hands versions funktionen. |
    | Prisnivå | Välj **ändra pris nivå** och välj **Basic**. Den här nivån krävs för för hands versionen. |
    |||
  
1. Välj **Nästa: skala**.

1. Lämna värdena som standard och välj **Nästa: nätverk**.

1. I **ny search service-nätverk**väljer du **privat** för **slut punkts anslutning (data)** .

1. I **ny search service-nätverk**väljer du **+ Lägg till** under **privat slut punkt**. 

1. I **skapa privat slut punkt**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | Location | Välj **västra USA**.|
    | Namn | Ange *myPrivateEndpoint*.  |
    | Mål under resurs | Lämna standard **searchService**. |
    | **NÄTVERK** |  |
    | Virtuellt nätverk  | Välj *MyVirtualNetwork* från resurs gruppen *myResourceGroup*. |
    | Undernät | Välj *undernät*. |
    | **PRIVAT DNS-INTEGRERING** |  |
    | Integrera med privat DNS-zon  | Låt standardinställningen vara **Ja**. |
    | Privat DNS-zon  | Lämna standardvärdet * * (New) privatelink.search.windows.net * *. |
    |||

1. Välj **OK**. 

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen. 

1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**. 
1. När tjänsten har skapats bläddrar du till den resurs som du nyss skapade.
1. Välj **nycklar** på menyn till vänster innehåll.
1. Kopiera den **primära administratörs nyckeln** för användning i nästa steg.

 
## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Anslut till VM- *myVm* från Internet på följande sätt:

1. Skriv *myVm*i portalens Sök fält.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade RDP *-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikat varning när du loggar in. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  


## <a name="access-the-search-service-privately-from-the-vm"></a>Få åtkomst till Sök tjänsten privat från den virtuella datorn

I det här avsnittet ska du verifiera privat nätverks åtkomst till Sök tjänsten och ansluta privat till lagrings kontot med hjälp av den privata slut punkten.

1. Öppna PowerShell i fjärr skrivbordet för *myVM*.

1. Ange ' nslookup [search service Name]. search. Windows. net '

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```
1. Följ den här [snabb](search-get-started-postman.md) starten från den virtuella datorn för att skapa ett nytt Sök index i din tjänst i Postman med hjälp av REST API.

1. Prova flera av dessa förfrågningar i Postman på din lokala arbets Station.

1. Om du kan slutföra snabb starten från den virtuella datorn, men får ett fel meddelande om att fjärrservern inte finns på din lokala arbets Station, har du konfigurerat en privat slut punkt för Sök tjänsten.

1. Stäng fjärr skrivbords anslutningen till *myVM*. 

## <a name="clean-up-resources"></a>Rensa resurser 

När du är klar med den privata slut punkten, Sök tjänst kontot och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller: 

1. Ange *myResourceGroup* i rutan **Sök** högst upp i portalen och välj *myResourceGroup* från Sök resultaten. 
1. Välj **Ta bort resursgrupp**. 
1. Ange *myResourceGroup* för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator i ett virtuellt nätverk och en Sök tjänst med en privat slut punkt. Du har anslutit till den virtuella datorn från Internet och kommunicerat på ett säkert sätt till Sök tjänsten med hjälp av en privat länk. 

> [!div class="nextstepaction"]
> [Vad är en privat Azure-slutpunkt?](../private-link/private-endpoint-overview.md).
