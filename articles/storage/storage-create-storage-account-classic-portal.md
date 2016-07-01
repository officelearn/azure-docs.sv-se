<properties
    pageTitle="Skapa, hantera eller ta bort ett lagringskonto på den klassiska Azure-portalen | Microsoft Azure"
    description="Skapa ett nytt lagringskonto, hantera åtkomstnycklarna för ditt konto eller ta bort ett lagringskonto på Azure Portal. Läs mer om premium- och standardlagringskonton."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/09/2016"
    ms.author="robinsh"/>


# Om Azure-lagringskonton

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## Översikt

Med ett Azure Storage-konto får du tillgång till Azures blobb-, kö-, tabell- och filtjänster i Azure Storage. Ditt lagringskonto tillhandahåller den unika namnrymden för dina Azure Storage-dataobjekt. Som standard är data i ditt konto endast tillgängliga för dig, kontoägaren.

Det finns två typer av lagringskonton:

- Ett standardlagringskonto som erbjuder blobb-, tabell-, kö- och fillagring.
- Ett premiumlagringskonto som för närvarande endast stöder virtuella datorer i Azure. En detaljerad översikt över Premium Storage finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](storage-premium-storage.md).

## Fakturering för lagringskonto

Du debiteras för användningen av Azure Storage baserat på ditt lagringskonto. Storage-kostnaderna baseras på fyra faktorer: lagringskapacitet, replikeringsschema, lagringstransaktioner och utgående datatrafik.

- Lagringskapacitet syftar på hur mycket av lagringskontots tilldelade utrymme som du använder för att lagra data. Kostnaden för att bara lagra data bestäms av hur mycket data du lagrar och hur de replikeras.
- Replikeringen anger hur många kopior av dina data som hanteras på samma gång och på vilka platser.
- Transaktioner avser alla läs- och skrivåtgärder till Azure Storage.
- Utgående datatrafik (egress) syftar på data som överförs ut från en Azure-region. När data på ditt lagringskonto används av ett program som inte körs i samma region, oavsett om programmet är en molntjänst eller en annan typ av program, så debiteras du för den utgående datatrafiken. (För Azure-tjänster kan du gruppera dina data och tjänster i samma datacenter för att därigenom minska eller eliminera kostnaderna för utgående datatrafik.)  

Sidan [Priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage) innehåller detaljerad prisinformation för lagringskapacitet, replikering och transaktioner. Sidan [Prisinformation om dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) innehåller detaljerad prisinformation för utgående datatrafik.

Detaljerad information om ett lagringskontos kapacitets- och prestandamål finns i [Skalbarhets- och prestandamål för Azure Storage](storage-scalability-targets.md).

> [AZURE.NOTE] När du skapar en virtuell dator i Azure skapas ett lagringskonto automatiskt åt dig på distributionsplatsen om du inte redan har ett lagringskonto på den platsen. Du behöver alltså inte följa stegen nedan för att skapa ett lagringskonto för dina virtuella datorer. Namnet på lagringskontot baseras på den virtuella datorns namn. Mer information finns i [dokumentationen för Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Skapa ett lagringskonto

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).

2. Klicka på **Nytt** i Aktivitetsfältet längst ned på sidan. Välj **Datatjänster** | **Storage** och klicka sedan på **Snabbregistrering**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. I **URL** anger du ett namn för ditt lagringskonto.

    > [AZURE.NOTE] Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
    >  
    > Namnet på ditt lagringskonto måste vara unikt i Azure. Den klassiska Azure-portalen meddelar dig om det namn som du väljer på lagringskontot redan är taget.

    Mer information om hur lagringskontots namn används för att adressera dina objekt i Azure Storage finns nedan i [Slutpunkter för lagringskonto](#storage-account-endpoints).

4. I **Plats/tillhörighetsgrupp** väljer du en plats för ditt lagringskonto som ligger nära dig eller dina kunder. Om data i ditt lagringskonto ska kunna nås från en annan Azure-tjänst, till exempel en molntjänst eller en virtuell dator i Azure, kan du välja en tillhörighetsgrupp i listan och gruppera ditt lagringskonto i samma datacenter med andra Azure-tjänster som du använder för att därigenom förbättra prestanda och sänka kostnaderna.

    Observera att du måste välja en tillhörighetsgrupp när ditt lagringskonto skapas. Du kan inte flytta ett befintligt konto till en tillhörighetsgrupp. Mer information om tillhörighetsgrupper finns i  [Samplacering av tjänster med en tillhörighetsgrupp](#service-co-location-with-an-affinity-group) nedan.

    >[AZURE.IMPORTANT] Om du vill ta reda på vilka platser som är tillgängliga för din prenumeration anropar du åtgärden som [visar en lista över alla resursproviders](https://msdn.microsoft.com/library/azure/dn790524.aspx). Om du vill visa alla tillgängliga providers med hjälp av PowerShell anropar du [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). Från .NET använder du metoden [List](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) i klassen ProviderOperationsExtensions.
    >
    >Mer information om vilka tjänster som är tillgängliga i vilken region finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).


5. Om du har mer än en Azure-prenumeration visas fältet **Prenumeration**. I **Prenumeration** anger du den Azure-prenumeration som du vill använda lagringskontot med.

6. I **Replikering** väljer du önskad replikeringsnivå för ditt lagringskonto. Det rekommenderade replikeringsalternativet är geo-redundant replikering, som ger maximal hållbarhet för dina data. Mer information om replikeringsalternativen för Azure Storage finns i [Azure Storage-replikering](storage-redundancy.md).

6. Klicka på **Skapa Storage-konto**.

    Det kan ta några minuter att skapa lagringskontot. Meddelandena som visas längst ned på den klassiska Azure-portalen anger statusen. När lagringskontot har skapats har det nya kontot statusen **Online** och är redo att användas.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### Slutpunkter för lagringskonto

Alla objekt som du lagrar i Azure Storage har en unik URL-adress. Lagringskontots namn bildar underdomänen i den adressen. Kombinationen av underdomän och domännamn, som är specifika för varje tjänst, utgör en *slutpunkt* för ditt lagringskonto.

Om ditt lagringskonto till exempel heter *mittlagringskonto*, så är standardslutpunkterna för ditt lagringskonto:

- Blob-tjänst: http://*mittlagringskonto*.blob.core.windows.net

- Tabelltjänst: http://*mittlagringskonto*.table.core.windows.net

- Kötjänst: http://*mittlagringskonto*.queue.core.windows.net

- Filtjänst: http://*mittlagringskonto*.file.core.windows.net

Du kan se slutpunkterna för ditt lagringskonto på instrumentpanelen för lagring på den [klassiska Azure-portalen](https://manage.windowsazure.com) när kontot har skapats.

URL:en för åtkomst till ett objekt i ett lagringskonto skapas genom att objektets plats i lagringskontot läggs till i slutpunkten. En blobbadress kan till exempel ha följande format: http://*mittlagringskonto*.blob.core.windows.net/*minbehållare*/*minblobb*.

Du kan också konfigurera ett eget domännamn som ska användas med ditt lagringskonto. Mer information finns i [Konfigurera ett eget domännamn för din slutpunkt för Blob Storage](storage-custom-domain-name.md).

### Samplacering av tjänster med en tillhörighetsgrupp

En *tillhörighetsgrupp* är en geografisk gruppering av dina Azure-tjänster och virtuella datorer med ditt Azure Storage-konto. En tillhörighetsgrupp kan förbättra tjänstprestanda genom att placera datorarbetsbelastningar i samma datacenter eller nära målanvändarna. Dessutom utgår inga faktureringskostnader för utgående trafik när en annan tjänst som ingår i samma tillhörighetsgrupp använder data i lagringskontot.

> [AZURE.NOTE]  Om du vill skapa en tillhörighetsgrupp öppnar du området <b>Inställningar</b> på den [klassiska Azure-portalen](https://manage.windowsazure.com), klickar på <b>Tillhörighetsgrupper</b> och klickar sedan antingen på <b>Lägg till en tillhörighetsgrupp</b> eller på knappen <b>Lägg till</b>. Du kan också skapa och hantera tillhörighetsgrupper med hjälp av Azure Service Management-API:et. Mer information finns i <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">Åtgärder med tillhörighetsgrupper</a>.

## Visa, kopiera och återskapa åtkomstnycklar för lagring

När du skapar ett lagringskonto genererar Azure två 512-bitars åtkomstnycklar för lagring, som används för autentisering när lagringskontot används. Eftersom två åtkomstnycklar för lagring genereras kan du återskapa nycklarna utan avbrott i lagringstjänsten eller i åtkomsten till den tjänsten.

> [AZURE.NOTE] Vi rekommenderar att du inte delar dina åtkomstnycklar för lagring med andra. Du kan ge åtkomst till lagringsresurser utan att lämna ut dina åtkomstnycklar genom att använda en *signatur för delad åtkomst*. En signatur för delad åtkomst ger åtkomst till en resurs i ditt konto under ett intervall som du definierar och med de behörigheter som du anger. Mer information finns i [Signaturer för delad åtkomst: Förstå SAS-modellen](storage-dotnet-shared-access-signature-part-1.md).

På den [klassiska Azure-portalen](https://manage.windowsazure.com) använder du **Hantera nycklar** på instrumentpanelen eller sidan **Storage** för att visa, kopiera och återskapa åtkomstnycklar för lagring som används för att komma åt blobb-, tabell- och kötjänsterna.

### Kopiera en lagringsåtkomstnyckel  

Du kan använda **Hantera nycklar** för att kopiera en lagringsåtkomstnyckel som ska användas i en anslutningssträng. Anslutningssträngen kräver lagringskontots namn och en nyckel som ska användas vid autentisering. Information om hur du konfigurerar anslutningssträngar för att få åtkomst till Azure-lagringstjänster finns i [Konfigurera Azure Storage-anslutningssträngar](storage-configure-connection-string.md).

1. På den [klassiska Azure-portalen](https://manage.windowsazure.com) klickar du på **Storage** och sedan på namnet på lagringskontot för att öppna instrumentpanelen.

2. Klicka på **Hantera nycklar**.

    **Hantera åtkomstnycklar** öppnas.

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Du kopierar en lagringsåtkomstnyckel genom att markera nyckeltexten. Högerklicka sedan och klicka på **Kopiera**.

### Återskapa åtkomstnycklar för lagring
Vi rekommenderar att du ändrar åtkomstnycklarna för ditt lagringskonto med jämna mellanrum för att skydda lagringsanslutningarna. Två åtkomstnycklar tilldelas så att du kan upprätthålla anslutningar till lagringskontot med den ena åtkomstnyckeln medan du återskapar den andra.

> [AZURE.WARNING] Tjänster i Azure och dina program som är beroende av lagringskontot kan påverkas när du återskapar åtkomstnycklarna. Alla klienter som använder åtkomstnycklar för att komma åt lagringskontot måste uppdateras så att de använder den nya nyckeln.

**Medietjänster** – Om du har medietjänster som är beroende av ditt lagringskonto måste du synkronisera åtkomstnycklarna med medietjänsten igen när du har återskapat nycklarna.

**Program** – Om du har webbprogram och molntjänster som använder lagringskontot går anslutningarna förlorade om du återskapa nycklar, såvida du inte ”rullar” nycklarna. 

**Lagringsutforskare** – Om du använder [lagringsutforskare](storage-explorers.md) behöver du förmodligen uppdatera lagringsnyckeln som används av dessa program.

Processen för att rotera åtkomstnycklar för lagring ser ut så här:

1. Uppdatera anslutningssträngarna i programkoden så att de refererar till lagringskontots sekundära åtkomstnyckel.

2. Återskapa lagringskontots primära åtkomstnyckel. På den [klassiska Azure-portalen](https://manage.windowsazure.com) klickar du på **Hantera nycklar** från instrumentpanelen eller sidan **Konfigurera**. Klicka på **Återskapa** under den primära åtkomstnyckeln och klicka sedan på **Ja** för att bekräfta att du vill skapa en ny nyckel.

3. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.

4. Återskapa den sekundära åtkomstnyckeln.

## Ta bort ett lagringskonto

Om du vill ta bort ett lagringskonto som du inte längre använder klickar du på **Ta bort** på instrumentpanelen eller sidan **Konfigurera**. **Ta bort** tar bort hela lagringskontot, inklusive alla blobbar, tabeller och köer i kontot.

> [AZURE.WARNING] Det går inte att återställa ett borttaget lagringskonto eller att hämta innehåll som det innehöll före borttagningen. Var noga med att säkerhetskopiera allt som du vill spara innan du tar bort kontot. Detta gäller även alla resurser i kontot. När du tar bort en blobb, tabell, kö eller fil tas den bort permanent.
>
> Om ditt lagringskonto innehåller VHD-filer för en virtuell dator i Azure måste du ta bort alla avbildningar och diskar som använder dessa VHD-filer innan du kan ta bort lagringskontot. Börja med att stoppa den virtuella datorn om den körs och ta sedan bort den. Om du vill ta bort diskar går du till fliken **Diskar** och tar bort diskarna där. Om du vill ta bort avbildningar går du till fliken **Avbildningar** och tar bort eventuella avbildningar som lagras i kontot.

1. På den [klassiska Azure-portalen](https://manage.windowsazure.com) klickar du på **Lagring**.

2. Klicka någonstans på lagringskontot utom på namnet och klicka sedan på **Ta bort**.

     Eller

    Klicka på namnet på lagringskontot för att öppna instrumentpanelen och klicka sedan på **Ta bort**.

3. Klicka på **Ja** för att bekräfta att du vill ta bort lagringskontot.

## Nästa steg

- Mer information om Azure Storage finns i [Azure Storage-dokumentationen](https://azure.microsoft.com/documentation/services/storage/).
- Besök [Azure Storage-teamets blogg](http://blogs.msdn.com/b/windowsazurestorage/).
- [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)



<!--HONumber=Jun16_HO2-->


