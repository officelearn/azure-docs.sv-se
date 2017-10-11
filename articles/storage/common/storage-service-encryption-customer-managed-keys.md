---
title: "Azure Storage Service-kryptering med hjälp av kunden hanterade nycklar i Azure Key Vault | Microsoft Docs"
description: "Funktionen Azure Storage Service-kryptering för att kryptera din Azure-Blobblagring på tjänstsidan när data lagrades och dekryptera den hämtar data med kunden hanterade nycklar."
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Lagringstjänstens kryptering med hjälp av kunden hanterade nycklar i Azure Key Vault

Microsoft Azure är hjälper dig att skydda och skydda dina data för att uppfylla din organisations säkerhet och efterlevnad åtaganden.  Ett sätt som du kan skydda data i vila är att använda Storage Service kryptering (SSE), som automatiskt krypterar dina data när du skriver till lagring och dekrypterar data vid hämtning av den. Kryptering och dekryptering är automatisk, helt transparent och använder 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste blocket chiffer tillgängliga.

Du kan använda Microsoft-hanterad krypteringsnycklarna med SSE eller du kan använda dina egna krypteringsnycklar. Den här artikeln beskriver den senare. Mer information om hur du använder Microsoft-hanterad nycklar eller om SSE i allmänhet finns [Lagringstjänstens kryptering av vilande Data](../storage-service-encryption.md).

För att tillhandahålla möjligheten att använda era egna krypteringsnycklar är SSE för Blob storage integrerad med Azure Key Vault (AKV). Du kan skapa egna krypteringsnycklar och lagrar dem i AKV eller du kan använda AKVS API: er för att generera krypteringsnycklar. Inte bara AKV tillåter dig att hantera och kontrollera dina nycklar, det kan du granska din nyckelanvändning. 

Varför skulle du vilja skapa egna nycklar? Den ger dig större flexibilitet, så att du kan skapa, rotera, inaktivera och definiera åtkomstkontroller. Du kan också granska de krypteringsnycklar som används för att skydda dina data.

## <a name="sse-with-customer-managed-keys-preview"></a>SSE med kunden hanterade nycklar förhandsgranskning

Den här funktionen är för närvarande en förhandsversion. Om du vill använda den här funktionen måste du skapa ett nytt lagringskonto. Du kan antingen skapa ett nytt nyckelvalv och nyckel eller du kan använda en befintlig nyckelvalvet och nyckel. Storage-konto och nyckelvalvet måste vara i samma region, men de kan ha olika prenumerationer.

Om du vill delta i förhandsgranskningen Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com). Vi ger en speciell länk för att delta i förhandsgranskningen.

Mer information finns i den [vanliga frågor och svar](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest).

> [!IMPORTANT]
> Du måste registrera dig för förhandsgranskning innan du följer instruktionerna i den här artikeln. Utan förhandsåtkomst, kommer du inte att kunna aktivera den här funktionen i portalen.

## <a name="getting-started"></a>Komma igång
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Steg 1: [skapa ett nytt lagringskonto](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>Steg 2: Aktivera kryptering
Du kan aktivera SSE för storage-konto med den [Azure-portalen](https://portal.azure.com). Sök efter avsnittet Blob-tjänsten enligt följande bild och klicka på kryptering på inställningsbladet för lagringskontot.

![Skärmbild som visar Portal krypteringsalternativet](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*Aktivera SSE för Blob-tjänsten*

Om du vill programmässigt, aktivera eller inaktivera Storage Service-kryptering på ett lagringskonto kan du använda den [Azure Storage Resource Provider REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN), [Storage-klientbiblioteket för .NET](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN), [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0), eller [Azure CLI](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli).

På den här sidan om du inte ser kryssrutan ”Använd din egen nyckel” har du inte godkänts för förhandsgranskningen. Skicka ett e-postmeddelande till [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) och begära godkännande.

![Portalen skärmbild som visar kryptering Preview](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

Som standard använder SSE Microsoft-hanterad nycklar. Markera kryssrutan om du vill använda egna nycklar. Och kan du ange en nyckel URI eller välj en nyckel och Key Vault Väljaren.

## <a name="step-3-select-your-key"></a>Steg 3: Välj din nyckel

![Portalen skärmbild som visar krypteringar använda din egen nyckel alternativet](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![Portalen skärmbild som visar kryptering med viktiga uri-alternativ](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Du kan köra följande kommando använder Azure Powershell för att bevilja åtkomst till lagringskonton krävs nyckelvalvet om lagringskontot inte har åtkomst till Nyckelvalvet.

![Portalen skärmbild som visar åtkomst nekades för nyckelvalvet](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Du kan också ge åtkomst via Azure portal genom att gå till Azure Key Vault i Azure-portalen och bevilja åtkomst till lagringskontot.

## <a name="step-4-copy-data-to-storage-account"></a>Steg 4: Kopiera data till storage-konto
Om du vill överföra data till det nya kontot så att den är krypterad, se [steg 3 av komma igång i Lagringstjänstens kryptering av vilande Data](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account).

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>Steg 5: Fråga status för krypterade data
Om du vill fråga status för krypterade data, referera till [steg 4 i komma igång i Lagringstjänstens kryptering av vilande Data](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data).

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Vanliga frågor om Storage Service-kryptering för Data i vila
**F: Jag använder Premium-lagring. kan jag använda SSE med hanterade Kundnycklar?**

S: Ja SSE med Microsoft-hanterad och kunden hanterade nycklar stöds både standardlagring och Premium-lagring. 

**F: kan jag skapa ny storage-konton med SSE med hanterade Kundnycklar aktiverad med Azure PowerShell och Azure CLI?**

S: Ja.

**F: hur mycket mer kostar Azure Storage om SSE med kunden hanterade nycklar är aktiverad?**

S: det kostar för att använda Azure Key Vault. Mer information finns [Key Vault-priser](https://azure.microsoft.com/en-us/pricing/details/key-vault/). Det finns utan extra kostnad för att använda SSE.

**F: kan jag återkalla åtkomst till krypteringsnycklarna?**

S: Ja, du kan återkalla åtkomst när som helst. Det finns flera sätt att återkalla åtkomst till dina nycklar. Referera till [Azure Key Vault PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) och [Azure Key Vault CLI](https://docs.microsoft.com/en-us/cli/azure/keyvault) för mer information. Återkalla åtkomst blockerar effektivt åtkomst till alla BLOB storage-kontot eftersom kontot krypteringsnyckeln är otillgänglig genom Azure Storage.

**F: kan jag skapa ett lagringskonto och en nyckel i annan region?**

S: Nej, storage-konto och nyckel valvnyckeln måste finnas i samma region. 

**F: kan jag aktivera SSE med hanterade Kundnycklar när du skapar storage-konto?**

S: Nej. När du aktiverar SSE när lagringskontot kan använda du bara Microsoft-hanterad nycklar. Om du vill använda hanterade Kundnycklar måste du uppdatera lagringskontoegenskaperna. Du kan använda REST- eller en lagringsklientbiblioteken programmässigt uppdatera ditt lagringskonto eller uppdatera lagringskontoegenskaperna med Azure-portalen när du har skapat kontot.

**F: kan jag inaktivera kryptering medan med kunden SSE hanterade nycklar?**

A: du kan inte Nej, inaktivera kryptering när med kunden SSE hanterade nycklar. Om du vill inaktivera kryptering, måste du växla till med hjälp av Microsoft-hanterad nycklar. Du kan göra detta med hjälp av Azure portal eller PowerShell.

**F: är SSE aktiverad som standard när jag skapar ett nytt lagringskonto?**

S: SSE är inte aktiverad som standard. Du kan använda Azure-portalen för att aktivera den. Du kan också programmässigt aktivera den här funktionen med Storage Resource Provider REST API. 

**F: Jag kan inte aktivera kryptering på mitt lagringskonto.**

S: är det en Resource Manager storage-konto? Klassiska lagringskonton stöds inte. SSE med hanterade Kundnycklar kan även aktiveras endast nyskapade Resource Manager storage-konton.

**F: är SSE med kunden hanterade nycklar får endast i vissa områden?**

S: SSE är tillgänglig i endast vissa regioner för Blob storage för den här förhandsversionen. E-post [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) att söka efter tillgänglighet och information om förhandsgranskning. 

**F: hur kan jag få någon om jag har problem eller vill ge feedback?**

A: Kontakta [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) för eventuella problem som rör Storage Service-kryptering. 

## <a name="next-steps"></a>Nästa steg

*   Mer information om en omfattande uppsättning säkerhet funktioner som hjälper utvecklare att skapa säkra program, finns det [säkerhetsguiden för lagring](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide).
*   Mer information om Azure Key Vault finns [vad är Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)?
*   Komma igång på Azure Key Vault finns [komma igång med Azure Key Vault](../../key-vault/key-vault-get-started.md).
