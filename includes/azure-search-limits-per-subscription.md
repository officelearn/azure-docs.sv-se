Du kan skapa flera tjänster inom en prenumeration, var och en tillhandahållas på en specifik nivå begränsas bara av antal tjänster på varje nivå. Du kan skapa upp till 12 services på den grundläggande nivån och en annan 12 tjänster på S1 nivån inom samma prenumeration. Läs mer om nivåer [Välj en SKU- eller nivå för Azure Search](../articles/search/search-sku-tier.md).

Högsta tillåtna gränser kan aktiveras på begäran. Kontakta Azure-supporten om du behöver fler tjänster inom samma prenumeration.

| Resurs | Kostnadsfri | Basic | S1 | S2 | S3 | S3 HD <sup>1</sup> |
| --- | --- | --- | --- | --- | --- | --- |
| Maximal tjänster |1 |12 |12 |6 |6 |6 |
| Maximal skala i SU <sup>2</sup> |EJ TILLÄMPLIGT <sup>3</sup> |3 SU <sup>4</sup> |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> S3 HD stöder inte [indexerare](../articles/search/search-indexer-overview.md) just nu. 

<sup>2</sup> search-enheter (SU) fakturerar enheter som allokerats som antingen en *replik* eller en *partition*. Du behöver båda resurserna för lagring, indexering och frågor. Mer information om hur sökenheter beräknas plus ett diagram över giltiga kombinationer som förblir under gränsvärden finns [skala resursen nivåer för fråge- och arbetsbelastningar](../articles/search/search-capacity-planning.md). 

<sup>3</sup> lediga baseras på delade resurser som används av flera prenumeranter. Det finns inga dedikerade resurser för en enskild prenumeranten för på den här nivån. Därför har maxskala markerats som ej tillämpligt.

<sup>4</sup> basic har en fast partition. I det här skiktet används ytterligare SUs för att tilldela flera repliker för ökad frågan arbetsbelastningar.

