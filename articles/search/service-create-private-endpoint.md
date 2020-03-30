---
title: Skapa en privat slutpunkt för en säker anslutning
titleSuffix: Azure Cognitive Search
description: Konfigurera en privat slutpunkt i ett virtuellt nätverk för en säker anslutning till en Azure Cognitive Search-tjänst
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945828"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Skapa en privat slutpunkt för en säker anslutning till Azure Cognitive Search (förhandsversion)

I den här artikeln använder du portalen för att skapa en ny Azure Cognitive Search-tjänstinstans som inte kan nås via en offentlig IP-adress. Konfigurera sedan en virtuell Azure-dator i samma virtuella nätverk och använda den för att komma åt söktjänsten via en privat slutpunkt.

> [!Important]
> Privat slutpunktsstöd för Azure Cognitive Search är tillgängligt [på begäran](https://aka.ms/SearchPrivateLinkRequestAccess) som en förhandsversion med begränsad åtkomst. Förhandsversionsfunktioner tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> När du har beviljats åtkomst till förhandsversionen kan du konfigurera privata slutpunkter för din tjänst med hjälp av Azure-portalen eller [HANDLÄGGNINGS-API-versionen 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Varför använda Privat slutpunkt för säker åtkomst?

[Privata slutpunkter](../private-link/private-endpoint-overview.md) för Azure Cognitive Search gör det möjligt för en klient i ett virtuellt nätverk att på ett säkert sätt komma åt data i ett sökindex via en [privat länk](../private-link/private-link-overview.md). Den privata slutpunkten använder en IP-adress från det [virtuella nätverksadressutrymmet](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) för söktjänsten. Nätverkstrafik mellan klienten och söktjänsten passerar över det virtuella nätverket och en privat länk i Microsofts stamnät, vilket eliminerar exponering från det offentliga internet. En lista över andra PaaS-tjänster som stöder Private Link finns i [avsnittet tillgänglighet](../private-link/private-link-overview.md#availability) i produktdokumentationen.

Med privata slutpunkter för söktjänsten kan du:

- Blockera alla anslutningar på den offentliga slutpunkten för söktjänsten.
- Öka säkerheten för det virtuella nätverket genom att du kan blockera exfiltration av data från det virtuella nätverket.
- Anslut till söktjänsten på ett säkert sätt från lokala nätverk som ansluter till det virtuella nätverket med HJÄLP AV [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoutes](../expressroute/expressroute-locations.md) med privat peering.

> [!NOTE]
> Det finns för närvarande vissa begränsningar i förhandsgranskningen som du bör vara medveten om:
> * Endast tillgängligt för söktjänster på **basic-nivån.** 
> * Finns i regionerna Västra USA 2, Västra centrala USA, östra USA, södra centrala USA, Östra Australien och Australien.
> * När tjänstslutpunkten är privat inaktiveras vissa portalfunktioner. Du kan visa och hantera information på servicenivå, men portalåtkomst till indexdata och de olika komponenterna i tjänsten, till exempel index-, indexerings- och kompetensdefinitionerna, är begränsad av säkerhetsskäl.
> * När tjänstslutpunkten är privat måste du använda [REST-API:et](https://docs.microsoft.com/rest/api/searchservice/) för sök för att överföra dokument till indexet.
> * Du måste använda följande länk för att se det privata slutpunktssupportalternativet i Azure-portalen:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Begär åtkomst 

Klicka [på Begär åtkomst](https://aka.ms/SearchPrivateLinkRequestAccess) för att registrera dig för den här förhandsgranskningsfunktionen. Formuläret begär information om dig, ditt företag och den allmänna nätverkstopologin. När vi har granskat din begäran får du ett e-postmeddelande med ytterligare instruktioner.

## <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

I det här avsnittet skapar du ett virtuellt nätverk och undernät som är värd för den virtuella datorn som ska användas för att komma åt söktjänstens privata slutpunkt.

1. På fliken Start för Azure-portalen väljer du **Skapa ett virtuellt nätverk för nätverk** > **för** > **Virtual network**nätverk .

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *MyVirtualNetwork* |
    | Adressutrymme | Ange *10.1.0.0/16* |
    | Prenumeration | Välj din prenumeration|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup*och välj sedan **OK** |
    | Location | Välj **Västra USA** eller vilken region du använder|
    | Undernät – Namn | Ange *mySubnet* |
    | Undernät – adressintervall | Ange *10.1.0.0/24* |
    |||

1. Lämna resten som standard och välj **Skapa**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Skapa en söktjänst med en privat slutpunkt

I det här avsnittet skapar du en ny Azure Cognitive Search-tjänst med en privat slutpunkt. 

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du Skapa en > **resurswebb** > **Azure Cognitive Search**. **Create a resource**

1. I **Ny söktjänst - Grunderna**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.|
    | **INSTANSINFORMATION** |  |
    | URL | Ange ett unikt namn. |
    | Location | Välj den region som du angav när du begärde åtkomst till den här förhandsgranskningsfunktionen. |
    | Prisnivå | Välj **Ändra prisnivå** och välj **Grundläggande**. Den här nivån krävs för förhandsgranskningen. |
    |||
  
1. Välj **Nästa: Skala**.

1. Lämna värdena som standard och välj **Nästa: Nätverk .**

1. I **Ny söktjänst - Nätverk**väljer du **Privat** för **slutpunktsanslutning(data)**.

1. I **Ny söktjänst - Nätverk**väljer du + Lägg **till** under **Privat slutpunkt**. 

1. Ange eller välj den här informationen i **Skapa privat slutpunkt:**

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.|
    | Location | Välj **USA, västra**.|
    | Namn | Ange *myPrivateEndpoint*.  |
    | Målunderresurs | Lämna **standardsökningstjänsten**. |
    | **Nätverk** |  |
    | Virtuellt nätverk  | Välj *MyVirtualNetwork* från resursgruppen *myResourceGroup*. |
    | Undernät | Välj *mySubnet*. |
    | **PRIVAT DNS-INTEGRERING** |  |
    | Integrera med privat DNS-zon  | Lämna standard **Ja**. |
    | Privat DNS-zon  | Lämna standard ** (Ny) privatelink.search.windows.net**. |
    |||

1. Välj **OK**. 

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration. 

1. När meddelandet **Validering har skickats** väljer du **Skapa**. 

1. När etableringen av den nya tjänsten är klar bläddrar du till den resurs som du just skapade.

1. Välj **Tangenter** på menyn för vänster innehåll.

1. Kopiera den **primära administratörsnyckeln** för senare, när du ansluter till tjänsten.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. På den övre vänstra sidan av skärmen i Azure-portalen väljer du Skapa en > **virtuell**dator**för resursberäknning** **Create a resource** > .

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade detta i föregående avsnitt.  |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm*. |
    | Region | Välj **Västra USA** eller vilken region du använder. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Bild | Välj **Windows Server 2019 Datacenter**. |
    | Storlek | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användarnamn som du väljer. |
    | lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna **standardvärdet Tillåt valda portar**. |
    | Välj inkommande portar | Lämna standard **RDP (3389)**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |
    |||

1. Välj **Nästa: Diskar**.

1. I **Skapa en virtuell dator - Diskar**, lämnar du standardvärdena och väljer **Nästa: Nätverk .**

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna **standardvärdet MyVirtualNetwork**.  |
    | Adressutrymme | Lämna **standardvärdet 10.1.0.0/24**.|
    | Undernät | Lämna standard **mySubnet (10.1.0.0/24)**.|
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.|
    ||

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration.

1. När meddelandet **Validering har skickats** väljer du **Skapa**. 


## <a name="connect-to-the-vm"></a>Anslut till VM:en

Ladda ner och anslut sedan till VM *myVm* enligt följande:

1. I portalens sökfält anger du *myVm*.

1. Välj knappen **Anslut**. När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna filen downloaded.rdp*.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.  


## <a name="test-connections"></a>Testa anslutningar

I det här avsnittet verifierar du privat nätverksåtkomst till söktjänsten och ansluter privat till den privata slutpunkten.

Återkalla från inledningen att alla interaktioner med söktjänsten kräver [REST API för sökstöd](https://docs.microsoft.com/rest/api/searchservice/). Portalen och .NET SDK stöds inte i den här förhandsversionen.

1. Öppna PowerShell i fjärrskrivbordet *för myVM.*

1. Ange 'nslookup [söktjänstnamn].search.windows.net'

    Du får ett meddelande som liknar detta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Anslut till söktjänsten från den virtuella datorn och skapa ett index. Du kan följa den här [snabbstarten](search-get-started-postman.md) för att skapa ett nytt sökindex i tjänsten i Postman med REST API. För att konfigurera begäranden från Postman krävs slutpunkten för söktjänsten (https://[search service name].search.windows.net) och den api-nyckel för admin som du kopierade i ett tidigare steg.

1. Slutföra snabbstart från den virtuella datorn är din bekräftelse på att tjänsten är i full drift.

1. Stäng fjärrskrivbordsanslutningen till *myVM*. 

1. Om du vill kontrollera att tjänsten inte är tillgänglig på en offentlig slutpunkt öppnar du Postman på din lokala arbetsstation och försöker utföra de första flera aktiviteterna i snabbstarten. Om du får ett felmeddelande om att fjärrservern inte finns har du konfigurerat en privat slutpunkt för söktjänsten.

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slutpunkten, söktjänsten och den virtuella datorn tar du bort resursgruppen och alla resurser som den innehåller:
1. Skriv *in myResourceGroup* i **sökrutan** högst upp på portalen och välj *myResourceGroup* från sökresultaten. 
1. Välj **Ta bort resursgrupp**. 
1. Ange *myResourceGroup* för **TYP RESURSGRUPPNAMN** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg
I den här artikeln skapade du en virtuell dator i ett virtuellt nätverk och en söktjänst med en privat slutpunkt. Du är ansluten till den virtuella datorn från internet och kommuniceras säkert till söktjänsten med private link. Mer information om privat slutpunkt finns i [Vad är Azure Private Endpoint?](../private-link/private-endpoint-overview.md).
