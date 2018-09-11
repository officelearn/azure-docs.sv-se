---
title: Azure Stack Ethereum blockchain-lösningsmallen
description: Använd mallar för anpassad lösning för att distribuera och konfigurera ett konsortienätverk Ethereum blockchain på Azure Stack
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 07/03/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: d3c89e2028986e05d5d3957c9805d22f07a840b3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298122"
---
# <a name="azure-stack-ethereum-blockchain-solution-templates"></a>Azure Stack Ethereum blockchain-lösningsmallar

Ethereum-lösningsmallen är utformad för att göra det enklare och snabbare att distribuera och konfigurera ett flera medlem Ethereum blockchain konsortienätverk med minimal kunskap om Azure och Ethereum.

Med en handfull användarindata och en enda musklick distribution via portalen för Azure Stack-klient, kan varje medlem etablera sina nätverk fotavtryck. Varje medlem nätverk fotavtryck består av en uppsättning noder för Utjämning av nätverksbelastning transaktion med som ett program eller en användare kan interagera om du vill skicka transaktioner, en uppsättning utvinning noder till poster transaktioner och en virtuell nätverksinstallation (NVA). Ett efterföljande anslutning steg ansluter nva: erna för att skapa en fullständigt konfigurerad flera medlem blockchain-nätverk.

## <a name="prerequisites"></a>Förutsättningar

Ladda ned de senaste objekten [från Marketplace](azure-stack-download-azure-marketplace-item.md):

* Ubuntu Server 16.04 LTS
* Windows Server 2016
* Anpassat skript för Linux 2.0
* Tillägget för anpassat skript för Windows

Läs mer om blockkedjescenarier [Ethereum proof of work consortium lösningsmallen](../blockchain-workbench/ethereum-deployment-guide.md).

## <a name="deployment-architecture"></a>Distributionsarkitektur för

Den här lösningsmallen kan distribuera en eller flera medlemmar Ethereum consortium network. Det virtuella nätverket är ansluten i en kedja topologi som använder virtuell nätverksinstallation och anslutning resurser. 

## <a name="deployment-use-cases"></a>Användningsfall för distribution

Mallen kan distribuera Ethereum consortium för ledare och Anslut till medlem i en mängd olika sätt, här är de som vi har testat:

- En Azure-Stack med flera noder, med Azure AD eller AD FS, distribuera lead och medlem med samma prenumeration eller med olika prenumerationer.
- Distribuera lead och medlem med samma prenumeration på en nod Azure Stack (med Azure AD).

### <a name="standalone-and-consortium-leader-deployment"></a>Distribution av fristående och consortium ledare

Mallen consortium ledare konfigurerar den första medlemmen fotavtryck i nätverket. 

1. Ladda ned den [ledare mall från GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. I Azure Stack-administrationsportalen väljer **New > malldistributionen** ska distribueras från en anpassad mall.
3. Välj **redigera mallen** så här redigerar du den anpassa mallen.
4. Kopiera och klistra in ledare-mallens JSON som du tidigare har hämtat i fönstret Redigera till höger.
    
    ![Redigera ledare mall](media/azure-stack-ethereum/edit-leader-template.png)

5. Välj **Spara**.
6. Välj **redigera parametrar** och slutför mallparametrar för din distribution.
    
    ![Redigera ledare mallparametrar](media/azure-stack-ethereum/edit-leader-parameters.png)

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Sträng som används som bas för namngivning av distribuerade resurser. | Alfanumeriska tecken med längden 1 till 6 | ETH
    AUTHTYPE | Metoden för att autentisera till den virtuella datorn. | Lösenordet eller SSH offentlig nyckel | Lösenord
    ADMINUSERNAME | Administratörens användarnamn för varje distribuerad virtuell dator | 1 – 64 tecken | gethadmin
    ADMINPASSWORD (autentiseringstyp = lösenord)| Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla 3 av följande krav: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken. <br />Även om alla virtuella datorer har ursprungligen samma lösenord, kan du ändra lösenordet när du har etablerat.|12 – 72 tecken|
    ADMINSSHKEY (autentiseringstyp = sshPublicKey) | SSH-nyckel som används för fjärrinloggning. | |
    GENESISBLOCK | JSON-sträng som representerar anpassade genesis block.  Ange ett värde för den här parametern är valfri. | |
    ETHEREUMACCOUNTPSSWD | Administratörslösenordet som används för att skydda Ethereum-konto. | |
    ETHEREUMACCOUNTPASSPHRASE | Lösenfras som används för att generera den privata nyckeln som är associerade med Ethereum-konto. | |
    ETHEREUMNETWORKID | Nätverks-ID för consortium. | Använd ett värde mellan 5 och 999,999,999 | 72
    CONSORTIUMMEMBERID | ID som är associerade med varje medlem i konsortienätverk.   | Detta ID måste vara unika i nätverket. | 0
    NUMMININGNODES | Antalet noder för datautvinning. | Mellan 2 och 15. | 2
    MNNODEVMSIZE | VM-storlek för noderna i utvinningsstrukturen. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Lagringsprestanda utvinning noder. | | Standard_LRS
    NUMTXNODES | Antalet noder i transaktionen. | Mellan 1 och 5. | 1
    TXNODEVMSIZE | VM-storlek för noderna i transaktionen. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Lagringsprestanda nodernas transaktion. | | Standard_LRS
    BASEURL | Bas-URL att hämta beroende mallar från. | Använd standardvärdet om du inte vill anpassa mallar för distribution. | 

7. Välj **OK**.
8. I **anpassad distribution**, ange **prenumeration**, **resursgrupp**, och **resursgruppsplats**.
    
    ![Ledande distributionsparametrarna](media/azure-stack-ethereum/leader-deployment-parameters.png)

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    Prenumeration | Den prenumeration som ska distribueras consortium network | | Användning-prenumeration
    Resursgrupp | Den resursgrupp som ska distribueras consortium network. | | EthereumResources
    Plats | Azure-regionen för resursgruppen. | | lokal

8. Välj **Skapa**.

Distributionen kan ta 20 minuter eller längre tid att slutföra.

När distributionen är klar kan du läsa distributionssammanfattning för **Microsoft. Mallen** under distributionen av resursgruppen. Sammanfattningen innehåller utdatavärden som kan användas för att ansluta till medlemmarna.

Kontrollera filmens distribution genom att bläddra filmens administrationswebbplats. Du kan hitta admin webbplatsadress i utdataavsnittet i **Microsoft.Template** distribution.  

![Ledande distributionssammanfattning](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Koppla consortium medlem distribution

1. Ladda ned den [consortium medlem mall från GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. I Azure Stack-administrationsportalen väljer **New > malldistributionen** ska distribueras från en anpassad mall.
3. Välj **redigera mallen** så här redigerar du den anpassa mallen.
4. Kopiera och klistra in ledare-mallens JSON som du hämtade tidigare i den redigering rutan till höger.
5. Välj **Spara**.
6. Välj **redigera parametrar** och slutför mallparametrar för din distribution.

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Sträng som används som bas för namngivning av distribuerade resurser. | Alfanumeriska tecken med längden 1 till 6 | ETH
    AUTHTYPE | Metoden för att autentisera till den virtuella datorn. | Lösenordet eller SSH offentlig nyckel | Lösenord
    ADMINUSERNAME | Administratörens användarnamn för varje distribuerad virtuell dator | 1 – 64 tecken | gethadmin
    ADMINPASSWORD (autentiseringstyp = lösenord)| Lösenordet för administratörskontot för var och en av de virtuella datorerna som distribueras. Lösenordet måste innehålla 3 av följande krav: 1 versal bokstav, 1 gemen bokstav, 1 siffra och 1 specialtecken. <br />Även om alla virtuella datorer har ursprungligen samma lösenord, kan du ändra lösenordet när du har etablerat.|12 – 72 tecken|
    ADMINSSHKEY (autentiseringstyp = sshPublicKey) | SSH-nyckel som används för fjärrinloggning. | |
    CONSORTIUMMEMBERID | ID som är associerade med varje medlem i konsortienätverk.   | Detta ID måste vara unika i nätverket. | 0
    NUMMININGNODES | Antalet noder för datautvinning. | Mellan 2 och 15. | 2
    MNNODEVMSIZE | VM-storlek för noderna i utvinningsstrukturen. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Lagringsprestanda utvinning noder. | | Standard_LRS
    NUMTXNODES | Antalet noder i transaktionen. | Mellan 1 och 5. | 1
    TXNODEVMSIZE | VM-storlek för noderna i transaktionen. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Lagringsprestanda nodernas transaktion. | | Standard_LRS
    CONSORTIUMDATA | Den URL som pekar mot den relevanta consortium konfigurationsdata som tillhandahålls av en annan medlem distribution. Det här värdet kan hittas på filmens distribution utdata. | |
    REMOTEMEMBERVNETADDRESSSPACE | NVA-IP-adressen för ledare. Det här värdet kan hittas på filmens distribution utdata. | | 
    REMOTEMEMBERNVAPUBLICIP | NVA-IP-adressen för ledare. Det här värdet kan hittas på filmens distribution utdata. | | 
    CONNECTIONSHAREDKEY | En i förväg upprättade hemlighet mellan medlemmarna i konsortienätverk som upprättar en anslutning. | |
    BASEURL | Bas-URL för mallen. | Använd standardvärdet om du inte vill anpassa mallar för distribution. | 

7. Välj **OK**.
8. I **anpassad distribution**, ange **prenumeration**, **resursgrupp**, och **resursgruppsplats**.

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    Prenumeration | Den prenumeration som ska distribueras consortium network | | Användning-prenumeration
    Resursgrupp | Den resursgrupp som ska distribueras consortium network. | | MemberResources
    Plats | Azure-regionen för resursgruppen. | | lokal

8. Välj **Skapa**.

Distributionen kan ta 20 minuter eller längre tid att slutföra.

När distributionen är klar kan du läsa distributionssammanfattning för **Microsoft.Template** i avsnittet distribution i resursgruppen. Sammanfattningen innehåller utdatavärden som kan användas för att ansluta consortium medlemmar.

Bläddra medlemmens administrationswebbplatsen för att verifiera medlemmens distribution. Du hittar webbadressen admin i utdataavsnittet i Microsoft.Template distribution.

![Medlemmen distributionssammanfattning](media/azure-stack-ethereum/ethereum-node-status-2.png)

I bilden visas medlemmens noder status är **körs inte**. Det beror på att inte upprättas anslutningen mellan medlems- och. Anslutningen mellan medlems- och är en dubbelriktad anslutning. När du distribuerar medlem skapar mallen anslutningen automatiskt från medlem till ledare. För att skapa anslutningen från ledande till medlem går du till nästa steg.

### <a name="connect-member-and-leader"></a>Ansluta medlems- och

Den här mallen skapar en anslutning från ledaren till en fjärransluten medlem. 

1. Ladda ned den [ansluta medlems- och mallen från GitHub](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. I Azure Stack-administrationsportalen väljer **New > malldistributionen** ska distribueras från en anpassad mall.
3. Välj **redigera mallen** så här redigerar du den anpassa mallen.
4. Kopiera och klistra in ledare-mallens JSON som du hämtade tidigare i den redigering rutan till höger.
    
    ![Redigera ansluta mall](media/azure-stack-ethereum/edit-connect-template.png)

5. Välj **Spara**.
6. Välj **redigera parametrar** och slutför mallparametrar för din distribution.
    
    ![Redigera ansluta mallparametrar](media/azure-stack-ethereum/edit-connect-parameters.png)

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Filmens namnprefix. Det här värdet kan hittas på filmens distribution utdata.  | Alfanumeriska tecken med längden 1 till 6 | |
    MEMBERROUTETABLENAME | Namnet på den ledande routningstabellen. Det här värdet kan hittas på filmens distribution utdata. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Adressutrymme för medlemmen. Det här värdet kan hittas på medlemmens distribution utdata. | |
    CONNECTIONSHAREDKEY | En i förväg upprättade hemlighet mellan medlemmarna i konsortienätverk som upprättar en anslutning.  | |
    REMOTEMEMBERNVAPUBLICIP | NVA-IP-adressen för medlemmen. Det här värdet kan hittas på medlemmens distribution utdata. | |
    MEMBERNVAPRIVATEIP | Filmens privat NVA IP-adress. Det här värdet kan hittas på filmens distribution utdata. | |
    PLATS | Platsen för Azure Stack-miljön. | | lokal
    BASEURL | Bas-URL för mallen. | Använd standardvärdet om du inte vill anpassa mallar för distribution. | 

7. Välj **OK**.
8. I **anpassad distribution**, ange **prenumeration**, **resursgrupp**, och **resursgruppsplats**.
    
    ![Ansluta distributionsparametrarna](media/azure-stack-ethereum/connect-deployment-parameters.png)

    Parameternamn | Beskrivning | Tillåtna värden | Exempelvärde
    ---------------|-------------|----------------|-------------
    Prenumeration | Prenumeration till ledare. | | Användning-prenumeration
    Resursgrupp | Resursgrupp för till ledare. | | EthereumResources
    Plats | Azure-regionen för resursgruppen. | | lokal

8. Välj **Skapa**.

När distributionen är klar, tar det några minuter för ledare och medlem att starta kommunikation. Uppdatera medlemmens administrationswebbplatsen för att verifiera distributionen. Status för medlemmens noder ska köras. 

![Kontrollera distributionen](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om Ethereum- och Azure i [Blockkedjeteknik och Blockkedjeprogram | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/).
- Läs mer om blockchain scenarier på Azure, [Ethereum proof of work consortium lösningsmallen](../blockchain-workbench/ethereum-deployment-guide.md).