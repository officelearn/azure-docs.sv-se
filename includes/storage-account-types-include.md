Det finns två typer av lagringskonton:

### Allmänna lagringskonton
<a id="general-purpose-storage-accounts" class="xliff"></a>
Ett allmänt lagringskonto ger dig åtkomst till Azure Storage-tjänster som tabeller, köer, filer, blobbar och virtuella Azure-datordiskar, allt med ett enda konto. Den här typen av lagringskonto har två prestandanivåer:

* Standardnivå för lagringsprestanda som låter dig lagra tabeller, köer, filer, blobbar och virtuella Azure-datordiskar.
* Premiumnivån för lagringsprestanda som för närvarande bara stöder virtuella Azure-datordiskar. En detaljerad översikt över Premium-lagring finns i [Premium Storage: högpresterande lagring för virtuella Azure-datorbelastningar](../articles/storage/storage-premium-storage.md).

### Blob Storage-konton
<a id="blob-storage-accounts" class="xliff"></a>
Ett Blob-lagringskonto är ett specialiserat lagringskonto för lagring av ostrukturerad data som blobbar (objekt) i Azure Storage. Blob-lagringskonton liknar dina befintliga allmänna lagringskonton och har samma höga hållbarhet, tillgänglighet, skalbarhet och prestanda som du använder idag, inklusive 100 % API-konsekvens för blockblobbar och tilläggsblobbar. För program som bara behöver lagring av block- eller tilläggsblobbar, rekommenderar vi att du använder Blob-lagringskonton.

> [!NOTE]
> Blob Storage-konton stöder endast block- och tilläggsblobar, inte sidblobar.
> 
> 

Blob-lagringskonton exponerar **Åtkomstnivå**-attributet som kan anges vid kontoskapandet och ändras senare vid behov. Det finns två typer av åtkomstnivåer som kan anges baserat på dina mönster för dataåtkomst:

* En nivå för **frekvent** åtkomst, vilket indikerar att objekten på lagringskontot kommer att användas oftare. På så sätt kan du lagra data med en lägre åtkomstkostnad.
* En nivå för **lågfrekvent** åtkomst, vilket indikerar att objekten på lagringskontot kommer att användas mindre ofta. På så sätt kan du lagra data med en lägre lagringskostnad.

Du kan när som helst växla mellan de olika nivåerna om användningsmönstret förändras. Observera att ytterligare kostnader kan tillkomma om du ändrar åtkomstnivå. Mer information finns i [Priser och fakturering för Blob-lagringskonton](../articles/storage/storage-blob-storage-tiers.md#pricing-and-billing).

Mer information om Blob-lagringskonton finns i [Azure Blob Storage: frekvent och lågfrekvent nivå](../articles/storage/storage-blob-storage-tiers.md).

Innan du kan skapa ett lagringskonto, måste du ha en Azure-prenumeration, som är en plan vilken ger dig tillgång till en mängd olika Azure-tjänster. Du kan komma igång med Azure med ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/). När du väl bestämt dig att köpa ett prenumerationsavtal, kan du välja från en rad olika [köpalternativ](https://azure.microsoft.com/pricing/purchase-options/). Om du är en [MSDN-prenumerant](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), får du kostnadsfria, månatliga krediter som kan användas med Azure-tjänster, inklusive Azure Storage. Se [Azure Storage-priser ](https://azure.microsoft.com/pricing/details/storage/) för information om volympriser.

Se [Skapa ett lagringskonto](../articles/storage/storage-create-storage-account.md#create-a-storage-account) för mer information om hur du skapar ett lagringskonto. Du kan skapa upp till 200 unikt namngivna lagringskonton med en enda prenumeration. Se [Skalbarhets- och prestandamål för Azure Storage](../articles/storage/storage-scalability-targets.md) för information om begränsningar för lagringskonton.

