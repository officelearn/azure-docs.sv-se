---
title: Översikt över Azure storage-konto | Microsoft Docs
description: Förstå alternativ för att skapa och använda ett Azure Storage-konto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/13/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: d7dbb808205c78b53277c6d916f5166a41c7e93d
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638434"
---
# <a name="azure-storage-account-overview"></a>Översikt över Azure storage-konto

Ett Azure storage-konto innehåller alla dina Azure Storage-dataobjekt: blobar, filer, köer, tabeller och diskar. Data i Azure storage-kontot är hållbar och med hög tillgänglighet, säkra, mycket skalbar och kan nås från var som helst i världen via HTTP eller HTTPS. 

Läs hur du skapar ett Azure storage-konto i [skapa ett lagringskonto](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Azure Storage erbjuder tre typer av lagringskonton. Varje typ av stöder olika funktioner och har en egen prismodell. Överväg att skillnaderna innan du skapar ett lagringskonto för att avgöra vilken typ av konto som är bäst för dina program. Typer av lagringskonton är:

* **[Gpv2-konton](#general-purpose-v2-accounts)**  (rekommenderas för de flesta fall)
* **[General-Purpose v1-konton](#general-purpose-v1-accounts)**
* **[BLOB storage-konton](#blob-storage-accounts)** 

I följande tabell beskrivs vilka typer av lagringskonton och deras funktioner:

| Storage Account-typ | Tjänster som stöds                       | Stöds prestandanivåer | Stöds åtkomstnivåerna               | Replikeringsalternativ                                                | Distributionsmodell<sup>1</sup>  | Kryptering<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| Generell användning V2   | BLOB, fil, kö, tabell och Disk       | Standard, Premium           | Frekvent, lågfrekvent, Arkiv<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager | Krypterade  |
| General-Purpose V1   | BLOB, fil, kö, tabell och Disk       | Standard, Premium           | Gäller inte                                  | LRS, GRS, RA-GRS                                                   | Resurshanteraren, klassisk  | Krypterade  |
| Blob Storage         | BLOB (blockblobbar och tilläggsblobbar endast) | Standard                    | Frekvent, lågfrekvent, Arkiv<sup>3</sup>                            | LRS, GRS, RA-GRS                                                   | Resource Manager  | Krypterade  |

<sup>1</sup>med hjälp av Azure Resource Manager-distributionsmodellen rekommenderas. Lagringskonton med hjälp av den klassiska distributionsmodellen kan fortfarande skapas på vissa platser och befintliga klassiska konton fortfarande användas. Mer information finns i [Azure Resource Manager och klassisk distribution: distributionsmodeller och dina resursers tillstånd](../../azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>alla lagringskonton har krypterats med Storage Service Encryption (SSE) för vilande data. Mer information finns i [Azure Storage Service Encryption för vilande Data](storage-service-encryption.md).

<sup>3</sup>the arkivnivån är tillgängliga på nivån för en enskild blob, inte på lagringskontonivån. Endast blockblob-objekt och lägger till BLOB-objekt kan arkiveras. Mer information finns i [Azure Blob storage: frekvent, lågfrekvent, och Arkivlagringsnivån](../blobs/storage-blob-storage-tiers.md).

<sup>4</sup>zone-redundant lagring (ZRS) är endast tillgänglig för standard gpv2-konton. Mer information om ZRS finns i [zonen-redundant lagring (ZRS): Azure Storage-program med hög tillgänglighet](storage-redundancy-zrs.md). Läs mer om andra replikeringsalternativ [Azure Storage-replikering](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>General-purpose v2-konton (GPv2)

Generell användning v2-konton stöder de senaste funktionerna i Azure Storage och alla funktioner i allmänna v1 och Blob storage-konton. Gpv2-konton leverera lägsta per gigabyte kapacitet priser för Azure Storage, samt bransch konkurrenskraftiga transaktionspriser. Generell användning v2-konton stöder dessa Azure Storage-tjänster:

- BLOB-objekt (alla typer: Block, Lägg till sida)
- Filer
- Diskar
- Köer
- Tabeller

> [!NOTE]
> Microsoft rekommenderar att du använder ett gpv2-lagringskonto för de flesta scenarier. Du kan enkelt uppgradera allmänna v1- eller Blob storage-konto till ett gpv2-konto utan avbrott och utan att behöva kopiera data.
>
> Mer information om hur du uppgraderar till ett gpv2-konto finns i [uppgradera till ett gpv2-lagringskonto](storage-account-upgrade.md). 

Storage-konton för generell användning v2 erbjuder flera åtkomstnivåerna för att lagra data baserat på dina användningsmönster. Mer information finns i [åtkomstnivåerna för block blob-data](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>General-purpose v1-konton (GPv1)

General-Purpose v1-konton ger åtkomst till alla Azure Storage-tjänster, men kanske inte har de senaste funktionerna eller det lägsta gigabytepriset. General-Purpose v1-konton stöder dessa Azure Storage-tjänster:

- BLOB-objekt (alla typer)
- Filer
- Diskar
- Köer
- Tabeller

Gpv2-konton rekommenderas i de flesta fall, är general-purpose v1-konton bäst för dessa scenarier: 

* Dina program kräver den klassiska distributionsmodellen. Gpv2-konton och Blob storage-konton stöder endast Azure Resource Manager-distributionsmodellen.

* Dina program är transaktionsintensiva eller använda bandbredd för betydande geo-replikering, men behöver inte stor kapacitet. General-purpose v1 kanske i det här fallet är det mest ekonomiska valet.

* Du använder en version av [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) som är äldre än 2014-02-14 eller ett klientbiblioteket med en tidigare version än 4.x och det inte går att uppgradera ditt program.

### <a name="blob-storage-accounts"></a>Blob Storage-konton

Blob storage-kontot är ett specialiserat lagringskonto för att lagra Ostrukturerade objektdata som blockblobar. BLOB storage-konton har samma tillförlitlighet, tillgänglighet, skalbarhet och avancerade funktioner som är tillgängliga med gpv2-konton. BLOB storage-konton stöder lagra blockblobbar och lägga till BLOB-objekt, men inte sidblobar.

BLOB storage-konton erbjuder flera åtkomstnivåerna för att lagra data baserat på dina användningsmönster. Mer information finns i [åtkomstnivåerna för block blob-data](#access-tiers-for-block-blob-data).

## <a name="naming-storage-accounts"></a>Namngivning av lagringskonton

Tänk på dessa regler när du namnger lagringskontot:

- Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
- Namnet på ditt lagringskonto måste vara unikt i Azure. Det får inte finnas två lagringskonton med samma namn.

## <a name="performance-tiers"></a>Prestandanivåer

Allmänna lagringskonton kan konfigureras för något av följande prestandanivåer:

* En standard prestandanivån för att lagra blobar, filer, tabeller, köer och Azure-datordiskar.
* En premium-prestandanivån för att lagra virtuell dator i Azure-diskar. En detaljerad översikt över Premium-lagring finns i [Premium Storage: högpresterande lagring för virtuella Azure-datorbelastningar](../../virtual-machines/windows/premium-storage.md).

## <a name="access-tiers-for-block-blob-data"></a>Åtkomstnivåerna för block blob-data

Azure Storage tillhandahåller olika alternativ för åtkomst till block blob-data baserat på användningsmönster. Varje åtkomstnivå i Azure Storage är optimerat för ett visst mönster för dataanvändning. Du kan lagra dina block blob-data på det mest kostnadseffektiva sättet genom att välja rätt åtkomstnivå för dina behov.

Tillgängliga åtkomstnivåer är:

> [!NOTE]
> Den [Premium åtkomstnivå](../blobs/storage-blob-storage-tiers.md#premium-access-tier) är tillgänglig i en begränsad förhandsversion som ett lokalt redundant lagring (LRS)-konto i Nordeuropa, USA, Öst 2, centrala USA och västra USA-regioner. Läs hur du registrera dig för förhandsversionen i [introduktion till Azure Premium-Bloblagring](http://aka.ms/premiumblob).

* Den **frekvent** åtkomstnivå, vilket är optimerad för frekvent åtkomst med objekt i lagringskontot. Åtkomst till data i den frekventa nivån är mest kostnadseffektiva, medan kostnader för lagring är något högre. Nya lagringskonton som skapas i frekvent nivå som standard.
* Den **lågfrekvent** åtkomstnivå, vilket är optimerad för att lagra stora mängder data som används sällan och som lagras i minst 30 dagar. Lagra data i den lågfrekventa nivån är mer kostnadseffektivt, men åtkomsten till dessa data kan vara något dyrare än att komma åt data på frekvent lagringsnivå.
* Den **Arkiv** åtkomstnivå, vilket är endast tillgänglig för enskilda blockblob-objekt. Arkivnivån är optimerad för data som kan tolerera flera timmars svarstid för hämtning och finns kvar på arkivnivån i minst 180 dagar. Arkivnivån är det mest kostnadseffektiva alternativet för att lagra data, men åtkomsten till dessa data är dyrare än att komma åt data i frekvent eller lågfrekvent nivå. 


Om det finns en ändring i användningsmönstret för dina data, kan du växla mellan de olika nivåerna när som helst. Läs mer om åtkomstnivåerna [Azure Blob storage: Premium (förhandsversion), frekvent, lågfrekvent och Arkivlagringsnivå](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Ändra åtkomstnivå för ett befintligt lagringskonto eller blob kan resultera i ytterligare avgifter. Mer information finns i den [lagringskonto fakturering avsnittet](#storage-account-billing).



## <a name="replication"></a>Replikering

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Mer information om storage-replikering finns i [Azure Storage-replikering](storage-redundancy.md).

## <a name="encryption"></a>Kryptering

På serversidan krypteras alla data i ditt lagringskonto. Läs mer om kryptering [Azure Storage Service Encryption för vilande data](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Slutpunkter för lagringskonto

Ett lagringskonto innehåller ett unikt namnområde i Azure för dina data. Alla objekt som du lagrar i Azure Storage har en adress som innehåller namnet på ditt unika. Kombinationen av namnet på kontot och Azure Storage service-slutpunkt utgör slutpunkterna för ditt lagringskonto.

Exempel: om din Allmänt lagringskonto heter *mystorageaccount*, så är standardslutpunkterna för kontot:

* BLOB-lagring: http://*mystorageaccount*. blob.core.windows.net
* Tabellagring: http://*mystorageaccount*. table.core.windows.net
* Köa lagring: http://*mystorageaccount*. queue.core.windows.net
* Azure Files: http://*mystorageaccount*. file.core.windows.net

> [!NOTE]
> Ett Blob storage-konto exponerar endast Blob service-slutpunkt.

URL: en för att komma åt ett objekt i ett lagringskonto skapas genom att lägga till att objektets plats i lagringskontot till slutpunkten. En blobbadress kan till exempel ha följande format: http://*mittlagringskonto*.blob.core.windows.net/*minbehållare*/*minblobb*.

Du kan också konfigurera ditt storage-konto om du vill använda en anpassad domän för BLOB. Mer information finns i [konfigurera ett anpassat domännamn för Azure Storage-kontot](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Kontrollera åtkomsten till kontodata

Som standard är data i ditt konto endast tillgängliga för dig, kontoägaren. Du har kontroll över vem som kan komma åt dina data och vilka behörigheter de har.

Varje begäran som görs mot ditt lagringskonto måste ha behörighet. På nivån för tjänsten begäran måste innehålla en giltig *auktorisering* rubriken, som innehåller all information som krävs för tjänsten för att verifiera begäran innan det körs.

Du kan bevilja åtkomst till data i ditt storage-konto med hjälp av någon av följande metoder:

- **Azure Active Directory:** Använd Azure Active Directory (Azure AD) autentiseringsuppgifter för att autentisera en användare, grupp eller andra identitet för åtkomst till blob och kö (förhandsversion). Om autentisering av en identitet lyckas returnerar en token som ska användas i auktorisera begäran till Azure Blob storage eller Queue storage med Azure AD. Mer information finns i [autentisera åtkomsten till Azure Storage med Azure Active Directory (förhandsversion)](storage-auth-aad.md).
- **Delad nyckel auktorisering:** använda din åtkomstnyckel för lagringskontot för att konstruera en anslutningssträng som programmet använder vid körning för att få åtkomst till Azure Storage. Värdena i anslutningssträngen som används för att konstruera den *auktorisering* -huvud som skickas till Azure Storage. Mer information finns i [konfigurera Azure Storage-anslutningssträngar](storage-configure-connection-string.md).
- **Signatur för delad åtkomst:** använda en signatur för delad åtkomst för att delegera åtkomst till resurser i ditt storage-konto om du inte använder Azure AD-autentisering. En signatur för delad åtkomst är en token som kapslar in all information som behövs för att auktorisera en begäran till Azure Storage på URL: en. Du kan ange i storage resurs- och behörigheterna intervallet behörigheterna som är giltig som en del av signaturen för delad åtkomst. Mer information finns i [använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Autentisera användare eller program med hjälp av autentiseringsuppgifter för Azure AD tillhandahåller överlägsen säkerhet och användarvänlighet över annan typ av auktorisering. Du kan fortsätta använda delad nyckel auktorisering med program, kringgår med hjälp av Azure AD behovet av att spara din åtkomstnyckel med din kod. Du kan också fortsätta att använda signaturer för delad åtkomst (SAS) för att ge detaljerad åtkomst till resurser i ditt storage-konto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller oroa dig om du återkallar en komprometterad SAS. 
>
> Microsoft rekommenderar att du använder Azure AD-autentisering för dina Azure Storage blob och kö program när det är möjligt.

## <a name="copying-data-into-a-storage-account"></a>Kopiera data till ett lagringskonto

Microsoft tillhandahåller verktyg och bibliotek för att importera dina data från lokala lagringsenheter eller molntjänster från tredje part lagringsprovidrar. Vilken lösning som du använder beror på mängden data som överförs. 

När du uppgraderar till ett gpv2-konto från en general-purpose v1 eller Blob storage-konto migreras automatiskt dina data. Microsoft rekommenderar att den här dina olika val för att uppgradera ditt konto. Men om du vill flytta data från ett general-purpose v1-konto till ett Blob storage-konto och sedan måste du migrera dina data manuellt, med verktyg och bibliotek som beskrivs nedan. 

### <a name="azcopy"></a>AzCopy

AzCopy är ett Windows-kommandoradsverktyg för högpresterande kopiering av data till och från Azure Storage. Du kan använda AzCopy för att kopiera data till Blob storage-kontot från ett befintligt Allmänt lagringskonto eller överföra data från lokala lagringsenheter. Mer information finns i [Överföra data med kommandoradsverktyget AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Bibliotek för flytt av data

Azure Storage-biblioteket för flytt av data för .NET är baserat på det ramverk för flytt av data som används för AzCopy. Biblioteket är utformat för högpresterande, tillförlitliga och enkla åtgärder för dataöverföring och liknar de som används i AzCopy. Du kan använda det för att utnyttja fördelarna med funktionerna som tillhandahålls av AzCopy direkt i din app utan att du behöver köra och övervaka externa instanser av AzCopy. Mer information finns i [Azure Storage Data Movement Library for .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST-API eller klientbibliotek

Du kan skapa en anpassad app för att migrera dina data till ett Blob Storage-konto med Azures klientbibliotek eller REST-API:t för Azure Storage-tjänster. Azure Storage innehåller omfattande klientbibliotek för flera språk och plattformar som .NET, Java, C++, Node.JS, PHP, Ruby och Python. Klientbiblioteken har avancerade funktioner, t.ex. logik för omprövning, loggning och parallell överföring. Du kan också utveckla direkt mot REST-API:t, som kan anropas med valfritt språk som kan skicka HTTP/HTTPS-begäranden.

Läs mer om Azure Storage REST API, [Azure Storage Services REST API-referens](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Blobar som krypteras med kryptering på klientsidan lagrar krypteringsrelaterade metadata tillsammans med bloben. Om du kopierar en blob som är krypterad med kryptering på klientsidan bör du se till att kopieringen bevarar blobmetadata och framför allt krypteringsrelaterade metadata. Om du kopierar en blob utan krypteringsmetadata kan blobinnehållet inte hämtas igen. Mer information om krypteringsrelaterade metadata finns i [Azure Storage Client Side Encryption](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Azure Import/Export-tjänsten

Om du har en stor mängd data som ska importeras till ditt lagringskonto kan du tjänsten Azure Import/Export. Import/Export-tjänsten används för att importera stora mängder data på ett säkert sätt till Azure Blob storage och Azure Files genom att leverera diskenheter till en Azure-datacenter. 

Import/Export-tjänsten kan också användas för att överföra data från Azure Blob storage till diskenheter och leverera till dina lokala platser. Data från en eller flera diskenheter kan importeras till Azure Blob storage eller Azure Files. Mer information finns i [vad är tjänsten Azure Import/Export?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Fakturering för lagringskonto

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Nästa steg

* Läs hur du skapar ett Azure storage-konto i [skapa ett lagringskonto](storage-quickstart-create-account.md).
* För att hantera eller ta bort ett befintligt lagringskonto, se [hantera Azure storage-konton](storage-account-manage.md).
