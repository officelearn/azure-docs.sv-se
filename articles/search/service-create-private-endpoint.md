---
title: Skapa en privat slut punkt för en säker anslutning
titleSuffix: Azure Cognitive Search
description: Konfigurera en privat slut punkt i ett virtuellt nätverk för en säker anslutning till en Azure Kognitiv sökning-tjänst
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: cfa8db0d00f351f5ab2bda96744305ca83cccb19
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922455"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Skapa en privat slut punkt för en säker anslutning till Azure Kognitiv sökning (för hands version)

[Privata slut punkter](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåta en klient i ett virtuellt nätverk att på ett säkert sätt få åtkomst till data i ett Sök index över en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från det [virtuella nätverkets adress utrymme](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) för Sök tjänsten. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. För en lista över andra PaaS-tjänster som stöder privat länk, se [avsnittet tillgänglighet](../private-link/private-link-overview.md#availability) i produkt dokumentationen.

> [!Important]
> Stöd för privata slut punkter för Azure Kognitiv sökning är tillgängligt som en för hands version med begränsad åtkomst och är för närvarande inte avsett för användning i produktion. Fyll i och skicka [formuläret](https://aka.ms/SearchPrivateLinkRequestAccess) åtkomstbegäran om du vill ha åtkomst till förhands granskningen. Formuläret efterfrågar information om dig, ditt företag och den allmänna program arkitekturen. När vi har granskat din begäran får du ett bekräftelse meddelande med ytterligare instruktioner.
>
> När du har beviljats åtkomst till för hands versionen kan du konfigurera privata slut punkter för tjänsten med hjälp av Azure Portal och REST API version [2019-10-06 – för hands](search-api-preview.md)version.
>   

Med privata slut punkter för Sök tjänsten kan du:

- Blockera alla anslutningar på den offentliga slut punkten för Sök tjänsten.
- Öka säkerheten för det virtuella nätverket genom att göra det möjligt att blockera exfiltrering av data från det virtuella nätverket.
- Anslut säkert till din Sök tjänst från lokala nätverk som ansluter till det virtuella nätverket med hjälp av [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-locations.md) med privat peering.

> [!NOTE]
> Det finns för närvarande vissa begränsningar i för hands versionen som du bör vara medveten om:
> * Endast tillgängligt för search Services på **Basic** -nivån. 
> * Tillgängligt i västra USA 2, västra centrala USA, östra USA, södra centrala USA, östra Australien och sydöstra Australien-regioner.
> * När tjänstens slut punkt är privat är vissa Portal funktioner inaktiverade. Du kan visa och hantera information om service nivå, men Portal åtkomst till index data och de olika komponenterna i tjänsten, till exempel index, indexerare och färdigheter definitioner, är begränsad av säkerhets skäl.
> * När tjänstens slut punkt är privat måste du använda Sök-API: et för att ladda upp dokument till indexet.
> * Du måste använda följande länk för att se alternativet för privat slut punkt support i Azure Portal: https://portal.azure.com/?feature.enablePrivateEndpoints=true

I den här artikeln får du lära dig hur du använder portalen för att skapa en ny Azure Kognitiv sökning-tjänstinstans som inte går att komma åt via en offentlig IP-adress, konfigurerar en virtuell Azure-dator i samma virtuella nätverk och använder den för att få åtkomst till Sök tjänsten via en privat Endpoint.


## <a name="create-a-vm"></a>Skapa en virtuell dator
I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät som är värd för den virtuella datorn som ska användas för att få åtkomst till Sök tjänstens privata slut punkt.

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

1. På fliken Azure Portal start väljer du **skapa en resurs** > **nätverk** > **virtuella nätverk**.

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


## <a name="create-your-search-service-with-a-private-endpoint"></a>Skapa din Sök tjänst med en privat slut punkt

I det här avsnittet ska du skapa en ny Azure Kognitiv sökning-tjänst med en privat slut punkt. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **Web** > **Azure kognitiv sökning**.

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

1. När etableringen av den nya tjänsten har slutförts bläddrar du till den resurs som du nyss skapade.

1. Välj **nycklar** på menyn till vänster innehåll.

1. Kopiera den **primära administratörs nyckeln** för senare.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs** > **Compute** > **virtuell dator**.

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
    | Offentliga inkommande portar | Lämna standardvärdet **Tillåt valda portar**. |
    | Välj inkommande portar | Lämna standardvärdet för **RDP (3389)** . |
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

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  


## <a name="access-the-search-service-privately-from-the-vm"></a>Få åtkomst till Sök tjänsten privat från den virtuella datorn

I det här avsnittet ska du verifiera privat nätverks åtkomst till Sök tjänsten och ansluta privat till med hjälp av den privata slut punkten.

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
1. Följ den här [snabb](search-get-started-postman.md) starten från den virtuella datorn för att skapa ett nytt Sök index i din tjänst i Postman med hjälp av REST API.  Använd den nyckel som du kopierade i föregående steg för att autentisera till tjänsten.

1. Prova flera av dessa förfrågningar i Postman på din lokala arbets Station.

1. Om du kan slutföra snabb starten från den virtuella datorn, men får ett fel meddelande om att fjärrservern inte finns på din lokala arbets Station, har du konfigurerat en privat slut punkt för Sök tjänsten.

1. Stäng fjärr skrivbords anslutningen till *myVM*. 


## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten, Sök tjänsten och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller:
1. Ange *myResourceGroup* i rutan **Sök** högst upp i portalen och välj *myResourceGroup* från Sök resultaten. 
1. Välj **Ta bort resursgrupp**. 
1. Ange *myResourceGroup* för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en virtuell dator i ett virtuellt nätverk och en Sök tjänst med en privat slut punkt. Du har anslutit till den virtuella datorn från Internet och kommunicerat på ett säkert sätt till Sök tjänsten med hjälp av en privat länk. Mer information om privata slut punkter finns i [Vad är Azures privata slut punkt?](../private-link/private-endpoint-overview.md).
