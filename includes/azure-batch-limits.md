| **Resurs** | **Standardgräns** | **Övre gräns** |
| --- | --- | --- |
| Batch-konton per region per prenumeration | 1 – 3 |50 |
| Reserverade kärnor per Batch-konto | 10 - 100 | N/A<sup>1</sup> |
| Lågprioritetskärnor per Batch-konto | 10 - 100 | EJ TILLÄMPLIGT<sup>2</sup> |
| Aktiva jobb och jobbscheman<sup>3</sup> per Batch-konto | 100 – 300 | 2500<sup>4</sup> |
| Pooler per Batch-konto | 20 – 100 | 500 |

> [!NOTE]
> Standardgränserna varierar beroende på vilken typ av prenumeration som du använder för att skapa ett Batch-konto. Kärnkvoter som visas är för Batch-konton i Batch-tjänstläge. [Visa kvoter i Batch-kontot](../articles/batch/batch-quota-limit.md#view-batch-quotas). 

<sup>1</sup> kan du öka antalet reserverade kärnor per Batch-konto, men det maximala antalet är okänt. Kontakta Azure-supporten diskutera alternativ för utökning.

<sup>2</sup> kan du öka antalet lågprioritetskärnor per Batch-konto, men det maximala antalet är okänt. Kontakta Azure-supporten diskutera alternativ för utökning.

<sup>3</sup> slutförda jobb och jobbscheman begränsas inte.

<sup>4</sup> kontakta Azure-supporten om du vill begära en ökning utöver denna gräns.
