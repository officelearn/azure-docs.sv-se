| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Batch-konton per region per prenumeration | 3 |50 |
| Dedikerad kärnor per Batch-kontot (service batchläge)<sup>1</sup> | 20 | EJ TILLÄMPLIGT<sup>2</sup> |
| Låg prioritet kärnor per Batch-kontot (service batchläge)<sup>3</sup> | 50 | EJ TILLÄMPLIGT<sup>4</sup> |
| Aktiva jobb och jobbscheman<sup>5</sup> per Batch-kontot | 20 | 5000<sup>6</sup> |
| Pooler per Batch-konto | 20 | 2500 |

<sup>1</sup> dedikerade core kvoter som visas är endast för konton med poolen allokering läge värdet **Batch-tjänsten**. Konton med läget inställd på **användarens prenumeration**, core kvoter baseras på VM kärnor kvoten på regional nivå eller per VM familj i din prenumeration.

<sup>2</sup> kan du öka antalet dedicerade kärnor per Batch-kontot, men det maximala antalet är okänt. Kontakta Azure-supporten att diskutera öka alternativ.

<sup>3</sup> låg prioritet core kvoter som visas är endast för konton med poolen allokering läge värdet **Batch-tjänsten**. Låg prioritet kärnor är inte tillgängliga för konton med poolen allokering läge värdet **användarens prenumeration**.

<sup>4</sup> kan du öka antalet låg prioritet kärnor per Batch-kontot, men det maximala antalet är okänt. Kontakta Azure-supporten att diskutera öka alternativ.

<sup>5</sup> slutförda jobb och jobbscheman begränsas inte.

<sup>6</sup> kontakta Azure-supporten om du vill begära en denna gräns.
