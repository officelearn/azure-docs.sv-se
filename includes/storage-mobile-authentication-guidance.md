---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 6911e06dc023027ab32b99387b9f7d3f5e708f86
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122750"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurera programmet för att få åtkomst till Azure Storage
Det finns två sätt att autentisera ditt program för att få åtkomst till lagringstjänster:

* Delad nyckel: Använd delad nyckel endast för testning
* Signatur för delad åtkomst (SAS): Använda SAS för program i produktion

### <a name="shared-key"></a>Delad nyckel
Autentisering med delad nyckel innebär att ditt program använder ditt kontonamn och nyckeln för att få åtkomst till lagringstjänster. Enligt snabbt visar hur du använder det här biblioteket, kommer vi att använda autentisering med delad nyckel i den här komma igång.

> [!WARNING] 
> **Endast använda autentisering med delad nyckel i testsyfte!** Ditt kontonamn och en kontonyckel, som ger fullständig åtkomst till det associera lagringskontot kommer att distribueras till varje person som laddar ned din app. Det här är **inte** en bra öva som riskerar du att få din nyckel som har drabbats av ej betrodda klienter.
> 
> 

När du använder autentisering med delad nyckel, skapar du en [anslutningssträngen](../articles/storage/common/storage-configure-connection-string.md). Strängen som består av:  

* Den **DefaultEndpointsProtocol** – du kan välja HTTP eller HTTPS. Men rekommenderas med hjälp av HTTPS starkt.
* Den **kontonamn** -namnet på ditt lagringskonto
* Den **Kontonyckel** – på den [Azure-portalen](https://portal.azure.com), navigera till ditt storage-konto och klicka på den **nycklar** ikon för att hitta den här informationen.
* (Valfritt) **EndpointSuffix** – det här används för lagringstjänster i regioner med annan slutpunkt suffix, till exempel Azure Kina eller Azure-styrning.

Här är ett exempel på anslutningssträngen med hjälp av autentisering med delad nyckel:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Signaturer för delad åtkomst (SAS)
För mobila program är den rekommenderade metoden för att autentisera en begäran från en klient mot Azure Storage-tjänsten med hjälp av en signatur för delad åtkomst (SAS). SAS kan du bevilja en klientåtkomst till en resurs för en angiven tidsperiod, med en angiven uppsättning behörigheter.
Som Kontoägare lagring måste du generera en SAS för mobila klienter att använda. Om du vill generera SAS vill du förmodligen att skriva en separat tjänst som genererar SAS ska distribueras till dina klienter. I testsyfte kan du använda den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) eller [Azure-portalen](https://portal.azure.com) att generera en SAS. När du har skapat SAS kan ange du tidsintervallet som Signaturen är giltig och de behörigheter som SAS ger till klienten.

I följande exempel visas hur du använder Microsoft Azure Storage Explorer för att generera en SAS.

1. Om du inte redan gjort [installera Microsoft Azure Storage Explorer](http://storageexplorer.com)
2. Ansluta till din prenumeration.
3. Klicka på ditt lagringskonto och klicka på fliken ”åtgärder” längst ned till vänster. Klicka på ”Hämta signatur för delad åtkomst” för att generera en ”anslutningssträng” för din SAS.
4. Här är ett exempel på en SAS-anslutningssträng som ger Läs- och skrivbehörighet på tjänsten, behållare och objektnivå för blob-tjänsten för Storage-konto.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Som du kan se när du använder en SAS, du inte exponera din kontonyckel i ditt program. Du kan lära dig mer om SAS och bästa praxis för att använda SAS genom att läsa [signaturer för delad åtkomst: Förstå SAS-modellen](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

