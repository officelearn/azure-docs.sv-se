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
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 20444d368c568ee716ff242e33323b91ffd198eb
ms.contentlocale: sv-se
ms.lasthandoff: 05/08/2017

---

# <a name="encryption-of-data-in-azure-data-lake-store"></a>Kryptera data i Azure Data Lake Store

Du kan skydda dina data, implementera säkerhetsprinciper och uppfylla efterlevnadskrav med hjälp av kryptering i Azure Data Lake Store. Den här artikeln innehåller en översikt över designen och beskriver några av de tekniska aspekterna av genomförandet.

Data Lake Store har stöd för kryptering av vilande data och data under överföringen. Data Lake Store stöder transparent kryptering av vilande data som är på som standard. En liten förklaring av vad detta betyder:

* **På som standard**: när du skapar ett nytt Data Lake Store-konto är kryptering aktiverat som standard. Därefter krypteras alltid data som lagras i Data Lake Store före lagring på permanenta media. Detta beteende gäller för alla data och kan inte ändras efter att ett konto har skapats.
* **Transparent**: Data Lake Store krypterar automatiskt data före beständig lagring dekrypterar data för hämtning. Krypteringen konfigureras och hanteras på Data Lake Store-nivå av en administratör. Inga ändringar görs i dataåtkomst API: er. Därför krävs inga ändringar i de program och tjänster som interagerar med Data Lake Store på grund av krypteringen.

Data under överföring (även kallat data i rörelse) krypteras också alltid i Data Lake Store. Förutom att kryptera data före lagring till permanenta media skyddas alltid även data under överföring eller i rörelse med hjälp av HTTPS. HTTPS är det enda protokoll som stöds för Data Lake Store REST-gränssnitt. Följande diagram visar hur data krypteras i Data Lake Store:

![Diagram över kryptering i Data Lake Store](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Konfigurera kryptering med Data Lake Store

Kryptering för Data Lake Store ställs in när kontot skapas, och är alltid aktiverat som standard. Du kan antingen hantera nycklarna själv eller låta Data Lake Store hantera dem åt dig (det är standard).

Mer information finns i [Komma igång](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-store"></a>Så här fungerar kryptering i Data Lake Store

Följande information beskriver hur du hanterar huvudkrypteringsnycklar och den förklarar tre olika typer av nycklar som du kan använda vid datakryptering för Data Lake Store.

### <a name="master-encryption-keys"></a>Huvudkrypteringsnycklar

Data Lake Store har två lägen för hantering av huvudkrypteringsnycklar (Master, Encryption Keys, MEK). Anta att huvudkrypteringsnyckeln är nyckeln på högsta nivån. Åtkomst till huvudkrypteringsnyckeln krävs för att dekryptera data som lagras i Data Lake Store.

Det finns två lägen för att hantera huvudkrypteringsnyckeln:

*   Tjänsthanterade nycklar
*   Kundhanterade nycklar

I båda lägena skyddas huvudkrypteringsnyckeln genom att den lagras i Azure Key Vault. Key Vault är en fullständigt hanterad tjänst på Azure med mycket hög säkerhet som kan användas för att skydda kryptografiska nycklar. Mer information finns i [Key Vault](https://azure.microsoft.com/services/key-vault).

Här är en kort jämförelse av funktionerna som tillhandahålls i de två lägena för att hantera huvudkrypteringsnycklar.

|  | Tjänsthanterade nycklar | Kundhanterade nycklar |
| --- | --- | --- |
|Hur lagras data?|Data krypteras alltid innan de lagras.|Data krypteras alltid innan de lagras.|
|Var lagras huvudkrypteringsnyckeln?|Key Vault|Key Vault|
|Finns det krypteringsnycklar som lagras i klartext utanför Key Vault? |Nej|Nej|
|Kan huvudkrypteringsnyckeln hämtas från Key Vault?|Nej. När den har lagrats i Key Vault kan den endast användas för kryptering och dekryptering.|Nej. När den har lagrats i Key Vault kan den endast användas för kryptering och dekryptering.|
|Vem äger Key Vault-instansen och huvudkrypteringsnyckeln?|Data Lake Store-tjänsten|Du äger nyckelvalvsinstansen som ingår i din Azure-prenumeration. Huvudkrypteringsnyckeln i Key Vault kan hanteras av programvara eller maskinvara.|
|Kan du återkalla åtkomst till huvudkrypteringsnyckeln för Data Lake Store-tjänsten?|Nej|Ja. Du kan hantera åtkomstkontrollistor i Azure Key Vault och ta bort åtkomstkontrollposter för tjänstidentiteten för Data Lake Store-tjänsten.|
|Kan man ta bort huvudkrypteringsnyckeln permanent?|Nej|Ja. Om du tar bort huvudkrypteringsnyckeln från Key Vault kan inte data i Data Lake Store-kontot dekrypteras av någon, inte heller av Data Lake Store-tjänsten. <br><br> Om du uttryckligen har säkerhetskopierat MEK innan du tog bort den från nyckelvalvet kan MEK återställas, och data kan återställas. Men om du inte har säkerhetskopierat huvudkrypteringsnyckeln innan den tas bort från nyckelvalvet kommer det sedan aldrig gå att dekryptera data på Data Lake Store-kontot.|


Förutom skillnaden när det gäller vem som hanterar huvudkrypteringsnyckeln och det nyckelvalv som nyckeln finns i är den övriga designen densamma för båda lägena.

Det är viktigt att komma ihåg följande när du väljer läge för huvudkrypteringsnycklarna:

*   Du kan välja om du vill använda kundhanterade eller tjänsthanterade nycklar när du etablerar ett Data Lake Store-konto.
*   När ett Data Lake Store-konto har etablerats går det inte att ändra läge.

### <a name="encryption-and-decryption-of-data"></a>Kryptering och dekryptering av data

Det finns tre typer av nycklar som används i utformningen av datakryptering. I följande tabell visas en sammanfattning:

| Nyckel                   | Förkortning | Kopplad till | Lagringsplats                             | Typ       | Anteckningar                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Huvudkrypteringsnyckel | MEK          | Ett Data Lake Store-konto | Key Vault                              | Asymmetrisk | Det kan hanteras av Data Lake Store eller dig.                                                              |
| Datakrypteringsnyckel   | DEK          | Ett Data Lake Store-konto | Beständig lagring – hanteras av Data Lake Store-tjänsten | Symmetrisk  | DEK krypteras av MEK. Den krypterade DEK lagras på permanenta medier. |
| Blockkrypteringsnyckel  | BEK          | Ett datablock | Ingen                                         | Symmetrisk  | BEK härleds från DEK och datablocket.                                                      |

Följande diagram illustrerar dessa begrepp:

![Nycklar i datakryptering](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritm när en fil ska dekrypteras:
1.  Kontrollera om DEK för Data Lake Store-kontot är cachelagrat och redo att användas.
    - Om inte, läser du den krypterade DEK från den beständiga lagringen och skickar den till Key Vault för dekryptering. Cachelagra den dekrypterade DEK i minnet. Nu är den redo att användas.
2.  För varje datablock i filen:
    - Läs det krypterade datablocket från den beständiga lagringen.
    - Generera BEK från DEK och det krypterade datablocket.
    - Använd BEK för att dekryptera data.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritm när ett datablock ska krypteras:
1.  Kontrollera om DEK för Data Lake Store-kontot är cachelagrat och redo att användas.
    - Om inte, läser du den krypterade DEK från den beständiga lagringen och skickar den till Key Vault för dekryptering. Cachelagra den dekrypterade DEK i minnet. Nu är den redo att användas.
2.  Generera en unik BEK för datablocket från DEK.
3.  Kryptera datablocket med BEK med hjälp av AES-256-kryptering.
4.  Lagra det krypterade datablocket i den beständiga lagringen.

> [!NOTE] 
> Av prestandaskäl cachelagras DEK i klartext i minnet under en kort tid och raderas omedelbart efteråt. I permanenta media lagras den alltid av huvudkrypteringsnyckeln (MEK).

## <a name="key-rotation"></a>Nyckelrotation

När du använder kundhanterade nycklar kan du rotera huvudkrypteringsnyckeln. Information om hur du konfigurerar ett Data Lake Store-konto med kundhanterade nycklar finns på sidan [Komma igång](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Krav

När du konfigurerar Data Lake Store-kontot har du valt att använda dina egna nycklar. Det här alternativet kan inte ändras efter att kontot har skapats. I anvisningarna nedan antas att du använder kundhanterade nycklar (att du valt egna nycklar från ditt nyckelvalv).

Observera att om du använder standardalternativen för kryptering krypteras data alltid med hjälp av nycklar som hanteras av Data Lake Store. Med det här alternativet kan har du inte möjlighet att rotera nycklar, eftersom de hanteras av Data Lake Store.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Så här roterar du MEK i Data Lake Store

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Navigera till det nyckelvalv där de nycklar som är kopplade till ditt Data Lake Store-konto lagras. Välj **Nycklar**.

    ![Skärmbild av Key Vault](./media/data-lake-store-encryption/keyvault.png)

3.  Välj nyckeln som är kopplad till ditt Data Lake Store-konto och skapa en ny version av nyckeln. Observera att Data Lake Store för närvarande endast stöder nyckelrotation till en ny version av en nyckel. Det stöder inte rotering till en annan nyckel.

   ![Skärmbild av nyckelfönstret med den nya versionen markerad](./media/data-lake-store-encryption/keynewversion.png)

4.  Gå till Azure Data Lake Store-lagringskontot och välj **Kryptering**.

    ![Skärmbild av Data Lake Store-lagringskontofönstret, med kryptering markerat](./media/data-lake-store-encryption/select-encryption.png)

5.  Ett meddelande informerar dig om att det finns en ny tillgänglig nyckelversion. Klicka på knappen **Rotate** (Rotera) för att uppdatera nyckeln till den nya versionen.

    ![Skärmbild av Data Lake Store-fönstret med meddelande och nyckelrotering markerat](./media/data-lake-store-encryption/rotatekey.png)

Den här åtgärden tar normalt mindre än två minuter och ingen stilleståndstid förväntas på grund av nyckelrotationen. När åtgärden har slutförts används den nya versionen av nyckeln.

