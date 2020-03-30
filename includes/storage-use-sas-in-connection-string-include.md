---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187561"
---
Om du har en SAS-URL (Shared Access Signature) som ger dig åtkomst till resurser i ett lagringskonto kan du använda SAS i en anslutningssträng. Eftersom SAS innehåller den information som krävs för att autentisera begäran, tillhandahåller en anslutningssträng med en SAS protokollet, tjänstslutpunkten och nödvändiga autentiseringsuppgifter för att komma åt resursen.

Om du vill skapa en anslutningssträng som innehåller en signatur för delad åtkomst anger du strängen i följande format:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Varje tjänstslutpunkt är valfri, även om anslutningssträngen måste innehålla minst en.

> [!NOTE]
> Att använda HTTPS med en SAS rekommenderas som en bästa praxis.
>
> Om du anger en SAS i en anslutningssträng i en konfigurationsfil kan du behöva koda specialtecken i URL:en.
>
>

### <a name="service-sas-example"></a>Exempel på service SAS
Här är ett exempel på en anslutningssträng som innehåller en tjänst SAS för Blob-lagring:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

Och här är ett exempel på samma anslutningssträng med kodning av specialtecken:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Exempel på konto SAS
Här är ett exempel på en anslutningssträng som innehåller ett konto SAS för Blob och Fillagring. Observera att slutpunkter för båda tjänsterna har angetts:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

Och här är ett exempel på samma anslutningssträng med URL-kodning:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

