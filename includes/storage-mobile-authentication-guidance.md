---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67673536"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurera ditt program för åtkomst till Azure Storage
Det finns två sätt att autentisera ditt program för att komma åt Lagringstjänster:

* Delad nyckel: Använd delad nyckel endast i testsyfte
* Signature för delad åtkomst (SAS): Använd SAS för produktionsprogram

### <a name="shared-key"></a>Delad nyckel
Autentisering med delad nyckel innebär att ditt program använder ditt kontonamn och kontonyckel för att komma åt lagringstjänster. För att snabbt visa hur du använder det här biblioteket använder vi autentisering med delad nyckel i den här starten.

> [!WARNING] 
> **Använd endast delad nyckelautentisering för testning!** Ditt kontonamn och kontonyckel, som ger fullständig läs-/skrivåtkomst till det associerade lagringskontot, kommer att distribueras till varje person som laddar ned din app. Detta är **inte** en god praxis som du riskerar att få din nyckel äventyras av opålitliga kunder.
> 
> 

När du använder autentisering med delad nyckel skapar du en [anslutningssträng](../articles/storage/common/storage-configure-connection-string.md). Anslutningssträngen består av:  

* **DefaultEndpointsProtocol** - du kan välja HTTP eller HTTPS. Det rekommenderas dock starkt att använda HTTPS.
* **Kontonamnet** - namnet på ditt lagringskonto
* **Kontonyckeln** - På [Azure Portal](https://portal.azure.com)navigerar du till ditt lagringskonto och klickar på **ikonen Nycklar** för att hitta den här informationen.
* (Valfritt) **EndpointSuffix** - Detta används för lagringstjänster i regioner med olika slutpunktssuffix, till exempel Azure China eller Azure Governance.

Här är ett exempel på anslutningssträng med autentisering av delad nyckel:

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Signaturer för delad åtkomst (SAS)
För ett mobilprogram är den rekommenderade metoden för att autentisera en begäran från en klient mot Azure Storage-tjänsten med hjälp av en SAS (Shared Access Signature). MED SAS kan du bevilja en klient åtkomst till en resurs under en angiven tidsperiod, med en angiven uppsättning behörigheter.
Som ägare av lagringskontot måste du skapa en SAS-enhet för att dina mobila klienter ska kunna använda. Om du vill generera SAS vill du förmodligen skriva en separat tjänst som genererar SAS som ska distribueras till dina klienter. I testsyfte kan du använda [Microsoft Azure Storage Explorer](https://storageexplorer.com) eller Azure [Portal](https://portal.azure.com) för att generera en SAS. När du skapar SAS kan du ange det tidsintervall som SAS är giltigt över och de behörigheter som SAS beviljar klienten.

I följande exempel visas hur du använder Microsoft Azure Storage Explorer för att generera en SAS.

1. Om du inte redan har gjort [det installerar du Microsoft Azure Storage Explorer](https://storageexplorer.com)
2. Ansluta till din prenumeration.
3. Klicka på ditt lagringskonto och klicka på fliken "Åtgärder" längst ned till vänster. Klicka på "Hämta signature för delad åtkomst" för att generera en "anslutningssträng" för din SAS.
4. Här är ett exempel på en SAS-anslutningssträng som ger läs- och skrivbehörighet på tjänst-, behållar- och objektnivå för blob-tjänsten för lagringskontot.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Som du kan se, när du använder en SAS, du inte exponera din kontonyckel i ditt program. Du kan läsa mer om SAS och metodtips för hur du använder SAS genom att checka ut [Signaturer för delad åtkomst: Förstå SAS-modellen](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

