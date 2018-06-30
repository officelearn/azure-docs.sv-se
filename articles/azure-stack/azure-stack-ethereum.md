---
title: Azure-stacken Ethereum lösningsmall
description: Använd anpassad lösningsmallar för att distribuera och konfigurera ett consortium Ethereum nätverk på Azure-stacken
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 6/28/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 4c2b0cda2d4144cde733f7f57ac6311e1a69f547
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114737"
---
# <a name="azure-stack-ethereum-solution-templates"></a>Azure-stacken Ethereum lösningsmallar

Ethereum lösning mallen är avsedd att göra det enklare och snabbare att distribuera och konfigurera flera medlem consortium Ethereum nätverket med minimal kunskap för Azure och Ethereum.

Varje medlem kan etablera storleken sina nätverk med ett fåtal av användarindata och en enda musklick distribution via Azure Stack-administratörsportalen. Storleken för varje medlem-nätverk består av en uppsättning belastningsutjämnade transaktion noder med som ett program eller en användare kan samverka för att skicka transaktioner, en uppsättning poster transaktioner utvinningsmodellen noder och ett nätverk virtuell installation (NVA). Ett efterföljande anslutning steg ansluter NVAs om du vill skapa ett helt konfigurerade flera medlem blockchain nätverk.

## <a name="prerequisites"></a>Förutsättningar

Hämta följande [från Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS version 16.04.201802220
* Windows Server 2016 
* Anpassat skript för Linux 2.0 
* Anpassat skripttillägg 

Mer information om blockchain scenarier i Azure finns [Ethereum bevis fungerar consortium lösningsmall](../blockchain-workbench/ethereum-deployment-guide.md).

Det krävs en Azure-prenumeration som har stöd för distribution av flera virtuella datorer. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="deployment-architecture"></a>Arkitektur för distribution

Den här lösningen mallen kan distribuera en eller flera medlem Ethereum consortium nätverk. Det virtuella nätverket är anslutet i en kedja med virtuell nätverksenhet och anslutning till resurser. 

## <a name="deployment-use-cases"></a>Användningsfall för distribution

Mallen kan distribuera Ethereum consortium för ledande och ansluta till medlem i en mängd olika sätt, här är de som vi har testat:
- Distribuera lead och medlemmen med samma prenumeration på en Azure-stacken med flera noder, med Azure AD eller AD FS, eller med olika prenumerationer.
- Distribuera lead och medlem med samma prenumeration på en enda nod Azure Stack (med Azure AD).

### <a name="standalone-and-consortium-leader-deployment"></a>Distribution av fristående och consortium ledande

Mallen consortium ledande konfigurerar den första medlemmen storleken i nätverket. 

1. Hämta den [ledande mall från GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/ConsortiumLeader/mainTemplate.json)
2. Välj i Azure Stack-administrationsportalen kan **New > malldistribution** ska distribueras från en anpassad mall.
3. Välj **redigera mallen** att redigera den anpassa mallen.
4. Kopiera och klistra in ledande-mallens JSON som du tidigare har hämtat i fönstret Redigera till höger.
    
    ![Redigera ledande mall](media/azure-stack-ethereum/edit-leader-template.png)

5. Välj **Spara**.
6. Välj **redigera parametrar** och Fyll i mallparametrarna för din distribution.
    
    ![Redigera ledande mallparametrar](media/azure-stack-ethereum/edit-leader-parameters.png)

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Sträng som används som bas för namngivning av distribuerade resurser. | Alfanumeriska tecken med längden 1 till 6 | ETH
    AUTENTISERINGSTYP | Metoden som används för att autentisera till den virtuella datorn. | Lösenord eller SSH offentlig nyckel | Lösenord
    ADMINUSERNAME | Administratörsanvändarnamnet för varje distribuerad virtuell dator | 1 – 64 tecken | gethadmin
    ADMINPASSWORD (autentiseringstyp = lösenord)| Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla 3 av följande krav: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken. <br />Alla virtuella datorer som ursprungligen har samma lösenord, kan du ändra lösenordet när du har etablerat.|12 - 72 tecken|
    ADMINSSHKEY (autentiseringstyp = sshPublicKey) | Secure shell-nyckel som används för fjärrinloggning. | |
    GENESISBLOCK | JSON-sträng som representerar anpassade genesis block. | |
    ETHEREUMACCOUNTPSSWD | Administratörslösenordet som används för att säkra Ethereum konto. | |
    ETHEREUMACCOUNTPASSPHRASE | Lösenfras som används för att generera privata nyckel som är associerat med kontot Ethereum. | |
    ETHEREUMNETWORKID | Nätverks-ID för consortium. | Använd ett värde mellan 5 och 999,999,999 | 72
    CONSORTIUMMEMBERID | Det ID som är associerade med varje medlem i nätverket consortium.   | Detta ID ska vara unikt i nätverket. | 0
    NUMMININGNODES | Antalet noder i utvinningsmodellen. | Mellan 2 och 15. | 2
    MNNODEVMSIZE | VM-storlek för datautvinning noder. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Lagringsprestanda utvinningsmodellen noder. | | Standard_LRS
    NUMTXNODES | Antalet noder i transaktionen. | Mellan 1 och 5. | 1
    TXNODEVMSIZE | VM-storlek för transaktionen noder. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Lagringsprestanda transaktion noder. | | Standard_LRS
    BASEURL | Bas-URL för att hämta beroende mallar från. | Använd standardvärdet om du vill anpassa mallar för distribution. | 

7. Välj **OK**.
8. I **anpassad distribution**, ange **prenumeration**, **resursgruppen**, och **resursgruppsplats**.
    
    ![Ledande distributionsparametrarna](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    Prenumeration | Den prenumeration som du distribuerar consortium-nätverk | | Förbrukning prenumeration
    Resursgrupp | Resursgrupp som du distribuerar consortium-nätverk. | | EthereumResources
    Plats | Azure-regionen för resursgruppen. | | lokal

8. Välj **Skapa**.

Distributionen kan ta 20 minuter eller längre tid att slutföra.

När distributionen är klar kan du granska sammanfattningen för distributionen **Microsoft. Mallen** under distributionen av resursgruppen. Sammanfattningen innehåller utdatavärden som kan användas för att ansluta till medlemmarna.

Bläddra ledandes administrationswebbplats för att kontrollera distributionen av ledandes. Du hittar webbadressen för admin i utdataavsnittet i **Microsoft.Template** distribution.  

![Ledande distribution sammanfattning](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Koppla consortium medlem distribution

1. Hämta den [consortium medlem mall från GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/JoiningMember/mainTemplate.json)
2. Välj i Azure Stack-administrationsportalen kan **New > malldistribution** ska distribueras från en anpassad mall.
3. Välj **redigera mallen** att redigera den anpassa mallen.
4. Kopiera och klistra in ledande-mallens JSON som du hämtade tidigare i fönstret Redigera till höger.
5. Välj **Spara**.
6. Välj **redigera parametrar** och Fyll i mallparametrarna för din distribution.

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Sträng som används som bas för namngivning av distribuerade resurser. | Alfanumeriska tecken med längden 1 till 6 | ETH
    AUTENTISERINGSTYP | Metoden som används för att autentisera till den virtuella datorn. | Lösenord eller SSH offentlig nyckel | Lösenord
    ADMINUSERNAME | Administratörsanvändarnamnet för varje distribuerad virtuell dator | 1 – 64 tecken | gethadmin
    ADMINPASSWORD (autentiseringstyp = lösenord)| Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla 3 av följande krav: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken. <br />Alla virtuella datorer som ursprungligen har samma lösenord, kan du ändra lösenordet när du har etablerat.|12 - 72 tecken|
    ADMINSSHKEY (autentiseringstyp = sshPublicKey) | Secure shell-nyckel som används för fjärrinloggning. | |
    CONSORTIUMMEMBERID | Det ID som är associerade med varje medlem i nätverket consortium.   | Detta ID ska vara unikt i nätverket. | 0
    NUMMININGNODES | Antalet noder i utvinningsmodellen. | Mellan 2 och 15. | 2
    MNNODEVMSIZE | VM-storlek för datautvinning noder. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Lagringsprestanda utvinningsmodellen noder. | | Standard_LRS
    NUMTXNODES | Antalet noder i transaktionen. | Mellan 1 och 5. | 1
    TXNODEVMSIZE | VM-storlek för transaktionen noder. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Lagringsprestanda transaktion noder. | | Standard_LRS
    CONSORTIUMDATA | URL: en pekar på relevanta consortium konfigurationsdata som tillhandahålls av en annan medlem distribution. Det här värdet kan hittas på Ledandes distribution utdata. | |
    REMOTEMEMBERVNETADDRESSSPACE | Ledande NVA IP-adress. Det här värdet kan hittas på ledandes distribution utdata. | | 
    REMOTEMEMBERNVAPUBLICIP | Ledande NVA IP-adress. Det här värdet kan hittas på ledandes distribution utdata. | | 
    CONNECTIONSHAREDKEY | En i förväg upprättade hemlighet mellan consortium network-medlemmar som upprättar en anslutning. | |
    BASEURL | Bas-URL för mallen. | Använd standardvärdet om du vill anpassa mallar för distribution. | 

7. Välj **OK**.
8. I **anpassad distribution**, ange **prenumeration**, **resursgruppen**, och **resursgruppsplats**.

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    Prenumeration | Den prenumeration som du distribuerar consortium-nätverk | | Förbrukning prenumeration
    Resursgrupp | Resursgrupp som du distribuerar consortium-nätverk. | | MemberResources
    Plats | Azure-regionen för resursgruppen. | | lokal

8. Välj **Skapa**.

Distributionen kan ta 20 minuter eller längre tid att slutföra.

När distributionen är klar kan du granska sammanfattningen för distributionen **Microsoft.Template** under distributionen av resursgruppen. Sammanfattningen innehåller utdatavärden som kan användas för att ansluta medlemmarna.

Bläddra medlemmens administrationswebbplats för att kontrollera distributionen av medlemmens. Du kan hitta admin webbplatsadress i utdataavsnittet i Microsoft.Template distribution.

![Medlemmen distribution sammanfattning](media/azure-stack-ethereum/ethereum-node-status-2.png)

I bilden visas medlemmens noder status är **körs inte**. Det beror på att anslutningen mellan medlems- och inte upprättas. Anslutningen mellan medlems- och är en dubbelriktad anslutning. När du distribuerar medlem skapar mallen anslutningen automatiskt från medlem till ledande. Så här skapar du anslutningen från ledande till medlem går du till nästa steg.

### <a name="connect-member-and-leader"></a>Ansluta medlems- och

Den här mallen skapar en anslutning från ledande till en fjärransluten medlem. 

1. Hämta den [ansluta medlems- och mallen från GitHub](https://raw.githubusercontent.com/seyadava/AzureStack-QuickStart-Templates-1/blockchain_nva/eth/marketplace/Connection/mainTemplate.json)
2. Välj i Azure Stack-administrationsportalen kan **New > malldistribution** ska distribueras från en anpassad mall.
3. Välj **redigera mallen** att redigera den anpassa mallen.
4. Kopiera och klistra in ledande-mallens JSON som du hämtade tidigare i fönstret Redigera till höger.
    
    ![Redigera ansluta mall](media/azure-stack-ethereum/edit-connect-template.png)

5. Välj **Spara**.
6. Välj **redigera parametrar** och Fyll i mallparametrarna för din distribution.
    
    ![Redigera ansluta mallparametrar](media/azure-stack-ethereum/edit-connect-parameters.png)

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Ledandes namnprefix. Det här värdet kan hittas på ledandes distribution utdata.  | Alfanumeriska tecken med längden 1 till 6 | |
    MEMBERROUTETABLENAME | Namnet på den styr routningstabellen. Det här värdet kan hittas på ledandes distribution utdata. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Adressutrymmet för medlemmen. Det här värdet kan hittas på medlemmens distribution utdata. | |
    CONNECTIONSHAREDKEY | En i förväg upprättade hemlighet mellan consortium network-medlemmar som upprättar en anslutning.  | |
    REMOTEMEMBERNVAPUBLICIP | NVA IP-adressen för medlemmen. Det här värdet kan hittas på medlemmens distribution utdata. | |
    MEMBERNVAPRIVATEIP | Ledandes privat NVA IP-adress. Det här värdet kan hittas på ledandes distribution utdata. | |
    PLATS | Platsen för din Azure Stack-miljö. | | lokal
    BASEURL | Bas-URL för mallen. | Använd standardvärdet om du vill anpassa mallar för distribution. | 

7. Välj **OK**.
8. I **anpassad distribution**, ange **prenumeration**, **resursgruppen**, och **resursgruppsplats**.
    
    ![Ansluta distributionsparametrarna](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    Prenumeration | Den styr prenumeration. | | Förbrukning prenumeration
    Resursgrupp | Den styr resursgrupp. | | EthereumResources
    Plats | Azure-regionen för resursgruppen. | | lokal

8. Välj **Skapa**.

När distributionen är klar tar några minuter för ledande och medlemmar att starta kommunikation. Uppdatera medlemmens administrationswebbplats för att kontrollera distributionen. Status för medlemmens noder ska köras. 

![Kontrollera distributionen](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om Ethereum och Azure i [Blockchain teknik och program | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Mer information om blockchain scenarier i Azure finns [Ethereum bevis fungerar consortium lösningsmall](../blockchain-workbench/ethereum-deployment-guide.md).