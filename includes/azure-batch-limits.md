| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Batch-konton per region per prenumeration | 1 - 3 |50 |
| Dedikerad kärnor per Batch-kontot | 10 - 100 | N/A<sup>1</sup> |
| Låg prioritet kärnor per Batch-kontot | 10 - 100 | EJ TILLÄMPLIGT<sup>2</sup> |
| Aktiva jobb och jobbscheman<sup>3</sup> per Batch-kontot | 20 - 100 | 5000<sup>4</sup> |
| Pooler per Batch-konto | 20 - 100 | 2500 |

> [!NOTE]
> Standardgränser varierar beroende på vilken typ av prenumeration som du använder för att skapa ett Batch-konto. Kärnor kvoter som visas är för Batch-konton i batchläge för tjänsten. [Visa kvoter i Batch-kontot](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup> kan du öka antalet dedicerade kärnor per Batch-kontot, men det maximala antalet är okänt. Kontakta Azure-supporten att diskutera öka alternativ.

<sup>2</sup> kan du öka antalet låg prioritet kärnor per Batch-kontot, men det maximala antalet är okänt. Kontakta Azure-supporten att diskutera öka alternativ.

<sup>3</sup> slutförda jobb och jobbscheman begränsas inte.

<sup>4</sup> kontakta Azure-supporten om du vill begära en denna gräns.
