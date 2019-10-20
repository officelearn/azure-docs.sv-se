---
title: Azure Storage säkerhets guide | Microsoft Docs
description: Information om många metoder för att skydda Azure Storage, inklusive men inte begränsat till RBAC, Kryptering för lagringstjänst, kryptering på klient sidan, SMB 3,0 och Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 72e695762f2e45309787e6f62fa97aae4c959f34
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598092"
---
# <a name="azure-storage-security-guide"></a>Azure Storage säkerhets guide

Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner som tillsammans gör det möjligt för utvecklare att skapa säkra program:

- Alla data (inklusive metadata) som skrivs till Azure Storage krypteras automatiskt med hjälp av [kryptering för lagringstjänst (SSE)](storage-service-encryption.md). Mer information finns i avsnittet [om att presentera standard kryptering för Azure-blobar, filer, tabeller och Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) och rollbaserad Access Control (RBAC) stöds för Azure Storage för både resurs hanterings åtgärder och data åtgärder, enligt följande:   
    - Du kan tilldela RBAC-roller som är begränsade till lagrings kontot för säkerhets objekt och använda Azure AD för att auktorisera resurs hanterings åtgärder, till exempel nyckel hantering.
    - Azure AD-integrering stöds för blob-och Queue data-åtgärder. Du kan tilldela RBAC-roller begränsade till en prenumeration, en resurs grupp, ett lagrings konto eller en enskild behållare eller kö till ett säkerhets objekt eller en hanterad identitet för Azure-resurser. Mer information finns i [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory](storage-auth-aad.md).   
- Data kan skyddas vid överföring mellan ett program och Azure med hjälp av [kryptering på klient sidan](../storage-client-side-encryption.md), https eller SMB 3,0.  
- Operativ system och data diskar som används av virtuella Azure-datorer kan krypteras med hjälp av [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
- Delegerad åtkomst till data objekt i Azure Storage kan beviljas med hjälp av en signatur för delad åtkomst. Mer information finns i [bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av signaturer för delad åtkomst (SAS)](storage-sas-overview.md).

Den här artikeln innehåller en översikt över de säkerhets funktioner som kan användas med Azure Storage. Det finns länkar till artiklar som innehåller information om varje funktion så att du enkelt kan utföra ytterligare undersökningar på varje ämne.

Här följer de avsnitt som beskrivs i den här artikeln:

* [Säkerhet för hanterings plan](#management-plane-security) – skydda ditt lagrings konto

  Hanterings planet består av de resurser som används för att hantera ditt lagrings konto. I det här avsnittet beskrivs Azure Resource Manager distributions modell och hur du använder rollbaserad Access Control (RBAC) för att kontrol lera åtkomsten till dina lagrings konton. Den behandlar också hantering av lagrings konto nycklar och hur du återskapar dem.
* [Säkerhet för data planet](#data-plane-security) – skydda åtkomsten till dina data

  I det här avsnittet ska vi titta på att tillåta åtkomst till de faktiska data objekten på ditt lagrings konto, till exempel blobbar, filer, köer och tabeller, med hjälp av signaturer för delad åtkomst och lagrade åtkomst principer. Vi kommer att avse SAS på både service nivå och konto nivå. Vi kommer också att se hur du begränsar åtkomsten till en speciell IP-adress (eller ett intervall med IP-adresser), hur du begränsar det protokoll som används för HTTPS och hur du återkallar en signatur för delad åtkomst utan att vänta tills den upphör att gälla.
* [Kryptering vid överföring](#encryption-in-transit)

  I det här avsnittet beskrivs hur du skyddar data när du överför dem till eller från Azure Storage. Vi pratar om den rekommenderade användningen av HTTPS och kryptering som används av SMB 3,0 för Azure-filresurser. Vi tar också en titt på kryptering på klient sidan, vilket gör att du kan kryptera data innan de överförs till lagringen i ett klient program och dekryptera data när de har överförts från lagrings utrymmet.
* [Kryptering i vila](#encryption-at-rest)

  Vi kommer att prata om Kryptering för lagringstjänst (SSE), som nu aktive ras automatiskt för nya och befintliga lagrings konton. Vi kommer också att se hur du kan använda Azure Disk Encryption och utforska de grundläggande skillnaderna och fallen av disk kryptering jämfört med SSE jämfört med kryptering på klient sidan. Vi ska kort se FIPS-kompatibiliteten för amerikanska myndighets datorer.
* Använda [Lagringsanalys](#storage-analytics) för att granska åtkomst till Azure Storage

  I det här avsnittet beskrivs hur du hittar information i Storage Analytics-loggarna för en begäran. Vi tar en titt på Real Storage Analytics-loggdata och se hur du fram om en begäran görs med lagrings konto nyckeln, med en signatur för delad åtkomst eller anonymt, och om den lyckades eller misslyckades.
* [Aktivera webbläsarbaserade klienter med CORS](#cross-origin-resource-sharing-cors)

  Det här avsnittet handlar om hur du tillåter resurs delning mellan ursprung (CORS). Vi pratar om åtkomst mellan domäner och hur du hanterar det med de CORS-funktioner som är inbyggda i Azure Storage.

## <a name="management-plane-security"></a>Säkerhet för hanterings plan
Hanterings planet består av åtgärder som påverkar själva lagrings kontot. Du kan till exempel skapa eller ta bort ett lagrings konto, hämta en lista över lagrings konton i en prenumeration, Hämta lagrings konto nycklar eller återskapa lagrings konto nycklarna.

När du skapar ett nytt lagrings konto väljer du en distributions modell av Classic eller Resource Manager. Den klassiska modellen för att skapa resurser i Azure tillåter bara all-eller-ingen åtkomst till prenumerationen, och i sin tur är lagrings kontot.

Den här guiden fokuserar på Resource Manager-modellen som är det rekommenderade sättet att skapa lagrings konton. Med lagrings kontona i Resource Manager, i stället för att ge åtkomst till hela prenumerationen, kan du kontrol lera åtkomsten på en mer begränsad nivå till hanterings planet med rollbaserad Access Control (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Skydda ditt lagrings konto med rollbaserad Access Control (RBAC)
Låt oss prata om vad RBAC är och hur du kan använda det. Varje Azure-prenumerationen har en Azure Active Directory. Användare, grupper och program från den katalogen kan beviljas åtkomst för att hantera resurser i Azure-prenumerationen som använder distributions modellen för Resource Manager. Den här typen av säkerhet kallas för rollbaserad Access Control (RBAC). Om du vill hantera den här åtkomsten kan du använda [Azure Portal](https://portal.azure.com/), [Azure CLI-verktyg](../../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs)eller [Azure Storage-resurs leverantörens REST-API: er](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Med Resource Manager-modellen ska du lagra lagrings kontot i en resurs grupp och kontrol lera åtkomsten till hanterings planet för det angivna lagrings kontot med hjälp av Azure Active Directory. Du kan till exempel ge vissa användare möjlighet att komma åt lagrings konto nycklarna, medan andra användare kan visa information om lagrings kontot, men inte åtkomst till lagrings konto nycklarna.

#### <a name="granting-access"></a>Bevilja åtkomst
Åtkomst beviljas genom att tilldela lämplig RBAC-roll till användare, grupper och program, i rätt omfång. Om du vill bevilja åtkomst till hela prenumerationen tilldelar du en roll på prenumerations nivån. Du kan bevilja åtkomst till alla resurser i en resurs grupp genom att bevilja behörighet till själva resurs gruppen. Du kan också tilldela vissa roller till vissa resurser, till exempel lagrings konton.

Här är de viktigaste punkterna som du behöver känna till om att använda RBAC för att få åtkomst till hanterings åtgärder för ett Azure Storage-konto:

* När du tilldelar åtkomst tilldelar du i princip en roll till det konto som du vill ha åtkomst till. Du kan kontrol lera åtkomsten till de åtgärder som används för att hantera lagrings kontot, men inte till data objekt i kontot. Du kan till exempel bevilja behörighet att hämta egenskaperna för lagrings kontot (till exempel redundans), men inte till en behållare eller data i en behållare i Blob Storage.
* För att någon ska ha behörighet att komma åt data objekt i lagrings kontot kan du ge dem behörighet att läsa lagrings konto nycklarna och användaren kan sedan använda nycklarna för att få åtkomst till blobbar, köer, tabeller och filer.
* Roller kan tilldelas till ett enskilt användar konto, en grupp användare eller till ett enskilt program.
* Varje roll har en lista med åtgärder och inte åtgärder. Rollen virtuell dator deltagare har till exempel åtgärden "Listnycklar" som tillåter lagrings konto nycklarna att läsas. Deltagaren har "inte åtgärder", till exempel uppdatering av åtkomsten för användare i Active Directory.
* Roller för lagring inkluderar (men är inte begränsade till) följande roller:

  * Ägare – de kan hantera allt, inklusive åtkomst.
  * Deltagare – de kan göra vad ägaren kan göra förutom att tilldela åtkomst. Någon med den här rollen kan visa och återskapa lagrings konto nycklarna. Med lagrings konto nycklarna kan de komma åt data objekt.
  * Läsare – de kan visa information om lagrings kontot, förutom hemligheter. Om du till exempel tilldelar en roll med läsar behörighet för lagrings kontot till någon, kan de se egenskaperna för lagrings kontot, men de kan inte göra några ändringar i egenskaperna eller Visa lagrings konto nycklarna.
  * Lagrings konto deltagare – de kan hantera lagrings kontot – de kan läsa prenumerationens resurs grupper och resurser och skapa och hantera distributioner av resurs grupper för prenumerationer. De kan också komma åt lagrings konto nycklarna, vilket i sin tur innebär att de kan komma åt data planet.
  * Administratör för användar åtkomst – de kan hantera användar åtkomst till lagrings kontot. De kan till exempel ge läsare åtkomst till en speciell användare.
  * Virtuell dator deltagare – de kan hantera virtuella datorer, men inte lagrings kontot som de är anslutna till. Den här rollen kan visa lagrings konto nycklar, vilket innebär att användaren som du tilldelar rollen kan uppdatera data planet.

    För att en användare ska kunna skapa en virtuell dator måste de kunna skapa motsvarande VHD-fil i ett lagrings konto. För att göra det måste de kunna hämta lagrings konto nyckeln och skicka den till API: et som skapar den virtuella datorn. De måste därför ha den här behörigheten så att de kan visa lagrings konto nycklarna.
* Möjligheten att definiera anpassade roller är en funktion som gör att du kan skapa en uppsättning åtgärder från en lista över tillgängliga åtgärder som kan utföras på Azure-resurser.
* Användaren måste konfigureras i Azure Active Directory innan du kan tilldela dem en roll.
* Du kan skapa en rapport över vem som har beviljat/återkallat vilken typ av åtkomst till/från vem och i vilken omfattning som använder PowerShell eller Azure CLI.

#### <a name="resources"></a>Resurser
* [Azure Active Directory rollbaserad åtkomstkontroll](../../role-based-access-control/role-assignments-portal.md)

  Den här artikeln förklarar Azure Active Directorys rollbaserade åtkomstkontroll och hur den fungerar.
* [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md)

  I den här artikeln beskrivs alla inbyggda roller som är tillgängliga i RBAC.
* [Förstå Resource Manager-distribution och klassisk distribution](../../azure-resource-manager/resource-manager-deployment-model.md)

  Den här artikeln beskriver distributions modellerna Resource Manager och klassiska distributioner och förklarar fördelarna med att använda Resource Manager och resurs grupper. Det förklarar hur Azure Compute-, Network-och Storage-leverantörer fungerar under Resource Manager-modellen.
* [Hantera rollbaserad åtkomstkontroll med REST API](../../role-based-access-control/role-assignments-rest.md)

  Den här artikeln visar hur du använder REST API för att hantera RBAC.
* [Referens för REST-API för Azure Storage Resource Provider](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  Denna API-referens beskriver de API: er som du kan använda för att hantera ditt lagrings konto program mässigt.

* [Rollbaserad åtkomstkontroll i Microsoft Azure från Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Det här är en länk till en video på Channel 9 från 2015 MS Ignite-konferensen. I den här sessionen talar de om åtkomsthantering och rapporteringsfunktioner i Azure och utforskar bästa praxis när det gäller att säkra åtkomst till Azure-prenumerationer med Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>Hantera dina lagrings konto nycklar
Lagrings konto nycklar är 512-bitars strängar som skapats av Azure och som tillsammans med lagrings kontots namn, kan användas för att komma åt de data objekt som lagras i lagrings kontot, till exempel blobbar, entiteter i en tabell, köa meddelanden och filer på en Azure-filresurs. Att kontrol lera åtkomsten till lagrings konto nycklarna styr åtkomsten till data planet för det lagrings kontot.

Varje lagrings konto har två nycklar som kallas "nyckel 1" och "nyckel 2" i [Azure Portal](https://portal.azure.com/) och i PowerShell-cmdletar. Dessa kan återskapas manuellt med hjälp av en av flera metoder, inklusive, men inte begränsat till att använda [Azure Portal](https://portal.azure.com/), PowerShell, Azure CLI eller program mässigt med hjälp av .net-lagrings klient biblioteket eller Azure Storage tjänster REST API.

Det finns ett antal skäl för att återskapa dina lagrings konto nycklar.

* Du kan återskapa dem regelbundet av säkerhets skäl.
* Du skulle återskapa dina lagrings konto nycklar om någon har hanterat att hacka i ett program och hämta nyckeln som var hårdkodad eller sparade i en konfigurations fil, vilket ger dem fullständig åtkomst till ditt lagrings konto.
* Ett annat exempel är om ditt team använder ett Storage Explorer program som behåller lagrings konto nyckeln och en av team medlemmarna lämnar. Programmet fortsätter att fungera, vilket ger dem åtkomst till ditt lagrings konto när de har varit borta. Detta är faktiskt den primära orsaken som de skapade signaturer för delad åtkomst på konto nivå – du kan använda en SAS på konto nivå i stället för att lagra åtkomst nycklarna i en konfigurations fil.

#### <a name="key-regeneration-plan"></a>Plan för återskapande av nyckel
Du vill inte bara återskapa den nyckel som du använder utan planering. Om du gör det kan du kapa all åtkomst till lagrings kontot, vilket kan orsaka större störningar. Det finns därför två nycklar. Du bör återskapa en nyckel i taget.

Innan du återskapar nycklar, se till att du har en lista över alla dina program som är beroende av lagrings kontot, samt andra tjänster som du använder i Azure. Om du till exempel använder Azure Media Services som är beroende av ditt lagrings konto, måste du synkronisera om åtkomst nycklarna med medie tjänsten när du har återskapat nyckeln. Om du använder ett program, till exempel en lagrings Utforskare, behöver du även ange de nya nycklarna till dessa program. Om du har virtuella datorer vars VHD-filer lagras i lagrings kontot kommer de inte att påverkas av återskapande av lagrings konto nycklar.

Du kan återskapa dina nycklar i Azure Portal. När nycklarna har återskapats kan de ta upp till 10 minuter att synkroniseras över lagrings tjänsterna.

När du är klar är det här den allmänna processen som beskriver hur du ska ändra din nyckel. I det här fallet är antagandet att du för närvarande använder nyckel 1 och du kommer att ändra allt för att använda nyckel 2 i stället.

1. Återskapa nyckel 2 för att säkerställa att den är säker. Det kan du göra i Azure Portal.
2. I alla program där lagrings nyckeln lagras ska du ändra lagrings nyckeln så att det nya värdet för nyckel 2 används. Testa och publicera programmet.
3. Återskapa nyckel 1 när alla program och tjänster har körts. Detta säkerställer att vem som du inte uttryckligen har fått den nya nyckeln inte längre kommer att ha åtkomst till lagrings kontot.

Om du för närvarande använder nyckel 2 kan du använda samma process, men byta nyckel namn.

Du kan migrera över några dagar, ändra varje program till att använda den nya nyckeln och publicera det. När alla är klara bör du gå tillbaka och återskapa den gamla nyckeln så att den inte längre fungerar.

Ett annat alternativ är att ange lagrings konto nyckeln i en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som en hemlighet och att dina program hämtar nyckeln därifrån. När du sedan återskapar nyckeln och uppdaterar den Azure Key Vault, behöver inte programmen omdistribueras eftersom de kommer att hämta den nya nyckeln från Azure Key Vault automatiskt. Observera att du kan få programmet att läsa nyckeln varje gång du behöver det, eller så kan du cachelagra det i minnet och om det Miss lyckas när du använder det, hämtar du nyckeln igen från Azure Key Vault.

Med Azure Key Vault lägger också till en annan säkerhets nivå för dina lagrings nycklar. Om du använder den här metoden kommer du aldrig att ha lagrings nyckeln hårdkodad i en konfigurations fil, vilket tar bort minimering från någon som får åtkomst till nycklarna utan någon speciell behörighet.

En annan fördel med att använda Azure Key Vault är att du också kan styra åtkomsten till dina nycklar med hjälp av Azure Active Directory. Det innebär att du kan bevilja åtkomst till fåtal för program som behöver hämta nycklar från Azure Key Vault och vet att andra program inte kommer att kunna komma åt nycklarna utan att ge dem behörighet.

> [!NOTE]
> Microsoft rekommenderar att du bara använder en av nycklarna i alla dina program på samma tidpunkt. Om du använder nyckel 1 på vissa platser och nyckel 2 i andra kommer du inte att kunna rotera dina nycklar utan att några program förlorar åtkomst.

#### <a name="resources"></a>Resurser

* [Hantera inställningar för lagringskonton i Azure-portalen](storage-account-manage.md)
* [Referens för REST-API för Azure Storage Resource Provider](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Säkerhet för data planet
Säkerhet för data planet avser de metoder som används för att skydda de data objekt som lagras i Azure Storage – blobbar, köer, tabeller och filer. Vi har sett metoder för att kryptera data och säkerhet vid överföring av data, men hur gör du för att kontrol lera åtkomsten till objekten?

Du har tre alternativ för att auktorisera åtkomst till data objekt i Azure Storage, inklusive:

- Använda Azure AD för att ge åtkomst till behållare och köer. Azure AD ger fördelar jämfört med andra metoder för auktorisering, inklusive att ta bort behovet av att lagra hemligheter i din kod. Mer information finns i [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory](storage-auth-aad.md). 
- Använd lagrings konto nycklar för att auktorisera åtkomst via delad nyckel. Att auktorisera via delad nyckel kräver lagrings konto nycklar i ditt program, så Microsoft rekommenderar att du använder Azure AD i stället där det är möjligt.
- Använd signaturer för delad åtkomst för att ge kontrollerade behörigheter till vissa data objekt under en angiven tids period.

För Blob Storage kan du dessutom bevilja offentlig åtkomst till dina blobbar genom att ange åtkomst nivån för den behållare som innehåller blobarna i enlighet därmed. Om du anger åtkomst för en behållare till BLOB eller container får den offentlig Läs behörighet för blobarna i behållaren. Det innebär att alla med en URL som pekar på en BLOB i den behållaren kan öppna den i en webbläsare utan att använda en signatur för delad åtkomst eller med lagrings konto nycklarna.

Förutom att begränsa åtkomst via auktorisering kan du också använda [brand väggar och virtuella nätverk](storage-network-security.md) för att begränsa åtkomsten till lagrings kontot baserat på nätverks regler.  Med den här metoden kan du neka åtkomst till offentlig Internet trafik och endast ge åtkomst till vissa virtuella Azure-nätverk eller offentliga IP-adressintervall.

### <a name="storage-account-keys"></a>Lagringskontonycklar
Lagrings konto nycklar är 512-bitars strängar som skapats av Azure och som, tillsammans med lagrings kontots namn, kan användas för att komma åt de data objekt som lagras i lagrings kontot.

Du kan till exempel läsa blobbar, skriva till köer, skapa tabeller och ändra filer. Många av de här åtgärderna kan utföras via Azure Portal eller med ett av många Storage Explorer-program. Du kan också skriva kod för att använda REST API eller ett av lagrings klient biblioteken för att utföra dessa åtgärder.

Som beskrivs i avsnittet om [hanterings planens säkerhet](#management-plane-security)kan åtkomst till lagrings nycklarna för ett klassiskt lagrings konto beviljas genom att ge fullständig åtkomst till Azure-prenumerationen. Åtkomst till lagrings nycklarna för ett lagrings konto med hjälp av Azure Resource Manager modellen kan styras via rollbaserad Access Control (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Så här delegerar du åtkomst till objekt i ditt konto med hjälp av signaturer för delad åtkomst och lagrade åtkomst principer
En signatur för delad åtkomst är en sträng som innehåller en säkerhetstoken som kan kopplas till en URI som gör att du kan delegera åtkomst till lagrings objekt och ange begränsningar, till exempel behörigheter och datum/tid-intervallet för åtkomst.

Du kan bevilja åtkomst till blobbar, behållare, köa meddelanden, filer och tabeller. Med tabeller kan du faktiskt bevilja behörighet att komma åt ett intervall med entiteter i tabellen genom att ange de partition-och rad nyckel intervall som du vill att användaren ska ha åtkomst till. Om du till exempel har data som lagras med en partitionsnyckel med geografisk status kan du ge någon åtkomst till enbart data för Kalifornien.

I ett annat exempel kan du ge ett webb program en SAS-token som gör det möjligt att skriva poster till en kö och ge en arbets roll program en SAS-token för att hämta meddelanden från kön och bearbeta dem. Eller så kan du ge en kund ett SAS-token som de kan använda för att ladda upp bilder till en behållare i Blob Storage och ge ett webb program behörighet att läsa dessa bilder. I båda fallen är det en åtskillnad mellan problem – varje program kan få bara den åtkomst som krävs för att utföra sina uppgifter. Detta är möjligt genom att signaturer för delad åtkomst används.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Varför du vill använda signaturer för delad åtkomst
Varför skulle du vilja använda en SAS i stället för att bara ange din lagrings konto nyckel, vilket är så mycket enklare? Att ge ut din lagrings konto nyckel är som att dela nycklarna i din lagrings kungariket. Den ger fullständig åtkomst. Någon kan använda dina nycklar och ladda upp hela musik biblioteket till ditt lagrings konto. De kan också ersätta dina filer med virus-infekterade versioner eller stjäla dina data. Att ge bort obegränsad åtkomst till ditt lagrings konto är något som inte bör beaktas lätt.

Med signaturer för delad åtkomst kan du ge en klient bara de behörigheter som krävs under en begränsad tid. Om någon till exempel laddar upp en blob till ditt konto, kan du ge dem Skriv behörighet för just nu tillräckligt med tid för att ladda upp bloben (beroende på blobens storlek, naturligtvis). Och om du ändrar dig kan du återkalla åtkomsten.

Dessutom kan du ange att förfrågningar som görs med hjälp av en SAS är begränsade till en viss IP-adress eller IP-adressintervall utanför Azure. Du kan också kräva att begär Anden görs med hjälp av ett speciellt protokoll (HTTPS eller HTTP/HTTPS). Det innebär att om du bara vill tillåta HTTPS-trafik kan du ange det protokoll som krävs till endast HTTPS, och HTTP-trafik kommer att blockeras.

#### <a name="definition-of-a-shared-access-signature"></a>Definition av signaturen för delad åtkomst
En signatur för delad åtkomst är en uppsättning frågeparametrar som läggs till i URL: en som pekar på resursen

ger information om åtkomst tillåten och hur länge åtkomst tillåts. Här är ett exempel. Denna URI ger Läs behörighet till en BLOB i fem minuter. Observera att SAS-frågeparametrar måste vara URL-kodade, till exempel% 3A för kolon (:) eller %20 för ett blank steg.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Hur signaturen för delad åtkomst auktoriseras av tjänsten Azure Storage
När lagrings tjänsten tar emot begäran, tar den emot indataparametrarna och skapar en signatur med samma metod som det anropande programmet. Den jämför sedan de två signaturerna. Om de samtycker kan lagrings tjänsten kontrol lera lagrings tjänst versionen för att kontrol lera att den är giltig, kontrol lera att det aktuella datumet och tiden är inom det angivna fönstret, se till att den begärda åtkomsten motsvarar förfrågan som gjorts osv.

Till exempel, med vår URL ovan, om URL: en pekar på en fil i stället för en BLOB, skulle den här begäran inte utföras eftersom den anger att signaturen för delad åtkomst är för en blob. Om REST-kommandot som anropades var för att uppdatera en BLOB misslyckades det eftersom signaturen för delad åtkomst anger att endast Läs behörighet tillåts.

#### <a name="types-of-shared-access-signatures"></a>Typer av signaturer för delad åtkomst
* En SAS på service nivå kan användas för att få åtkomst till vissa resurser i ett lagrings konto. Några exempel på detta är att hämta en lista över blobbar i en behållare, hämta en BLOB, uppdatera en entitet i en tabell, lägga till meddelanden i en kö eller ladda upp en fil till en fil resurs.
* En SAS på konto nivå kan användas för att få åtkomst till allt som en SAS på service nivå kan användas för. Dessutom kan den ge alternativ till resurser som inte är tillåtna med en SAS på service nivå, till exempel möjligheten att skapa behållare, tabeller, köer och fil resurser. Du kan också ange åtkomst till flera tjänster samtidigt. Du kan till exempel ge någon åtkomst till både blobar och filer i ditt lagrings konto.

#### <a name="creating-a-sas-uri"></a>Skapa en SAS-URI
1. Du kan skapa en URI på begäran och definiera alla frågeparametrar varje tillfälle.

   Den här metoden är flexibel, men om du har en logisk uppsättning parametrar som liknar varje gång, är det en bättre idé att använda en lagrad åtkomst princip.
2. Du kan skapa en lagrad åtkomst princip för en hel behållare, fil resurs, tabell eller kö. Sedan kan du använda detta som grund för de SAS-URI: er som du skapar. Behörigheter som baseras på lagrade åtkomst principer kan enkelt återkallas. Du kan ha upp till fem principer definierade för varje behållare, kö, tabell eller fil resurs.

   Om du till exempel vill att många användare ska läsa Blobbarna i en speciell behållare kan du skapa en lagrad åtkomst princip som säger "ge Läs behörighet" och andra inställningar som ska vara identiska varje gång. Sedan kan du skapa en SAS-URI med inställningarna för den lagrade åtkomst principen och ange förfallo datum/tid. Fördelen med detta är att du inte behöver ange alla frågeparametrar varje gång.

#### <a name="revocation"></a>Återkallade
Anta att din SAS har komprometterats eller att du vill ändra den på grund av företagets säkerhets-eller reglerings krav. Hur återkallar du åtkomst till en resurs med den SAS-funktionen? Det beror på hur du skapade SAS-URI: n.

Om du använder ad hoc-URI: er har du tre alternativ. Du kan utfärda SAS-token med korta förfallo principer och vänta tills SAS-tiden upphör att gälla. Du kan byta namn på eller ta bort resursen (förutsatt att token har tilldelats ett enskilt objekt). Du kan ändra lagrings kontots nycklar. Det här sista alternativet kan ha en betydande inverkan, beroende på hur många tjänster som använder lagrings kontot och det är förmodligen inte något som du vill göra utan någon planering.

Om du använder en SAS som härletts från en lagrad åtkomst princip kan du ta bort åtkomst genom att återkalla den lagrade åtkomst principen – du kan ändra den så att den redan har gått ut, eller så kan du ta bort den helt. Detta börjar gälla omedelbart och inaktive ras varje SAS som skapats med den lagrade åtkomst principen. Om du uppdaterar eller tar bort den lagrade åtkomst principen kan det påverka användare som har åtkomst till den aktuella behållaren, fil resursen, tabellen eller kön via SAS, men om klienterna skrivs så att de begär en ny säkerhets Association när den gamla blir ogiltig, fungerar detta bra.

Eftersom användning av en SAS som härletts från en lagrad åtkomst princip ger dig möjlighet att återkalla SAS omedelbart, är det rekommenderade bästa praxis att alltid använda lagrade åtkomst principer när det är möjligt.

#### <a name="resources"></a>Resurser
Mer detaljerad information om hur du använder signaturer för delad åtkomst och lagrade åtkomst principer, komplett med exempel, finns i följande artiklar:

* Detta är referens artiklarna.

  * [Tjänstens SAS](https://msdn.microsoft.com/library/dn140256.aspx)

    Den här artikeln innehåller exempel på hur du kan använda en SAS på service nivå med blobbar, köa meddelanden, tabell intervall och filer.
  * [Skapa en tjänst-SAS](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Skapa ett konto SAS](https://msdn.microsoft.com/library/mt584140.aspx)

* Autentisering

  * [Autentisering för Azure Storage-tjänsterna](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Signaturer för delad åtkomst Komma igång självstudie

  * [Själv studie kurs om SAS-Komma igång](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Kryptering under överföring
### <a name="transport-level-encryption--using-https"></a>Kryptering på transport nivå – med HTTPS
Ett annat steg som du bör vidta för att säkerställa säkerheten för dina Azure Storage data är att kryptera data mellan klienten och Azure Storage. Den första rekommendationen är att alltid använda [https](https://en.wikipedia.org/wiki/HTTPS) -protokollet, vilket garanterar säker kommunikation via det offentliga Internet.

Om du vill ha en säker kommunikations kanal bör du alltid använda HTTPS när du anropar REST-API: erna eller använder objekt i Storage. Dessutom innehåller **signaturer för delad åtkomst**som kan användas för att delegera åtkomst till Azure Storage objekt, innehåller ett alternativ för att ange att endast HTTPS-protokollet kan användas när du använder signaturer för delad åtkomst, vilket säkerställer att vem skickar ut länkar med SAS-token kommer att använda rätt protokoll.

Du kan använda HTTPS när du anropar REST-API: er för att komma åt objekt i lagrings konton genom att aktivera [säker överföring som krävs](../storage-require-secure-transfer.md) för lagrings kontot. Anslutningar som använder HTTP kommer att nekas när det är aktiverat.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Använda kryptering under överföring med Azure-filresurser
[Azure Files](../files/storage-files-introduction.md) stöder kryptering via SMB 3,0 och med https när du använder filen REST API. När du monterar utanför Azure-regionen finns Azure-filresursen i, till exempel lokalt eller i en annan Azure-region, SMB 3,0 med kryptering krävs alltid. SMB 2,1 stöder inte kryptering, så eftersom standard anslutningar endast tillåts inom samma region i Azure, men SMB 3,0 med kryptering kan tvingas genom att [kräva säker överföring](../storage-require-secure-transfer.md) för lagrings kontot.

SMB 3,0 med kryptering är tillgängligt i [alla Windows-och Windows Server-operativsystem som stöds](../files/storage-how-to-use-files-windows.md) utom Windows 7 och windows Server 2008 R2, som endast stöder SMB 2,1. SMB 3,0 stöds också på [MacOS](../files/storage-how-to-use-files-mac.md) och i distributioner av [Linux](../files/storage-how-to-use-files-linux.md) med hjälp av Linux kernel 4,11 och senare. Krypterings stöd för SMB 3,0 har också lästs in till äldre versioner av Linux-kärnan av flera Linux-distributioner. mer information finns i [så här fungerar krav för SMB-klienter](../files/storage-how-to-use-files-linux.md#smb-client-reqs).

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Använda kryptering på klient sidan för att skydda data som du skickar till lagring
Ett annat alternativ som hjälper dig att säkerställa att dina data är säkra vid överföring mellan ett klient program och att lagringen är kryptering på klient sidan. Data krypteras innan de överförs till Azure Storage. När data hämtas från Azure Storage dekrypteras data när de tas emot på klient sidan. Även om data krypteras över hela kabeln rekommenderar vi att du även använder HTTPS, eftersom det har kontroller för data integritet som skapats som hjälper till att minska nätverks fel som påverkar data integriteten.

Kryptering på klient sidan är också en metod för att kryptera dina data i vila, eftersom data lagras i krypterad form. Vi pratar om detta i mer detalj i avsnittet om [kryptering i vila](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Kryptering i vila
Det finns tre Azure-funktioner som ger kryptering i vila. Azure Disk Encryption används för att kryptera operativ system och data diskar i IaaS Virtual Machines. Kryptering på klient sidan och SSE används båda för att kryptera data i Azure Storage. 

Även om du kan använda kryptering på klient sidan för att kryptera data under överföring (som också lagras i krypterad form i lagrings utrymmet), kanske du föredrar att använda HTTPS under överföringen och har ett visst sätt för data som ska krypteras automatiskt när de lagras. Det finns två sätt att göra detta – Azure Disk Encryption och SSE. En används för att kryptera data på operativ system och data diskar som används av virtuella datorer direkt, och den andra används för att kryptera data som skrivs till Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Kryptering för lagringstjänst (SSE)

SSE har Aktiver ATS för alla lagrings konton och kan inte inaktive ras. SSE krypterar automatiskt dina data när de skrivs till Azure Storage. När du läser data från Azure Storage dekrypteras de av Azure Storage innan de returneras. SSE gör att du kan skydda dina data utan att behöva ändra kod eller lägga till kod i några program.

Du kan använda antingen Microsoft-hanterade nycklar eller dina egna anpassade nycklar. Microsoft genererar hanterade nycklar och hanterar deras skyddade lagrings utrymme samt sin regelbundna rotation, enligt definitionen i den interna Microsoft-principen. Mer information om hur du använder anpassade nycklar finns [kryptering för lagringstjänst att använda Kundhanterade nycklar i Azure Key Vault](storage-service-encryption-customer-managed-keys.md).

SSE krypterar automatiskt data på alla prestandanivåer (Standard och Premium), alla distributionsmodeller (Azure Resource Manager och klassisk) och alla Azure Storage-tjänster (blob, kö, tabell och fil). 

### <a name="client-side-encryption"></a>Kryptering på klient Sidan
Vi har nämnt kryptering på klient sidan när du diskuterar kryptering av data under överföringen. Med den här funktionen kan du kryptera dina data program mässigt i ett klient program innan du skickar dem över kabeln som ska skrivas till Azure Storage och för att program mässigt dekryptera data efter att du har hämtat den från Azure Storage.

Detta ger kryptering vid överföring, men tillhandahåller även funktionen för kryptering i vila. Även om data krypteras under överföringen rekommenderar vi fortfarande att du använder HTTPS för att dra nytta av de inbyggda data integritets kontrollerna som minimerar nätverks fel som påverkar data integriteten.

Ett exempel på hur du kan använda det här är om du har ett webb program som lagrar blobbar och hämtar blobbar, och du vill att programmet och data ska vara så säkra som möjligt. I så fall använder du kryptering på klient sidan. Trafiken mellan klienten och Azure Blob-tjänsten innehåller den krypterade resursen och ingen kan tolka data under överföringen och dela in dem i dina privata blobbar.

Kryptering på klient sidan är inbyggd i Java-och .NET-lagrings klient biblioteken, vilket i sin tur använder Azure Key Vault API: er, vilket gör det enkelt för dig att implementera. Processen för att kryptera och dekryptera data använder kuvert tekniken och lagrar metadata som används av krypteringen i varje lagrings objekt. För blobbar lagras till exempel den i BLOB-metadata, medan för köer läggs den till i varje Queue-meddelande.

För själva krypteringen kan du skapa och hantera dina egna krypterings nycklar. Du kan också använda nycklar som genereras av Azure Storage klient biblioteket, eller så kan du låta Azure Key Vault generera nycklarna. Du kan lagra dina krypterings nycklar i din lokala nyckel lagring, eller så kan du lagra dem i en Azure Key Vault. Med Azure Key Vault kan du bevilja åtkomst till hemligheter i Azure Key Vault till vissa användare som använder Azure Active Directory. Det innebär att inte bara vem kan läsa Azure Key Vault och hämta de nycklar som du använder för kryptering på klient sidan.

#### <a name="resources"></a>Resurser
* [Kryptera och dekryptera blobbar i Microsoft Azure Storage med Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  Den här artikeln visar hur du använder kryptering på klient sidan med Azure Key Vault, inklusive hur du skapar KEK och lagrar den i valvet med PowerShell.
* [Kryptering på klient sidan och Azure Key Vault för Microsoft Azure Storage](../storage-client-side-encryption.md)

  Den här artikeln innehåller en förklaring av kryptering på klient sidan och innehåller exempel på hur du använder lagrings klient biblioteket för att kryptera och dekryptera resurser från de fyra lagrings tjänsterna. Den pratar också om Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Använda Azure Disk Encryption för att kryptera diskar som används av dina virtuella datorer
Med Azure Disk Encryption kan du kryptera de OS-diskar och data diskar som används av en virtuell IaaS-dator. För Windows krypteras enheterna med hjälp av teknik för BitLocker-kryptering enligt bransch standard. För Linux krypteras diskarna med hjälp av DM-crypt-teknik. Detta är integrerat med Azure Key Vault så att du kan styra och hantera disk krypterings nycklarna.

Lösningen har stöd för följande scenarier för virtuella IaaS-datorer när de är aktiverade i Microsoft Azure:

* Integrering med Azure Key Vault
* Virtuella datorer på standard nivå: [A, D, DS, G, GS och så vidare serien IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/) : ar
* Aktivera kryptering för virtuella Windows-och Linux IaaS-datorer
* Inaktivera kryptering på operativ system och data enheter för virtuella Windows IaaS-datorer
* Inaktivera kryptering på data enheter för virtuella Linux IaaS-datorer
* Aktivera kryptering på virtuella IaaS-datorer som kör Windows Client OS
* Aktivera kryptering på volymer med monterings Sök vägar
* Aktivera kryptering på virtuella Linux-datorer som är konfigurerade med disk striping (RAID) med hjälp av mdadm
* Aktivera kryptering på virtuella Linux-datorer med hjälp av LVM för data diskar
* Aktivera kryptering på virtuella Windows-datorer som har kon figurer ATS med hjälp av lagrings utrymmen
* Alla offentliga Azure-regioner stöds

Lösningen har inte stöd för följande scenarier, funktioner och teknik i versionen:

* Basic-nivå IaaS VM
* Inaktivera kryptering på en OS-enhet för virtuella Linux IaaS-datorer
* Virtuella IaaS-datorer som skapas med hjälp av den klassiska skapande metoden för virtuella datorer
* Integrering med din lokala nyckel hanterings tjänst
* Azure Files (delat fil system), virtuella nätverks fil system (NFS), dynamiska volymer och virtuella Windows-datorer som är konfigurerade med programvarubaserade RAID-system


> [!NOTE]
> Linux OS disk Encryption stöds för närvarande på följande Linux-distributioner: RHEL 7,2, CentOS 7,2 n och Ubuntu 16,04.
>
>

Den här funktionen ser till att alla data på de virtuella dator diskarna krypteras i vila i Azure Storage.

#### <a name="resources"></a>Resurser
* [Azure Disk Encryption för virtuella Windows-och Linux IaaS-datorer](../../security/fundamentals/encryption-overview.md)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Jämförelse av Azure Disk Encryption, SSE och kryptering på klient Sidan

#### <a name="iaas-vms-and-their-vhd-files"></a>Virtuella IaaS-datorer och deras VHD-filer

För data diskar som används av virtuella IaaS-datorer rekommenderas Azure Disk Encryption. Om du skapar en virtuell dator med ohanterade diskar med hjälp av en avbildning från Azure Marketplace utför Azure en [ytlig kopia](https://en.wikipedia.org/wiki/Object_copying) av avbildningen till ditt lagrings konto i Azure Storage, och den är inte krypterad även om SSE är aktiverat. När den virtuella datorn har skapats och börjar uppdatera avbildningen börjar SSE att kryptera data. Därför är det bäst att använda Azure Disk Encryption på virtuella datorer med ohanterade diskar som skapats från avbildningar på Azure Marketplace om du vill att de ska vara fullständigt krypterade. Om du skapar en virtuell dator med Managed Disks krypterar SSE alla data som standard med hjälp av plattforms hanterade nycklar. 

Om du tar en förkrypterad virtuell dator i Azure från en lokal plats kan du ladda upp krypterings nycklarna till Azure Key Vault och fortsätta använda krypteringen för den virtuella datorn som du använde lokalt. Azure Disk Encryption har Aktiver ATS för att hantera det här scenariot.

Om du har icke-krypterad virtuell hård disk lokalt kan du överföra den till galleriet som en anpassad avbildning och etablera en virtuell dator från den. Om du gör detta med hjälp av Resource Manager-mallar kan du be den att aktivera Azure Disk Encryption när den virtuella datorn startas.

När du lägger till en datadisk och monterar den på den virtuella datorn, kan du aktivera Azure Disk Encryption på den data disken. Den kommer att kryptera den data disken lokalt och sedan görs en Lazy Write-lagring i det klassiska distributions modell lagret, vilket innebär att lagrings innehållet krypteras.

#### <a name="client-side-encryption"></a>Kryptering av klientsidan
Kryptering på klient sidan är den säkraste metoden för att kryptera dina data, eftersom den krypterar data före överföringen.  Det kräver dock att du lägger till kod till dina program med hjälp av lagring, som du kanske inte vill göra. I dessa fall kan du använda HTTPS för att skydda dina data under överföring. När data når Azure Storage krypteras de av SSE.

Med kryptering på klient sidan kan du kryptera tabell enheter, köa meddelanden och blobbar. 

Kryptering på klient sidan hanteras helt av programmet. Detta är den säkraste metoden, men du måste göra program ändringar i programmet och lägga till nyckel hanterings processer på plats. Du använder det här alternativet om du vill ha extra säkerhet under överföringen och vill att dina lagrade data ska krypteras.

Kryptering på klient sidan är mer belastning på klienten och du måste ta hänsyn till detta i dina skalbarhets planer, särskilt om du krypterar och överför en stor mängd data.

#### <a name="storage-service-encryption-sse"></a>Kryptering för lagringstjänst (SSE)

SSE hanteras av Azure Storage. SSE tillhandahåller inte säkerheten för data som överförs, men krypterar data som skrivs till Azure Storage. SSE påverkar inte Azure Storage-prestanda.

Du kan kryptera alla typer av data från lagrings kontot med hjälp av SSE (block blobbar, bifoga blobbar, Page blobbar, tabell data, köa data och filer).

Om du har ett arkiv eller bibliotek med VHD-filer som du använder som grund för att skapa nya virtuella datorer kan du skapa ett nytt lagrings konto och sedan ladda upp VHD-filerna till det kontot. Dessa VHD-filer kommer att krypteras av Azure Storage.

Om du har Azure Disk Encryption aktiverat för diskarna i en virtuell dator krypteras alla nyligen skrivna data både av SSE och av Azure Disk Encryption.

## <a name="storage-analytics"></a>Lagringsanalys
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Använda Lagringsanalys för att övervaka Authorization-typ
För varje lagrings konto kan du aktivera Azure-lagringsanalys för att utföra loggnings-och lagrings statistik data. Det här är ett bra verktyg som du kan använda när du vill kontrol lera prestanda måtten för ett lagrings konto eller om du behöver felsöka ett lagrings konto eftersom du har prestanda problem.

En annan typ av data som du kan se i Storage Analytics-loggarna är autentiseringsmetoden som används av någon vid åtkomst till lagring. Med Blob Storage kan du till exempel se om de använde en signatur för delad åtkomst eller lagrings konto nycklar, eller om den använda bloben var offentlig.

Detta kan vara användbart om du nära skyddar åtkomsten till lagringen. I Blob Storage kan du till exempel ange att alla behållare ska vara privata och implementera användningen av en SAS-tjänst i alla program. Sedan kan du kontrol lera loggarna regelbundet för att se om dina blobbar nås med hjälp av lagrings konto nycklarna, vilket kan tyda på en säkerhets överträdelse eller om Blobbarna är offentliga men inte bör vara det.

#### <a name="what-do-the-logs-look-like"></a>Vad ser loggarna ut?
När du har aktiverat måtten för lagrings kontot och loggat in via Azure Portal börjar analys data att ackumuleras snabbt. Loggning och mått för varje tjänst är separat. loggningen skrivs bara när det finns en aktivitet i det lagrings kontot, medan måtten loggas varje minut, varje timme eller varje dag, beroende på hur du konfigurerar det.

Loggarna lagras i block-blobar i en behållare med namnet $logs i lagrings kontot. Den här behållaren skapas automatiskt när Lagringsanalys är aktive rad. När den här behållaren har skapats kan du inte ta bort den, men du kan ta bort innehållet.

Under $logs container finns en mapp för varje tjänst och sedan finns det undermappar för året/månaden/dagen/timmen. Under en timme numreras loggarna. Så här kommer katalog strukturen att se ut:

![Vy över loggfiler](./media/storage-security-guide/image1.png)

Varje begäran till Azure Storage loggas. Här är en ögonblicks bild av en loggfil som visar de första fälten.

![Ögonblicks bild av en loggfil](./media/storage-security-guide/image2.png)

Du kan se att du kan använda loggarna för att spåra alla typer av anrop till ett lagrings konto.

#### <a name="what-are-all-of-those-fields-for"></a>Vilka är dessa fält för?
En artikel som listas i resurserna nedan innehåller en lista över de många fälten i loggarna och vad de används för. Här är listan med fält i ordning:

![Ögonblicks bild av fält i en loggfil](./media/storage-security-guide/image3.png)

Vi är intresserade av posterna för GetBlob, och hur de är auktoriserade, så vi måste leta efter poster med åtgärds typ "Get-BLOB" och kontrol lera status för begäran (fjärde </sup> kolumn) och typen Authorization (åttonde </sup>).

I de första raderna i listan ovan är till exempel begäran-status "lyckades" och behörighets typen är "autentiserad". Det innebär att begäran auktoriserades med hjälp av lagrings konto nyckeln.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Hur beviljas åtkomst till mina blobbar?
Vi har tre fall som vi är intresserade av.

1. Blobben är offentlig och används med en URL utan en signatur för delad åtkomst. I det här fallet är förfrågan-status "AnonymousSuccess" och typen av auktorisering är "Anonym".

   1.0; 2015-11-17T02:01:29.0488963 Z; GetBlob **AnonymousSuccess**; 200; 124; 37; **Anonym**;; Mina lagrings enheter...
2. Blobben är privat och användes med en signatur för delad åtkomst. I det här fallet är förfrågan-status "SASSuccess" och behörighets typen "SAS".

   1.0; 2015-11-16T18:30:05.6556115 Z; GetBlob **SASSuccess**; 200; 416; 64; **SAS**;; Mina lagrings enheter...
3. Blobben är privat och lagrings nyckeln användes för att komma åt den. I det här fallet är Request-status "**lyckades**" och behörighets typen är "**autentiserad**".

   1.0; 2015-11-16T18:32:24.3174537 Z; GetBlob **Lyckades**; 206; 59; 22; **autentiserad**; lagring...

Du kan använda Microsoft Message Analyzer för att visa och analysera dessa loggar. Det innehåller funktioner för Sök och filter. Du kanske till exempel vill söka efter instanser av GetBlob för att se om användningen är vad du förväntar dig, det vill säga att ingen kommer åt ditt lagrings konto på ett felaktigt sätt.

#### <a name="resources"></a>Resurser
* [Lagringsanalys](../storage-analytics.md)

  Den här artikeln är en översikt över lagrings analys och hur du aktiverar dem.
* [Lagringsanalys logg format](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  Den här artikeln visar Lagringsanalys logg format och innehåller information om de fält som är tillgängliga där, inklusive autentiseringstyp, som anger vilken typ av autentisering som används för begäran.
* [Övervaka ett lagrings konto i Azure Portal](../storage-monitor-storage-account.md)

  Den här artikeln visar hur du konfigurerar övervakning av mått och loggning för ett lagrings konto.
* [Fel sökning från slut punkt till slut punkt med hjälp av Azure Storage mått och loggning, AzCopy och Message Analyzer](../storage-e2e-troubleshooting.md)

  Den här artikeln beskriver hur du felsöker med hjälp av Lagringsanalys och visar hur du använder Microsoft Message Analyzer.
* [Microsoft Message Analyzer-funktions guide](https://technet.microsoft.com/library/jj649776.aspx)

  Den här artikeln är referens för Microsoft Message Analyzer och innehåller länkar till självstudier, snabb start och funktions Sammanfattning.

## <a name="cross-origin-resource-sharing-cors"></a>Resursdelning för korsande ursprung (CORS)
### <a name="cross-domain-access-of-resources"></a>Åtkomst mellan domäner av resurser
När en webbläsare som körs i en domän gör en HTTP-begäran för en resurs från en annan domän kallas detta för en HTTP-begäran över flera ursprung. En HTML-sida som hanteras från contoso.com gör till exempel en begäran om en JPEG-värd på fabrikam.blob.core.windows.net. Av säkerhets skäl begränsar webbläsare de HTTP-begäranden över flera ursprung som initieras från skript, till exempel Java Script. Det innebär att när en viss JavaScript-kod på en webb sida på contoso.com begär att JPEG på fabrikam.blob.core.windows.net, kommer webbläsaren inte att tillåta begäran.

Vad ska detta göra med Azure Storage? Om du lagrar statiska till gångar, till exempel JSON-eller XML-datafiler i Blob Storage med ett lagrings konto som heter Fabrikam, kommer domänen för till gångarna att fabrikam.blob.core.windows.net och contoso.com-webbappen kommer inte att kunna komma åt dem med hjälp av Java Script eftersom domänerna skiljer sig åt. Detta gäller även om du försöker anropa en av de Azure Storage tjänsterna – till exempel Table Storage – som returnerar JSON-data som ska bearbetas av JavaScript-klienten.

#### <a name="possible-solutions"></a>Möjliga lösningar
Ett sätt att lösa detta är att tilldela en anpassad domän som "storage.contoso.com" till fabrikam.blob.core.windows.net. Problemet är att du bara kan tilldela den anpassade domänen till ett lagrings konto. Vad händer om till gångarna lagras på flera lagrings konton?

Ett annat sätt att lösa detta är att låta webb programmet fungera som en proxy för lagrings samtal. Det innebär att om du laddar upp en fil till Blob Storage kan webb programmet antingen skriva det lokalt och sedan kopiera det till Blob Storage, eller läsa in allt i minnet och skriva det till Blob Storage. Alternativt kan du skriva ett dedikerat webb program (t. ex. ett webb-API) som laddar upp filerna lokalt och skriver dem till Blob Storage. Oavsett vilket du behöver måste du ta hänsyn till den funktionen när du fastställer skalbarhets behoven.

#### <a name="how-can-cors-help"></a>Hur kan CORS hjälpa dig?
Med Azure Storage kan du aktivera CORS – resurs delning mellan ursprung. För varje lagrings konto kan du ange domäner som har åtkomst till resurserna i det lagrings kontot. I vårt fall som beskrivs ovan kan vi till exempel aktivera CORS på fabrikam.blob.core.windows.net lagrings konto och konfigurera det för att tillåta åtkomst till contoso.com. Sedan kan webb programmets contoso.com komma åt resurserna direkt i fabrikam.blob.core.windows.net.

En sak att observera är att CORS tillåter åtkomst, men den ger inte autentisering, vilket krävs för all icke-offentlig åtkomst till lagrings resurser. Det innebär att du bara kan komma åt blobar om de är offentliga eller om du inkluderar en signatur för delad åtkomst som ger dig rätt behörighet. Tabeller, köer och filer har ingen offentlig åtkomst och kräver en SAS.

Som standard är CORS inaktive rad på alla tjänster. Du kan aktivera CORS genom att använda REST API eller lagrings klient biblioteket för att anropa en av metoderna för att ange tjänst principerna. När du gör det inkluderar du en CORS-regel, som är i XML. Här är ett exempel på en CORS-regel som har angetts med åtgärden Ange tjänst egenskaper för BLOB service för ett lagrings konto. Du kan utföra åtgärden med hjälp av lagrings klient biblioteket eller REST-API: erna för Azure Storage.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Så här betyder varje rad:

* **AllowedOrigins** Detta anger vilka icke-matchande domäner som kan begära och ta emot data från lagrings tjänsten. Detta säger att både contoso.com och fabrikam.com kan begära data från Blob Storage för ett särskilt lagrings konto. Du kan också ställa in detta till ett jokertecken (\*) så att alla domäner får åtkomst till begär Anden.
* **AllowedMethods** Detta är listan över metoder (HTTP-begär verb) som kan användas när du gör en begäran. I det här exemplet tillåts endast placering och GET. Du kan ställa in detta till ett jokertecken (\*) för att tillåta att alla metoder används.
* **AllowedHeaders** Detta är begärandehuvuden som ursprungs domänen kan ange när begäran görs. I det här exemplet är alla metadata-huvuden som börjar med x-MS-meta-data, x-MS-meta-Target och x-MS-Meta-ABC tillåtna. Jokertecknet (\*) anger att alla sidhuvuden som börjar med det angivna prefixet är tillåtna.
* **ExposedHeaders** Detta anger vilka svarshuvuden som ska visas av webbläsaren till begär ande utfärdaren. I det här exemplet kommer alla sidhuvuden som börjar med "x-MS-meta-" att visas.
* **MaxAgeInSeconds** Detta är den maximala tid som en webbläsare cachelagrar begäran om preflight-alternativ. (Mer information om preflight-begäran finns i den första artikeln nedan.)

#### <a name="resources"></a>Resurser
Om du vill ha mer information om CORS och hur du aktiverar den, kan du ta en titt på dessa resurser.

* [Stöd för resurs delning mellan ursprung (CORS) för Azure Storage Services på Azure.com](../storage-cors-support.md)

  Den här artikeln innehåller en översikt över CORS och hur du ställer in reglerna för de olika lagrings tjänsterna.
* [Stöd för resurs delning mellan ursprung (CORS) för Azure Storage Services på MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Det här är referens dokumentationen för CORS-stöd för Azure Storage-tjänsterna. Detta innehåller länkar till artiklar som gäller för varje lagrings tjänst och visar ett exempel och förklarar varje element i CORS-filen.
* [Microsoft Azure Storage: Introduktion till CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Det här är en länk till den inledande blogg artikeln om att presentera CORS och visa hur du använder den.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Vanliga frågor och svar om Azure Storage säkerhet
1. **Hur kan jag kontrol lera integriteten för de blobbar som jag överför till eller från Azure Storage om jag inte kan använda HTTPS-protokollet?**

   Om du av någon anledning behöver använda HTTP i stället för HTTPS och du arbetar med block blobbar, kan du använda MD5-kontroll för att kontrol lera integriteten för de blobbar som överförs. Detta hjälper till att skydda mot fel på nätverks-/transport nivå, men inte nödvändigt vis med mellanliggande attacker.

   Om du kan använda HTTPS, som tillhandahåller säkerhet på transport nivå, är det redundant och onödigt att använda MD5-kontrollen.

   Mer information finns i [Översikt över Azure Blob MD5](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).
2. **Vad gäller FIPS-efterlevnad för amerikanska myndigheter?**

   FIPS (USA Federal Information Processing Standard) definierar kryptografiska algoritmer som godkänns för användning av amerikanska federala myndighets dator system för skydd av känsliga data. Att aktivera FIPS-läge på en Windows-Server eller skriv bord visar vilket operativ system som endast ska användas av FIPS-validerade kryptografiska algoritmer. Om ett program använder icke-kompatibla algoritmer avbryts programmen. With.NET Framework-versioner 4.5.2 eller senare växlar programmet automatiskt till krypteringsalgoritmerna för att använda FIPS-kompatibla algoritmer när datorn är i FIPS-läge.

   Microsoft lämnar dem till varje kund för att bestämma om FIPS-läge ska aktive ras. Vi tror att det inte finns något övertygande skäl för kunder som inte omfattas av myndighets bestämmelser för att aktivera FIPS-läge som standard.

### <a name="resources"></a>Resurser
* [Varför rekommenderar vi inte att "FIPS-läge" är längre](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  Den här blogg artikeln ger en översikt över FIPS och förklarar varför de inte aktiverar FIPS-läge som standard.
* [FIPS 140-validering](https://technet.microsoft.com/library/cc750357.aspx)

  Den här artikeln innehåller information om hur Microsoft-produkter och-kryptografiska moduler följer FIPS-standarden för amerikanska federala myndigheter.
* ["System kryptografi: Använd FIPS-kompatibla algoritmer för kryptering, hashing och signering" säkerhets inställnings effekter i Windows XP och senare versioner av Windows](https://support.microsoft.com/kb/811833)

  Den här artikeln pratar om användningen av FIPS-läge på äldre Windows-datorer.
