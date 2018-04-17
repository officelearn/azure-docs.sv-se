---
title: Metodtips för säkerhet för data och kryptering | Microsoft Docs
description: Den här artikeln innehåller en uppsättning av bästa praxis för datasäkerhet och kryptering med hjälp av inbyggda funktioner i Azure.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 159bdf681761b9fc46f77cbcf25a210db11d1d9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Metodtips för säkerhet för Azure Data och kryptering
En av nycklar på dataskydd i molnet redovisning för möjliga tillstånd som kan uppstå i dina data och vilka kontroller som är tillgängliga för det aktuella tillståndet. Metodtips för säkerhet och kryptering rekommendationer för Azure data kommer att vara runt följande data tillstånd:

* I vila: Detta innehåller all information som lagringsobjekt, behållare och typer som finns statiskt på fysiska media som ska vara det magnetiska eller optical disk.
* Under överföring: När data överförs mellan komponenter, platser eller program, som i nätverket via en service bus (från lokalt till molnet och vice versa, inklusive hybridanslutningar, till exempel ExpressRoute), eller under en in-/ utdata-process kan anses av som i rörelse.

I den här artikeln diskuteras en samling Azure data säkerhets- och bästa praxis. Följande rekommendationer härleds från våra upplevelse med Azure datasäkerhet och kryptering och erfarenheter från kunder som dig själv.

För varje rekommenderar förklarar vi:

* Vad som är bästa praxis
* Varför du vill aktivera den bästa praxis
* Vad kan vara resultatet om du inte aktivera bästa praxis
* Möjliga alternativ till bästa praxis
* Hur du kan lära dig att aktivera bästa praxis

Den här artikeln Azure datasäkerhet och bästa praxis för kryptering baseras på en konsensus åsikt och Azure plattformsfunktioner och funktioner, som de finns på den tid som den här artikeln skrevs. Åsikter och tekniker ändras med tiden och den här artikeln kommer att uppdateras regelbundet så att dessa ändringar.

Azure data säkerhets- och bästa praxis i den här artikeln omfattar:

* Använda multifaktorautentisering
* Använd rollbaserad åtkomstkontroll (RBAC)
* Kryptera virtuella Azure-datorer
* Använda maskinvara säkerhetsmodeller
* Hantera med säkra arbetsstationer
* Aktivera SQL-datakryptering
* Skydda data under överföring
* Tillämpa fil nivån datakryptering

## <a name="enforce-multi-factor-authentication"></a>Använda Multifaktorautentisering
Det första steget i dataåtkomst och kontroll i Microsoft Azure är att autentisera användaren. [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) är en metod för att verifiera användarens identitet med hjälp av en annan metod än bara ett användarnamn och lösenord. Den här autentisering metoden hjälper dig att skydda åtkomst till data och program och uppfyller efterfrågan från användarna för en process för enkel inloggning.

Genom att aktivera Azure MFA för dina användare kan du lägger till ett andra säkerhetslager till användarinloggningar och transaktioner. I det här fallet en transaktion kan att komma åt ett dokument som finns på en filserver eller i SharePoint Online. Azure MFA hjälper även IT att minska sannolikheten att avslöjade autentiseringsuppgifter kommer att ha åtkomst till organisationens data.

Exempel: Om du införa Azure MFA för användarna och konfigurera den så att du använder ett telefonsamtal eller SMS som verifiering om användarens autentiseringsuppgifter har komprometterats angriparen inte åtkomst till alla resurser eftersom han inte har åtkomst till användarens telefon. Organisationer som inte lägger till den här extra skyddslager identitet är mer känslig för autentiseringsuppgifter attack med lösenordsstöld, vilket kan leda till röjande av data.

Ett alternativ för organisationer som vill behålla autentisering kontrollen lokal är att använda [Azure Multi-Factor Authentication-servern](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), kallas även MFA lokala. Med den här metoden kan du fortfarande att kunna använda multifaktorautentisering, samtidigt som den MFA server lokalt.

Mer information om Azure MFA finns i artikel [komma igång med Azure Multi-Factor Authentication i molnet](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Använd rollbaserad åtkomstkontroll (RBAC)
Begränsa åtkomst baserat på de [behöver](https://en.wikipedia.org/wiki/Need_to_know) och [minsta privilegium](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper. Det här är viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Azure rollbaserad åtkomstkontroll (RBAC) kan användas för att tilldela behörigheter till användare, grupper och program för ett visst område. Omfånget för en rolltilldelning kan vara en prenumeration, resursgrupp eller en enskild resurs.

Du kan utnyttja [inbyggda RBAC-roller](../role-based-access-control/built-in-roles.md) i Azure för att tilldela behörigheter till användare. Överväg att använda *Storage-konto deltagare* för molnoperatörer som behöver kunna hantera storage-konton och *klassiska Storage-konto deltagare* att hantera klassiska lagringskonton. Överväg att lägga till dem för molnoperatörer som behöver hantera virtuella datorer och storage-kontot, *Virtual Machine-deltagare* roll.

Organisationer som inte behöver använda data åtkomstkontroll genom att utnyttja funktioner, till exempel RBAC kan ger fler behörigheter än vad som krävs för sina användare. Detta kan leda till röjande av data genom att vissa användare har åtkomst till data som de inte borde ha i första hand.

Du kan lära dig mer om Azure RBAC genom att läsa artikeln [rollbaserad åtkomstkontroll i](../role-based-access-control/role-assignments-portal.md).

## <a name="encrypt-azure-virtual-machines"></a>Kryptera virtuella Azure-datorer
I många organisationer [datakryptering i viloläge](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg mot data sekretess, efterlevnad och data suveränitet. Azure Disk Encryption kan IT-administratörer att kryptera Windows-och Linux IaaS virtuell dator (VM). Azure Disk Encryption använder funktionen industry standard BitLocker i Windows och funktionen DM-Crypt i Linux att tillhandahålla volymkryptering för Operativsystemet och datadiskar.

Du kan utnyttja Azure Disk Encryption för att skydda och skydda dina data för att uppfylla organisationens säkerhets- och efterlevnadskrav. Organisationer bör överväga att använda kryptering för att minska riskerna rör obehörig dataåtkomst. Vi rekommenderar också att du krypterar enheter innan känsliga data skrivs till dem.

Se till att kryptera den Virtuella datorns datavolymer och startvolymen för att skydda data i vila i Azure storage-konto. Skydda krypteringsnycklar och hemligheter genom att utnyttja [Azure Key Vault](../key-vault/key-vault-whatis.md).

Överväg följande krypteringen Metodtips för lokala Windows-servrar:

* Använd [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) för kryptering av data
* Lagra återställningsinformation i AD DS.
* Om det finns några problem att BitLocker-nycklar har komprometterats rekommenderar vi att du antingen formatera på enheten för att ta bort alla förekomster av BitLocker-metadata från enheten eller att dekryptera och kryptera hela enheten igen.

Organisationer som inte behöver använda datakryptering är mer sannolikt att utsättas för problem med dataintegriteten, till exempel skadliga eller icke-registrerade användare stjäla data och drabbade konton få obehörig åtkomst till data i Rensa format. Förutom dessa risker, måste du bekräfta att de är et och använder rätt säkerhetsåtgärder för att förbättra datasäkerhet företag som har att följa industrins föreskrifter.

Du kan lära dig mer om Azure Disk Encryption genom att läsa artikeln [Azure Disk Encryption för Windows och Linux IaaS-VM](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Använd Maskinvarusäkerhetsmoduler
Branschen krypteringslösningar använder hemliga nycklar för att kryptera data. Det är därför viktigt att nycklarna lagras på ett säkert sätt. Nyckelhantering blir en del av dataskydd, eftersom det kommer utnyttjas för att lagra hemliga nycklar som används för att kryptera data.

Azure disk encryption använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i nyckelvalvet-prenumeration, medan du säkerställer att krypteras alla data i virtuella diskar i vila i din Azure lagring. Granska nycklar och användning av princip bör du använda Azure Key Vault.

Det finns många inneboende risker som rör slipper lämpliga säkerhetsåtgärder för att skydda de hemliga nycklarna som användes för att kryptera data. Om angripare har åtkomst till de hemliga nycklarna, kommer de att kunna dekryptera data och eventuellt få åtkomst till konfidentiell information.

Du kan lära dig mer om allmänna rekommendationer för certifikathantering i Azure genom att läsa artikeln [certifikathantering i Azure: bra att veta](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Mer information om Azure Key Vault [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Hantera med säkra arbetsstationer
Eftersom det stora flertalet av attacker riktar slutanvändaren kan blir slutpunkten en av de primära aspekterna av angrepp. Om en angripare komprometterar slutpunkten, kan han använda användarens autentiseringsuppgifter för att få åtkomst till organisationens data. De flesta endpoint-attacker kan dra nytta av det faktum att slutanvändare administratörer i sina lokala arbetsstationer.

Du kan minska riskerna med hjälp av en säker hanterings-arbetsstation. Vi rekommenderar att du använder en [privilegierad åtkomst arbetsstationer (PAW)](https://technet.microsoft.com/library/mt634654.aspx) att minska risken för angrepp i arbetsstationer. Dessa säker hantering av arbetsstationer kan hjälpa dig att minimera vissa av dessa attacker försäkra dig om dina data är säkrare. Se till att använda PAW för att skydda och låsa arbetsstationen. Detta är ett viktigt steg för att tillhandahålla hög säkerhet garantier för känsliga konton, uppgifter och dataskydd.

Avsaknad av endpoint protection kan medföra risker för dina data, se till att tillämpa säkerhetsprinciper på alla enheter som används för att nyttja data, oavsett var data (molnet eller lokalt).

Du kan lära dig mer om privilegierad åtkomst till arbetsstationen genom att läsa artikeln [skydda privilegierad åtkomst](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Aktivera SQL-datakryptering
[Azure SQL Database transparent datakryptering](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) skyddar mot hot från skadlig aktivitet genom att utföra realtid kryptering och dekryptering av databas, tillhörande säkerhetskopior och transaktionsloggfiler utan vilande kräver ändringar i programmet.  TDE krypterar lagring av en hel databas med hjälp av en symmetrisk nyckel som heter databaskrypteringsnyckeln.

Även om hela lagring krypteras är det mycket viktigt att kryptera även själva databasen. Det här är en implementering av skydd på djupet metod för att skydda data. Om du använder [Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) och vill skydda känsliga data, till exempel kreditkort eller personnummer, kan du kryptera databaser med FIPS 140-2-verifierade 256-bitars AES-kryptering som uppfyller kraven för många branschstandarder (till exempel HIPAA, PCI).

Det är viktigt att förstå att filer relaterade till [bufferten poolen tillägget](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) är inte krypterat när en databas har krypterats med TDE. Du måste använda filen kryptering på Systemverktyg som BitLocker eller [Krypterande filsystem](https://technet.microsoft.com/library/cc700811.aspx) (EFS) för BPE relaterade filer.

Eftersom en behörig användare som en administratör eller en databasadministratör kan komma åt data även om databasen är krypterad med TDE, bör du också följa rekommendationerna nedan:

* SQL-autentisering på databasnivå
* Azure AD-autentisering med hjälp av RBAC-roller
* Användare och program bör använda separata konton för att autentisera. Det här sättet kan du begränsa behörigheterna för användare och program och minska riskerna med skadliga aktiviteter
* Implementera databasen säkerhetsnivå via fasta databasroller (till exempel db_datareader eller db_datawriter) eller du kan skapa anpassade roller för programmet att bevilja explicit behörighet till valda databasobjekt

Organisationer som inte använder kryptering på databasen kan vara mer sårbara för attacker som kan påverka data som finns i SQL-databaser.

Du kan lära dig mer om SQL TDE kryptering genom att läsa artikeln [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Skydda data under överföring
Skydda data under överföringen ska väsentlig del av strategin för skydd av data. Eftersom data flyttar fram och tillbaka från många platser, vi Allmänt rekommenderar att du alltid använder SSL/TLS-protokoll för att utbyta data mellan olika platser. I vissa fall kanske du vill isolera hela kommunikationskanalen mellan din lokala och moln infrastruktur med hjälp av ett virtuellt privat nätverk (VPN).

För data som flyttas mellan din lokala infrastruktur och Azure, bör du lämpliga skyddsåtgärder, till exempel HTTPS eller VPN.

För organisationer som behöver för att skydda åtkomst från flera arbetsstationer som finns lokalt till Azure kan använda [Azure plats-till-plats-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md).

För organisationer som behöver för att skydda åtkomst från en dator som finns på plats till Azure kan använda [punkt-till-plats VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Större datauppsättningar kan flyttas dedikerade snabba WAN-länk som [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Om du väljer att använda ExpressRoute kan du också kryptera data på programnivå med hjälp av [SSL/TLS](https://support.microsoft.com/kb/257591) eller andra protokoll för extra skydd.

Om du interagerar med Azure Storage via Azure Portal, alla transaktioner ska ske via HTTPS. [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) över HTTPS kan också användas för att interagera med [Azure Storage](https://azure.microsoft.com/services/storage/) och [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organisationer som inte kan skydda data under överföringen är mer känslig för [man-in-the-middle-attacker](https://technet.microsoft.com/library/gg195821.aspx), [avlyssning](https://technet.microsoft.com/library/gg195641.aspx) och sessionskapning. Dessa attacker kan vara det första steget i att komma åt känsliga data.

Du kan lära dig mer om Azure VPN-alternativet genom att läsa artikeln [planering och design för VPN-Gateway](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Tillämpa fil nivån datakryptering
Ett extra skyddslager som kan öka säkerhetsnivån för dina data krypterar själva filen, oavsett filens plats.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) använder kryptering, identitet och auktorisering för att skydda filer och e-post. Azure RMS fungerar över flera enheter – telefoner, surfplattor och datorer genom att skydda både inom organisationen och utanför organisationen. Den här funktionen är möjligt eftersom Azure RMS lägger till en skyddsnivå som finns kvar med data, även när de lämnar organisationens gränser.

När du använder Azure RMS för att skydda dina filer, använder du branschstandardkryptografi med fullständigt stöd för [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). När du använder Azure RMS för dataskydd, har garantier att skyddet kvar för filen även om de kopieras till lagringsplats som inte kontrolleras av IT-avdelningen, till exempel en molntjänst för lagring. Samma inträffar för filer som delas via e-post, skyddas som en bilaga i ett e-postmeddelande med instruktioner för hur du öppnar den skyddade bifogade filen.

När du planerar för införandet av Azure RMS rekommenderar vi följande:

* Installera den [RMS-delningsappen](https://technet.microsoft.com/library/dn339006.aspx). Den här appen integreras med Office program genom att installera ett Office-tillägget så att användarna kan enkelt skydda filer direkt.
* Konfigurera program och tjänster för att stödja Azure RMS
* Skapa [anpassade mallar](https://technet.microsoft.com/library/dn642472.aspx) som motsvarar dina affärsbehov. Exempel: en mall för övre hemlig information som ska användas i alla översta hemlighet relaterade e-postmeddelanden.

Organisationer som är beroende av på [dataklassificering](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) och Filskydd kan vara svårare att data sprids. Utan rätt Filskydd kan organisationer inte hämta affärsinsikter, övervaka missbruk och förhindra obehörig åtkomst till filer.

Du kan lära dig mer om Azure RMS genom att läsa artikeln [komma igång med Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
