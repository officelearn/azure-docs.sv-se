---
title: Skapa en privat slut punkt för en säker anslutning
titleSuffix: Azure Cognitive Search
description: Konfigurera en privat slut punkt i ett virtuellt nätverk för en säker anslutning till en Azure Kognitiv sökning-tjänst
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 0945743fb2cf3e37345ff562250e48511944cee6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125561"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Skapa en privat slut punkt för en säker anslutning till Azure Kognitiv sökning

I den här artikeln använder du Azure Portal för att skapa en ny Azure Kognitiv sökning-tjänstinstans som inte kan nås via Internet. Sedan konfigurerar du en virtuell Azure-dator i samma virtuella nätverk och använder den för att få åtkomst till Sök tjänsten via en privat slut punkt.

> [!Important]
> Stöd för privata slut punkter för Azure Kognitiv sökning kan konfigureras med hjälp av Azure Portal eller [hanterings REST API version 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/). När tjänstens slut punkt är privat är vissa Portal funktioner inaktiverade. Du kan visa och hantera information om service nivå, men Portal åtkomst till index data och de olika komponenterna i tjänsten, till exempel index, indexerare och färdigheter definitioner, är begränsad av säkerhets skäl.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Varför ska jag använda en privat slut punkt för säker åtkomst?

[Privata slut punkter](../private-link/private-endpoint-overview.md) för Azure kognitiv sökning tillåta en klient i ett virtuellt nätverk att på ett säkert sätt få åtkomst till data i ett Sök index över en [privat länk](../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från det [virtuella nätverkets adress utrymme](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) för Sök tjänsten. Nätverks trafiken mellan klienten och Sök tjänsten passerar över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. För en lista över andra PaaS-tjänster som stöder privat länk, se [avsnittet tillgänglighet](../private-link/private-link-overview.md#availability) i produkt dokumentationen.

Med privata slut punkter för Sök tjänsten kan du:

- Blockera alla anslutningar på den offentliga slut punkten för Sök tjänsten.
- Öka säkerheten för det virtuella nätverket genom att göra det möjligt att blockera exfiltrering av data från det virtuella nätverket.
- Anslut säkert till din Sök tjänst från lokala nätverk som ansluter till det virtuella nätverket med hjälp av [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../expressroute/expressroute-locations.md) med privat peering.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet ska du skapa ett virtuellt nätverk och ett undernät som är värd för den virtuella datorn som ska användas för att få åtkomst till Sök tjänstens privata slut punkt.

1. På fliken Azure Portal start väljer du **skapa en resurs**  >  **nätverk**  >  **virtuellt nätverk**.

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup*och välj sedan **OK** |
    | Name | Ange *MyVirtualNetwork* |
    | Region | Välj önskad region |
    |||

1. Lämna standardinställningarna för resten av inställningarna. Klicka på **Granska + skapa** och sedan på **skapa**

## <a name="create-a-search-service-with-a-private-endpoint"></a>Skapa en Sök tjänst med en privat slut punkt

I det här avsnittet ska du skapa en ny Azure Kognitiv sökning-tjänst med en privat slut punkt. 

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **Web**  >  **Azure-kognitiv sökning**.

1. I **nya search service – grunderna**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | URL | Ange ett unikt namn. |
    | Plats | Välj önskad region. |
    | Prisnivå | Välj **pris nivå för ändring** och välj önskad tjänst nivå. (Stöds inte på den **kostnads fria** nivån. Måste vara **Basic** eller högre.) |
    |||
  
1. Välj **Nästa: skala**.

1. Lämna värdena som standard och välj **Nästa: nätverk**.

1. I **ny search service-nätverk**väljer du **privat** för **slut punkts anslutning (data)**.

1. I **ny search service-nätverk**väljer du **+ Lägg till** under **privat slut punkt**. 

1. I **skapa privat slut punkt**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt.|
    | Plats | Välj **USA, västra**.|
    | Name | Ange *myPrivateEndpoint*.  |
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

1. Kopiera den **primära administratörs nyckeln** för senare när du ansluter till tjänsten.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure Portal väljer du **skapa en resurs**  >  **beräknings**  >  **virtuell dator**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställningen | Värde |
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
    | lösenordsinställning | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna standardvärdet **Tillåt valda portar**. |
    | Välj inkommande portar | Lämna standardvärdet för **RDP (3389)**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |
    |||

1. Välj **Nästa: diskar**.

1. Lämna standardvärdena i **skapa en virtuell dator – diskar**och välj **Nästa: nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställningen | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna standard **MyVirtualNetwork**.  |
    | Adressutrymme | Lämna standard **10.1.0.0/24**.|
    | Undernät | Lämna standard **under nätet (10.1.0.0/24)**.|
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    ||

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.

1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**. 


## <a name="connect-to-the-vm"></a>Anslut till VM:en

Ladda ned och Anslut till VM- *myVm* på följande sätt:

1. Skriv *myVm*i portalens Sök fält.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade RDP *-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  


## <a name="test-connections"></a>Test anslutningar

I det här avsnittet ska du verifiera privat nätverks åtkomst till Sök tjänsten och ansluta privat till med hjälp av den privata slut punkten.

När Sök tjänstens slut punkt är privat är vissa Portal funktioner inaktiverade. Du kan visa och hantera inställningar för service nivå, men Portal åtkomst till index data och andra komponenter i tjänsten, till exempel index, indexerare och färdigheter definitioner, är begränsad av säkerhets skäl.

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

1. Från den virtuella datorn ansluter du till Sök tjänsten och skapar ett index. Du kan följa den här [snabb](search-get-started-postman.md) starten för att skapa ett nytt Sök index i din tjänst i Postman med hjälp av REST API. Att ställa in begär Anden från Postman kräver Sök tjänstens slut punkt (https://[Sök tjänstens namn]. search. Windows. net) och den Admin API-nyckel som du kopierade i föregående steg.

1. Att slutföra snabb starten från den virtuella datorn är din bekräftelse på att tjänsten är fullt fungerande.

1. Stäng fjärr skrivbords anslutningen till *myVM*. 

1. Du kan kontrol lera att tjänsten inte är tillgänglig på en offentlig slut punkt genom att öppna Postman på den lokala arbets stationen och försöka utföra de första uppgifterna i snabb starten. Om du får ett fel meddelande om att fjärrservern inte finns har du konfigurerat en privat slut punkt för Sök tjänsten.

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten, Sök tjänsten och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller:
1. Skriv *myResourceGroup*   i sökrutan längst upp i portalen och välj **Search**  *myResourceGroup*   från Sök resultaten. 
1. Välj **Ta bort resursgrupp**. 
1. Ange *myResourceGroup*   för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en virtuell dator i ett virtuellt nätverk och en Sök tjänst med en privat slut punkt. Du har anslutit till den virtuella datorn från Internet och kommunicerat på ett säkert sätt till Sök tjänsten med hjälp av en privat länk. Mer information om privata slut punkter finns i [Vad är Azures privata slut punkt?](../private-link/private-endpoint-overview.md).
