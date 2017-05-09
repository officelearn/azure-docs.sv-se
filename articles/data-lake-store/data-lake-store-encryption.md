---
title: Kryptering i Azure Data Lake Store | Microsoft Docs
description: "Så här fungerar kryptering och nyckelrotation i Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: 9bd9996a4a22b2f57510b6f3b6625e22b3183b1c
ms.contentlocale: sv-se
ms.lasthandoff: 04/29/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Kryptera data i Azure Data Lake Store

## <a name="overview-of-encryption-in-azure-data-lake-store"></a>Översikt över kryptering i Azure Data Lake Store

Du kan skydda dina data, implementera säkerhetsprinciper och uppfylla efterlevnadskrav med hjälp av kryptering i Azure Data Lake Store (ADLS). Den här artikeln ger en översikt över designen och beskriver de tekniska aspekterna av hur Data Lake Store implementerar datakryptering.

ADLS stöder transparent, kryptering av vilande data som är på som standard. En liten förklaring av vad detta betyder:

* På som standard: när du skapar ett nytt Azure Data Lake Store-konto är kryptering aktiverat som standard. Därefter krypteras alltid data som lagras i Data Lake Store före lagring på permanenta media. Detta beteende gäller för alla data och kan inte ändras efter att ett konto har skapats.
* Transparent: ADLS krypterar automatiskt data före beständig lagring dekrypterar data för hämtning. Krypteringen konfigureras och hanteras på Data Lake Store-nivå av en administratör. Inga ändringar görs i API:erna för dataåtkomst och därför krävs inga ändringar i de program och tjänster som interagerar med Data Lake Store på grund av krypteringen.

Data under överföring (även kallat data i rörelse) krypteras också alltid i Data Lake Store. Förutom att kryptera data före lagring till permanenta media skyddas alltid även data under överföring eller i rörelse med hjälp av HTTPS (HTTP över Secure Sockets Layer). HTTPS är det enda protokoll som stöds för Data Lake Store REST-gränssnitt.

![Bild 1](./media/data-lake-store-encryption/fig1.png)


## <a name="setting-up-encryption-with-azure-data-lake-store"></a>Konfigurera kryptering med Azure Data Lake Store

Kryptering för Azure Data Lake Store ställs in när kontot skapas, och är alltid aktiverat som standard. Kunder kan välja om de vill hantera nycklar eller låta Azure Data Lake Store hantera nycklarna åt dem (standard).

Information om hur du konfigurerar kryptering med Azure Data Lake Store finns i [Komma igång](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

## <a name="under-the-hood--how-encryption-works-in-azure-data-lake-store"></a>Så här fungerar kryptering i Azure Data Lake Store

### <a name="master-encryption-keys"></a>Huvudkrypteringsnycklar

Azure Data Lake Store har två lägen för hantering av huvudkrypteringsnycklar (Master, Encryption Keys, MEK). Användningen av huvudkrypteringsnycklarna förklaras i detalj nedan. Anta att huvudkrypteringsnyckeln är nyckeln på högsta nivån. Åtkomst till huvudkrypteringsnyckeln krävs för att dekryptera data som lagras i Data Lake Store.

Det finns två lägen för att hantera huvudkrypteringsnyckeln:

1.    Tjänsthanterade nycklar
2.    Kundhanterade nycklar

I båda lägena skyddas huvudkrypteringsnyckeln genom att den lagras i Azure Key Vault. Azure Key Vault är en fullständigt hanterad tjänst på Azure med mycket hög säkerhet som kan användas för att skydda kryptografiska nycklar. Du kan läsa mer om Azure Key Vault [här](https://azure.microsoft.com/services/key-vault)

Här är en kort jämförelse av funktionerna som tillhandahålls i de två lägena för att hantera huvudkrypteringsnycklar.

|  | Tjänsthanterade nycklar | Kundhanterade nycklar |
| --- | --- | --- |
|Hur lagras data?|Data krypteras alltid innan de lagras|Data krypteras alltid innan de lagras|
|Var lagras huvudkrypteringsnyckeln?|Azure Key Vault|Azure Key Vault|
|Finns det krypteringsnycklar som lagras i klartext utanför Azure Key Vault|Nej|Nej|
|Kan huvudkrypteringsnyckeln hämtas från Azure Key Vault?|Nej. När den har lagrats i nyckelvalvet kan den endast användas för kryptering och dekryptering.|Nej. När den har lagrats i nyckelvalvet kan den endast användas för kryptering och dekryptering.|
|Vem äger Azure Key Vault och huvudkrypteringsnyckeln?|Azure Data Lake Store-tjänsten.|Kunden äger den Azure Key Vault som tillhör kundens Azure-prenumeration. Huvudkrypteringsnyckeln i nyckelvalvet kan hanteras av programvara eller maskinvara (HSM).|
|Kan kunden återkalla åtkomst till huvudkrypteringsnyckeln för Azure Data Lake Store-tjänsten?|Nej|Ja. De kan hantera åtkomstkontrollistor i Azure Key Vault och ta bort åtkomstkontrollposter för tjänstidentiteten för Azure Data Lake Store-tjänsten.|
|Kan kunden ta bort huvudkrypteringsnyckeln permanent?|Nej|Ja. Om kunden tar bort huvudkrypteringsnyckeln från Azure Key Vault kan inte data i ADLS-konto dekrypteras av någon, inte heller av Azure Data Lake Store-tjänsten. <br><br> Om huvudkrypteringsnyckeln säkerhetskopieras av kunden innan den tas bort från Azure Key Vault kan nyckeln och data återställas. Men om huvudkrypteringsnyckeln inte har säkerhetskopierats av kunden innan den tas bort från Azure Key Vault kommer det sedan aldrig gå att dekryptera data i ADLS-kontot.|


Förutom skillnaden på den översta nivån (vem som hanterar huvudkrypteringsnyckeln och det nyckelvalv som nyckeln finns i) är den övriga designen densamma för båda lägena.

Det finns några viktiga aspekter att komma ihåg när det gäller att välja läge för huvudkrypteringsnycklarna.

1.    Du kan välja om du vill använda kundhanterade nycklar eller ADLS-hanterade nycklar när du etablerar ett ADLS-konto
2.    När ett ADLS-konto har etablerats går det inte att ändra läge

### <a name="encryption-and-decryption-of-data"></a>Kryptering och dekryptering av data

Det finns tre typer av nycklar som används i utformningen av datakryptering för Azure Data Lake. I följande tabell sammanfattas deras roll i krypteringen:

| Nyckel                   | Förkortning | Associerad med | Lagringsplats                             | Typ       | Anteckningar                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Huvudkrypteringsnyckel | MEK          | Ett ADLS-konto | Azure Key Vault                              | Asymmetrisk | Kan hanteras av ADLS eller av kunden                                                              |
| Datakrypteringsnyckel   | DEK          | Ett ADLS-konto | Beständig lagring – hanteras av ADLS-tjänsten | Symmetrisk  | DEK krypteras av MEK och det är den krypterade DEK som lagras i permanenta media |
| Blockkrypteringsnyckel  | BEK          | Ett datablock | Ingen                                         | Symmetrisk  | BEK härleds från DEK och datablocket                                                      |

Följande diagram illustrerar dessa begrepp:

![Bild 2](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritm när en fil ska dekrypteras:
1.    Kontrollera om DEK för ADLS-kontot är cachelagrat och redo att användas.
    * Om inte, läser du den krypterade DEK från den beständiga lagringen och skickar den till Azure Key Vault för dekryptering. Cachelagra den dekrypterade DEK i minnet. Nu är den redo att användas.
2.    För varje datablock i filen
    * Läs det krypterade datablocket från den beständiga lagringen
    * Generera BEK från DEK och det krypterade datablocket
    * Använd BEK för att dekryptera data
#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritm när ett datablock ska krypteras:
1.    Kontrollera om DEK för ADLS-kontot är cachelagrat och redo att användas.
    * Om inte, läser du den krypterade DEK från den beständiga lagringen och skickar den till Azure Key Vault för dekryptering. Cachelagra den dekrypterade DEK i minnet. Nu är den redo att användas.
2.    Generera en unik BEK för datablocket från DEK.
3.    Kryptera datablocket med BEK med hjälp av AES-256-kryptering.
4.    Lagra det krypterade datablocket i den beständiga lagringen

> [!NOTE] 
> Av prestandaskäl cachelagras datakrypteringsnyckeln (DEK) i minnet en kort tid och raderas omedelbart efter att tiden har gått ut. I permanenta media lagras den alltid av huvudkrypteringsnyckeln (MEK).

## <a name="key-rotation"></a>Nyckelrotation

Azure Data Lake Store möjliggör rotation av huvudkrypteringsnyckeln (MEK) vid användning av kundhanterade nycklar. Information om hur du konfigurerar ett ADLS-konto med kundhanterade nycklar finns på sidan [Komma igång](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="pre-requisites"></a>Förutsättningar

När du konfigurerar Azure Data Lake-kontot har kunder valt att använda sina egna nycklar. Det här alternativet kan inte ändras efter att kontot har skapats. Om du använder standardalternativen för kryptering krypteras dina data alltid med nycklarna som hanteras av Azure Data Lake. I det här fallet kan kunderna inte rotera nycklar eftersom de hanteras av Azure Data Lake. I anvisningarna nedan antas att du använder kundhanterade nycklar (att du valt egna nycklar från ditt nyckelvalv).

### <a name="how-to-rotate-the-key-mek-in-azure-data-lake-store"></a>Så här roterar du nyckeln (MEK) i Azure Data Lake Store

1. Logga in på nya [Azure Portal](https://portal.azure.com/)
2. Navigera till det nyckelvalv där de nycklar som är kopplade till ditt Azure Data Lake Store-konto lagras och välj Nycklar.

    ![Nycklar](./media/data-lake-store-encryption/keyvault.png)

3.    Välj nyckeln som är kopplad till ditt Azure Data Lake Store-konto och skapa en ny version av nyckeln.
  
   I nuläget stöds endast nyckelrotation till en ny version av en nyckel i Azure Data Lake. Rotation till en annan nyckel stöds inte

   ![ny version](./media/data-lake-store-encryption/keynewversion.png)

4.    Gå till Azure Data Lake Storage-kontot och välj Kryptering

    ![ny version](./media/data-lake-store-encryption/select-encryption.png)

5.    Ett meddelande visas om att en ny version av nyckeln är tillgänglig och en knapp visas för att rotera nyckeln till den nya versionen. Klicka på knappen för att uppdatera nyckeln till den nya versionen.

    ![klar](./media/data-lake-store-encryption/rotatekey.png)

6. Den här åtgärden tar normalt mindre än 2 minuter och ingen stilleståndstid förväntas på grund av nyckelrotationen. När åtgärden har slutförts används den nya versionen av nyckeln.

