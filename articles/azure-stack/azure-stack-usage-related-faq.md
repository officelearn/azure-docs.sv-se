 ---
Rubrik: användnings-API relaterade vanliga frågor och svar | Microsoft Docs beskrivning: listan över Azure Stack mätare jämförelse för användning av Azure API, tid för användning och rapporterade tid felkoder.
tjänster: azure-stacken dokumentationcenter: '' författare: mattbriggs manager: femila editor: ''

ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071 ms.service: azure-stack ms.workload: na ms.tgt_pltfrm: na ms.devlang: na ms.topic: article ms.date: 03/09/2018 ms.author: mabrigg ms.reviewer: alfredop

---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Vanliga frågor i Azure-stacken användnings-API
Den här artikeln besvarar några vanliga frågor om Azure-stacken användning API.

## <a name="what-meter-ids-can-i-see"></a>Vilka mätaren ID: N kan se?
Användning har rapporterats för följande resursproviders:

| **Resursprovider** | **Mätaren ID** | **Mätaren namn** | **enhet** | **Ytterligare information** |
| --- | --- | --- | --- | --- |
| **Nätverk** |F271A8A388C44D93956A063E1D2FA80B |Användning av statisk IP-adress |IP-adresser| Räkna IP-adresser som används. Om du anropar API för användning med en timme, returnerar mätaren IP-adress multiplicerat med antalet timmar. |
| |9E2739BA86744796B465F64674B822BA |Dynamisk IP-Adressanvändning |IP-adresser| Räkna IP-adresser som används. Om du anropar API för användning med en timme, returnerar mätaren IP-adress multiplicerat med antalet timmar. |
| **Storage** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*timmar |Total kapacitet som används av tabeller. |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*timmar |Total kapacitet som används av sidblobar. |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*timmar |Total kapacitet som används av kön. |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*timmar |Total kapacitet som används av blockblobbar. |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Antalet begäranden i 10 000's |Tabellen tjänstbegäranden (i 10 000-tal). |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Ingång data i GB |Tabellen service data ingång i GB. |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Utgående trafik i GB |Tabellen service datatrafik i GB |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Antal begäranden i 10 för 000's |BLOB tjänstbegäranden (i 10 000-tal). |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Ingång data i GB |BLOB-tjänsten data ingång i GB. |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Utgående trafik i GB |BLOB-tjänsten datatrafik i GB. |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Antal begäranden i 10 för 000's |Kön tjänstbegäranden (i 10 000-tal). |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Ingång data i GB |Kön tjänsten data ingång i GB. |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Utgående trafik i GB |Kön service datatrafik i GB |
| **Sql RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*timmar   | Total DB kapacitet vid skapandet. Om du anropar API för användning med en timme, returnerar mätaren MB multiplicerat med antalet timmar. |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*timmar    | Total DB kapacitet vid skapandet. Om du anropar API för användning med en timme, returnerar mätaren MB multiplicerat med antalet timmar. |
| **Compute** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Timmar för grundläggande VM-storlek |Virtuella kärntimmar | Antal virtuella kärnor multiplicerat med timmar som den virtuella datorn kördes. |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM storlek timmar |Virtuella kärntimmar | Antal virtuella kärnor multiplicerat med timmar för den virtuella datorn kördes. |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Timmar för VM-storlek |VM-timmar |Samlar in bas- och Windows-VM. Justeras inte för kärnor. |
| **Key Vault** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Key Vault transaktioner | Antalet begäranden i 10 000's| Antal REST API-begäranden tas emot av Key Vault dataplan. |
| |2C354225-B2FE-42E5-AD89-14F0EA302C87 |Avancerade nycklar transaktioner | 10K transaktioner|     RSA 3K / 4K, ECC key transaktioner. (förhandsversion). |
| **Apptjänst** | 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA | App Service | Virtuella kärntimmar | Antal virtuella kärnor som används för att köra app service. Obs: Microsoft använder den här mätaren debiterar Apptjänst Azure stacken. Leverantörer av molntjänster kan använda App Service mätare (nedan) för att beräkna användning för sina klienter. |
|  | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | Funktioner begäranden | 10 begäranden | Totalt antal begärda körningar (per 10 körningar). Körningar räknas varje gång en funktion körs som svar på en händelse eller utlöses av en bindning. |
|  | D1D04836-075C-4F27-BF65-0A1130EC60ED | Funktioner - beräkning | GB-s | Resursförbrukning mätt i sekunder för gigabyte (GB/s). **Observerade resursförbrukning** beräknas genom att multiplicera genomsnittlig minne i GB med tid i millisekunder det tar för att köra funktionen. Minne som används av en funktion som mäts med avrundningen upp till närmaste 128 MB upp till de största minnesstorleken 1,536 MB med körningstid beräknas genom att avrundas till närmaste 1 ms. Den minsta körningstid minne för en enskild funktion körningen är och 100 ms och 128 mb respektive. |
|  | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | Timmar för delad Apptjänst | 1 timme | Per timme användning av Fragmentera App Service-Plan. Planer förbrukade på grundval av per App. |
|  | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | Kostnadsfri Apptjänst-timmar | 1 timme | Per timme användning av ledigt App Service-Plan. Planer förbrukade på grundval av per App. |
|  | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | Liten Standard Apptjänst-timmar | 1 timme | Beräknas baserat på antalet och storleken på instanser. |
|  | 83A2A13E-4788-78DD-5D55-2831B68ED825 | Medelhög Standard Apptjänst-timmar | 1 timme | Beräknas baserat på antalet och storleken på instanser. |
|  | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | Stora Standard Apptjänst-timmar | 1 timme | Beräknas baserat på antalet och storleken på instanser. |
|  | *Anpassade Worker nivåer* | Anpassade Worker nivåer | Timmar | Deterministiska mätaren ID skapas baserat på SKU: N och anpassade worker nivånamn. Den här mätaren-ID är unikt för varje anpassad worker-nivå. |
|  | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL | Per SNI SSL-bindning | Apptjänst stöder två typer av SSL-anslutningar: Server Servernamnsindikation (SNI) SSL-anslutningar och IP-adress SSL-anslutningar. SNI-baserat SSL fungerar på moderna webbläsare och IP-baserat SSL fungerar på alla webbläsare. |
|  | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL | Per IP baserade SSL-bindning | Apptjänst stöder två typer av SSL-anslutningar: Server Servernamnsindikation (SNI) SSL-anslutningar och IP-adress SSL-anslutningar. SNI-baserat SSL fungerar på moderna webbläsare och IP-baserat SSL fungerar på alla webbläsare. |
|  | 73215A6C-FA54-4284-B9C1-7E8EC871CC5B | Webbprocess |  | Beräkna per aktiv plats per timme. |
|  | 5887D39B-0253-4E12-83C7-03E1A93DFFD9 | Externa utgång bandbredd | GB | Totalt antal inkommande begäran svar byte + total utgående begärans byte + totala inkommande FTP-begäran svar byte + totalt antal inkommande web distribuera begäran svar byte. |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Hur gör Azure Stack-användning API: er som jämför med den [Azure användnings-API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (för närvarande i förhandsversion)?
* Användning-API-klient är konsekvent med Azure-API, med ett undantag: den *showDetails* flaggan för närvarande inte stöds i Azure-stacken.
* API: et för providern användning gäller bara för Azure-stacken.
* För närvarande den [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) som är tillgängliga i Azure är inte tillgängligt i Azure-stacken.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Vad är skillnaden mellan användning och rapporterade tid?
Data användningsrapporter har två huvudsakliga tidsvärden:

* **Rapporterat gång**. Den tid när händelsen användning anges systemets användning
* **Användning tid**. Den tid när resursen Azure Stack förbrukades

Du kan se en avvikelse i värden för användning och rapporterade tiden för en specifik händelse. Fördröjningen kan vara upp till flera timmar i alla miljöer.

För närvarande kan du fråga endast av *rapporterade tid*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Vad betyder dessa användning API-felkoder?
| **HTTP-statuskod** | **Felkod** | **Beskrivning** |
| --- | --- | --- |
| 400/Felaktig begäran |*NoApiVersion* |Den *api-versionen* frågeparameter saknas. |
| 400/Felaktig begäran |*InvalidProperty* |En egenskap saknas eller har ett ogiltigt värde. Meddelandet i felkoden i svarstexten identifierar egenskapen saknas. |
| 400/Felaktig begäran |*RequestEndTimeIsInFuture* |Värdet för *ReportedEndTime* ligger i framtiden. Värden är i framtiden inte tillåtna för det här argumentet. |
| 400/Felaktig begäran |*SubscriberIdIsNotDirectTenant* |Tjänstproviderns API-anrop har använt ett prenumerations-ID inte är en giltig klient för anroparen. |
| 400/Felaktig begäran |*SubscriptionIdMissingInRequest* |Prenumerations-ID för anroparen saknas. |
| 400/Felaktig begäran |*InvalidAggregationGranularity* |En ogiltig aggregering granularitet begärdes. Giltiga värden är varje dag och varje timme. |
| 503 |*ServiceUnavailable* |Ett återförsökbart fel inträffade eftersom tjänsten är upptagen eller anropet har begränsats. |

## <a name="next-steps"></a>Nästa steg
[Kunden fakturering och återbetalning i Azure-stacken](azure-stack-billing-and-chargeback.md)

[Providern Resursanvändning API](azure-stack-provider-resource-api.md)

[Resursanvändning API-klient](azure-stack-tenant-resource-usage-api.md)
