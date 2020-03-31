---
title: Kryptering i Azure Data Lake Storage Gen1 | Microsoft-dokument
description: Kryptering i Azure Data Lake Storage Gen1 hjälper dig att skydda dina data, implementera företagets säkerhetsprinciper och uppfylla efterlevnadskraven. Den här artikeln innehåller en översikt över designen och beskriver några av de tekniska aspekterna av genomförandet.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878450"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Kryptering av data i Azure Data Lake Storage Gen1

Kryptering i Azure Data Lake Storage Gen1 hjälper dig att skydda dina data, implementera företagets säkerhetsprinciper och uppfylla efterlevnadskraven. Den här artikeln innehåller en översikt över designen och beskriver några av de tekniska aspekterna av genomförandet.

Data Lake Storage Gen1 stöder kryptering av data både i vila och under överföring. För data i vila stöder Data Lake Storage Gen1 "på som standard", transparent kryptering. En liten förklaring av vad detta betyder:

* **På som standard**: När du skapar ett nytt DataSjölagringsgenm1-konto aktiveras kryptering. Därefter krypteras data som lagras i Data Lake Storage Gen1 alltid innan de lagras på beständiga medier. Detta beteende gäller för alla data och kan inte ändras efter att ett konto har skapats.
* **Transparent**: Data Lake Storage Gen1 krypterar automatiskt data innan data sparas och dekrypterar data före hämtning. Krypteringen konfigureras och hanteras på datasjölagringsgend1-kontonivå av en administratör. Inga ändringar görs i dataåtkomst API: er. Därför krävs inga ändringar i program och tjänster som interagerar med Data Lake Storage Gen1 på grund av kryptering.

Data under överföring (kallas även data i rörelse) krypteras också alltid i Data Lake Storage Gen1. Förutom att kryptera data före lagring till permanenta media skyddas alltid även data under överföring eller i rörelse med hjälp av HTTPS. HTTPS är det enda protokoll som stöds för REST-gränssnitten Data Lake Storage Gen1 REST. Följande diagram visar hur data krypteras i Data Lake Storage Gen1:

![Diagram över datakryptering i Gen1 för lagring av datasjö](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Konfigurera kryptering med Gen1 för lagring av datasjö

Kryptering för Data Lake Storage Gen1 ställs in när kontot skapas och är alltid aktiverat som standard. Du kan antingen hantera nycklarna själv eller tillåta att Data Lake Storage Gen1 hanterar dem åt dig (detta är standard).

Mer information finns i [Komma igång](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Så här fungerar kryptering i Gen1 för lagring av datasjö

Följande information beskriver hur du hanterar huvudkrypteringsnycklar och det förklarar de tre olika typer av nycklar som du kan använda i datakryptering för Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Huvudkrypteringsnycklar

Data Lake Storage Gen1 ger två lägen för hantering av huvudkrypteringsnycklar (MEK). Anta att huvudkrypteringsnyckeln är nyckeln på högsta nivån. Åtkomst till huvudkrypteringsnyckeln krävs för att dekryptera data som lagras i Data Lake Storage Gen1.

Det finns två lägen för att hantera huvudkrypteringsnyckeln:

*   Tjänsthanterade nycklar
*   Kundhanterade nycklar

I båda lägena skyddas huvudkrypteringsnyckeln genom att den lagras i Azure Key Vault. Key Vault är en fullständigt hanterad tjänst på Azure med mycket hög säkerhet som kan användas för att skydda kryptografiska nycklar. Mer information finns i [Key Vault](https://azure.microsoft.com/services/key-vault).

Här är en kort jämförelse av funktionerna som tillhandahålls i de två lägena för att hantera huvudkrypteringsnycklar.

|  | Tjänsthanterade nycklar | Kundhanterade nycklar |
| --- | --- | --- |
|Hur lagras data?|Data krypteras alltid innan de lagras.|Data krypteras alltid innan de lagras.|
|Var lagras huvudkrypteringsnyckeln?|Key Vault|Key Vault|
|Finns det krypteringsnycklar som lagras i klartext utanför Key Vault? |Inga|Inga|
|Kan huvudkrypteringsnyckeln hämtas från Key Vault?|Nej. När den har lagrats i Key Vault kan den endast användas för kryptering och dekryptering.|Nej. När den har lagrats i Key Vault kan den endast användas för kryptering och dekryptering.|
|Vem äger Key Vault-instansen och huvudkrypteringsnyckeln?|Tjänsten Data Lake Storage Gen1|Du äger nyckelvalvsinstansen som ingår i din Azure-prenumeration. Huvudkrypteringsnyckeln i Key Vault kan hanteras av programvara eller maskinvara.|
|Kan du återkalla åtkomsten till MEK för tjänsten Data Lake Storage Gen1?|Inga|Ja. Du kan hantera åtkomstkontrollistor i Key Vault och ta bort åtkomstkontrollposter till tjänstidentiteten för tjänsten Data Lake Storage Gen1.|
|Kan man ta bort huvudkrypteringsnyckeln permanent?|Inga|Ja. Om du tar bort MEK från Key Vault kan data i datasjölagringsgenm1-kontot inte dekrypteras av någon, inklusive datasjölagringsgenm1-tjänsten. <br><br> Om du uttryckligen har säkerhetskopierat MEK innan du tog bort den från nyckelvalvet kan MEK återställas, och data kan återställas. Men om du inte har säkerhetskopierat MEK innan du tar bort den från Key Vault, kan data i Data Lake Storage Gen1-kontot aldrig dekrypteras därefter.|


Förutom skillnaden när det gäller vem som hanterar huvudkrypteringsnyckeln och det nyckelvalv som nyckeln finns i är den övriga designen densamma för båda lägena.

Det är viktigt att komma ihåg följande när du väljer läge för huvudkrypteringsnycklarna:

*   Du kan välja om du vill använda kundhanterade nycklar eller tjänsthanterade nycklar när du etablerar ett Data Lake Storage Gen1-konto.
*   När ett Data Lake Storage Gen1-konto har etablerats kan inte läget ändras.

### <a name="encryption-and-decryption-of-data"></a>Kryptering och dekryptering av data

Det finns tre typer av nycklar som används i utformningen av datakryptering. I följande tabell visas en sammanfattning:

| Nyckel                   | Förkortning | Kopplad till | Lagringsplats                             | Typ       | Anteckningar                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Huvudkrypteringsnyckel | MEK          | Ett Gen1-konto för lagring av datasjö | Key Vault                              | Asymmetrisk | Den kan hanteras av Data Lake Storage Gen1 eller dig.                                                              |
| Datakrypteringsnyckel   | DEK          | Ett Gen1-konto för lagring av datasjö | Beständig lagring, hanterad av tjänsten Data Lake Storage Gen1 | Symmetrisk  | DEK krypteras av MEK. Den krypterade DEK lagras på permanenta medier. |
| Blockkrypteringsnyckel  | BEK          | Ett datablock | Inget                                         | Symmetrisk  | BEK härleds från DEK och datablocket.                                                      |

Följande diagram illustrerar dessa begrepp:

![Nycklar i datakryptering](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritm när en fil ska dekrypteras:
1.  Kontrollera om DEK för DataSjölagring Gen1-kontot är cachelagrat och klart för användning.
    - Om inte, läser du den krypterade DEK från den beständiga lagringen och skickar den till Key Vault för dekryptering. Cachelagra den dekrypterade DEK i minnet. Nu är den redo att användas.
2.  För varje datablock i filen:
    - Läs det krypterade datablocket från den beständiga lagringen.
    - Generera BEK från DEK och det krypterade datablocket.
    - Använd BEK för att dekryptera data.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritm när ett datablock ska krypteras:
1.  Kontrollera om DEK för DataSjölagring Gen1-kontot är cachelagrat och klart för användning.
    - Om inte, läser du den krypterade DEK från den beständiga lagringen och skickar den till Key Vault för dekryptering. Cachelagra den dekrypterade DEK i minnet. Nu är den redo att användas.
2.  Generera en unik BEK för datablocket från DEK.
3.  Kryptera datablocket med BEK med hjälp av AES-256-kryptering.
4.  Lagra det krypterade datablocket i den beständiga lagringen.

> [!NOTE] 
> DEK lagras alltid krypterat av MEK på beständiga media eller i cacheminnet.

## <a name="key-rotation"></a>Nyckelrotation

När du använder kundhanterade nycklar kan du rotera huvudkrypteringsnyckeln. Mer information om hur du konfigurerar ett DataSjölagringsgenm1-konto med kundhanterade nycklar finns i [Komma igång](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Krav

När du konfigurerar datasjölagringsgenm1-kontot har du valt att använda dina egna nycklar. Det här alternativet kan inte ändras efter att kontot har skapats. I anvisningarna nedan antas att du använder kundhanterade nycklar (att du valt egna nycklar från ditt nyckelvalv).

Observera att om du använder standardalternativen för kryptering krypteras dina data alltid med hjälp av nycklar som hanteras av Data Lake Storage Gen1. I det här alternativet har du inte möjlighet att rotera nycklar, eftersom de hanteras av Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Så här roterar du MEK i Data Lake Storage Gen1

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Bläddra till Key Vault-instansen som lagrar dina nycklar som är associerade med ditt Data Lake Storage Gen1-konto. Välj **Nycklar**.

    ![Skärmbild av Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Välj nyckeln som är associerad med ditt Data Lake Storage Gen1-konto och skapa en ny version av den här nyckeln. Observera att Data Lake Storage Gen1 för närvarande endast stöder nyckelrotation till en ny version av en nyckel. Det stöder inte rotering till en annan nyckel.

   ![Skärmbild av nyckelfönstret med den nya versionen markerad](./media/data-lake-store-encryption/keynewversion.png)

4. Bläddra till datasjölagringsgenm1-kontot och välj **Kryptering**.

   ![Skärmbild av kontofönstret Data Lake Storage Gen1, med kryptering markerad](./media/data-lake-store-encryption/select-encryption.png)

5. Ett meddelande informerar dig om att det finns en ny tillgänglig nyckelversion. Klicka på knappen **Rotate** (Rotera) för att uppdatera nyckeln till den nya versionen.

   ![Skärmbild av fönstret Datasjölagring gen1 med meddelande och rotationsnyckel markerad](./media/data-lake-store-encryption/rotatekey.png)

Den här åtgärden tar normalt mindre än två minuter och ingen stilleståndstid förväntas på grund av nyckelrotationen. När åtgärden har slutförts används den nya versionen av nyckeln.

> [!IMPORTANT]
> När nyckelrotationen har slutförts används inte längre den äldre nyckelversionen aktivt till att kryptera data.  I sällsynta fall när ett oväntat fel inträffar och även redundanta kopior av dina data påverkas, kan dock data återställas från en säkerhetskopia som fortfarande använder den gamla nyckeln. Om du vill se till att dina data är tillgängliga i dessa sällsynta fall, behåller du en kopia av den tidigare versionen av krypteringsnyckeln. Se [vägledning om katastrofåterställning för data i DataSjölagring Gen1](data-lake-store-disaster-recovery-guidance.md) för bästa praxis för din planering av katastrofåterställning. 